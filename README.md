- 👋 Hi, I’m @MaheswariBorra
- 👀 I’m interested in ...
- 🌱 I’m currently learning ...
- 💞️ I’m looking to collaborate on ...
- 📫 How to reach me ...

<!---
MaheswariBorra/MaheswariBorra is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->
USER STORY-2
"1-Users should have option to select their role option - user/Admin
2-Admin should select the Admin option. 
2-Admin's User id and password should be pre-loaded in the database, which can be used to log in.
"



create database testdb;
use testdb;
CREATE TABLE ADMIN(USER_ID VARCHAR(45) NOT NULL, PASSWORD VARCHAR(15) NOT NULL)
engine=InnoDB default charset=utf8 collate=utf8_unicode_ci;
insert into ADMIN values("admin@gmail.com","Abc@1234");
_________________________________________________________________________________________________________________________________

USER STORY-4


"1-When the Vendor/User clicks on the registration link, it should re-direct to registration form.
2-Vendor/User needs to fill some of the basic attributes/fields.
The appropriate fields should be displayed based on the role selected.
Below are some of the fields:
 First Name, Last Name, DOB, Gender, Contact Number, Vendor Id/User Id, Password, Address, Map location (for vendor only)
Clicking ‘Submit’ should validate the datatype constraints for each field

Name should be in range 4-50
DOB should be entered and the age should not be less than 18. DOB is not required for Vendor.
Contact number should be of 10 digits
Password should be of minimum 6 letters with special characters included.
Note - Trainees can add/neglect fields that will be appropriate and validations should be handled for all fields
3-Vendor failing to provide information on the mandatory fields be provided with an alert message – ‘Please update the highlighted mandatory field(s).’ Also, highlight the missed out field in red
4-Post-successful field level validation, save the information in the database
5-Upon saving the information in the database, display the message ‘Your details are submitted successfully’.
7-Admin should be able to view the New Vendor registration
8-Admin should approve / reject theVendor Request.
9-If rejected, the Manager should not be allowed to login with the registered credentials
10-Vendor should be notified upon approval/rejection while trying to enter the application.
"

User.java
package com.Price.quotation.Model;

import org.springframework.stereotype.Component;
@Component
public class User {
    private String firstName;
    private String lastName;
    private String dateOfBirth;
    private String gender;
    private String contactNumber;
    private String userId;
    private String password;
    private String address;
	public User() {
		super();
	}
	public String getFirstName() {
		return firstName;
	}
	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}
	public String getLastName() {
		return lastName;
	}
	public void setLastName(String lastName) {
		this.lastName = lastName;
	}
	public String getDateOfBirth() {
		return dateOfBirth;
	}
	public void setDateOfBirth(String dateOfBirth) {
		this.dateOfBirth = dateOfBirth;
	}
	public String getGender() {
		return gender;
	}
	public void setGender(String gender) {
		this.gender = gender;
	}
	public String getContactNumber() {
		return contactNumber;
	}
	public void setContactNumber(String contactNumber) {
		this.contactNumber = contactNumber;
	}
	public String getUserId() {
		return userId;
	}
	public void setUserId(String userId) {
		this.userId = userId;
	}
	public String getPassword() {
		return password;
	}
	public void setPassword(String password) {
		this.password = password;
	}
	public String getAddress() {
		return address;
	}
	public void setAddress(String address) {
		this.address = address;
	}
	@Override
	public String toString() {
		return "User [firstName=" + firstName + ", lastName=" + lastName + ", dateOfBirth=" + dateOfBirth + ", gender=" + gender
				+ ", contactNumber=" + contactNumber + ", userId=" + userId + ", password=" + password + ", address="
				+ address + "]";
	}
	
}

UserController.java

package com.Price.quotation.Controller;

import java.util.List;
import javax.servlet.http.HttpServletRequest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.ui.ModelMap;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.SessionAttributes;

import com.Price.quotation.Model.Product;
import com.Price.quotation.Model.User;
import com.Price.quotation.Service.UserService;
import com.Price.quotation.Service.UserServiceImpl;

