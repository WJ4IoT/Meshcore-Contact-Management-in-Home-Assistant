# Simple Counting Contacts with a markdown card
Just a simple card to count contacts. How can it look like?

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
