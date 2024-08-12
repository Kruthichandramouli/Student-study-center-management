1.adminServlet.java(SERVLET)
package study;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

/**
 * Servlet implementation class adminServlet
 */
@WebServlet("/adminServlet")
public class adminServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
	
	    // JDBC URL, username and password of MySQL server
	    private static final String JDBC_URL = "jdbc:mysql://localhost:3306/rail";
	    private static final String DB_USER = "root";
	    private static final String DB_PASSWORD = "root";

    /**
     * @see HttpServlet#HttpServlet()
     */
    public adminServlet() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		doGet(request, response);
		 String username = request.getParameter("username");
	        String password = request.getParameter("password");
	        PrintWriter out=response.getWriter();

	        try {
	            // Load MySQL JDBC Driver
	            Class.forName("com.mysql.cj.jdbc.Driver");

	            // Connect to MySQL database
	            Connection connection = DriverManager.getConnection(JDBC_URL, DB_USER, DB_PASSWORD);

	            // SQL query to check if username and password are valid
	            String sql = "SELECT * FROM admin WHERE username=? AND password=?";
	            PreparedStatement statement = connection.prepareStatement(sql);
	            statement.setString(1, username);
	            statement.setString(2, password);
	            ResultSet result = statement.executeQuery();

	            if (result.next()) {
	                // If user exists, create a session and store username
	                HttpSession session = request.getSession();
	                session.setAttribute("username", username);
	                response.sendRedirect("add_details.jsp");
	            } else {
	                out.println("<p>Sorry, invalid username or password.</p>");

	                // If user doesn't exist, redirect back to login page with error message
	                response.sendRedirect("dashboard.jsp?error=1");
	            }

	            // Close resources
	            result.close();
	            statement.close();
	            connection.close();
	        } catch (SQLException | ClassNotFoundException e) {
	            e.printStackTrace();
	            throw new ServletException("Database access error", e);
	        }
	}

}
2.studentservlet.java(SERVLET)
package study;

import java.io.*;


import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;

@WebServlet("/studentservlet")
public class studentservlet extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.sendRedirect("search.jsp");
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter pw = response.getWriter();
        String name = request.getParameter("studentUsername");
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/rail", "root", "root");
            PreparedStatement ps = con.prepareStatement("SELECT * FROM student WHERE name = ?");
            ps.setString(1, name);
            ResultSet rs = ps.executeQuery();

            // Start building the HTML table
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<meta charset=\"ISO-8859-1\">");
            pw.println("<title>Search Results</title>");
            pw.println("<style>");
            pw.println("body {");
            pw.println("    margin: 0;");
            pw.println("    padding: 0;");
            pw.println("    font-family: Arial, sans-serif;");
            pw.println("    background-image: url('https://example.com/background-image.jpg');"); // Replace 'https://example.com/background-image.jpg' with the URL of your background image
            pw.println("    background-size: cover;");
            pw.println("    background-position: center;");
            pw.println("}");
            pw.println("table {");
            pw.println("    font-family: Arial, sans-serif;");
            pw.println("    border-collapse: collapse;");
            pw.println("    width: 80%;");
            pw.println("    margin: 20px auto;");
            pw.println("}");
            pw.println("th, td {");
            pw.println("    border: 1px solid #ddd;");
            pw.println("    padding: 8px;");
            pw.println("    text-align: center;");
            pw.println("}");
            pw.println("th {");
            pw.println("    background-color: #4CAF50;");
            pw.println("    color: white;");
            pw.println("}");
            pw.println("tr:nth-child(even) {");
            pw.println("    background-color: #f2f2f2;");
            pw.println("}");
            pw.println("</style>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<h2 style=\"text-align:center;\">Search Results for " + name + "</h2>");
            pw.println("<table>");
            pw.println("<tr><th>NUMBER</th><th>NAME</th><th>EMAIL</th><th>DESK</th></tr>");

            // Iterate through the ResultSet and add data to the table
            while (rs.next()) {
                pw.println("<tr>");
                pw.println("<td>" + rs.getInt(1) + "</td>");
                pw.println("<td>" + rs.getString(2) + "</td>");
                pw.println("<td>" + rs.getString(3) + "</td>");
                pw.println("<td>" + rs.getString(4) + "</td>");
               
                pw.println("</tr>");
            }

            pw.println("</table>");
            pw.println("</body>");
            pw.println("</html>");
        } catch (Exception e) {
            pw.println(e);
        }
    }
}


3.insertdetails.java(class file)
package study;

import java.io.IOException;

import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class insertpass
 */