@Controller
@SessionAttributes({"name"})
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @Autowired
    private UserServiceImpl userServiceImpl;
    @RequestMapping(value = "/user", method = RequestMethod.GET)
    public String UserFront(@ModelAttribute("user") User userView) {
        return "user";
    }
    
    @RequestMapping(value="/userRegister",method = RequestMethod.POST)
    public String UserRegistration(@ModelAttribute("user")User userReg,ModelMap model){
        if(userService.addUser(userReg)) {
            model.put("status", "Your details are submitted successfully");
        }
        else {
            model.put("status", "User Id is already used");
        }
        
        return "user";
    }
    
    @RequestMapping(value="/userLogin", method=RequestMethod.GET)
    public String userLoginDisplay(@ModelAttribute("user") User user) {
        return "userLogIn";
    }
   
    @RequestMapping(value="/userSuccessLogin", method=RequestMethod.POST)
    public String userLogin(HttpServletRequest request,@ModelAttribute("user") User user,BindingResult result,ModelMap model) {
        
        if(result.hasErrors())
        {
            return "userLogIn";
        }
        
        
        List<User> userList = userService.read();
        for(User user1 : userList)
        {
            if(user1.getUserId().equals(user.getUserId()) && user1.getPassword().equals(user.getPassword()))
            {     
               model.put("name", user.getUserId());
               return "userSuccessLogin";
            }
        }
	    model.put("error", "Wrong Credentials");
	    return "userLogIn";
       
    }
    @RequestMapping(value = "/userLogOut", method = RequestMethod.GET)
	public String userLogout(@ModelAttribute("user") User user) {
		return "userLogIn";
	}
    
    /* It displays object data into form for the given id.   
     * The @PathVariable puts URL data into variable.*/    
    @RequestMapping(value="/editUser/{Id}")    
    public String edit(@PathVariable int Id, Model m){    
        User user=userServiceImpl.getUserById(Id);    
        m.addAttribute("command",user);  
        return "userSuccessLogin";    
    }    
    /* It updates model object. */    
    @RequestMapping(value="/editsave",method = RequestMethod.POST)    
    public String editsave(@ModelAttribute("user") User user){    
		userServiceImpl.update(user);    
        return "redirect:/UserEdit";    
    }    
    
}

UserService.java

package com.Price.quotation.Service;

import java.util.List;
import org.springframework.stereotype.Service;
import com.Price.quotation.Model.User;

@Service
public interface UserService {
	//this method is used for to add user data in Database(Price Quotation)
	public boolean addUser(User user);
	
	//this method is used to fetch User data from Database(Price Quotation)
	public List<User> read();
	
}


UserServiceImpl.java
package com.Price.quotation.Service;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Service;

import com.Price.quotation.Model.Product;
import com.Price.quotation.Model.User;

@Service("userService")
public class UserServiceImpl implements UserService {
	@Autowired
	private static JdbcTemplate jdbcTemplate;

	// --------------------------------------------------------
	public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	// --------------------------------------------------------
	public boolean addUser(User register) {
		
		
		String sql = "insert into user_table(firstName,lastName,dateOfBirth,gender,contact_number,userId,password,address) values(?,?,?,?,?,?,?,?)";
			
		 try {
	            int counter = jdbcTemplate.update(sql,
	                    new Object[] { register.getFirstName(), register.getLastName(), register.getDateOfBirth(),
	                            register.getGender(), register.getContactNumber(),
	                            register.getUserId(), register.getPassword(),register.getAddress()});

	 
			return true;

		} catch (Exception e) {
			e.printStackTrace();
			return false;
		}
	}

	// -----------------------------------------------
	
	
	public int update(User u){    
	    String sql="update user_table set firstName='"+u.getFirstName()+"',lastName='"+u.getLastName()+"',dateOfBirth='"+u.getDateOfBirth()+"',gender='"+u.getGender()+"',contact_number='"+u.getContactNumber()+"',userId='"+u.getUserId()+"',password='"+u.getPassword()+"',address='"+u.getAddress(); 
	    return jdbcTemplate.update(sql);    
	}    
	
	public  User getUserById(int Id){    
	    String sql="select * from product_table where Id=?";    
	    return jdbcTemplate.queryForObject(sql, new Object[]{Id},new BeanPropertyRowMapper<User>(User.class));    
	}    
	
	
	@Override
	public List<User> read() {
		List<User> userList = jdbcTemplate.query("select * from user_table", new RowMapper<User>() {

			@Override
			public User mapRow(ResultSet set, int rowNum) throws SQLException {
				User user = new User();
				user.setUserId(set.getString("userId"));
				user.setPassword(set.getString("password"));
				return user;
			}
		});
		return userList;
	}
	
	
}


