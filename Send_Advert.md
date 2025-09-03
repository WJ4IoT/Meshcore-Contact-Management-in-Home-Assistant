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
