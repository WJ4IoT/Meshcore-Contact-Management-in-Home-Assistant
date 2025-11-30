# Reply in the Correct Channel!
Did you ever reply to a message in the wrong channel?
If you ever did (I did!) these card(s) will avoid this.

## 1. What do you need to do?
Easy it just work together with the official `messaging card` and set the focus on the same channel as this `messaging card` to prevent thia wrong reply.
Each channel you have requires the folowing card in the same dashboard as the `messaging card`:

````
type: custom:auto-entities
card:
  type: logbook
  title: Focus on Channel
filter:
  include:
    - options: {}
      entity_id: binary_sensor.meshcore_*_ch_0_messages
visibility:
  - condition: and
    conditions:
      - condition: state
        entity: select.meshcore_recipient_type
        state: Channel
      - condition: state
        entity: select.meshcore_channel
        state: Public (0)
grid_options:
  columns: 18
  rows: auto
````
You need to duplicate this card for each channel you have or want to reply to.
After duplication you only need to change the following lines:
* `entity_id: binary_sensor.meshcore_*_ch_0_messages` into the next channel like `entity_id: binary_sensor.meshcore_*_ch_1_messages`
* and line `state: Public (0)` into ..... (best to do this part not in yaml but visual editor under `visibility`

## 2. Private messaging
WiP