@WebServlet("/insertdetails")
public class insertdetails extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public insertdetails() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.setContentType("text/html");
		PrintWriter pw=response.getWriter();
		try {
		Class.forName("com.mysql.cj.jdbc.Driver");
		Connection con=DriverManager.getConnection("jdbc:mysql://localhost:3306/rail","root","root");
		PreparedStatement ps=con.prepareStatement("insert into student values(?,?,?,?)");
		int num=Integer.parseInt(request.getParameter("num"));
		String name=request.getParameter("name");
		String email=request.getParameter("email");
		String desk=request.getParameter("desk");
	
		ps.setInt(1, num);
		ps.setString(2, name);
		ps.setString(3, email);
		ps.setString(4, desk);
		
		int rows=ps.executeUpdate();
//		if(rows>0) {
//			pw.println("PASS GENERATED SUCCESSFULL!!!");
//		}
//		else {
//			pw.println(" SORRY PASS GENERATION FAILED!!!");
//		}
		 if (rows > 0) {
             pw.println("<!DOCTYPE html>");
             pw.println("<html>");
             pw.println("<head>");
             pw.println("<title>Pass Generation Status</title>");
             pw.println("</head>");
             pw.println("<body>");
             pw.println("<center>");
             pw.println("<img src=https://img.freepik.com/free-psd/check-symbol-isolated_23-2150500363.jpg?size=338&ext=jpg&ga=GA1.1.1224184972.1714521600&semt=ais alt=\"Success\">");
             pw.println("<h2 style=\"color:green;\">DESK ALOCATION SUCCESSFULL!!!</h2>");
             pw.println("</center>");
             pw.println("</body>");
             pw.println("</html>");
         } else {
             pw.println("<!DOCTYPE html>");
             pw.println("<html>");
             pw.println("<head>");
             pw.println("<title>Pass Generation Status</title>");
             pw.println("</head>");
             pw.println("<body>");
             pw.println("<center>");
             pw.println("<img src=https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR6twsDLkvEhwjWOsLx-_UZaLFEaXpfdCbepAMJdMmdQw&s alt=\"Failure\">");
             pw.println("<h2 style=\"color:red;\">SORRY DESK ALOCATION FAILED!!!</h2>");
             pw.println("</center>");
             pw.println("</body>");
             pw.println("</html>");
         }
		
		}
		catch(Exception e ) {
			pw.println(e);
		}

	}

}


4.deletedetails.java(class file)
package study;



import java.io.IOException;

import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class deletepass
 */
@WebServlet("/deletedetails")
public class deletedetails extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public deletedetails() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.setContentType("text/html");
		PrintWriter pw=response.getWriter();
		try {
		Class.forName("com.mysql.cj.jdbc.Driver");
		Connection con=DriverManager.getConnection("jdbc:mysql://localhost:3306/rail","root","root");
		PreparedStatement ps=con.prepareStatement("delete from student where number=?");
		int number=Integer.parseInt(request.getParameter("delete_id"));
		
		ps.setInt(1, number);
		
		int rows=ps.executeUpdate();
		if (rows > 0) {
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<title>Pass Deletion Status</title>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<center>");
            pw.println("<img src=https://img.freepik.com/free-psd/check-symbol-isolated_23-2150500363.jpg?size=338&ext=jpg&ga=GA1.1.1224184972.1714521600&semt=ais alt=\"Success\">");
            pw.println("<h2 style=\"color:green;\"> DELETED SUCCESSFULL!!!</h2>");
            pw.println("</center>");
            pw.println("</body>");
            pw.println("</html>");
        } else {
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<title>Pass DELETION Status</title>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<center>");
            pw.println("<img src=https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR6twsDLkvEhwjWOsLx-_UZaLFEaXpfdCbepAMJdMmdQw&s alt=\"Failure\">");
            pw.println("<h2 style=\"color:red;\">SORRY  DELETION FAILED!!!</h2>");
            pw.println("</center>");
            pw.println("</body>");
            pw.println("</html>");
        }
		
		
		
		}
		catch(Exception e ) {
			pw.println(e);
		}
	}

}


5.updatedetails.java(class file)
package study;



import java.io.IOException;

import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class updatepass
 */