-----------------------------------------------------------------------------------------------------------------------------
USER STORY-9


"1-Vendor providing product should provide the contact details, available time and Maps option to locate
2-Vendor should be able to update his product details
3-All category divisions available should be listed for the product and Vendor should be able to select one.
4-Clicking ‘Submit’ should validate the datatype constraints for each field
5-Vendor failing to provide information on the mandatory fields be provided with an alert message – ‘Please update the highlighted mandatory field(s).’ Also, highlight the missed out field in red
6-Upon saving the information in the database, display the message ‘Your details are submitted successfully’. 
7-The product details entered saved should be linked with the vendor ID creating it
8-Vendor should have the option to edit / delete the product details entered by them.
"


ProductForm.jsp

<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

 

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
  <script>
 
function validateForm() {
  var pname = document.forms["product_form"]["productName"].value;
  if (pname == "") {
    alert("Product name must be filled out");
    return false;
  }
  var type = document.forms["product_form"]["type"].value;
  if (type == "") {
    alert("Categoey must be choose");
    return false;
  }
  var desc = document.forms["product_form"]["description"].value;
  if (desc == "") {
    alert("description must be filled out");
    return false;
  }
  var avblty = document.forms["product_form"]["availability"].value;
  if (avblty == "") {
    alert("Availability must be filled out");
    return false;
  } 
  var color = document.forms["product_form"]["color"].value;
  if (color == "") {
    alert("Color must be filled out");
    return false;
  }
  var qty = document.forms["product_form"]["quantity"].value;
  if (qty == "") {
    alert("Quantity must be filled out");
    return false;
  }
  var price = document.forms["product_form"]["price"].value;
  if (price == "") {
    alert("Price must be filled out");
    return false;
  }
}
</script>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>
 <body>
  <div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
   </div>  
   <div class="container mt-5">
	<div class="row">
	 <div class="col-md-4 mt-5">
		<img alt="logo"  src='/images/vendor.jpg'/ style="position: absolute; padding-top: 40%">
	 </div>
	 <div class="col-md-8">
     <h1 style="text-align: center;">Add New Product</h1>
    
     <form name="product_form" onsubmit="return validateForm()" method="post" action="/save" modelAttribute="product" >
        <h5 style="text-align:center; font-family: cursive; color: red;">    ${vStatus }  </h5>  
        <div class="form-group">       
          <label>Product Name<b style="color:red">*</b>: </label>  
          <input name="productName" path="productName" class="form-control" size="150" />
        </div>
        <div class="form-group"> 
          <label>Type<b style="color:red">*</b>:</label>   
          <select name="type" path="type" class="form-control" >
            <option value=""></option>
            <option value = "Electronics">Electronics</option>
            <option value = " Clothes">Clothes</option>
            <option value = "Accessories">Accessories</option>
            <option value = "Foot Wear">Foot Wear</option>
            <option value = "Fashion">Fashion</option>
            <option value = "Grocery">Grocery</option>
            </select></div>
        <div class="form-group"> 
          <label>Description<b style="color:red">*</b>:</label>   
          <input name="description" path="description" class="form-control" size="150" /></div>
        <div class="form-group"> 
          <label>Availability<b style="color:red">*</b>:</label>   
          <input name="availability" path="availability" class="form-control" size="150" /></div>
         <div class="form-group">    
          <label>Color<b style="color:red">*</b>:</label>   
          <input name="color" path="color" class="form-control" size="150" /></div>
        <div class="form-group"> 
          <label>Quantity<b style="color:red">*</b>:</label>   
          <input name="quantity" path="quantity" class="form-control" size="150" /></div>
         <div class="form-group"> 
          <label>price<b style="color:red">*</b>:</label>   
          <input name= "price" path="price" class="form-control" size="150" /></div>
         <div class="form-check"> 
          <input id="my-input" required="required" class="form-check-input" type="checkbox" name="" value="false">
          <label for="my-input" class="form-check-label">Accept the terms and conditions</label></div><br>
         <div class="form-group"> 
          <b style="color:red">Note: * mandatory field </b></div>
          
            <button type="submit"  class="btn btn-success" >Save</button>
            </form></div></div></div><br>
           
              <div class="row">
                 <div class="col-sm-2"></div>
            <div class="col-sm-3">
         	<form:form method="post" action="/productForm">
     	    <button type="submit" class="btn btn-success" style="float: center;">Add Product</button>
            </form:form></div>
		    <div class="col-sm-3">
		    <form method="get" action="/viewproduct">
		    <button type="submit" class="btn btn-success" style="float: right;">View Products</button>
		   </form></div></div>      
       
       </body>
     <!-- productName,type,description,availability,color,quantity,price -->
       </html>  
       
       
       ProductEditForm.jsp
       
       <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

 

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<script>

