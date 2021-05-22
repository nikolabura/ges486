# Final Project

For my final project, I created a web app called FCCLE, the FCC Location Explorer (pronounced "fickle"; it's a little contrived, but it works).
The aim of this project was to load in data from the FCC Universal Licensing System database (specifically microwave radio data),
reprocess it into a useful format, and display it to be explored and interpreted in an interactive way online.

First things first. The web app can be accessed [via this link](http://umbcsad.crabdance.com/), or by clicking the logo:

![FCCLE Icon](https://user-images.githubusercontent.com/2071451/119211568-de1db080-ba80-11eb-88b8-1544356269b4.png)

## Write-Up

### Microwave Towers

The main goal of this project was to analyze FCC records of microwave towers and microwave paths.
[Microwave radio](https://en.wikipedia.org/wiki/Microwave_transmission), on the large/outdoor scale, is commonly used for _point-to-point_ communications, meaning that as opposed to traditional omnidirectional broadcasting, there is a transmitter antenna, a reciever antenna, and a straight line-of-sight path between the two.

In the US, these antenna and paths are registered with the FCC, which makes them available for download through the [ULS database weekly export](https://www.fcc.gov/uls/transactions/daily-weekly#fcc-uls-transaction-files-weekly).

Common uses of microwave links include cellular network backbone (when it's too expensive or remote to trench a fiber optic cable all the way to the cell site), internal data transfer for television companies, linking [TV and radio studios](https://en.wikipedia.org/wiki/Studio_transmitter_link) to mountaintop transmitters, extremely-low-latency communications for high-frequency-trading firms, and backup communications for emergency situations.

<img align="right" src="https://cdn.discordapp.com/attachments/552980096315686955/845486202828095508/20210520_183217_HDR.jpg">

### Paging Towers
