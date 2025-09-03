#  Advert Trigger
This is a (extended) automation to advert your companion. One trigger is based on time and does 1 daily advert. The other trigger is based upon changes in the number of contact (*). Because in the beginning this can create a lot of adverts this trigger is in the example disabled. In principle during mainenance (read remove contacts) this automation will not send an advert. You should also aks yourself do I really want to send an advert each time?
Here is my version of this automation:

```
alias: MeshCore - Advert Trigger
description: Sends a MeshCore advert when desired
triggers:
  - trigger: time_pattern
    hours: "3"
    minutes: "25"
    id: TIME
  - trigger: state
    entity_id:
      - sensor.meshcore_696e4b_node_count_wj_domusip
    not_to:
      - unavailable
      - unknown
    not_from:
      - unavailable
      - unknown
    enabled: true
conditions:
  - condition: or
    conditions:
      - condition: trigger
        id:
          - TIME
      - condition: template
        value_template: "{{ trigger.to_state.state|float > trigger.from_state.state|float }}"
actions:
  - data:
      command: send_advert true
    action: meshcore.execute_command
mode: single
```


(* a change of the number of companions would be better because only this type requires an advert. However I did not want to create an additional template sensor for this).

## Counting Contacts with a markdown card
This is not really part of this scope, more belongs to "Path_Info_Advert". How can it look like?

<img width="338" height="226" alt="image" src="https://github.com/user-attachments/assets/cd55238b-1d52-42bc-ac66-4c63eed6590d" />

Just copy the following into an empty card:

```
type: markdown
content: >-  
  Total: {{ states.binary_sensor
    | selectattr('attributes.last_advert', 'defined')
    | list
    | count 
    }}
  {{ states.binary_sensor
    | selectattr('attributes.last_advert', 'defined')
    | selectattr('attributes.type', 'eq', 1 )
    | list
    | count 
    }} Companions
  {{ states.binary_sensor
    | selectattr('attributes.last_advert', 'defined')
    | selectattr('attributes.type', 'eq', 2 )
    | list
    | count 
    }} Repeaters
  {{ states.binary_sensor
    | selectattr('attributes.last_advert', 'defined')
    | selectattr('attributes.type', 'eq', 3 )
    | list
    | count 
    }} Room Servers
```
