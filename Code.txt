public class App {
    public static void main(String[] args) throws Exception {
        new LoginGui();
    }
}

import javax.swing.*;

public abstract class BaseFrame extends JFrame {

    public BaseFrame(String title) {
        ini(title);
    }

    private void ini(String title) {
        setTitle(title);
        setSize(350,550);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(null);
        setResizable(false);
        setLocationRelativeTo(null);

        addGuiContent();
    }

    protected abstract void addGuiContent();
}

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class LoginGui extends BaseFrame implements ActionListener {
    private JButton b1,b2,b3;
    private JLabel l1,l2,lw;
    private JTextField t1;
    private JPasswordField p1;

    LoginGui() {
        super("Login Interface");
        addGuiContent();
        this.setVisible(true);
    }

    // Override
    protected void addGuiContent() {

     // Panels
     JPanel plogin = (JPanel)this.getContentPane();
     JPanel toplog = new JPanel();
     JPanel midlog = new JPanel();
     JPanel bottomlog = new JPanel();

     // Layouts
     plogin.setLayout(new BorderLayout());
     toplog.setLayout(new FlowLayout(FlowLayout.CENTER, 0, 45)); // Adjusted vertical space
     midlog.setLayout(new GridLayout(2,1,10,10));
     bottomlog.setLayout(new FlowLayout(FlowLayout.CENTER));

     // Welcome
     JPanel welcomePanel = new JPanel(new GridLayout(1,1,150,150));
     lw = new JLabel("Welcome!");
     lw.setFont(new Font("Arial", Font.BOLD, 30));

     welcomePanel.add(lw);
     toplog.add(welcomePanel);
     

     // Username Panel
     JPanel usernamePanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
     l1 = new JLabel("Username:");
     t1 = new JTextField();

     t1.setPreferredSize(new Dimension(150,25));

     usernamePanel.add(l1);
     usernamePanel.add(t1);
     midlog.add(usernamePanel);
     

     //Password Panel
     JPanel passwordPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
     l2 = new JLabel("Password:");
     p1 = new JPasswordField();

     p1.setPreferredSize(new Dimension(150, 25));

     passwordPanel.add(l2);
     passwordPanel.add(p1);

     midlog.add(passwordPanel);


     // Buttons
     b1 = new JButton("Login");
     b3 = new JButton("Register");
     b2 = new JButton("Exit");

     bottomlog.add(b1);
     bottomlog.add(b3);
     bottomlog.add(b2);
     
     b1.addActionListener(this);
     b3.addActionListener(this);
     b2.addActionListener(this);

     // Add the panels to the main panel
     plogin.add(toplog,BorderLayout.NORTH);
     plogin.add(midlog,BorderLayout.CENTER);
     plogin.add(bottomlog,BorderLayout.SOUTH);
    }
    

    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == b2) {
            System.out.println("Exit button pressed!");
            System.exit(0);
        }
    
        if (e.getSource() == b3) {
            new RegisterGui();
        } else if (e.getSource() == b1) {
            System.out.println("Login button pressed!");
    
            DatabaseConnection db = new DatabaseConnection();
            String username = t1.getText().trim();
            String password = new String(p1.getPassword()).trim();
    
            // Debug logs for username and password
            System.out.println("Input Username: " + username);
            System.out.println("Input Password: " + password);
    
            String id = db.getId(username, password);
    
            // Debug log for user ID
            System.out.println("Retrieved user ID: " + id);
    
            String role = db.getRole(username, password);
            System.out.println("Retrieved Role: " + role);
    
            if (db.validateUser(username, password)) {
                JOptionPane.showMessageDialog(null, "Login successfully!\nYou're logged in as " + role, "Success", JOptionPane.INFORMATION_MESSAGE);
                setVisible(false); // Hide the Login GUI
                System.out.println("Username: " + username);
                System.out.println("ID: " + id);
                System.out.println("Role: " + role);
    
                if (role.trim().equalsIgnoreCase("Student")) {
                    new StudentGui(username, id);
                } else if (role.trim().equalsIgnoreCase("Admin")) {
                    new AdminGui();
                } else if (role.trim().equalsIgnoreCase("Teacher")) {
                    new TeacherGui();
                }
            } else {
                JOptionPane.showMessageDialog(null, "Invalid username or password", "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }
    
    
    

    

}

import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.*;
import javax.swing.*;
import java.awt.*;

class RegisterGui extends BaseFrame implements ActionListener {
    private JTextField usernameField;
    private JPasswordField passwordField;
    private JComboBox<String> roleComboBox;
    private JButton registerButton, cancelButton;

    RegisterGui() {
        super("Register");
        addGuiContent();
        this.setVisible(true);
    }

    @Override
    protected void addGuiContent() {
        JPanel mainPanel = new JPanel();
        mainPanel.setLayout(new BoxLayout(mainPanel, BoxLayout.Y_AXIS));
        this.setContentPane(mainPanel);

        // Username Row
        JPanel usernamePanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        usernamePanel.add(new JLabel("Username:"));
        usernameField = new JTextField(15);
        usernamePanel.add(usernameField);

        // Password Row
        JPanel passwordPanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        passwordPanel.add(new JLabel("Password:"));
        passwordField = new JPasswordField(15);
        passwordPanel.add(passwordField);

        // Role Row
        JPanel rolePanel = new JPanel(new FlowLayout(FlowLayout.LEFT));
        rolePanel.add(new JLabel("Role:"));
        roleComboBox = new JComboBox<>(new String[] { "Student", "Teacher", "Admin" }); // JComboBox for roles
        rolePanel.add(roleComboBox);

        // Buttons Row
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
        registerButton = new JButton("Register");
        cancelButton = new JButton("Cancel");
        registerButton.addActionListener(this);
        cancelButton.addActionListener(this);
        buttonPanel.add(registerButton);
        buttonPanel.add(cancelButton);

        // Add all rows to the main panel
        mainPanel.add(usernamePanel);
        mainPanel.add(passwordPanel);
        mainPanel.add(rolePanel);
        mainPanel.add(buttonPanel);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == cancelButton) {
            dispose(); // Close the Register window
        } else if (e.getSource() == registerButton) {
            String username = usernameField.getText();
            String password = new String(passwordField.getPassword());
            String role = roleComboBox.getSelectedItem().toString().toLowerCase(); // Get selected role

            if (username.isEmpty() || password.isEmpty()) {
                JOptionPane.showMessageDialog(this, "All fields are required!", "Error", JOptionPane.ERROR_MESSAGE);
            } else {
                DatabaseConnection db = new DatabaseConnection();
                String query = "INSERT INTO users (username, password, role) VALUES (?, ?, ?)";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setString(1, username);
                    stmt.setString(2, password);
                    stmt.setString(3, role);
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "User registered successfully!", "Success", JOptionPane.INFORMATION_MESSAGE);
                    dispose(); // Close the Register window after successful registration
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error registering user: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        }
    }
}

import java.sql.*;

public class DatabaseConnection implements AutoCloseable {
    private String url = "jdbc:mysql://localhost:3306/StudentGradingSystem";
    private String user = "root";
    private String password = "";
    private Connection connection;

    public DatabaseConnection() {
        try {
            connection = DriverManager.getConnection(url, user, password);
            System.out.println("Connected to database successfully!");
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public Connection getConnection() {
        return connection;
    }

    public boolean validateUser(String username, String password) {
        String query = "SELECT * FROM users WHERE username = ? AND password = ?";
        try (PreparedStatement stmt = connection.prepareStatement(query)) {
            stmt.setString(1, username);
            stmt.setString(2, password);
            try (ResultSet rs = stmt.executeQuery()) {
                return rs.next();
            }
        } catch (SQLException e) {
            e.printStackTrace();
            return false;
        }
    }

    public String getRole(String username, String password) {
        String query = "SELECT role FROM users WHERE username = ? AND password = ?";
        try (PreparedStatement stmt = connection.prepareStatement(query)) {
            stmt.setString(1, username);
            stmt.setString(2, password);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    return rs.getString("role");
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return "";
    }

    public String getId(String username, String password) {
        String query = "SELECT user_id FROM users WHERE username = ? AND password = ?";
        try (PreparedStatement stmt = connection.prepareStatement(query)) {
            System.out.println("Executing query: " + query + " with username = " + username + " and password = " + password);
            stmt.setString(1, username);
            stmt.setString(2, password);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    String userId = rs.getString("user_id");
                    System.out.println("Retrieved user ID: " + userId);
                    return userId;
                } else {
                    System.err.println("No matching user found for username: " + username);
                }
            }
        } catch (SQLException e) {
            System.err.println("Error in getId: " + e.getMessage());
            e.printStackTrace();
        }
        return null; // Return null if no matching user is found
    }
    
    
    

    public ResultSet getGrades(String userId) {
        String query = "SELECT course_id, assignment_score, quiz_score, exam_score, final_grade, course_name FROM grades WHERE user_id = ?";
        System.out.println("Executing query: " + query + " with userId = " + userId);
        try {
            PreparedStatement stmt = connection.prepareStatement(query);
            stmt.setString(1, userId);
            ResultSet rs = stmt.executeQuery();
            if (rs == null) {
                System.err.println("ResultSet is null for userId = " + userId);
            } else {
                System.out.println("Query executed successfully.");
            }
            return rs;
        } catch (SQLException e) {
            System.err.println("Error executing query in getGrades: " + e.getMessage());
            e.printStackTrace();
            return null; // Return null if the query fails
        }
    }
    

    public ResultSet getNotifications(String userId) {
        String query = "SELECT notification_id, message, created_at FROM notifications WHERE user_id = ?";
        try {
            System.out.println("Executing query: " + query + " with userId = " + userId);
            PreparedStatement stmt = connection.prepareStatement(query);
            stmt.setString(1, userId);
            return stmt.executeQuery();
        } catch (SQLException e) {
            System.err.println("Error in getNotifications: " + e.getMessage());
            e.printStackTrace();
            return null;
        }
    }
    

    public ResultSet getNotificationsForAdmin() {
        String query = "SELECT notification_id, user_id, message, created_at FROM notifications ORDER BY created_at DESC";
        try {
            PreparedStatement stmt = connection.prepareStatement(query);
            return stmt.executeQuery();
        } catch (SQLException e) {
            System.err.println("Error fetching notifications for admin: " + e.getMessage());
            return null;
        }
    }

    public ResultSet getUsers() {
        try {
            String query = "SELECT * FROM users";
            return connection.prepareStatement(query).executeQuery();
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }

    public ResultSet getCourses() {
        try {
            String query = "SELECT * FROM courses";
            return connection.prepareStatement(query).executeQuery();
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }

    public ResultSet getGradesForAdmin() {
        try {
            String query = "SELECT * FROM grades";
            return connection.prepareStatement(query).executeQuery();
        } catch (SQLException e) {
            e.printStackTrace();
            return null;
        }
    }

    public double calculatePercentage(double assignmentScore, double quizScore, double examScore) {
        // Normalize each score to a scale of 100
        double normalizedAssignment = (assignmentScore / 30) * 100;
        double normalizedQuiz = (quizScore / 20) * 100;
        double normalizedExam = (examScore / 50) * 100;
    
        // Calculate the weighted percentage
        double percentage = (normalizedAssignment * 0.3) + (normalizedQuiz * 0.2) + (normalizedExam * 0.5);
        return percentage;
    }

    

    public String getGrade(double percentage) {
        if (percentage >= 94) return "A+";
        if (percentage >= 89) return "A";
        if (percentage >= 84) return "B+";
        if (percentage >= 79) return "B";
        if (percentage >= 74) return "C+";
        if (percentage >= 69) return "C";
        if (percentage >= 64) return "D+";
        if (percentage >= 60) return "D";
        return "F";
    }

    @Override
    public void close() {
        try {
            if (connection != null && !connection.isClosed()) {
                connection.close();
                System.out.println("Connection closed.");
            }
        } catch (SQLException e) {
            System.err.println("Error closing connection: " + e.getMessage());
        }
    }
}

import javax.swing.*;
import javax.swing.border.TitledBorder;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.sql.*;
import java.util.Vector;

public class StudentGui extends BaseFrame {

    private JLabel usernameLabel, idLabel, gpaLabel, leastGradeLabel, mostGradeLabel;
    private String username, id;
    private JTable gradesTable, notificationsTable;
    private JButton exitButton, logoutButton;

    StudentGui(String username, String id) {
        super("Student Dashboard");
        if (id == null || id.isEmpty()) {
            JOptionPane.showMessageDialog(this, "Invalid user ID. Please try again.", "Error", JOptionPane.ERROR_MESSAGE);
            dispose();
            return;
        }
        this.username = username != null ? username : "Guest";
        this.id = id;
        this.setSize(750, 700);
        addGuiContent();
        this.setVisible(true);
    }

    protected void addGuiContent() {
        JPanel mainPanel = (JPanel) this.getContentPane();
        mainPanel.setLayout(new BorderLayout());
    
        // Top Panel for Username and ID
        JPanel topPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 100, 15));
        usernameLabel = new JLabel("Username: " + username);
        usernameLabel.setFont(new Font("Arial", Font.BOLD, 20));
        idLabel = new JLabel("ID: " + id);
        idLabel.setFont(new Font("Arial", Font.BOLD, 20));
        topPanel.add(usernameLabel);
        topPanel.add(idLabel);
        mainPanel.add(topPanel, BorderLayout.NORTH);
    
        // Center Panel for Grades, Report, and Notifications
        JPanel centerPanel = new JPanel(new GridLayout(3, 1, 10, 10));
        gradesTable = new JTable();
        notificationsTable = new JTable();
    
        JScrollPane gradesScrollPane = new JScrollPane(gradesTable);
        JScrollPane notificationsScrollPane = new JScrollPane(notificationsTable);
    
        gradesScrollPane.setBorder(new TitledBorder("Grades"));
        notificationsScrollPane.setBorder(new TitledBorder("Notifications"));
    
        // Report Section for GPA, Average, Least Grade, and Most Grade
        JPanel reportPanel = new JPanel(new GridLayout(4, 1, 5, 5));
        reportPanel.setBorder(new TitledBorder("Report"));
        gpaLabel = new JLabel("GPA (out of 5): Calculating...");
        leastGradeLabel = new JLabel("Worst Grade: Calculating...");
        mostGradeLabel = new JLabel("Best Grade: Calculating...");
        JLabel averagePerformanceLabel = new JLabel("Average Performance: Calculating...");
    
        reportPanel.add(gpaLabel);
        reportPanel.add(leastGradeLabel);
        reportPanel.add(mostGradeLabel);
        reportPanel.add(averagePerformanceLabel);
    
        // Add Sections to Center Panel
        centerPanel.add(gradesScrollPane); // Grades Section
        centerPanel.add(reportPanel);     // Report Section
        centerPanel.add(notificationsScrollPane); // Notifications Section
        mainPanel.add(centerPanel, BorderLayout.CENTER);
    
        // Exit and Logout Buttons
        JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 100, 15));
        exitButton = new JButton("Exit");
        exitButton.addActionListener(e -> System.exit(0));
        logoutButton = new JButton("Logout");
        logoutButton.addActionListener(e -> {
            dispose();
            new LoginGui();
        });
        buttonPanel.add(exitButton);
        buttonPanel.add(logoutButton);
        mainPanel.add(buttonPanel, BorderLayout.SOUTH);
    
        // Load Data
        try {
            loadGrades(averagePerformanceLabel); // Pass the label for average performance
            loadNotifications();
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(this, "Error loading data: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
    
    private void loadGrades(JLabel averagePerformanceLabel) {
        DatabaseConnection db = new DatabaseConnection();
        try {
            ResultSet rs = db.getGrades(id);
            if (rs == null || !rs.next()) {
                System.out.println("No grades found for user ID: " + id);
                return;
            }
    
            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();
            columnNames.add("Course Name");
            columnNames.add("Assignment Score");
            columnNames.add("Quiz Score");
            columnNames.add("Exam Score");
            columnNames.add("Total Percentage");
            columnNames.add("Grade");
    
            // GPA Calculation Variables
            double totalGradePoints = 0.0;
            int gradeCount = 0;
            double totalPercentage = 0.0; // New variable for calculating average percentage
            String leastGrade = null, mostGrade = null;
    
            do {
                Vector<Object> row = new Vector<>();
                double assignment = rs.getDouble("assignment_score");
                double quiz = rs.getDouble("quiz_score");
                double exam = rs.getDouble("exam_score");
                double percentage = db.calculatePercentage(assignment, quiz, exam);
                String finalGrade = db.getGrade(percentage);
    
                // GPA Calculation
                double gradePoint = getGradePoint(finalGrade);
                totalGradePoints += gradePoint;
                totalPercentage += percentage; // Sum up percentages
                gradeCount++;
    
                // Least and Most Grade Calculation
                if (leastGrade == null || compareGrades(finalGrade, leastGrade) < 0) {
                    leastGrade = finalGrade;
                }
                if (mostGrade == null || compareGrades(finalGrade, mostGrade) > 0) {
                    mostGrade = finalGrade;
                }
    
                row.add(rs.getString("course_name"));
                row.add(assignment);
                row.add(quiz);
                row.add(exam);
                row.add(String.format("%.2f%%", percentage));
                row.add(finalGrade);
    
                data.add(row);
            } while (rs.next());
    
            gradesTable.setModel(new DefaultTableModel(data, columnNames));
    
            // Display GPA, Least Grade, Most Grade, and Average Performance
            double gpa = totalGradePoints / gradeCount;
            double averagePerformance = totalPercentage / gradeCount; // Calculate average percentage
            gpaLabel.setText(String.format("GPA (out of 5): %.2f", gpa));
            leastGradeLabel.setText("Least Grade: " + leastGrade);
            mostGradeLabel.setText("Most Grade: " + mostGrade);
            averagePerformanceLabel.setText(String.format("Average Performance: %.2f%%", averagePerformance));
    
            // Refresh Labels
            gpaLabel.revalidate();
            gpaLabel.repaint();
            leastGradeLabel.revalidate();
            leastGradeLabel.repaint();
            mostGradeLabel.revalidate();
            mostGradeLabel.repaint();
            averagePerformanceLabel.revalidate();
            averagePerformanceLabel.repaint();
    
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading grades: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        } finally {
            db.close();
        }
    }
    

    private void loadNotifications() {
        DatabaseConnection db = new DatabaseConnection();
        try {
            ResultSet rs = db.getNotifications(id);
            if (rs == null || !rs.next()) {
                System.out.println("No notifications found for user ID: " + id);
                return;
            }

            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();
            columnNames.add("Message");
            columnNames.add("Created At");

            do {
                Vector<Object> row = new Vector<>();
                row.add(rs.getString("message"));
                row.add(rs.getTimestamp("created_at"));
                data.add(row);
            } while (rs.next());

            notificationsTable.setModel(new DefaultTableModel(data, columnNames));

        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading notifications: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        } finally {
            db.close();
        }
    }

    private double getGradePoint(String grade) {
        switch (grade) {
            case "A+": return 5.0;
            case "A": return 4.5;
            case "B+": return 4.0;
            case "B": return 3.5;
            case "C+": return 3.0;
            case "C": return 2.5;
            case "D+": return 2.0;
            case "D": return 1.5;
            case "F": return 0.0;
            default: return 0.0;
        }
    }

    private int compareGrades(String grade1, String grade2) {
        return Double.compare(getGradePoint(grade1), getGradePoint(grade2));
    }
}

import javax.swing.*;
import javax.swing.border.TitledBorder;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Vector;

public class TeacherGui extends BaseFrame implements ActionListener {

    private JTable gradesTable, notificationsTable, studentsTable;
    private JButton addGradeButton, editGradeButton, deleteGradeButton;
    private JButton addNotificationButton;

    public TeacherGui() {
        super("Teacher Dashboard");
        this.setSize(800, 600);
        addGuiContent();
        this.setVisible(true);
    }

    @Override
    protected void addGuiContent() {
        JPanel mainPanel = (JPanel) this.getContentPane();
        mainPanel.setLayout(new BorderLayout());

        JPanel centerPanel = new JPanel(new GridLayout(3, 1, 10, 10));
        JPanel bottomPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));

        gradesTable = new JTable();
        notificationsTable = new JTable();
        studentsTable = new JTable();

        JScrollPane gradesScrollPane = new JScrollPane(gradesTable);
        gradesScrollPane.setBorder(new TitledBorder("Grades"));

        JScrollPane notificationsScrollPane = new JScrollPane(notificationsTable);
        notificationsScrollPane.setBorder(new TitledBorder("Notifications"));

        JScrollPane studentsScrollPane = new JScrollPane(studentsTable);
        studentsScrollPane.setBorder(new TitledBorder("Enrolled Students"));

        centerPanel.add(gradesScrollPane);
        centerPanel.add(notificationsScrollPane);
        centerPanel.add(studentsScrollPane);

        addGradeButton = new JButton("Add Grade");
        editGradeButton = new JButton("Edit Grade");
        deleteGradeButton = new JButton("Delete Grade");
        addNotificationButton = new JButton("Add Notification");

        addGradeButton.addActionListener(this);
        editGradeButton.addActionListener(this);
        deleteGradeButton.addActionListener(this);
        addNotificationButton.addActionListener(this);

        bottomPanel.add(addGradeButton);
        bottomPanel.add(editGradeButton);
        bottomPanel.add(deleteGradeButton);
        bottomPanel.add(addNotificationButton);

        mainPanel.add(centerPanel, BorderLayout.CENTER);
        mainPanel.add(bottomPanel, BorderLayout.SOUTH);

        loadGrades();
        loadNotifications();
        loadStudents();
    }

    private void loadGrades() {
        if (gradesTable == null) {
            System.err.println("Error: gradesTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getGradesForAdmin()) {

            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();

            columnNames.add("User ID");
            columnNames.add("Course Name");
            columnNames.add("Assignment Score");
            columnNames.add("Quiz Score");
            columnNames.add("Exam Score");
            columnNames.add("Final Grade");

            while (rs.next()) {
                Vector<Object> row = new Vector<>();
                row.add(rs.getInt("user_id"));
                row.add(rs.getString("course_name"));
                row.add(rs.getDouble("assignment_score"));
                row.add(rs.getDouble("quiz_score"));
                row.add(rs.getDouble("exam_score"));
                row.add(rs.getString("final_grade"));
                data.add(row);
            }

            gradesTable.setModel(new DefaultTableModel(data, columnNames));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading grades: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadNotifications() {
        if (notificationsTable == null) {
            System.err.println("Error: notificationsTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getNotificationsForAdmin()) {

            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();

            columnNames.add("Notification Message");
            columnNames.add("Created At");

            while (rs.next()) {
                Vector<Object> row = new Vector<>();
                row.add(rs.getString("message"));
                row.add(rs.getTimestamp("created_at"));
                data.add(row);
            }

            notificationsTable.setModel(new DefaultTableModel(data, columnNames));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading notifications: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadStudents() {
        if (studentsTable == null) {
            System.err.println("Error: studentsTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getCourses()) {

            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();

            columnNames.add("Course ID");
            columnNames.add("Course Name");
            columnNames.add("Course Code");

            while (rs.next()) {
                Vector<Object> row = new Vector<>();
                row.add(rs.getInt("course_id"));
                row.add(rs.getString("course_name"));
                row.add(rs.getString("course_code"));
                data.add(row);
            }

            studentsTable.setModel(new DefaultTableModel(data, columnNames));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading students: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    @Override
public void actionPerformed(ActionEvent e) {
    DatabaseConnection db = new DatabaseConnection();

    // Add Grade
    if (e.getSource() == addGradeButton) {
        String studentId = JOptionPane.showInputDialog(this, "Enter Student ID:");
        String courseId = JOptionPane.showInputDialog(this, "Enter Course ID:");
        String assignmentScore = JOptionPane.showInputDialog(this, "Enter Assignment Score:");
        String quizScore = JOptionPane.showInputDialog(this, "Enter Quiz Score:");
        String examScore = JOptionPane.showInputDialog(this, "Enter Exam Score:");

        if (studentId != null && courseId != null && assignmentScore != null && quizScore != null && examScore != null) {
            try {
                String query = "INSERT INTO grades (user_id, course_id, assignment_score, quiz_score, exam_score, final_grade) VALUES (?, ?, ?, ?, ?, ?)";
                double assignment = Double.parseDouble(assignmentScore);
                double quiz = Double.parseDouble(quizScore);
                double exam = Double.parseDouble(examScore);
                double percentage = db.calculatePercentage(assignment, quiz, exam);
                String finalGrade = db.getGrade(percentage);

                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setInt(1, Integer.parseInt(studentId));
                    stmt.setInt(2, Integer.parseInt(courseId));
                    stmt.setDouble(3, assignment);
                    stmt.setDouble(4, quiz);
                    stmt.setDouble(5, exam);
                    stmt.setString(6, finalGrade);
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "Grade added successfully!");
                    loadGrades();
                }
            } catch (SQLException | NumberFormatException ex) {
                JOptionPane.showMessageDialog(this, "Error adding grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    // Edit Grade
    else if (e.getSource() == editGradeButton) {
        int selectedRow = gradesTable.getSelectedRow();
        if (selectedRow >= 0) {
            try {
                // Get current grade details
                int userId = (int) gradesTable.getValueAt(selectedRow, 0);
                String courseName = (String) gradesTable.getValueAt(selectedRow, 1);
                double assignmentScore = (double) gradesTable.getValueAt(selectedRow, 2);
                double quizScore = (double) gradesTable.getValueAt(selectedRow, 3);
                double examScore = (double) gradesTable.getValueAt(selectedRow, 4);

                // Prompt the teacher to edit the scores
                String newAssignmentScore = JOptionPane.showInputDialog(this, "Edit Assignment Score:", assignmentScore);
                String newQuizScore = JOptionPane.showInputDialog(this, "Edit Quiz Score:", quizScore);
                String newExamScore = JOptionPane.showInputDialog(this, "Edit Exam Score:", examScore);

                // Validate and update the grade
                if (newAssignmentScore != null && newQuizScore != null && newExamScore != null) {
                    double newAssignment = Double.parseDouble(newAssignmentScore);
                    double newQuiz = Double.parseDouble(newQuizScore);
                    double newExam = Double.parseDouble(newExamScore);

                    double percentage = db.calculatePercentage(newAssignment, newQuiz, newExam);
                    String finalGrade = db.getGrade(percentage);

                    String query = "UPDATE grades SET assignment_score = ?, quiz_score = ?, exam_score = ?, final_grade = ? WHERE user_id = ? AND course_name = ?";
                    try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                        stmt.setDouble(1, newAssignment);
                        stmt.setDouble(2, newQuiz);
                        stmt.setDouble(3, newExam);
                        stmt.setString(4, finalGrade);
                        stmt.setInt(5, userId);
                        stmt.setString(6, courseName);
                        stmt.executeUpdate();
                        JOptionPane.showMessageDialog(this, "Grade updated successfully!");
                        loadGrades();
                    }
                }
            } catch (SQLException | NumberFormatException ex) {
                JOptionPane.showMessageDialog(this, "Error editing grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a grade to edit.");
        }
    }

    // Delete Grade
    else if (e.getSource() == deleteGradeButton) {
        int selectedRow = gradesTable.getSelectedRow();
        if (selectedRow >= 0) {
            int confirm = JOptionPane.showConfirmDialog(this, "Are you sure you want to delete this grade?", "Confirm Delete", JOptionPane.YES_NO_OPTION);
            if (confirm == JOptionPane.YES_OPTION) {
                try {
                    // Get grade details
                    int userId = (int) gradesTable.getValueAt(selectedRow, 0);
                    String courseName = (String) gradesTable.getValueAt(selectedRow, 1);

                    // Delete the grade
                    String query = "DELETE FROM grades WHERE user_id = ? AND course_name = ?";
                    try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                        stmt.setInt(1, userId);
                        stmt.setString(2, courseName);
                        stmt.executeUpdate();
                        JOptionPane.showMessageDialog(this, "Grade deleted successfully!");
                        loadGrades();
                    }
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error deleting grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a grade to delete.");
        }
    }

    // Add Notification
    else if (e.getSource() == addNotificationButton) {
        String message = JOptionPane.showInputDialog(this, "Enter Notification Message:");
        if (message != null && !message.trim().isEmpty()) {
            try {
                String query = "INSERT INTO notifications (message) VALUES (?)";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setString(1, message);
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "Notification added successfully!");
                    loadNotifications();
                }
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error adding notification: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }
}
}

import javax.swing.*;
import javax.swing.border.TitledBorder;
import javax.swing.table.DefaultTableModel;
import java.sql.ResultSetMetaData;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.Vector;

public class AdminGui extends BaseFrame implements ActionListener {

    private JTable usersTable, coursesTable, gradesTable, notificationsTable;
    private JButton addUserButton, editUserButton, deleteUserButton;
    private JButton addCourseButton, editCourseButton, deleteCourseButton;
    private JButton addGradeButton, editGradeButton, deleteGradeButton;
    private JButton addNotificationButton, deleteNotificationButton;
    

    public AdminGui() {
        super("Admin Dashboard");
        try {
            initializeComponents(); // Ensure components are initialized first
            this.setSize(1000, 800); // Adjusted size for additional table
            addGuiContent();
            this.setVisible(true);
        } catch (Exception e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this, "Error initializing GUI: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void initializeComponents() {
        System.out.println("Initializing components...");
        try {
            // Initialize tables
            usersTable = new JTable(new DefaultTableModel());
            coursesTable = new JTable(new DefaultTableModel());
            gradesTable = new JTable(new DefaultTableModel());
            notificationsTable = new JTable(new DefaultTableModel());

            // Initialize buttons for user management
            addUserButton = new JButton("Add User");
            editUserButton = new JButton("Edit User");
            deleteUserButton = new JButton("Delete User");

            // Initialize buttons for course management
            addCourseButton = new JButton("Add Course");
            editCourseButton = new JButton("Edit Course");
            deleteCourseButton = new JButton("Delete Course");

            // Initialize buttons for grade management
            addGradeButton = new JButton("Add Grade");
            editGradeButton = new JButton("Edit Grade");
            deleteGradeButton = new JButton("Delete Grade");

            // Initialize buttons for notifications management
            addNotificationButton = new JButton("Add Notification");
            deleteNotificationButton = new JButton("Delete Notification");

            // Add action listeners
            addUserButton.addActionListener(this);
            editUserButton.addActionListener(this);
            deleteUserButton.addActionListener(this);

            addCourseButton.addActionListener(this);
            editCourseButton.addActionListener(this);
            deleteCourseButton.addActionListener(this);

            addGradeButton.addActionListener(this);
            editGradeButton.addActionListener(this);
            deleteGradeButton.addActionListener(this);

            addNotificationButton.addActionListener(this);
            deleteNotificationButton.addActionListener(this);

            System.out.println("Components initialized successfully.");
        } catch (Exception e) {
            System.err.println("Component initialization failed: " + e.getMessage());
            throw e; // Let the exception propagate
        }
    }

    @Override
    protected void addGuiContent() {
        System.out.println("Adding GUI content...");
        JPanel mainPanel = (JPanel) this.getContentPane();
        mainPanel.setLayout(new BorderLayout()); // Use BorderLayout to manage content more efficiently

        // Create a panel for the sections
        JPanel sectionPanel = new JPanel();
        sectionPanel.setLayout(new GridLayout(2, 2, 10, 10)); // 2x2 Grid for tables and buttons sections

        // Create sections
        JPanel userPanel = createSectionPanel(usersTable, "Users", addUserButton, editUserButton, deleteUserButton);
        JPanel coursePanel = createSectionPanel(coursesTable, "Courses", addCourseButton, editCourseButton, deleteCourseButton);
        JPanel gradePanel = createSectionPanel(gradesTable, "Grades", addGradeButton, editGradeButton, deleteGradeButton);
        JPanel notificationPanel = createSectionPanel(notificationsTable, "Notifications", addNotificationButton, null, deleteNotificationButton);

        // Add panels to sectionPanel
        sectionPanel.add(userPanel);
        sectionPanel.add(coursePanel);
        sectionPanel.add(gradePanel);
        sectionPanel.add(notificationPanel);

        // Add sectionPanel to mainPanel with a center layout to remove empty space at top
        mainPanel.add(sectionPanel, BorderLayout.CENTER);

        // Load data for tables
        loadUsers();
        loadCourses();
        loadGrades();
        loadNotifications();
    }

    private JPanel createSectionPanel(JTable table, String title, JButton addButton, JButton editButton, JButton deleteButton) {
        if (table == null || addButton == null || deleteButton == null) {
            System.err.println("Error: Table or buttons are not initialized for section: " + title);
            return new JPanel(); // Return an empty panel to avoid crashing
        }

        JPanel panel = new JPanel(new BorderLayout());
        JScrollPane scrollPane = new JScrollPane(table);
        scrollPane.setBorder(new TitledBorder(title));

        JPanel buttonsPanel = new JPanel(new GridLayout(3, 1, 10, 10));
        buttonsPanel.add(addButton);
        if (editButton != null) {
            buttonsPanel.add(editButton);
        }
        buttonsPanel.add(deleteButton);

        panel.add(scrollPane, BorderLayout.CENTER);
        panel.add(buttonsPanel, BorderLayout.EAST);
        return panel;
    }

    private void loadUsers() {
        if (usersTable == null) {
            System.err.println("Error: usersTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getUsers()) {
            usersTable.setModel(buildTableModel(rs));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading users: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadCourses() {
        if (coursesTable == null) {
            System.err.println("Error: coursesTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getCourses()) {
            coursesTable.setModel(buildTableModel(rs));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading courses: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadGrades() {
        if (gradesTable == null) {
            System.err.println("Error: gradesTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getGradesForAdmin()) {

            // Initialize a Vector to hold data rows and column names for the table
            Vector<Vector<Object>> data = new Vector<>();
            Vector<String> columnNames = new Vector<>();

            // Define column headers
            columnNames.add("Course Name");
            columnNames.add("Assignment Score");
            columnNames.add("Quiz Score");
            columnNames.add("Exam Score");
            columnNames.add("Total Percentage");
            columnNames.add("Grade");

            // Process the result set to add the data to the table
            while (rs.next()) {
                Vector<Object> row = new Vector<>();
                double assignment = rs.getDouble("assignment_score");
                double quiz = rs.getDouble("quiz_score");
                double exam = rs.getDouble("exam_score");

                // Calculate the percentage
                double percentage = db.calculatePercentage(assignment, quiz, exam);
                String finalPercentage = String.format("%.2f%%", percentage); // Formatting the percentage

                // Get the corresponding grade based on the percentage
                String finalGrade = db.getGrade(percentage);

                // Add data to the row
                row.add(rs.getString("course_name"));
                row.add(assignment);
                row.add(quiz);
                row.add(exam);
                row.add(finalPercentage);
                row.add(finalGrade);

                // Add row to data
                data.add(row);
            }

            // Set the table model with the data and column names
            gradesTable.setModel(new DefaultTableModel(data, columnNames));

        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading grades: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadNotifications() {
        if (notificationsTable == null) {
            System.err.println("Error: notificationsTable is null!");
            return;
        }
        try (DatabaseConnection db = new DatabaseConnection();
             ResultSet rs = db.getNotificationsForAdmin()) {
            notificationsTable.setModel(buildTableModel(rs));
        } catch (SQLException e) {
            JOptionPane.showMessageDialog(this, "Error loading notifications: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private DefaultTableModel buildTableModel(ResultSet rs) throws SQLException {
        if (rs == null) {
            throw new IllegalArgumentException("ResultSet is null.");
        }

        Vector<String> columnNames = new Vector<>();
        Vector<Vector<Object>> data = new Vector<>();
        ResultSetMetaData metaData = rs.getMetaData();

        // Column names
        for (int column = 1; column <= metaData.getColumnCount(); column++) {
            columnNames.add(metaData.getColumnName(column));
        }

        // Data rows
        while (rs.next()) {
            Vector<Object> row = new Vector<>();
            for (int columnIndex = 1; columnIndex <= metaData.getColumnCount(); columnIndex++) {
                row.add(rs.getObject(columnIndex));
            }
            data.add(row);
        }

        return new DefaultTableModel(data, columnNames);
    }

    @Override
public void actionPerformed(ActionEvent e) {
    DatabaseConnection db = new DatabaseConnection();

    // Add User
    if (e.getSource() == addUserButton) {
        String username = JOptionPane.showInputDialog(this, "Enter username:");
        String password = JOptionPane.showInputDialog(this, "Enter password:");
        String role = JOptionPane.showInputDialog(this, "Enter role (Student, Teacher, Admin):");

        if (username != null && password != null && role != null) {
            String query = "INSERT INTO users (username, password, role) VALUES (?, ?, ?)";
            try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                stmt.setString(1, username);
                stmt.setString(2, password);
                stmt.setString(3, role);
                stmt.executeUpdate();
                JOptionPane.showMessageDialog(this, "User added successfully!");
                loadUsers(); // Reload the users table
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error adding user: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    } 

    // Edit User
    else if (e.getSource() == editUserButton) {
        int row = usersTable.getSelectedRow();
        if (row >= 0) {
            String username = (String) usersTable.getValueAt(row, 1);
            String password = (String) usersTable.getValueAt(row, 2);
            String role = (String) usersTable.getValueAt(row, 3);

            username = JOptionPane.showInputDialog(this, "Edit username:", username);
            password = JOptionPane.showInputDialog(this, "Edit password:", password);
            role = JOptionPane.showInputDialog(this, "Edit role:", role);

            if (username != null && password != null && role != null) {
                String query = "UPDATE users SET username = ?, password = ?, role = ? WHERE user_id = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setString(1, username);
                    stmt.setString(2, password);
                    stmt.setString(3, role);
                    stmt.setInt(4, (int) usersTable.getValueAt(row, 0));
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "User updated successfully!");
                    loadUsers(); // Reload the users table
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error editing user: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a user to edit.");
        }
    }

    // Delete User
    else if (e.getSource() == deleteUserButton) {
        int row = usersTable.getSelectedRow();
        if (row >= 0) {
            int userId = (int) usersTable.getValueAt(row, 0);
            int confirm = JOptionPane.showConfirmDialog(this, "Are you sure you want to delete this user?", "Confirm Delete", JOptionPane.YES_NO_OPTION);
            if (confirm == JOptionPane.YES_OPTION) {
                String query = "DELETE FROM users WHERE user_id = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setInt(1, userId);
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "User deleted successfully!");
                    loadUsers(); // Reload the users table
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error deleting user: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a user to delete.");
        }
    }

    // Add Course
    else if (e.getSource() == addCourseButton) {
        String courseName = JOptionPane.showInputDialog(this, "Enter course name:");
        String courseCode = JOptionPane.showInputDialog(this, "Enter course code:");

        if (courseName != null && courseCode != null) {
            String query = "INSERT INTO courses (course_name, course_code) VALUES (?, ?)";
            try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                stmt.setString(1, courseName);
                stmt.setString(2, courseCode);
                stmt.executeUpdate();
                JOptionPane.showMessageDialog(this, "Course added successfully!");
                loadCourses(); // Reload the courses table
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error adding course: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    // Edit Course
    else if (e.getSource() == editCourseButton) {
        int row = coursesTable.getSelectedRow();
        if (row >= 0) {
            String courseName = (String) coursesTable.getValueAt(row, 1);
            String courseCode = (String) coursesTable.getValueAt(row, 2);

            courseName = JOptionPane.showInputDialog(this, "Edit course name:", courseName);
            courseCode = JOptionPane.showInputDialog(this, "Edit course code:", courseCode);

            if (courseName != null && courseCode != null) {
                String query = "UPDATE courses SET course_name = ?, course_code = ? WHERE course_id = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setString(1, courseName);
                    stmt.setString(2, courseCode);
                    stmt.setInt(3, (int) coursesTable.getValueAt(row, 0));
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "Course updated successfully!");
                    loadCourses(); // Reload the courses table
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error editing course: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a course to edit.");
        }
    }

    // Delete Course
    else if (e.getSource() == deleteCourseButton) {
        int row = coursesTable.getSelectedRow();
        if (row >= 0) {
            int courseId = (int) coursesTable.getValueAt(row, 0);
            int confirm = JOptionPane.showConfirmDialog(this, "Are you sure you want to delete this course?", "Confirm Delete", JOptionPane.YES_NO_OPTION);
            if (confirm == JOptionPane.YES_OPTION) {
                String query = "DELETE FROM courses WHERE course_id = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setInt(1, courseId);
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "Course deleted successfully!");
                    loadCourses(); // Reload the courses table
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error deleting course: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a course to delete.");
        }
    }

    else if (e.getSource() == addGradeButton) {
        // Get input from user
        String assignmentScoreStr = JOptionPane.showInputDialog(this, "Enter assignment score:");
        String quizScoreStr = JOptionPane.showInputDialog(this, "Enter quiz score:");
        String examScoreStr = JOptionPane.showInputDialog(this, "Enter exam score:");
    
        if (assignmentScoreStr != null && quizScoreStr != null && examScoreStr != null) {
            try {
                // Parse input values to double
                double assignmentScore = Double.parseDouble(assignmentScoreStr);
                double quizScore = Double.parseDouble(quizScoreStr);
                double examScore = Double.parseDouble(examScoreStr);
    
                // Calculate percentage and grade
                double percentage = db.calculatePercentage(assignmentScore, quizScore, examScore);
                String finalGrade = db.getGrade(percentage); // Get grade letter
    
                // Assuming user_id and course_id need to be selected from UI (you need to retrieve them from your data)
                int userId = 1; // Replace with actual logic to retrieve user_id
                int courseId = 1; // Replace with actual logic to retrieve course_id
    
                // SQL query to insert grade
                String query = "INSERT INTO grades (user_id, course_id, assignment_score, quiz_score, exam_score, final_grade) VALUES (?, ?, ?, ?, ?, ?)";
    
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    // Set parameters in the prepared statement
                    stmt.setInt(1, userId); // User ID (replace with actual value)
                    stmt.setInt(2, courseId); // Course ID (replace with actual value)
                    stmt.setDouble(3, assignmentScore);
                    stmt.setDouble(4, quizScore);
                    stmt.setDouble(5, examScore);
                    stmt.setString(6, finalGrade);
    
                    // Execute the update
                    stmt.executeUpdate();
                    JOptionPane.showMessageDialog(this, "Grade added successfully!");
                    loadGrades(); // Reload the grades table
                } catch (SQLException ex) {
                    JOptionPane.showMessageDialog(this, "Error adding grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
                }
            } catch (NumberFormatException ex) {
                JOptionPane.showMessageDialog(this, "Please enter valid numbers for the scores.", "Invalid Input", JOptionPane.ERROR_MESSAGE);
            }
        }
    }
    

    // Edit Grade
else if (e.getSource() == editGradeButton) {
    int row = gradesTable.getSelectedRow();
    if (row >= 0) {
        try {
            // Retrieve selected grade details from the table
            int userId = (int) gradesTable.getValueAt(row, 0);
            String courseName = (String) gradesTable.getValueAt(row, 1);
            double assignmentScore = (double) gradesTable.getValueAt(row, 2);
            double quizScore = (double) gradesTable.getValueAt(row, 3);
            double examScore = (double) gradesTable.getValueAt(row, 4);

            // Prompt the teacher to edit the scores
            String newAssignmentScore = JOptionPane.showInputDialog(this, "Edit Assignment Score:", assignmentScore);
            String newQuizScore = JOptionPane.showInputDialog(this, "Edit Quiz Score:", quizScore);
            String newExamScore = JOptionPane.showInputDialog(this, "Edit Exam Score:", examScore);

            // Validate the input and update the grade
            if (newAssignmentScore != null && newQuizScore != null && newExamScore != null) {
                double updatedAssignmentScore = Double.parseDouble(newAssignmentScore);
                double updatedQuizScore = Double.parseDouble(newQuizScore);
                double updatedExamScore = Double.parseDouble(newExamScore);

                // Calculate new percentage and final grade
                double percentage = db.calculatePercentage(updatedAssignmentScore, updatedQuizScore, updatedExamScore);
                String finalGrade = db.getGrade(percentage);

                // Update the grade in the database
                String query = "UPDATE grades SET assignment_score = ?, quiz_score = ?, exam_score = ?, final_grade = ? WHERE user_id = ? AND course_name = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setDouble(1, updatedAssignmentScore);
                    stmt.setDouble(2, updatedQuizScore);
                    stmt.setDouble(3, updatedExamScore);
                    stmt.setString(4, finalGrade);
                    stmt.setInt(5, userId);
                    stmt.setString(6, courseName);
                    stmt.executeUpdate();

                    JOptionPane.showMessageDialog(this, "Grade updated successfully!");
                    loadGrades(); // Refresh the grades table
                }
            }
        } catch (SQLException | NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Error editing grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    } else {
        JOptionPane.showMessageDialog(this, "Please select a grade to edit.");
    }
}

// Delete Grade
else if (e.getSource() == deleteGradeButton) {
    int row = gradesTable.getSelectedRow();
    if (row >= 0) {
        int confirm = JOptionPane.showConfirmDialog(this, "Are you sure you want to delete this grade?", "Confirm Delete", JOptionPane.YES_NO_OPTION);
        if (confirm == JOptionPane.YES_OPTION) {
            try {
                // Retrieve selected grade details from the table
                int userId = (int) gradesTable.getValueAt(row, 0);
                String courseName = (String) gradesTable.getValueAt(row, 1);

                // Delete the grade from the database
                String query = "DELETE FROM grades WHERE user_id = ? AND course_name = ?";
                try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                    stmt.setInt(1, userId);
                    stmt.setString(2, courseName);
                    stmt.executeUpdate();

                    JOptionPane.showMessageDialog(this, "Grade deleted successfully!");
                    loadGrades(); // Refresh the grades table
                }
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error deleting grade: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    } else {
        JOptionPane.showMessageDialog(this, "Please select a grade to delete.");
    }
}


    // Add Notification
    else if (e.getSource() == addNotificationButton) {
        String message = JOptionPane.showInputDialog(this, "Enter notification message:");
        if (message != null && !message.trim().isEmpty()) {
            String query = "INSERT INTO notifications (message) VALUES (?)";
            try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                stmt.setString(1, message);
                stmt.executeUpdate();
                JOptionPane.showMessageDialog(this, "Notification added successfully!");
                loadNotifications(); // Reload the notifications table
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error adding notification: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    // Delete Notification
    else if (e.getSource() == deleteNotificationButton) {
        int row = notificationsTable.getSelectedRow();
        if (row >= 0) {
            int notificationId = (int) notificationsTable.getValueAt(row, 0);
            String query = "DELETE FROM notifications WHERE notification_id = ?";
            try (PreparedStatement stmt = db.getConnection().prepareStatement(query)) {
                stmt.setInt(1, notificationId);
                stmt.executeUpdate();
                JOptionPane.showMessageDialog(this, "Notification deleted successfully!");
                loadNotifications(); // Reload the notifications table
            } catch (SQLException ex) {
                JOptionPane.showMessageDialog(this, "Error deleting notification: " + ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        } else {
            JOptionPane.showMessageDialog(this, "Please select a notification to delete.");
        }
    }
}
}