function validateForm() {
  var pname = document.forms["product_form"]["productName"].value;
  if (pname == "") {
    alert("Product name must be filled out");
    return false;
  }
  var type = document.forms["product_form"]["type"].value;
  if (type == "") {
    alert("Categoey must be choose");
    return false;
  }
  var desc = document.forms["product_form"]["description"].value;
  if (desc == "") {
    alert("description must be filled out");
    return false;
  }
  var avblty = document.forms["product_form"]["availability"].value;
  if (avblty == "") {
    alert("Availability must be filled out");
    return false;
  } 
  var color = document.forms["product_form"]["color"].value;
  if (color == "") {
    alert("Color must be filled out");
    return false;
  }
  var qty = document.forms["product_form"]["quantity"].value;
  if (qty == "") {
    alert("Quantity must be filled out");
    return false;
  }
  var price = document.forms["product_form"]["price"].value;
  if (price == "") {
    alert("Price must be filled out");
    return false;
  }
}
</script>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
</head>
 <body>
  <div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
   </div>  
   <div class="container mt-5">
	<div class="row">
	 <div class="col-md-4 mt-5">
		<img alt="logo"  src='/images/vendor.jpg'/ style="position: absolute; padding-top: 30%">
	 </div>
	 <div class="col-md-8">
 
        <center><h1>Edit product details</h1></center>
       <form:form method="POST" action="/editsave" name="product_form" onsubmit="return validateForm()" >   
        <div class="form-group">  
         <form:hidden  path="vId" /></div>
         <div class="form-group"> 
          <label>Product Name<b style="color:red">*</b>: </label>
          <form:input path="productName" class="form-control" size="150" /></div>
        <div class="form-group">
          <label>Type<b style="color:red">*</b>:</label>
          <form:select path="type" name="type" class="form-control" >
            <form:option value = "Electronics">Electronics</form:option>
            <form:option value = " Clothes">Clothes</form:option>
            <form:option value = "Accessories">Accessories</form:option>
            <form:option value = "Foot Wear">Foot Wear</form:option>
            <form:option value = "Fashion">Fashion</form:option>
            <form:option value = "Grocery">Grocery</form:option>
            </form:select></div>
        <div class="form-group">   
          <label>Description<b style="color:red">*</b>:</label>  
          <form:input path="description" class="form-control" size="150" /></div>
        <div class="form-group"> 
          <label>Availability<b style="color:red">*</b>:</label> 
          <form:input path="availability" class="form-control" size="150" /></div>
        <div class="form-group">   
          <label>Color<b style="color:red">*</b>:</label>
          <form:input path="color" class="form-control" size="150" /></div>
         <div class="form-group">
          <label>Quantity<b style="color:red">*</b>:</label>   
          <form:input path="quantity" class="form-control" size="150" /></div>
        <div class="form-group"> 
          <label>price<b style="color:red">*</b>:</label> 
          <form:input path="price" class="form-control" size="150" /></div>
         <div class="form-group">
        <div class="form-group"> 
          <b style="color:red">Note: * mandatory field </b></div>
          <input type="submit" class="btn btn-success" value="Edit Save" />   
       </div>
       </form:form>
       </div></div></div>
       </body>
       </html>
       
       ViewProduct.jsp
       
       <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page import="java.sql.*,java.util.*"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
  <style>
  th{
  background-color:cyan;
  }
  </style>
    <title>Cognizant E-commerce</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
  <body>
    <div class="jumbotron" style="background-color: bisque;">
         <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
      <form:form action="/vendorLogOut" method="get">
      <button type="submit" class="btn btn-info" style="float: right;">Logout</button>
      </form:form>
    </div>	
   <div class="container mt-5" >
<div class="row">
<div class="col-md-4 mt-5">

<img alt="logo"  src='/images/vendor.jpg'/ style="position: absolute; padding-top: 30%">
</div>
<div class="col-md-8">
 <center>
