Bitcoin-price consists of two modules:
- Backend application on laravel
- Frontend application Vue.JS

Application flow:
- The backend part is made for making API for posting coindesk API on get request from frontend part
- The getdata() function from frontend part is used for calling Bitcoin API data from backend laravel for showing results on click of render button, dates are set from today-10 and can be changed accordingly. 

Requirements:
- Local Server (Xampp or Wampp)
- PHP 7.2
- Laravel
- Composer
- NodeJS

Setup:
- Download and zip the project folder in C:\xampp\htdocs
- Open CMD
- Go to the folder C:\xampp\htdocs\Bitcoin-price\frontend-VueJS
- Run Commands:
  - npm install
  - npm run serve 
- Copy the URL and paste it into the browser
- Change the dates accordingly and press render button and see results.

# Output:

![GitHub Logo](/images/Result.png)