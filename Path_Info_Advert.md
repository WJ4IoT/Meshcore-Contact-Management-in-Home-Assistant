# Path information of an Advert.
This will try to attempt to collect the first received advert. This advert will likely have the best path.

At the momment I am not bitten by the snake yet so my noobish solution for this process is done with MQTT(-discovery). Perhaps you can add in Python the new (additional) attributes straight into the existing record, if so let me know and i will refer to your solution.
## 1. How does it look like?
Lets start with an impression how it will look like:

<img width="843" height="488" alt="image" src="https://github.com/user-attachments/assets/d1a08528-e3dc-4547-813b-8d8525c90996" />

On the left side you see the selection, on the right the result of it
## 2. Requirements
- MQTT setup
For the selection you need three helper entities:
- input_select.meshcore_contact_type; likely already created, needs 3 options
- input_number.meshcore_advert_age; value is in days
- input_text.meshcore_contact
How to create helper entitiies is beyond the scope of this document.

## 3. result of selection
This is a markdown card were each result has 4 lines. The first 3 are derived from the original contact identy. The last line comes from an extra entity were the path information of the advert is stored. These records needs to be created which is explained in the next chapter.
## 4 Building your extra records
When an advert is received this advert will be stored in an additional entity record. Please copy below automation into a new automation. It also use an extra helper timer "timer.meshcore_path_timer" (3 seconds countdown) in an attempt not to overwrite with the next entry. At the moment I am not very successful with hopless path but that is not to annoying.
```
alias: Meshcore - Contact (Additional Info)
description: Create Addition Info for contacts
triggers:
  - trigger: event
    event_type: meshcore_raw_event
    event_data:
      event_type: EventType.RX_LOG_DATA
conditions:
  - condition: template
    value_template: "{{ (trigger.event.data.payload.payload [0:2] | int) * 1  == 11 }}"
actions:
  - variables:
      short: "{{ trigger.event.data.payload.payload }}"
      hops: "{{ (short[3:4] | int) * 1 | int}}"
      start: "{{ (hops * 2 + 4 | int) }}"
      contact: "{{ short[start:start+12] }}"
      entity: sensor.meshcore_{{ contact }}_hops
      path: "{% if hops == 0 %} \"NA\" {% else %} {{ short[4:start] }} {% endif %}"
      record: Old
  - if:
      - condition: template
        value_template: "{{ states[entity] == None }}"
    then:
      - action: script.meshcore_path_data
        data:
          contact: "{{ contact }}"
        enabled: true
      - variables:
          record: New
  - variables:
      previous: "{{ as_timestamp(states[entity].last_updated,default=0) | int }}"
      time: "{{ now() | as_timestamp| int }}"
      interval: "{{ (( time - previous ) / 3600 ) | int }}"
  - if:
      - condition: state
        entity_id: timer.meshcore_path_timer
        state: idle
    then:
      - action: mqtt.publish
        metadata: {}
        data:
          topic: meshcore/{{ contact }}
          payload: |-
            {
              "hops":"{{ hops }}",
              "path":"{{ path }}",
              "advert":"{{ time }}", 
              "interval":"{{ interval }}", 
              "record":"{{ record }}"
            }
          qos: "0"
          retain: true
      - action: timer.start
        metadata: {}
        data: {}
        target:
          entity_id: timer.meshcore_path_timer
      - delay:
          seconds: 1
mode: single
```
If this is a new record also a discovery record should be created. This is done with "script.meshcore_path_data"
```
description: Create additional attributes from Meshcore
sequence:
  - action: mqtt.publish
    data:
      topic: homeassistant/sensor/mc-{{contact}}/hops/config
      retain: true
      payload: |-
        {
          "name": "Meshcore {{ contact }} Hops",
          "unique_id": "meshcore_{{ contact }}_hops",
          "state_topic": "meshcore/{{ contact }}",
          "value_template": {% raw %}"{{ value_json.hops }}"{% endraw %},
          "icon": "mdi:information-box-outline",
          "entity_category": "diagnostic",
          "json_attributes_topic" : "meshcore/{{ contact }}"
        }
      qos: "0"
mode: single
alias: Meshcore - Path data
fields:
  contact:
    selector:
      text: null
    name: contact
    required: true
```