<h1>Products List</h1></center>
<table border="2" width="70%" cellpadding="2">
<tr><th>vId</th><th>productName</th><th>type</th><th>description</th><th>availability</th><th>color</th><th>quantity</th><th>price</th><th>Edit</th><th>Delete</th></tr>
   <c:forEach var="product" items="${list}">  
   <tr>
   <td>${product.vId}</td>
   <td>${product.productName}</td>
   <td>${product.type}</td>
   <td>${product.description}</td>
   <td>${product.availability}</td>
   <td>${product.color}</td>
   <td>${product.quantity}</td>
   <td>${product.price}</td>
   <td><a href="editProduct/${product.vId}">Edit</a></td>
   <td><a href="deleteproduct/${product.vId}">Delete</a></td>
   </tr>
   </c:forEach>
   </table>
   <br/>
   <div class="col-sm-3">
  	<form:form method="post" action="/productForm">
    <button type="submit" class="btn btn-success" style="float: center;">Add Product</button>
    </form:form></div>
    <!-- productName,type,description,availability,color,quantity,price --></div></div></div></body></html>
    
    --------------------------------------------------------------------------------------------------------------------------------------------------------
    USER STORY-13
    
    "1-User is able to access the products list page post the successful validation of the user credentials
2-User can search any product depending on the applied filters like availability, type, category, color, etc.
3-User can see the descriptive details of any item by clicking on the item.
4-The detail will include the name, type, description, availability, color, quantity, etc."



User.jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>



<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
 <script>
 function ValidateDOB() {
     var lblError = document.getElementById("lblError");

     //Get the date from the TextBox.
     var dateString = document.getElementById("txtDate").value;
     var regex = /(((0|1)[0-9]|2[0-9]|3[0-1])\/(0[1-9]|1[0-2])\/((19|20)\d\d))$/;

     //Check whether valid dd/MM/yyyy Date Format.
     if (regex.test(dateString)) {
         var parts = dateString.split("/");
         var dtDOB = new Date(parts[1] + "/" + parts[0] + "/" + parts[2]);
         var dtCurrent = new Date();
         lblError.innerHTML = "Eligibility 18 years ONLY."
         if (dtCurrent.getFullYear() - dtDOB.getFullYear() < 18) {
             return false;
         }

         if (dtCurrent.getFullYear() - dtDOB.getFullYear() == 18) {

             //CD: 11/06/2018 and DB: 15/07/2000. Will turned 18 on 15/07/2018.
             if (dtCurrent.getMonth() < dtDOB.getMonth()) {
                 return false;
             }
             if (dtCurrent.getMonth() == dtDOB.getMonth()) {
                 //CD: 11/06/2018 and DB: 15/06/2000. Will turned 18 on 15/06/2018.
                 if (dtCurrent.getDate() < dtDOB.getDate()) {
                     return false;
                 }
             }
         }
         lblError.innerHTML = "";
         return true;
     } else {
         lblError.innerHTML = "Enter date in dd/MM/yyyy format ONLY."
         return false;
     }
 }
