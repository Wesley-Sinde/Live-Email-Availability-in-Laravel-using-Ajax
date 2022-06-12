# Live-Email-Availability-in-Laravel-using-Ajax
Ajax laravel username availability, check email availability, check username availability, email availability with ajax in laravel, laravel, laravel tutorial, username already exists in php using ajax
<br>
We'll learn how to use Ajax Jquery Mysql to make live email available or not for registration in a Laravel application. This is a highly frequent and well-liked feature in almost all online applications.

When a user creates a new account, the user inputs their email address fields, and an Ajax request is sent to the Laravel controller to check the availability of the email address that the user has entered. In Laravel Controller, the user email is matched against the mysql database, and if the email has already been registered, the register button is disabled. If the email is accessible for registration, the register button is enabled.

<br>

For this first we want to make database connection in going to .env file and config/database.php file. After this we have make one EmailAvailable.php controller by write following command in command prompt.

```php
php artisan make:controller EmailAvailable

```
Under this controller we have make index() method for load view file on browser. For this we have write following code.

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class EmailAvailable extends Controller
{
    function index()
    {
     return view('email_available');
    }

}

?>

```
In email_available.php we have define email, password and register html field. And Below this we have write jquery code for check email in proper format and also write Ajax request for check email already available or not in our database. It will send Ajax request to check() method of EmailAvailable controller.

```php
<!DOCTYPE html>
<html>
 <head>
  <title>Live Email Availability in Laravel using Ajax</title>
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.1.0/jquery.min.js"></script>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css" />
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
  <style type="text/css">
   .box{
    width:600px;
    margin:0 auto;
    border:1px solid #ccc;
   }
   .has-error
   {
    border-color:#cc0000;
    background-color:#ffff99;
   }
  </style>
 </head>
 <body>
  <br />
  <div class="container box">
   <h3 align="center">Live Email Availability in Laravel using Ajax</h3><br />
   
   <div class="form-group">
    <label>Enter Your Email</label>
    <input type="text" name="email" id="email" class="form-control input-lg" />
    <span id="error_email"></span>
   </div>
   <div class="form-group">
    <label>Enter Your Password</label>
    <input type="password" name="password" id="password" class="form-control input-lg" />
   </div>
   <div class="form-group" align="center">
    <button type="button" name="register" id="register" class="btn btn-info btn-lg">Register</button>
   </div>
   {{ csrf_field() }}
   
   <br />
   <br />
  </div>
 </body>
</html>

<script>
$(document).ready(function(){

 $('#email').blur(function(){
  var error_email = '';
  var email = $('#email').val();
  var _token = $('input[name="_token"]').val();
  var filter = /^([a-zA-Z0-9_\.\-])+\@(([a-zA-Z0-9\-])+\.)+([a-zA-Z0-9]{2,4})+$/;
  if(!filter.test(email))
  {    
   $('#error_email').html('<label class="text-danger">Invalid Email</label>');
   $('#email').addClass('has-error');
   $('#register').attr('disabled', 'disabled');
  }
  else
  {
   $.ajax({
    url:"{{ route('email_available.check') }}",
    method:"POST",
    data:{email:email, _token:_token},
    success:function(result)
    {
     if(result == 'unique')
     {
      $('#error_email').html('<label class="text-success">Email Available</label>');
      $('#email').removeClass('has-error');
      $('#register').attr('disabled', false);
     }
     else
     {
      $('#error_email').html('<label class="text-danger">Email not Available</label>');
      $('#email').addClass('has-error');
      $('#register').attr('disabled', 'disabled');
     }
    }
   })
  }
 });
 
});
</script>

```
For received Ajax request we have go EmailAvailable.php controller and under this we have create check method. This method will check particular email is available or not in mysql database. For this we have use Laravel DB class.
```php

<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class EmailAvailable extends Controller
{
    function index()
    {
     return view('email_available');
    }

    function check(Request $request)
    {
     if($request->get('email'))
     {
      $email = $request->get('email');
      $data = DB::table("tbl_login")
       ->where('email', $email)
       ->count();
      if($data > 0)
      {
       echo 'not_unique';
      }
      else
      {
       echo 'unique';
      }
     }
    }

}
?>

```
Lastly we want to set route for both method of EmailAvailable controller. For this we have to go to routes/web.php file.

```
<?php


Route::get('/', function () {
    return view('welcome');
});

Route::get('/email_available', 'EmailAvailable@index');

Route::post('/email_available/check', 'EmailAvailable@check')->name('email_available.check');

```



# Database
```

--
-- Database: `testing`
--

-- --------------------------------------------------------

--
-- Table structure for table `tbl_login`
--

CREATE TABLE `tbl_login` (
  `id` int(11) NOT NULL,
  `first_name` varchar(100) NOT NULL,
  `last_name` varchar(100) NOT NULL,
  `gender` varchar(30) NOT NULL,
  `email` varchar(200) NOT NULL,
  `password` varchar(200) NOT NULL,
  `address` text NOT NULL,
  `mobile_no` varchar(15) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;

--
-- Indexes for dumped tables
--

--
-- Indexes for table `tbl_login`
--
ALTER TABLE `tbl_login`
  ADD PRIMARY KEY (`id`);

--
-- AUTO_INCREMENT for dumped tables
--

--
-- AUTO_INCREMENT for table `tbl_login`
--
ALTER TABLE `tbl_login`
  MODIFY `id` int(11) NOT NULL AUTO_INCREMENT;
COMMIT;
```