@WebServlet("/updatedetails")
public class updatedetails extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public updatedetails() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.getWriter().append("Served at: ").append(request.getContextPath());
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		response.setContentType("text/html");
		PrintWriter pw=response.getWriter();
		try {
		Class.forName("com.mysql.cj.jdbc.Driver");
		Connection con=DriverManager.getConnection("jdbc:mysql://localhost:3306/rail","root","root");
//		PreparedStatement ps=con.prepareStatement("update student set name=? where num=?");
//		int num=Integer.parseInt(request.getParameter("update_id"));
//		String name=request.getParameter("update_name");
//		
//		ps.setInt(2, num);
//		ps.setString(1, name);
		
		PreparedStatement ps = con.prepareStatement("update student set name=? where number=?");
		String name = request.getParameter("update_name");
		int number = Integer.parseInt(request.getParameter("update_id"));

		ps.setString(1, name); // Set name first
		ps.setInt(2, number);      // Set num second

		
		int rows=ps.executeUpdate();
		if (rows > 0) {
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<title>Pass updation Status</title>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<center>");
            pw.println("<img src=https://img.freepik.com/free-psd/check-symbol-isolated_23-2150500363.jpg?size=338&ext=jpg&ga=GA1.1.1224184972.1714521600&semt=ais alt=\"Success\">");
            pw.println("<h2 style=\"color:green;\"> UPDATION SUCCESSFULL!!!</h2>");
            pw.println("</center>");
            pw.println("</body>");
            pw.println("</html>");
        } else {
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<title>Pass updation Status</title>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<center>");
            pw.println("<img src=https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcR6twsDLkvEhwjWOsLx-_UZaLFEaXpfdCbepAMJdMmdQw&s alt=\"Failure\">");
            pw.println("<h2 style=\"color:red;\">SORRY  UPDATION FAILED!!!</h2>");
            pw.println("</center>");
            pw.println("</body>");
            pw.println("</html>");
        }
		
			
		
		}
		catch(Exception e ) {
			pw.println(e);
		}
	}

}


6.displaydetails.java(class file)

package study;
import java.io.IOException;

import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class displaypass
 */
@WebServlet("/displaydetails")
public class displaydetails extends HttpServlet {
    private static final long serialVersionUID = 1L;

    /**
     * @see HttpServlet#HttpServlet()
     */
    public displaydetails() {
        super();
        // TODO Auto-generated constructor stub
    }

    /**
     * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // TODO Auto-generated method stub
        response.getWriter().append("Served at: ").append(request.getContextPath());
    }

    /**
     * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
     */
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter pw = response.getWriter();
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/rail", "root", "root");
            PreparedStatement ps = con.prepareStatement("select * from student");
            ResultSet rs = ps.executeQuery();

            // Start building the HTML table
            pw.println("<!DOCTYPE html>");
            pw.println("<html>");
            pw.println("<head>");
            pw.println("<meta charset=\"ISO-8859-1\">");
            pw.println("<title>LIST OF PASSES GENERATED </title>");
            pw.println("<style>");
            pw.println("table {");
            pw.println("    font-family: Arial, sans-serif;");
            pw.println("    border-collapse: collapse;");
            pw.println("    width: 80%;");
            pw.println("    margin: 20px auto;");
            pw.println("}");
            pw.println("th, td {");
            pw.println("    border: 1px solid #ddd;");
            pw.println("    padding: 8px;");
            pw.println("    text-align: center;");
            pw.println("}");
            pw.println("th {");
            pw.println("    background-color: #4CAF50;");
            pw.println("    color: white;");
            pw.println("}");
            pw.println("tr:nth-child(even) {");
            pw.println("    background-color: #f2f2f2;");
            pw.println("}");
            pw.println("</style>");
            pw.println("</head>");
            pw.println("<body>");
            pw.println("<h2 style=\"text-align:center;\">LIST OF DESKS ALLOCATED  </h2>");
            pw.println("<table>");
            pw.println("<tr><th>NUMBER</th><th>NAME</th><th>EMAIL</th><th>DESK</th></tr>");

            // Iterate through the ResultSet and add data to the table
            while (rs.next()) {
                pw.println("<tr>");
                pw.println("<td>" + rs.getInt(1) + "</td>");
                pw.println("<td>" + rs.getString(2) + "</td>");
                pw.println("<td>" + rs.getString(3) + "</td>");
                pw.println("<td>" + rs.getString(4) + "</td>");
                
                pw.println("</tr>");
            }

            pw.println("</table>");
            pw.println("</body>");
            pw.println("</html>");
        } catch (Exception e) {
            pw.println(e);
        }
    }

}


7.add_details.jsp(jsp file)
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>SSC MANAGEMENT SYSTEM</title>
<style>
    body {
        background-image: url('https://www.inside.unsw.edu.au/sites/default/files/inline-images/Spaces_2A.jpg');
        background-size: cover;
        background-repeat: no-repeat;
        background-attachment: fixed;
        font-family: Arial, sans-serif;
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
    }
    h1 {
        font-family: Arial, sans-serif;
        font-size: 62px;
        color: BLACK;
        text-align: ;
        font-weight: bold;
        margin: 0;
        padding: 10px;
        margin-bottom: 400px;
    }
    .container {
        display: flex;
        flex-direction: column;
        align-items: center;
        background-color: rgba(255, 255, 255, 0.8);
        padding: 40px;
        border-radius: 10px;
        width: 90%;
        max-width: 800px;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
    }
    .section {
        margin-bottom: 20px;
        width: 100%;
    }
    form {
        text-align: center;
    }
    input[type="text"], input[type="number"], input[type="email"], input[type="submit"], select {
        padding: 10px;
        width: 100%;
        min-width: 200px;
        margin-top: 10px;
        border: 1px solid #ccc;
        border-radius: 5px;
        box-sizing: border-box;
    }
    input[type="submit"] {
        background-color: #4CAF50;
        color: white;
        cursor: pointer;
    }