function userValidateForm() {
  var a = document.forms["user_form"]["firstName"].value;
  if (a == "") {
    alert("First name must be filled out");
    return false;
  }
  var b = document.forms["user_form"]["lastName"].value;
  if (b == "") {
    alert("Last name must be filled out");
    return false;
  }
  var c = document.forms["user_form"]["dateOfBirth"].value;
  if (c == "") {
    alert("Date of birth must be filled out");
    return false;
  }
  var d = document.forms["user_form"]["gender"].value;
  if (d == "") {
    alert("gender must be choose");
    return false;
  }
  var e = document.forms["user_form"]["contactNumber"].value;
  if (e == "") {
    alert("Contact number must be filled out");
    return false;
  }
  var f = document.forms["user_form"]["userId"].value;
  if (f == "") {
    alert("UserId must be filled out");
    return false;
  }
  var g = document.forms["user_form"]["password"].value;
  if (g == "") {
    alert("Password must be filled out");
    return false;
  }
  var h = document.forms["user_form"]["address"].value;
  if (h == "") {
    alert("Address must be filled out");
    return false;
  }
  
}
</script>
</head>
<body>
<div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
    </div>	

	<div class="container mt-5">
	<div class="row">
	<div class="col-md-4 mt-5">
	
	<img alt="logo"  src='/images/user.png' style="position: absolute; padding-top: 40%">
	</div>
	<div class="col-md-8">
	<h1 style="text-align: center;">USER REGISTRATION</h1>

	<form:form name="user_form" method="post" onsubmit="return userValidateForm()" action="/userRegister" modelAttribute="user"> 
 
		<h5 style="text-align:center; font-family: cursive; color: red;">	${status }  </h5>
			<div class="form-group mt-3">
			<label>First Name<b style="color:red">*</b>:</label>
			<form:input name="firstName" class="form-control" path="firstName" size="150" title="Name should be in range 4-50" />
			</div>
			
			<div class="form-group">
			<label>Last Name<b style="color:red">*</b>:</label>
			<form:input name="lastName" class="form-control" path="lastName" size="150" />
			</div>

			<div class="form-group">
			<label>Date Of Birth<b style="color:red">*</b>:</label>
			<form:input id="txtDate" onblur="ValidateDOB()" name="dateOfBirth" class="form-control" path="dateOfBirth" size="150" />
			<span class="error" id="lblError"></span><br>
			<input type="button" id="btnValidate" value="Validate" class="btn btn-success" onclick="return ValidateDOB()" />
			</div>

			<div class="form-group">
			<label>Gender<b style="color:red">*</b>:</label>
			<select name="gender" class="form-control" path="gender" >
			<option>Select</option>
			<option value="M">Male</option>
			<option value = "F">Female</option>
			<option value = "O">Others</option>
			</select>
			</div>
			
			<div class="form-group">
			<label>Contact Number<b style="color:red">*</b>:</label>
			<form:input name="contactNumber" class="form-control" path="contactNumber" size="150" title="contact number should be of 10 digits" />
			</div>
			
			<div class="form-group">
			<label>User Id<b style="color:red">*</b>:</label>
			<form:input name="userId" class="form-control" path="userId" size="150" />
			</div>
			
			<div class="form-group">
			<label>Password<b style="color:red">*</b>:</label>
			<form:input type="password" name="password" class="form-control" path="password" size="150" pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,}"
  			title="Must contain at least one  number and one uppercase and lowercase letter, and at least 6 or more characters"  />
			</div>

			<div class="form-group">
			<label>Address of the User<b style="color:red">*</b>:</label>
			<form:textarea class="form-control" name="address" path="address" size="150" placeholder="Enter your full address...." />
			</div>
			
			<div class="form-check">
                        <input id="my-input" class="form-check-input" type="checkbox" name="" value="false">
                        <label for="my-input" class="form-check-label">Accept the terms and conditions</label>
                    </div>
			<div class="form-check"><br>
 			
 					<b style="color:red">Note: * mandatory field </b>
 		    </div>
			<div class="container mt-3" style="text-align: center;">
                 <div class="row">
                     <div class="col-sm-4"></div>
                     <div class="col-sm-2">
                         <button type="submit" class="btn btn-success">Register</button>
                     </div>
                     <div class="col-sm-2">
                         <button class="btn btn-info" type="reset">Reset</button>
                     </div>
                    
                 </div>
                       
             </div>
	 
			
		</form:form> <br>
		
	</div>
	</div>
	</div>
</body>
</html>

UserLogIn.jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
<body>
<div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
    </div>	

<div class="container mt-5" >
<div class="row">
<div class="col-md-4 mt-5">

<img alt="logo"  src='/images/user.png'/ style="position: absolute; padding-top: 30%">
</div>
<div class="col-md-8">
<h1 style="text-align: center;">USER LOGIN</h1>
	<form:form method="post" action="/userSuccessLogin"	modelAttribute="user">
 	<h5 style="text-align:center; font-family: cursive; color: red;">	${error }  </h5>
 	
 	<div class="form-group mt-3">
			<label>User Id :</label>
			<form:input path="userId" class="form-control" name="userId" size="150" />
			</div>
 	
 	<div class="form-group">
			<label>Password :</label>
			<form:password path="password" class="form-control"  name="password" />
			<form:errors path="password" cssClass="error"/>
			</div>
			
			<div class="container mt-5" style="text-align: center;">
                        <div class="row">
                            <div class="col-sm-5"></div>
                            <div class="col-sm-2">
                                <button type="submit" class="btn btn-success">Login</button>
                            </div>
                            </div>
                            </div>
 	
	<!-- <table>
 			
			<tr>
				<td id="id1"><label>User Id</label></td>
				<td id="id2"><form:input path="userId" name="userId" /></td>
				
			</tr>
 
			<tr>
				<td id="id1"><label>Password</label></td>
				<td id="id5">
				<form:password path="password"  name="password" />
				<form:errors path="password" cssClass="error"/>
				</td>
				
			</tr>
  
			<tr>
				<td><input type="Submit" name="SignIn" value="Sign In" /></td>
				
			</tr>
  
		</table> -->	
	</form:form><br>
	 
	</div>
	</div>
	</div>
