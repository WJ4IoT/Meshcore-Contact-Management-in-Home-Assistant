# A noob-way to show Private Messages in the Meshcore Integration
This method requires an additional integration because so far I know that is the only way to add a label to entities without the (G)UI.
## 1. Install integration "Spook 👻 Your homie."
This integrations gives you the possibility to add labels without using the UI.
## 2. Create a label
Create a label called PB (that's Dutch for PM). You might wish to change this.
## 3. Create automation
```
alias: Meshcore - Give incomming PB label PB
description: Meshcore Raw Event - CONTACT_MSG_RECV
triggers:
  - trigger: event
    event_type: meshcore_raw_event
    event_data:
      event_type: EventType.CONTACT_MSG_RECV
conditions: []
actions:
  - variables:
      sender: "{{ trigger.event.data.payload.pubkey_prefix[:6] }}"
  - action: homeassistant.add_label_to_entity
    metadata: {}
    data:
      label_id:
        - pb
      entity_id:
        - binary_sensor.meshcore_696e4b_{{ sender }}_messages
mode: single
```
Please note the you need to change value 696e4b into your own companion value
## 4. create the following card 
```
type: custom:tabbed-card
options: {}
tabs:
  - card:
      type: logbook
      target:
        entity_id:
          - binary_sensor.meshcore_696e4b_ch_0_messages
    attributes:
      label: Public
  - card:
      type: logbook
      target:
        entity_id:
          - binary_sensor.meshcore_696e4b_ch_1_messages
    attributes:
      label: ch_1
  - card:
      type: logbook
      target:
        label_id:
          - pb
    attributes:
      label: PM
grid_options:
  columns: 18
  rows: auto
```
Again you need to change value 696e4b into your own companion value.
If you wish you can add more channels and give ch_1 etc a more meaningfull name.

The card is based on a example from @jrkalf found in the Dutch Meshcore Telegram Group,
the PM/PB extention was created by me. hope it is useful for you.

## 5. How does it look like?
No rocket science overhere.

<img width="619" height="470" alt="image" src="https://github.com/user-attachments/assets/52b521b9-8e91-4968-9a1f-4b8cb9aa7f13" />
