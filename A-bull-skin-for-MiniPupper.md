# A 3D-printed Bull Skin for Mini Pupper

## Why?
The original idea came to me when I saw this tweet by [@nisshan_](https://twitter.com/nisshan_).

[<img src="./assets/images/bull/nisshan-tweet.png" alt=" " width="360" />](https://twitter.com/nisshan_/status/1480923457621983233?s=20)

It would be fun to dress up the MiniPupper!

However, the shape of Mini Pupper reminds me more of a bull, so I decided to make a 3D-printed Bull Skin for my Mini Pupper. 

Seems like the perfect excuse to learn about CAD and 3D printing...

## Searching for inspiration

* Downloaded the STL of a bull: https://www.ameede.net/bull-low-poly-b003554-file-stl-free-download-3d-model-for-cnc-and-3d/, but it is asymmetrical (head, torso turned)

<img src="./assets/images/bull/STL-bull.png" alt ="STL bull " width="360" />

* I found my main source for inspiration for the robot bull here: [Behance - Mechanical bull - 3d model](https://www.behance.net/gallery/27627969/Mechanical-bull-3d-model)
![Screenshot from 2022-01-17 01-08-13](./assets/images/bull/behance-bull.png)


| Other sources of inspiration                                 |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| https://twitter.com/dmacalvert/status/1112406096323317760    | <img src="./assets/images/bull/D3APcfpUgAAhkn3.jpg" alt=" " width="240" /> |
| https://www.pinterest.com/pin/457185799654271997/            | <img src="./assets/images/bull/0f7c763a50cad9706fed969c29846d5e.jpg" alt=" " width="240" /> |
| https://www.artstation.com/artwork/9mmK2v                    | <img src="./assets/images/bull/john-france-bull.png" width="240" /> |
| https://es.dreamstime.com/vaca-mec%C3%A1nica-del-robot-en-estilo-stiunk-un-fondo-blanco-aislado-ilustraci%C3%B3n-d-image132338671 | <img src="./assets/images/bull/vaca-mecánica-del-robot-en-estilo-stiunk-un-fondo-blanco-aislado-ilustración-d-132338671.jpg" width="240" /> |
| https://www.turbosquid.com/3d-models/mechanical-bull-design-3d-model/445960 | <img src="./assets/images/bull/taurus.jpg" width="240" />    |
| https://www.pinterest.com/pin/398427898276324254/            | <img src="./assets/images/bull/pinterest-bull.png" alt =" " width="240" /> |

## Importing Mini Pupper CAD files to FreeCAD

I was frustrated that I could visualize but not download [these Fusion360 files of the Mini Pupper](https://a360.co/3fS15a1), from the link provided in the official project page. But then I found [this other link with the updated model](https://myhub.autodesk360.com/ue2d9cf55/g/shares/SH9285eQTcf875d3c5392da49ebed9324f4d), and even better, a download button!

![](./assets/images/bull/minipupper-in-Fusion360_xs.png)

The process is simple: click the **Download** button in the top right corner, select the format (STEP is recommended in the [FreeCAD wiki](https://wiki.freecadweb.org/Migrating_to_FreeCAD_from_Fusion360)), and provide an e-mail address to receive a link from which to download the STP file (42Mb). There are [instructions on how to preserve the color](https://forum.freecadweb.org/viewtopic.php?f=3&t=50308), but in my case it worked without issues.

![](./assets/images/bull/minipupper-in-FreeCAD_xs.png)

The file is heavy. I deleted all screws (the bodies named M2x5 etc.) and this reduced the size from >40Mb to 8Mb. I wont be needing the screws for my purposes, so I found this a good trade-off.  

## Drafting the bull skin

<img src="./assets/images/bull/minipupper_dimension_mm.jpg" alt =" " width="360" />

After some freehand sketching on the iPad:

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <img src="./assets/images/bull/Paper_sketches_1.jpg" alt =" " width="360" /> | <img src="./assets/images/bull/Paper_sketches_2.jpg" alt =" " width="360" /> |

I can start tracing and building 3D volumes on FreeCAD:

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Jan 18th: import CAD and trace <img src="./assets/images/bull/01-tracing-in-FreeCAD-18.01.png" alt =" " width="360" /> | Jan 20th: face <img src="./assets/images/bull/02-bull-status-20.01.png" alt =" " width="360" /> |
| Jan 24th: hinder leg <img src="./assets/images/bull/03-bull-status-24.01.png" alt =" " width="360" /> | Jan 25th: shoulder and back <img src="./assets/images/bull/04-bull-status-25.01.png" alt =" " width="360" /> |
| Jan 26th horns and ears <img src="./assets/images/bull/05-bull-status-26.01.png" alt =" " width="360" /> | 7th Jan: skull <img src="./assets/images/bull/06-bull-status-27.01.png" alt =" " width="360" /> |

## Next steps
- [x] download Fusion 360 files
- [x] I am quite rusty at CAD and never tried FreeCAD so lets do some FreeCAD tutorials: see [FreeCAD-notes.md](./FreeCAD-notes.md). 
- [x] sketch the design for the bull skin
- [ ] build the 3d model and merge it with existing parts
- [ ] preprocess for 3D printing
- [ ] print
- [ ] test!
- [ ] iterate!



