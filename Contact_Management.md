# Meshcore Contact Management Dashboard in Home Assistant 
A way to manage your contacts when using the Meshcore Integration. This was a slow-cooked 'noobish' development in the GUI so it is not a single package. This a revised version when the option to toggle the favirite flag came available. What can it do?
* Add Discovered Contact (extended selection)
* Remove contact (extended selection)
* Toggle Favorite Flag (new)
* Remove Discovered Contact (on wishlist)

## 1. How does it look like?

<img width="808" height="678" alt="image" src="https://github.com/user-attachments/assets/eaa81811-f81c-4b21-bbdd-0bbba39fb971" />

Some parts explained:
* The `Overview` card gives information of the contacts in Home Assistant and on your node. With the setting `days_old = 0` there are no possible inactives.
* With the rest of this section set the focus and reduce your selection to something appropriate.
* The dropdown card `select contact` is the result of the left section.
* When the focus is on a contact the details are given in the card below, the `Hops` line comes from an additional entity which is filled with advert details. Expect this will not give errors if absent.
* The next card is a new function with distance, bearing in degrees (both based upon a HA-topic) extended with Intercardinal points of the compass.
* The following cards are visible depending on the focus (see what can it do).
* The last card is the existing `cleanup unavailable contacts`.

## 2. Requirements
* Mescore Integration Version v2.2.1 beta (Favorite Flag available).
* HACS Repository: Mushroom
* Additional Helper fields:
  *   input_select.meshcore_maintenance_focus (with 1 fake option correct ones are added by [this automation](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/automations/meshcore_maintenance_initial.yaml))
  *   input_select.meshcore_contact_type (with 1 fake option correct ones are added in previous step.
  *   input_number.meshcore_days_old
  *   input_text.meshcore_contact_search 
* The automation for the [selection](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/automations/meshcore_maintenance_do_action.yaml)
* The automation for the action [selection](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/automations/meshcore_maintenance_selection.yaml)
* The Revised Maintenance [Dashboard](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/dashboards/maintenance_contacts.yaml)

## 3. Examples
* If you want to `add discovered contacts` it is likely your focus will be on adverts not older than x days.
* If you want to `remove contacts` it is likely your focus will be on adverts not younger than x days.
