# Employee_Database_App
This Java project connects applications to a MySQL database using **MySQL Connector/J**, the official JDBC driver. It enables seamless data exchange between Java programs and MySQL by supporting SQL queries, CRUD operations, and secure connections for building dynamic, database-driven applications efficiently.
import java.sql.*;
import java.util.Scanner;

public class EmployeeDBApp {
    // Database credentials
    private static final String URL = "jdbc:mysql://localhost:3306/employee_db";
private static final String USER = "root";
private static final String PASSWORD = "Vedant@07"; 
    
    private Connection conn = null;
    private Scanner scanner = new Scanner(System.in); 
    
    // Constructor - Establish database connection
    public EmployeeDBApp() {
        try {
            // Load MySQL JDBC Driver
            Class.forName("com.mysql.cj.jdbc.Driver");
            
            // Establish connection
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            System.out.println("✓ Database connected successfully!\n");
        } catch (ClassNotFoundException e) {
            System.err.println("MySQL JDBC Driver not found!");
            e.printStackTrace();
        } catch (SQLException e) {
            System.err.println("Connection failed!");
            e.printStackTrace();
        }
    }
    
    // CREATE - Add new employee
    public void addEmployee() {
        try {
            System.out.println("\n=== ADD NEW EMPLOYEE ===");
            System.out.print("Enter Name: ");
            String name = scanner.nextLine();
            
            System.out.print("Enter Email: ");
            String email = scanner.nextLine();
            
            System.out.print("Enter Department: ");
            String department = scanner.nextLine();
            
            System.out.print("Enter Salary: ");
            double salary = scanner.nextDouble();
            scanner.nextLine(); // Consume newline
            
            String sql = "INSERT INTO employees (name, email, department, salary) VALUES (?, ?, ?, ?)";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, name);
            pstmt.setString(2, email);
            pstmt.setString(3, department);
            pstmt.setDouble(4, salary);
            
            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                System.out.println("✓ Employee added successfully!");
            }
            pstmt.close();
        } catch (SQLException e) {
            System.err.println("Error adding employee!");
            e.printStackTrace();
        }
    }
    
    // READ - View all employees
    public void viewAllEmployees() {
        try {
            System.out.println("\n=== ALL EMPLOYEES ===");
            String sql = "SELECT * FROM employees";
            Statement stmt = conn.createStatement();
            ResultSet rs = stmt.executeQuery(sql);
            
            System.out.println("─────────────────────────────────────────────────────────────────────");
            System.out.printf("%-5s %-20s %-25s %-15s %-10s%n", 
                "ID", "Name", "Email", "Department", "Salary");
            System.out.println("─────────────────────────────────────────────────────────────────────");
            
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                String email = rs.getString("email");
                String department = rs.getString("department");
                double salary = rs.getDouble("salary");
                
                System.out.printf("%-5d %-20s %-25s %-15s $%-10.2f%n", 
                    id, name, email, department, salary);
            }
            System.out.println("─────────────────────────────────────────────────────────────────────");
            
            rs.close();
            stmt.close();
        } catch (SQLException e) {
            System.err.println("Error viewing employees!");
            e.printStackTrace();
        }
    }
    
    // READ - Search employee by ID
    public void searchEmployeeById() {
        try {
            System.out.println("\n=== SEARCH EMPLOYEE ===");
            System.out.print("Enter Employee ID: ");
            int id = scanner.nextInt();
            scanner.nextLine(); // Consume newline
            
            String sql = "SELECT * FROM employees WHERE id = ?";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, id);
            ResultSet rs = pstmt.executeQuery();
            
            if (rs.next()) {
                System.out.println("\n✓ Employee Found:");
                System.out.println("ID: " + rs.getInt("id"));
                System.out.println("Name: " + rs.getString("name"));
                System.out.println("Email: " + rs.getString("email"));
                System.out.println("Department: " + rs.getString("department"));
                System.out.println("Salary: $" + rs.getDouble("salary"));
            } else {
                System.out.println("✗ No employee found with ID: " + id);
            }
            
            rs.close();
            pstmt.close();
        } catch (SQLException e) {
            System.err.println("Error searching employee!");
            e.printStackTrace();
        }
    }
    
    // UPDATE - Modify employee details
    public void updateEmployee() {
        try {
            System.out.println("\n=== UPDATE EMPLOYEE ===");
            System.out.print("Enter Employee ID to update: ");
            int id = scanner.nextInt();
            scanner.nextLine(); // Consume newline
            
            System.out.print("Enter New Name: ");
            String name = scanner.nextLine();
            
            System.out.print("Enter New Email: ");
            String email = scanner.nextLine();
            
            System.out.print("Enter New Department: ");
            String department = scanner.nextLine();
            
            System.out.print("Enter New Salary: ");
            double salary = scanner.nextDouble();
            scanner.nextLine(); // Consume newline
            
            String sql = "UPDATE employees SET name = ?, email = ?, department = ?, salary = ? WHERE id = ?";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, name);
            pstmt.setString(2, email);
            pstmt.setString(3, department);
            pstmt.setDouble(4, salary);
            pstmt.setInt(5, id);
            
            int rowsAffected = pstmt.executeUpdate();
            if (rowsAffected > 0) {
                System.out.println("✓ Employee updated successfully!");
            } else {
                System.out.println("✗ No employee found with ID: " + id);
            }
            pstmt.close();
        } catch (SQLException e) {
            System.err.println("Error updating employee!");
            e.printStackTrace();
        }
    }
    
    // DELETE - Remove employee
    public void deleteEmployee() {
        try {
            System.out.println("\n=== DELETE EMPLOYEE ===");
            System.out.print("Enter Employee ID to delete: ");
            int id = scanner.nextInt();
            scanner.nextLine(); // Consume newline
            
            System.out.print("Are you sure? (yes/no): ");
            String confirm = scanner.nextLine();
            
            if (confirm.equalsIgnoreCase("yes")) {
                String sql = "DELETE FROM employees WHERE id = ?";
                PreparedStatement pstmt = conn.prepareStatement(sql);
                pstmt.setInt(1, id);
                
                int rowsAffected = pstmt.executeUpdate();
                if (rowsAffected > 0) {
                    System.out.println("✓ Employee deleted successfully!");
                } else {
                    System.out.println("✗ No employee found with ID: " + id);
                }
                pstmt.close();
            } else {
                System.out.println("Delete operation cancelled.");
            }
        } catch (SQLException e) {
            System.err.println("Error deleting employee!");
            e.printStackTrace();
        }
    }
    
    // Close database connection
    public void closeConnection() {
        try {
            if (conn != null && !conn.isClosed()) {
                conn.close();
                System.out.println("\n✓ Database connection closed.");
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    
    // Main menu
    public void displayMenu() {
        while (true) {
            System.out.println("\n╔════════════════════════════════════╗");
            System.out.println("║   EMPLOYEE DATABASE MANAGEMENT     ║");
            System.out.println("╚════════════════════════════════════╝");
            System.out.println("1. Add Employee");
            System.out.println("2. View All Employees");
            System.out.println("3. Search Employee by ID");
            System.out.println("4. Update Employee");
            System.out.println("5. Delete Employee");
            System.out.println("6. Exit");
            System.out.print("\nEnter your choice: ");
            
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline
            
            switch (choice) {
                case 1:
                    addEmployee();
                    break;
                case 2:
                    viewAllEmployees();
                    break;
                case 3:
                    searchEmployeeById();
                    break;
                case 4:
                    updateEmployee();
                    break;
                case 5:
                    deleteEmployee();
                    break;
                case 6:
                    closeConnection();
                    System.out.println("Goodbye!");
                    System.exit(0);
                default:
                    System.out.println("Invalid choice! Please try again.");
            }
        }
    }
    
    // Main method
    public static void main(String[] args) {
        EmployeeDBApp app = new EmployeeDBApp();
        app.displayMenu();
    }
}
