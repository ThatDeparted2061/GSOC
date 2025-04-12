## How are we going about integrating the Vue simulator while implementing the version control along the way?
So while loading the simulator when we click on simulator while being on the homepage, we are redirected to `/simulator`. and it is further transferred to `/simulatorvue` if the flipper flage is enabled. At the crux of the version implementation (if I bypass the flipper flag toggling and introduce the simulator vue as default). The steps are 
- We load the simulator to the URL
- then we check for the presaved data using the `load(data)` and the functions are loaded further on
- The next thing is managing the further routes of the application, which go beyond `/simulatorvue,` which were conventionally saved here
  <br><br>
![Screenshot from 2025-04-11 23-05-31](https://github.com/user-attachments/assets/e5449592-84e7-4f10-9627-7635d1ef0cd7)
<br><br>
- Since we won't be needing this (as we have components in vue defining this using vue-router), we transfer these routes to `simulatorvue/*path`
  <br><br>
  ![image](https://github.com/user-attachments/assets/7b0efa1d-3bde-42df-a11e-43ea7fd4f24c)
  <br><br>
## How are we implementing the verion control in this ? 
So, every time we load the URL, we introduce a parameter to look into the version of the simulator. We do this in several ways
