# My Meshcore Home Assistant Solutions

A collection of (in my view) practical solutions on top of the excellent [Meshcore Integration in Home Assistant](https://github.com/meshcore-dev/meshcore-ha).

Do not expect fancy single packages or blueprints. In the end they are not rocket science and the description fits on one page. The name of the md-files will hint in the direction what kind of solution I was aiming to achieve. Most of the work consist of cut and paste knitted together but sometimes with a new element which I was not able to find in other examples. Or I just did not came across the desired solution. When the solution was found in the end they looked pretty simple and I wondered why sometimes it took so long to find it. I think I will not be the only NOOB with this problem so hence the reason to share my solutions.

Enough blah blah, My first two solutions were:

- [Contact Management](Contact_Management.md): the syntax of the combination template and a (hold-)action was almost a bridge too far for me.
- [Private_Messages.md](Private_Messages.md): finding how to add labels was easier but stupid enough not a standard Home Assistant action (luckily I found a hint somewhere to use the spook integration).

Maybe more will follow, you will see them in the overview

*sharing is caring*

A thought of consideration: The Meshcore integration is re-introducing automated telemetry into the game. The abundancy of this is one of the flaws of Meshtastic. Please make the interval as long as possible and avoid going below 900 seconds (perhaps 1800 is more than sufficient). Another example: An advert of your companion is mostly forgotten in the normal situation so 1 daily advert might be more than sufficient.
