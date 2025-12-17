# Custom Templates
Because I was using some coding at multiple places I deciced to created to my first templates.
1. [Bearing Calculation](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/custom_templates/bearing.jinja)
2. [Formatting Path](https://github.com/WJ4IoT/Meshcore-Home-Assistant-Solutions/blob/main/custom_templates/formatter.jinja)
## bearing calculation
usage: {%- from 'bearing.jinja' import calc_bearing -%} {{- calc_bearing( lat, lon, option) -}}
Option Values:
- 'D' output value Distance
- 'B' output value bearing
- 'C' output value compass direction like NW
- 'T' output tekt of all three in format: 89.8 km E 93.8°

Already found some potential improvements in Home Assistant Community.
But please share your improvements.
## formatting path
usage: {% from 'formatter.jinja' import format_path -%} {{ format_path(hops, path_in ) }} 
- output path_in e.g. 'abcdef' to 'ab->cd->ef',