</body>
</html>

userSuccessLogin.jsp
<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
<body>

<div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
      <form:form action="/userLogOut" method="get">
      <button type="submit" class="btn btn-info" style="float: right;">Logout</button>
      </form:form>
      
    </div>	
    <div class="container mt-5" >
<div class="row">
<div class="col-md-4 mt-5">

<img alt="logo"  src='/images/user.png'/ style="position: absolute; padding-top: 30%">
</div>
<div class="col-md-8">
<center>
	<h3>
		Welcome <h2 style="color: red;">${name} </h2> 
	</h3>
 <form:form action="/userLogOut" method="get">
      <button type="submit" class="btn btn-info" >Logout</button>
      </form:form>
	<!-- <a href="/searchVendor">search Vendor</a>
 <br><br>
 <a href="/viewOrders">View Orders</a>
 <br><br>
 <a href="/viewVendors">View Vendor Details</a>
 <br> <br>
 <a href="/customerOrder"> Place Order  </a>
 <br> <br>
 <a href = "/userLogOut">Logout</a><br><br>
 <a href = "/index">Home</a> -->
</center></div></div></div>
</body>
</html>

UserEdit.jsp

<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>

 

 

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
 <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
 <script>
 function ValidateDOB() {
     var lblError = document.getElementById("lblError");

 

     //Get the date from the TextBox.
     var dateString = document.getElementById("txtDate").value;
     var regex = /(((0|1)[0-9]|2[0-9]|3[0-1])\/(0[1-9]|1[0-2])\/((19|20)\d\d))$/;

 

     //Check whether valid dd/MM/yyyy Date Format.
     if (regex.test(dateString)) {
         var parts = dateString.split("/");
         var dtDOB = new Date(parts[1] + "/" + parts[0] + "/" + parts[2]);
         var dtCurrent = new Date();
         lblError.innerHTML = "Eligibility 18 years ONLY."
         if (dtCurrent.getFullYear() - dtDOB.getFullYear() < 18) {
             return false;
         }

 

         if (dtCurrent.getFullYear() - dtDOB.getFullYear() == 18) {

 

             //CD: 11/06/2018 and DB: 15/07/2000. Will turned 18 on 15/07/2018.
             if (dtCurrent.getMonth() < dtDOB.getMonth()) {
                 return false;
             }
             if (dtCurrent.getMonth() == dtDOB.getMonth()) {
                 //CD: 11/06/2018 and DB: 15/06/2000. Will turned 18 on 15/06/2018.
                 if (dtCurrent.getDate() < dtDOB.getDate()) {
                     return false;
                 }
             }
         }
         lblError.innerHTML = "";
         return true;
     } else {
         lblError.innerHTML = "Enter date in dd/MM/yyyy format ONLY."
         return false;
     }
 }
