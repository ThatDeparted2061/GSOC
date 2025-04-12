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
## How are we implementing the verion control in this? 
So, every time we load the URL, we introduce a parameter to look into the version of the simulator. We do this in several ways
- To transfer the routes after `/simulator/` we render the paths in static_controller.rb with a version parameter `simver` here.
  <br><br>
  ![Screenshot from 2025-04-12 13-07-07](https://github.com/user-attachments/assets/25692368-f63f-41b9-a998-4847dc5876b5)
  <br><br>
and we call this in `routes.rb` as
<br><br>
![image](https://github.com/user-attachments/assets/e17efbf4-7158-44f0-aa01-31a748018770)
<br><br>
- This was about HTTP requests. Now, we  would want to load data to the simulator. If you hover through the wiki of Circuitverse and read the developer blogs on simulator working we'd understand the need for the next change. So we make an AJAX request to load the data in a simulator to load the data, we read the version in that data and name it as simulator version and we relocate the URL as per that 
<br><br>
![Screenshot from 2025-04-12 13-26-40](https://github.com/user-attachments/assets/92acfee9-a53c-40ea-936f-dc1d8a10e871)
<br><br>
## Aftermath
- After this, we still would require a lot of work. We have merely diverted the URLs and HTTP requests to the Vue simulator we'd require to change the innate calls from the erb files in `app/views` to call simulator vue. Removing the excess controllers and erb files like say, testbench since they are being handled in vue simulator. We'd require excessive file system cleanups and chaning more minute APIs towards vussim, while doing this we'd also have to update the URL for vue simulator thought vite files after syncing `src` with `v0/src` and `v1/src'
