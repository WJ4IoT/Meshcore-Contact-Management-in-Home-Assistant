# Meshcore Contact Management in Home Assistant
A way to manage your contact when using the Meshcore Intergration.

The is a small help to remove your obsolete contacts in Home assistant. It is set up as a visual manual way to manage your contacts. The actual action is in a script. If deseried you can easily use this script in a more automated way to boldly run-though your contacts and delete them if they fit your criteria.

## 1. Selection: Identify Possible Obsolete Contacts
It is handy if you limit your view to a relevant selection. For this you need two helpers:
- input_number.meshcore_days_old: limit your view to contacts with adverts are older than x days
- input_select.meshcore_contact_type; containing each different type like "1 - Companion"
Why visual and not automated> Mainly a choise, companions are on holiday or not allways online or just simply forget to advert. Repeaters is more simpler but conditions varies.

## 2. Meshcore - Obsolete Contacts (More Info)
This markdown card is not really required but gives some additional information of the contact

## 3. Hold Contact to Remove (wait for circle)
An auto-entities card and the instructions are already in the tile. I deliberate choose for a hold-action because it is more visible (and even works better than the tab-action). It was not easy to find the right syntax for the template with action combination for a noob like but in the end I was successful after a lot of trial and error.

```
{% set time = now() | as_timestamp | int %} {% set type =
states['input_select.meshcore_contact_type'].state[:1] |  int %} {% set
days_old = states['input_number.meshcore_days_old'].state |  int %} {% set
ignore_list = ['not', 'required' ] %} 

{% set DEVICES = states.binary_sensor
  | rejectattr('entity_id', 'in', ignore_list)
  | selectattr('entity_id', 'search', 'meshcore')
  | selectattr('entity_id', 'search', '_contact')
  | selectattr('state', 'eq', 'stale') 
  | selectattr('attributes.type', 'eq', type )
  | selectattr('attributes.lastmod', 'lt', time - days_old*60*60*24)
  | sort(attribute='attributes.lastmod')
  | list  
%}  
{% if DEVICES | length > 0 %}
  [
  {% for DEVICE in DEVICES -%}
    {{{
      'entity': DEVICE.entity_id,
      'name': DEVICE.name,
      'hold_action': {
        'action':'call-service',
        'service' : 'script.meshcore_remove_contact_script',
        'service_data' : {
          'entity_id' : DEVICE.entity_id }}
          
    }}},
  {% endfor %}
  ]
{% endif %}
```
## 4. Meshcore Maintenance
This button triggers an automation which will reload an config entry. The device_id will be different in your system and please note if your screen is NOT refreshed during this process this device_id has been changed and need to updated. Alternally you can can restart Home Assistant.

```
alias: Mechcore - Maintenance
description: Remove obsolete Clients and Repeaters
triggers: []
conditions: []
actions:
  - action: homeassistant.reload_config_entry
    metadata: {}
    data: {}
    enabled: true
    target:
      device_id: e30d8ce94559e6ac12f23934fda9a82a
```
## 5. Actual delete not provided entities
Previous steps deleted the contact on the device itself but did not remove the entity itself. This can be automated with an add-on but I prefer to delete them manually however do not think it is required for Meshcore itself. 