function userValidateForm() {
  var a = document.forms["user_form"]["firstName"].value;
  if (a == "") {
    alert("First name must be filled out");
    return false;
  }
  var b = document.forms["user_form"]["lastName"].value;
  if (b == "") {
    alert("Last name must be filled out");
    return false;
  }
  var c = document.forms["user_form"]["dateOfBirth"].value;
  if (c == "") {
    alert("Date of birth must be filled out");
    return false;
  }
  var d = document.forms["user_form"]["gender"].value;
  if (d == "") {
    alert("gender must be choose");
    return false;
  }
  var e = document.forms["user_form"]["contactNumber"].value;
  if (e == "") {
    alert("Contact number must be filled out");
    return false;
  }
  var f = document.forms["user_form"]["userId"].value;
  if (f == "") {
    alert("UserId must be filled out");
    return false;
  }
  var g = document.forms["user_form"]["password"].value;
  if (g == "") {
    alert("Password must be filled out");
    return false;
  }
  var h = document.forms["user_form"]["address"].value;
  if (h == "") {
    alert("Address must be filled out");
    return false;
  }
  
}
</script>
</head>
<body>
<div class="jumbotron" style="background-color: bisque;">
    <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
    </div>    

 

    <div class="container mt-5">
    <div class="row">
    <div class="col-md-4 mt-5">
    
    <img alt="logo"  src='/images/user.png' style="position: absolute; padding-top: 40%">
    </div>
    <div class="col-md-8">
    <h1 style="text-align: center;">EDIT YOUR PROFILE</h1>

 

    <form:form name="user_form" method="post" onsubmit="return userValidateForm()" action="/editsave" modelAttribute="user"> 
 
        
       <h5 style="text-align:center; font-family: cursive; color: red;">    ${status }  </h5>
            <div class="form-group">  
         <form:hidden  path="id" /></div>
            <div class="form-group mt-3">
   			<label>First Name<b style="color:red">*</b>:</label>
            <form:input name="firstName" class="form-control" path="firstName" size="150" title="Name should be in range 4-50" />
            </div>
            
            <div class="form-group">
            <label>Last Name<b style="color:red">*</b>:</label>
            <form:input name="lastName" class="form-control" path="lastName" size="150" />
            </div>

 

            <div class="form-group">
            <label>Date Of Birth<b style="color:red">*</b>:</label>
            <form:input id="txtDate" onblur="ValidateDOB()" name="dateOfBirth" class="form-control" path="dateOfBirth" size="150" />
            <span class="error" id="lblError"></span><br>
            <input type="button" id="btnValidate" value="Validate" class="btn btn-success" onclick="return ValidateDOB()" />
            </div>

 

            <div class="form-group">
            <label>Gender<b style="color:red">*</b>:</label>
            <select name="gender" class="form-control" path="gender" >
            <option>Select</option>
            <option value="M">Male</option>
            <option value = "F">Female</option>
            <option value = "O">Others</option>
            </select>
            </div>
            
            <div class="form-group">
            <label>Contact Number<b style="color:red">*</b>:</label>
            <form:input name="contactNumber" class="form-control" path="contactNumber" size="150" title="contact number should be of 10 digits" />
            </div>
            
            <div class="form-group">
            <label>User Id<b style="color:red">*</b>:</label>
            <form:input name="userId" class="form-control" path="userId" size="150" />
            </div>
            
            <div class="form-group">
            <label>Password<b style="color:red">*</b>:</label>
            <form:input type="password" name="password" class="form-control" path="password" size="150" pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{6,}"
              title="Must contain at least one  number and one uppercase and lowercase letter, and at least 6 or more characters"  />
            </div>

 

            <div class="form-group">
            <label>Address of the User<b style="color:red">*</b>:</label>
            <form:textarea class="form-control" name="address" path="address" size="150" placeholder="Enter your full address...." />
            </div>
            
            <div class="form-check">
                        <input id="my-input" class="form-check-input" type="checkbox" name="" value="false">
                        <label for="my-input" class="form-check-label">Accept the terms and conditions</label>
                    </div>
            <div class="form-check"><br>
             
                     <b style="color:red">Note: * mandatory field </b>
             </div>
            <div class="container mt-3" style="text-align: center;">
                 <div class="row">
                     <div class="col-sm-4"></div>
                     <div class="col-sm-2">
                         <button type="submit" class="btn btn-success">Edit Save</button>
                     </div>
                     
                    
                 </div>
                       
             </div>
     
            
        </form:form> <br>
        
    </div>
    </div>
    </div>
</body>
</html>

UserView.jsp

<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    pageEncoding="ISO-8859-1" isELIgnored="false"%>
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="form"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<%@ page import="java.sql.*,java.util.*"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
  <style>
  th{
  background-color:cyan;
  }
  </style>
    <title>Cognizant E-commerce</title>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

 

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">
  </head>
  <body>
    <div class="jumbotron" style="background-color: bisque;">
         <center>  <h1>Cognizant E-commerce</h1>
      <small>A place for your daily needs</small> </center>
      <form:form method="get" action="/index">
      <button type="submit" class="btn btn-info">Home</button>
      </form:form>
      <form:form action="/UserLogOut" method="get">
      <button type="submit" class="btn btn-info" style="float: right;">Logout</button>
      </form:form>
    </div>    
   <div class="container mt-5" >
<div class="row">
<div class="col-md-4 mt-5">

 

<img alt="logo"  src='/images/vendor.jpg'/ style="position: absolute; padding-top: 30%">
</div>
   <div class="col-sm-3">
      <form:form method="post" action="/UserForm">
    </form:form>
    </div>
    </div>
    </div>
    </body>
    </html>



