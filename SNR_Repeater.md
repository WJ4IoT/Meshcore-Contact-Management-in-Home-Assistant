# RX SNR Received from Repeaters
An attempt to show how strong the last repeater is received
## How does it look like?

<img width="1008" height="478" alt="image" src="https://github.com/user-attachments/assets/baaa8bab-13f1-4be1-b4d6-fdb9f181f912" />

## Requirements
- MQTT-server (I lack snake skills)
- spook integration (you may remove this from the automation)
## automation
- The automation will extract the paths from advert and messages and extract the last repeater, total path, number of hops and SNR
- It checks if MQTT-discovery is already created (it not it runs the script for it)
- publish data to MQTT
- add label "SNR" to this entity (not really required)
Just copy and paste below data into a new automation:
```
alias: Meshcore - SNR Info Repeater
description: Collect SNR data of the last hop
triggers:
  - trigger: event
    event_type: meshcore_raw_event
    event_data:
      event_type: EventType.RX_LOG_DATA
conditions:
  - condition: and
    conditions:
      - condition: or
        conditions:
          - condition: template
            value_template: "{{ trigger.event.data.payload.payload[0:2] | int(0) == 5 }}"
          - condition: template
            value_template: "{{ trigger.event.data.payload.payload [0:2]| int(0) == 11 }}"
          - condition: template
            value_template: "{{ trigger.event.data.payload.payload[0:2] | int(0) == 15 }}"
      - condition: template
        value_template: "{{ trigger.event.data.payload.payload[2:4] | int(1) != 0 }}"
actions:
  - variables:
      payload: "{{ trigger.event.data.payload }}"
      short: "{{ trigger.event.data.payload.payload }}"
      hops: "{{ short[2:4] | int(0) }}"
      origin: "{{ short[0:2] | int(0) }}"
      start: "{{ hops * 2 + 4 | int }}"
      path: "{{ short[4:start] }}"
      contact: "{{ path[-2:] }}"
      entity: sensor.meshcore_snr_{{ contact }}
  - if:
      - condition: template
        value_template: "{{ states[entity] == None }}"
    then:
      - action: script.meshcore_snr
        data:
          contact: "{{ contact }}"
  - action: mqtt.publish
    metadata: {}
    data:
      topic: meshcore/snr/{{ contact }}
      payload: |-
        {
          "snr":"{{ payload.snr | float(1) }}",
          "rssi":"{{ payload.rssi }}",
          "path":"{{ path }}",
          "hops":"{{ hops }}",
          "origin":"{{ origin }}",
          "timestamp":"{{ trigger.event.data.timestamp | int }}"
        }
      qos: "0"
      retain: true
  - action: homeassistant.add_label_to_entity
    metadata: {}
    data:
      label_id:
        - snr
      entity_id:
        - sensor.meshcore_snr_{{ contact }}
mode: single
```
## script
Just copy and paste below data into a new script:
```
alias: Meshcore - SNR
description: Create SNR of each (last) contact
fields:
  contact:
    selector:
      text: null
    name: contact
    required: true
sequence:
  - action: mqtt.publish
    data:
      topic: homeassistant/sensor/mc-{{contact}}/snr/config
      retain: true
      payload: |-
        {
          "name": "Meshcore SNR {{ contact }}",
          "unique_id": "meshcore_snr_{{ contact }}",
          "state_topic": "meshcore/snr/{{ contact }}",
          "state_class": "measurement",
          "unit_of_measurement": "dB",
            "value_template": {% raw %}"{{ value_json.snr }}"{% endraw %},
          "icon": "mdi:signal",
          "json_attributes_topic" : "meshcore/snr/{{ contact }}"
        }
      qos: "0"
mode: single
```
## Dashboard
In the example you see two cards: markdown and history-graph
The history-graph you can make the easiest from the field "Last SNR", show more and add label "SNR" to it (this requires some time to build but after a few messages the most important repeaters will be available) create a card from this and edit the card with a costum period etc.

Copy below in an empty card to create the markdown card.
```
type: markdown
content: |-
  {% set DEVICES = states.sensor
    | selectattr('attributes.timestamp', 'defined')
    | sort(attribute='attributes.timestamp')
    | reverse
  %}

  {% for id in DEVICES %}
    Repeater: {{ states[id.entity_id].name }} ({{ states[id.entity_id].entity_id[-2:] }})
    SNR: {{ states[id.entity_id].attributes.snr }} dB
    Last Seen: {{ states[id.entity_id].attributes.timestamp | int |timestamp_custom('%Y-%m-%d %H:%M')}}
    Hops/Path: {{ states[id.entity_id].attributes.hops }} = {{ states[id.entity_id].attributes.path }}
  {% endfor %}
title: RX SNR Received from Repeaters
grid_options:
  columns: 12
  rows: full
```
