# Meshcore Contact Management Dashboard in Home Assistant 
A way to manage your contacts when using the Meshcore Integration. This was a slow-cooked 'noobish' development in the GUI so it is not a single package (but now only two files). This a revised version after many tweaking and re-tweaking. What can it do?
* the dashboard is included the excellent manage contact card
* An Overview of the count of all contact and potential inactive contacts.
* An overview of all important details of the contact in focus
* Toggle Favorite Flag (revised) of an added contact
* Manage Contacts in BULK (NEW!!)

## 1. How does it look like?

<img width="939" height="622" alt="image" src="https://github.com/user-attachments/assets/93276d7a-db97-48af-a0ee-dfc968073b0e" />

Some parts explained:
* The `Manage Contact (Overview)` card gives information of the contacts in Home Assistant and on your node. With the fix setting for companions 3 days old and 7 for others (feel free to change this preference).
* The +1 difference between `node count` and `Added to Node - True` seems a small mistake, so expect this difference. If this section is blank you might to change the entity_id of your own node.
* The `manage Contacts (BULK)` is in potential destructive, to make sure you understand what will happen essential parts of the script are disabled, Other safecards are days should be given and cannot be lower than a certain threshold (again feel free to change this preference). The `🔄 Reload Meshcore Device` needs to refer to the `device_id` of your node, easiest way to find this unlogical number is in a new automation.
* The `Manage Contacts (Individual)` is standard except for the last line `🚩 Favorite Flag (script/WiP)`. This part is not but also not working but I liked the 🚩.
* When the focus is on an added contact the `Hold for Circle to Toggle Favorite Flag` will be visible. If you hold (until the cicle) it will set the favorite flag, but please be patience, very very patience because the effect will not be visible for some (long) time.
* Last card is filled with the contact in focus, if both `discovered` and `added` are filed in the detail are of the `added` (change the `added` to `Select a contact to ...` to see `discovered`
* The second last line is distance, bearing in degrees (both based upon a HA-topic) extended with intercardinal points of the compass (and is new)
* The last line with Hops, Path details is probably not visible so previous line is actually the last line.

## 2. Requirements
* Mescore Integration Version v2.2.3.
* HACS Repository: Mushroom
* [The New script file](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/scripts/meshcore_contact_management.yaml)
* The Revised Maintenance [Dashboard](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/dashboards/meshcore_contacts.yaml)

## 3. Tweaking Essential
You must tweak these files for your personnal needs:
Imagine you want to remove all `stale` contact on your node older than 6 days (defined as `option: old_node_contacts`) 
In the `dashboard` you need to change the value of days '14' in '6'.

```
type: button
name: 🛑 Remove Old Contacts From Node
action_name: Old
tap_action:
  action: call-service
  service: script.meshcore_contact_management
  data:
    option: old_node_contacts
    days: 14
```

But in the `script` for `old_node_contacts` the `days_old` will never below 7 until you change it.
And also the action will never run until you decide this is what you want by enable the last line.
These safe-guards are built in because when run it the job is done and most likely you & I tend to start reading TFM later.
```
- conditions:
    - condition: template
      value_template: "{{ option == 'old_node_contacts' }}"
  sequence:
    - variables:
        days_old: |-
          {% if days < 7 %}
            7
          {% else %}
            {{ days }}
          {% endif %}
    - repeat:
        for_each: |-
          {%- set time = now() | as_timestamp | int -%} 
          {%- set time = time - days_old*24*60*60 -%}  
          {{ states.binary_sensor  | selectattr('attributes.lastmod',
          'defined') | selectattr('state', 'eq', 'stale')  |
          selectattr('attributes.lastmod', 'lt', time ) |
          sort(attribute='attributes.lastmod')   |
          map(attribute='object_id') | list }}
        sequence:
          - variables:
              public_key: "{{ repeat.item[-20:-8] }}"
          - action: meshcore.remove_discovered_contact
            data:
              pubkey_prefix: "{{ public_key }}"
            enabled: false
```