</style>
</head>
<body>
<h1>STUDENT STUDY CENTER MANAGEMENT SYSTEM</h1>
<div class="container">
    <!-- INSERTING THE PASS -->
    <div class="section">
        <form action="./insertdetails" method="post">
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <br>
        <caption>ADD STUDENT DETAILS</caption>
        <BR>
        <BR>
            <label for="num">Number:</label><br>
            <input type="number" id="num" name="num"><br>
            <label for="name">Name:</label><br>
            <input type="text" id="name" name="name"><br>
            <label for="email">Email:</label><br>
            <input type="text" id="email" name="email"><br>
            <label for="desk">Select Desk:</label><br>
            <select id="desk" name="desk">
                <option value="Desk1">Desk 1</option>
                <option value="Desk2">Desk 2</option>
                <option value="Desk3">Desk 3</option>
                <!-- Add more options as needed -->
            </select><br>
            <input type="submit" value="ADD">
            <br>
            <br>
        </form>
    </div>

    <!-- DELETING THE PASS -->
    <div class="section">
        <form action="./deletedetails" method="post">
        <br>
        <br>
        <caption>DELETE STUDENT DETAILS</caption>
        <BR>
        <BR>
            <label for="delete_id">Number to Delete:</label><br>
            <input type="number" id="delete_id" name="delete_id"><br>
            <input type="submit" value="DELETE">
            <br>
            <br>
        </form>
    </div>

    <!-- UPDATING THE PASS -->
    <div class="section">
        <form action="./updatedetails" method="post">
        <br>
        <br>
        <caption>UPDATE STUDENT DETAILS</caption>
        <BR>
        <BR>
            <label for="update_id">Number to Update:</label><br>
            <input type="number" id="update_id" name="update_id"><br>
            <label for="update_name">New Name:</label><br>
            <input type="text" id="update_name" name="update_name"><br>
            <input type="submit" value="UPDATE">
            <br>
            <br>
        </form>
    </div>

    <!-- DISPLAY THE PASS -->
    <div class="section">
        <form action="./displaydetails" method="post">
        <br>
        <br>
        <caption>DISPLAY STUDENT DETAILS</caption>
        <BR>
        <BR>
            <input type="submit" value="DISPLAY">
           
        </form>
    </div>
</div>
</body>
</html>


8.dashboard.jsp(jsp file)
<!DOCTYPE html>
<html>
<head>
    <title>ssdms</title>
    <style>
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-image: url('https://www.inside.unsw.edu.au/sites/default/files/inline-images/Spaces_2A.jpg'); /* Path to your background image */
            background-size: cover; /* Cover the entire viewport */
            background-position: center; /* Center the background image */
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        h1 {
            text-align: center;
            color: #333; /* Dark gray text */
            margin-bottom: 30px;
            text-transform: uppercase; /* Convert text to uppercase */
        }
        .container {
            background-color: rgba(255, 255, 255, 0.9); /* Semi-transparent white background for container */
            padding: 40px;
            border-radius: 10px;
            box-shadow: 0 0 20px rgba(0, 0, 0, 0.1); /* Shadow effect */
            width: 400px; /* Set container width */
            max-width: 90%; /* Set maximum width */
        }
        form {
            display: flex;
            flex-direction: column; /* Arrange form elements vertically */
            align-items: center;
        }
        label {
            margin-bottom: 10px;
            color: #666; /* Gray text color */
            font-size: 14px;
        }
        input[type="text"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 20px;
            border: 1px solid #ccc; /* Light gray border */
            border-radius: 5px;
            box-sizing: border-box; /* Include padding and border in element's total width and height */
        }
        button {
            padding: 12px 24px;
            font-size: 16px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            background-color: #4CAF50; /* Green background */
            color: #fff; /* White text color */
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #45a049; /* Darker green background on hover */
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Student Study Center Management System</h1>
    
        <form id="form1" action="adminServlet" method="post">
            <caption>ADMIN LOGIN</caption>
            <label for="username">Username:</label>
            <input type="text" id="username" name="username">
            <label for="password">Password:</label>
            <input type="text" id="password" name="password">
            <button type="submit">Admin Login</button>
            <br>
            <br>
        </form>
    
        <form id="form2" action="studentservlet" method="post">
        <caption>STUDENT DETAILS </caption>
            <label for="studentUsername">Username:</label>
            <input type="text" id="studentUsername" name="studentUsername">
           
            <button type="submit">Student Search</button>
            <br>
            <br>
        </form>
    </div>
</body>
</html>


