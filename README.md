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

USER STORY-3

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



