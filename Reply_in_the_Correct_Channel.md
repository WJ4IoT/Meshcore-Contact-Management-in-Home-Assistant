# Reply in the Correct Channel!
Did you ever reply to a message in the wrong channel?
If you ever did (I did!) these card(s) will avoid this.

## How can it look like?

![msg](https://github.com/user-attachments/assets/d885b0ed-ff24-44f2-966f-c6e0708d5fdf)

## What do you need to do?
Easy it just work together with the official `messaging card` and set the focus on the same channel as this `messaging card`. This will prevent a reply in the wrong channel.
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
* and line `state: Public (0)` into `your channel name (1)` (best to do this part not in yaml but visual editor under `visibility`

## Private messaging
The next step is to show you all Direct Messaging, this can be done for each contact inividual but here is the card for all Direct Messages (so here it is still possible to reply to the wrong contact, you can solve this if  for each contact an additional cards.

```
type: custom:auto-entities
card:
  type: logbook
  title: Focus on Contacts
filter:
  include:
    - options: {}
      entity_id: binary_sensor.meshcore_*_messages
  exclude:
    - options: {}
      entity_id: binary_sensor.meshcore_*_ch*_messages
visibility:
  - condition: state
    entity: select.meshcore_recipient_type
    state: Contact
grid_options:
  columns: 18
  rows: auto
```

Hope you like this solution.
