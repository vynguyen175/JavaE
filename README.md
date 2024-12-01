package com.example.demo3;

import java.io.*;
import java.util.ArrayList;

public class DataStorage {
    private static final String EMPLOYEE_FILE = "employees.ser";
    private static final String PAYROLL_FILE = "payrolls.ser";

    // Save employee data to a file
    public static void saveEmployees(ArrayList<Employee> employees) {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(EMPLOYEE_FILE))) {
            oos.writeObject(employees);
            System.out.println("Employee data saved successfully.");
        } catch (IOException e) {
            System.err.println("Error saving employee data: " + e.getMessage());
        }
    }

    // Load employee data from a file
    public static ArrayList<Employee> loadEmployees() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(EMPLOYEE_FILE))) {
            return (ArrayList<Employee>) ois.readObject();
        } catch (IOException | ClassNotFoundException e) {
            System.err.println("Error loading employee data: " + e.getMessage());
            return new ArrayList<>();  // Return empty list in case of error
        }
    }

    // Save payroll data to a file
    public static void savePayrolls(ArrayList<Payroll> payrolls) {
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(PAYROLL_FILE))) {
            oos.writeObject(payrolls);
            System.out.println("Payroll data saved successfully.");
        } catch (IOException e) {
            System.err.println("Error saving payroll data: " + e.getMessage());
        }
    }

    // Load payroll data from a file
    public static ArrayList<Payroll> loadPayrolls() {
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(PAYROLL_FILE))) {
            return (ArrayList<Payroll>) ois.readObject();
        } catch (IOException | ClassNotFoundException e) {
            System.err.println("Error loading payroll data: " + e.getMessage());
            return new ArrayList<>();  // Return empty list in case of error
        }
    }
}
package com.example.demo3;

import java.io.FileWriter;
import java.io.IOException;
import java.io.PrintWriter;
import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class Department implements Serializable {
    private static ArrayList<Employee> employees = DataStorage.loadEmployees();
    private static ArrayList<Payroll> payrolls = DataStorage.loadPayrolls();
    private static final Scanner scanner = new Scanner(System.in);

    // Employee Manager Method
    public static void employeeManager() {
        while (true) {
            System.out.println("\nEmployee Management System");
            System.out.println("1. Add Employee");
            System.out.println("2. View Employees");
            System.out.println("3. Update Employee");
            System.out.println("4. Delete Employee");
            System.out.println("5. Manage Payrolls");
            System.out.println("6. Generate Report");
            System.out.println("7. Save and Exit");
            System.out.print("Choose an option: ");

            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline character

            switch (choice) {
                case 1 -> addEmployee();
                case 2 -> viewEmployees();
                case 3 -> updateEmployee();
                case 4 -> deleteEmployee();
                case 5 -> payrollManager();
                case 6 -> generateReport(); // Option for report generation
                case 7 -> {
                    saveData();
                    return;
                }
                default -> System.out.println("Invalid option. Please try again.");
            }
        }
    }

    // Add Employee
    private static void addEmployee() {
        System.out.println("Enter Employee ID: ");
        int id = scanner.nextInt();
        scanner.nextLine(); // Consume newline character
        System.out.println("Enter Employee's First Name: ");
        String firstName = scanner.nextLine();
        System.out.println("Enter Employee's Last Name: ");
        String lastName = scanner.nextLine();
        System.out.println("Enter Employee's Department: ");
        String department = scanner.nextLine();
        System.out.println("Enter Employee's Hourly Rate: ");
        double hourlyRate = scanner.nextDouble();

        Employee employee = new Employee(id, firstName, lastName, department, hourlyRate);
        employees.add(employee);
        System.out.println("Employee added successfully.");
    }

    // View Employees
    private static void viewEmployees() {
        System.out.println("\nEmployee List:");
        if (employees.isEmpty()) {
            System.out.println("No employees found.");
        } else {
            for (Employee employee : employees) {
                System.out.println(employee);
            }
        }
    }

    // Update Employee
    private static void updateEmployee() {
        System.out.println("Enter Employee ID to update: ");
        int id = scanner.nextInt();
        scanner.nextLine(); // Consume newline character

        for (Employee employee : employees) {
            if (employee.getId() == id) {
                System.out.println("Updating Employee: " + employee.getFname() + " " + employee.getLname());
                System.out.println("Enter new first name (or press Enter to skip): ");
                String firstName = scanner.nextLine();
                if (!firstName.isEmpty()) employee.setFname(firstName);
                System.out.println("Enter new last name (or press Enter to skip): ");
                String lastName = scanner.nextLine();
                if (!lastName.isEmpty()) employee.setLname(lastName);
                System.out.println("Enter new department (or press Enter to skip): ");
                String department = scanner.nextLine();
                if (!department.isEmpty()) employee.setDepartment(department);
                System.out.println("Enter new hourly rate (or press Enter to skip): ");
                String hourlyRateStr = scanner.nextLine();
                if (!hourlyRateStr.isEmpty()) {
                    double hourlyRate = Double.parseDouble(hourlyRateStr);
                    employee.setHoursRate(hourlyRate);
                }
                System.out.println("Employee updated successfully.");
                return;
            }
        }
        System.out.println("Employee not found.");
    }

    // Delete Employee
    private static void deleteEmployee() {
        System.out.println("Enter Employee ID to delete: ");
        int id = scanner.nextInt();
        scanner.nextLine(); // Consume newline character

        Employee employeeToRemove = null;
        for (Employee employee : employees) {
            if (employee.getId() == id) {
                employeeToRemove = employee;
                break;
            }
        }

        if (employeeToRemove != null) {
            employees.remove(employeeToRemove);
            System.out.println("Employee removed successfully.");
        } else {
            System.out.println("Employee not found.");
        }
    }

    // Payroll Manager
    private static void payrollManager() {
        System.out.println("\nPayroll Management");
        System.out.println("1. Add Payroll");
        System.out.println("2. View Payrolls");
        System.out.println("3. Calculate Net Pay for All Employees");
        System.out.print("Choose an option: ");

        int choice = scanner.nextInt();
        scanner.nextLine();

        switch (choice) {
            case 1 -> addPayroll();
            case 2 -> viewPayrolls();
            case 3 -> calculateNetPayForAll();
            default -> System.out.println("Invalid option. Please try again.");
        }
    }

    // Add Payroll
    private static void addPayroll() {
        System.out.println("Enter Employee ID for payroll: ");
        int id = scanner.nextInt();
        scanner.nextLine();

        for (Employee employee : employees) {
            if (employee.getId() == id) {
                System.out.println("Enter worked hours: ");
                double workedHours = scanner.nextDouble();
                System.out.println("Enter overtime hours: ");
                double overtimeHours = scanner.nextDouble();
                System.out.println("Enter bonuses: ");
                double bonuses = scanner.nextDouble();
                System.out.println("Enter deductions: ");
                double deductions = scanner.nextDouble();

                Payroll payroll = new Payroll(employee, workedHours, overtimeHours, bonuses, deductions);
                payroll.calculateNetPay();
                payrolls.add(payroll);

                System.out.println("Payroll added successfully.");
                return;
            }
        }
        System.out.println("Employee not found.");
    }

    // View Payrolls
    private static void viewPayrolls() {
        System.out.println("\nPayroll List:");
        if (payrolls.isEmpty()) {
            System.out.println("No payroll data found.");
        } else {
            for (Payroll payroll : payrolls) {
                System.out.println(payroll);
            }
        }
    }

    // Calculate Net Pay for All Employees
    private static void calculateNetPayForAll() {
        for (Payroll payroll : payrolls) {
            payroll.calculateNetPay();
        }
        System.out.println("Net pay calculated for all employees.");
    }

    // Generate Report
    private static void generateReport() {
        System.out.println("Generating report...");

        try (FileWriter fileWriter = new FileWriter("employee_report.txt");
             PrintWriter printWriter = new PrintWriter(fileWriter)) {

            // Write the report header
            printWriter.println("Employee Report");
            printWriter.println("====================================");

            // Write employee details
            if (employees.isEmpty()) {
                printWriter.println("No employees found.");
            } else {
                for (Employee employee : employees) {
                    printWriter.println("Employee ID: " + employee.getId());
                    printWriter.println("First Name: " + employee.getFname());
                    printWriter.println("Last Name: " + employee.getLname());
                    printWriter.println("Department: " + employee.getDepartment());
                    printWriter.println("Hourly Rate: $" + employee.getHoursRate());
                    printWriter.println("------------------------------------");

                    // Find corresponding payroll details
                    boolean foundPayroll = false;
                    for (Payroll payroll : payrolls) {
                        if (payroll.getEmployee().getId() == employee.getId()) {
                            foundPayroll = true;
                            printWriter.println("Worked Hours: " + payroll.getWorkedHours());
                            printWriter.println("Overtime Hours: " + payroll.getOvertimeHours());
                            printWriter.println("Bonuses: $" + payroll.getBonuses());
                            printWriter.println("Deductions: $" + payroll.getDeduction());
                            printWriter.println("Net Pay: $" + payroll.getNetPay());
                            printWriter.println("------------------------------------");
                            break;
                        }
                    }

                    if (!foundPayroll) {
                        printWriter.println("No payroll data found.");
                        printWriter.println("------------------------------------");
                    }
                }
            }

            System.out.println("Report generated and saved as 'employee_report.txt'.");

        } catch (IOException e) {
            System.out.println("Error generating report: " + e.getMessage());
        }
    }

    // Save employee and payroll data
    private static void saveData() {
        DataStorage.saveEmployees(employees);
        DataStorage.savePayrolls(payrolls);
    }
}
package com.example.demo3;

import java.io.Serializable;
import java.time.LocalDate;

public class Employee implements Serializable {
    private int id;
    private String fname;
    private String lname;
    private double hoursRate;
    private String department;
    private boolean isOnLeave;
    private int totalLeaveDays;
    private int totalAttendanceDays;

    // Constructors, getters, setters...
    public void logAttendance() {
        totalAttendanceDays++;
    }

    public void requestLeave(int days) {
        totalLeaveDays += days;
    }

    public Employee(int id, String fname, String lname, String department, double hoursRate) {
        this.id = id;
        this.fname = fname;
        this.lname = lname;
        this.department = department;
        this.hoursRate = hoursRate;
        this.isOnLeave = false; // Default value
    }

    public int getId() { return id; }
    public void setId(int id) { this.id = id; }

    public String getFname() { return fname; }
    public void setFname(String fname) { this.fname = fname; }

    public String getLname() { return lname; }
    public void setLname(String lname) { this.lname = lname; }

    public double getHoursRate() { return hoursRate; }
    public void setHoursRate(double hoursRate) { this.hoursRate = hoursRate; }

    public String getDepartment() { return department; }
    public void setDepartment(String department) { this.department = department; }


    @Override
    public String toString() {
        return String.format("------Employee Information-----\nEmployee ID: %d\nFirst Name: %s\nLast Name: %s\nDepartment: %s\nHourly Rate: %.2f",
                this.id, this.fname, this.lname, this.department, this.hoursRate);
    }
}
package com.example.demo3;

import javafx.animation.*;
import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.geometry.Pos;
import javafx.scene.Scene;
import javafx.scene.control.*;
import javafx.scene.layout.*;
import javafx.scene.paint.Color;
import javafx.scene.shape.*;
import javafx.scene.text.Font;
import javafx.scene.text.FontWeight;
import javafx.stage.Stage;
import javafx.util.Duration;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

public class HRMApplication extends Application {

    private List<Employee> employees = new ArrayList<>();
    private List<Payroll> payrolls = new ArrayList<>();
    private UserManager userManager = new UserManager();
    public static void main(String[] args) {
        launch(args);
    }


    @Override
    public void start(Stage primaryStage) {
        showLoginScreen(primaryStage);

        BorderPane root = new BorderPane();



        // Title and Layout
        Label titleLabel = new Label("HR Management System");
        titleLabel.setFont(Font.font("Arial", FontWeight.BOLD, 24));
        titleLabel.setStyle("-fx-text-fill: #6200EE;");
        titleLabel.setAlignment(Pos.CENTER);

        TabPane tabPane = new TabPane();
        Tab employeeTab = new Tab("Employees");
        Tab payrollTab = new Tab("Payroll");


        // Employee Management Layout
        VBox employeeLayout = new VBox(10);
        employeeLayout.setPadding(new Insets(20));
        Button addEmployeeButton = createButton("Add Employee", "add.png");
        Button updateEmployeeButton = createButton("Update Employee", "update.png");
        Button deleteEmployeeButton = createButton("Delete Employee", "delete.png");
        Button viewEmployeeButton = createButton("View Employees", "view.png");

        addEmployeeButton.setOnAction(e -> showAddEmployeeDialog());
        updateEmployeeButton.setOnAction(e -> updateEmployeeDialog());
        deleteEmployeeButton.setOnAction(e -> deleteEmployeeDialog());
        viewEmployeeButton.setOnAction(e -> showEmployeeList());

        employeeLayout.getChildren().addAll(addEmployeeButton, updateEmployeeButton, deleteEmployeeButton, viewEmployeeButton);
        employeeTab.setContent(employeeLayout);

        // Payroll Management Layout
        VBox payrollLayout = new VBox(10);
        payrollLayout.setPadding(new Insets(20));
        Button generatePayrollButton = createButton("Generate Payroll", "generate.png");
        Button viewPayrollButton = createButton("View Payrolls", "view.png");
        Button deletePayrollButton = createButton("Delete Payroll", "delete.png");

        generatePayrollButton.setOnAction(e -> showPayrollAddDialog());
        viewPayrollButton.setOnAction(e -> showPayrollList());
        deletePayrollButton.setOnAction(e -> deletePayrollDialog());

        payrollLayout.getChildren().addAll(generatePayrollButton, viewPayrollButton, deletePayrollButton);
        payrollTab.setContent(payrollLayout);

        tabPane.getTabs().addAll(employeeTab, payrollTab);

        HBox footer = new HBox(10);
        footer.setPadding(new Insets(10));
        footer.setAlignment(Pos.CENTER);
        footer.setStyle("-fx-background-color: #f0f0f0;");

        Button logAttendanceButton = createButton("Log Attendance", "attendance.png");
        Button requestLeaveButton = createButton("Request Leave", "leave.png");

        logAttendanceButton.setOnAction(e -> logAttendanceDialog());
        requestLeaveButton.setOnAction(e -> requestLeaveDialog());

        employeeLayout.getChildren().addAll(logAttendanceButton, requestLeaveButton);

        Button saveEmployeesButton = createButton("Save Employees", "save.png");
        Button savePayrollsButton = createButton("Save Payrolls", "save.png");

        saveEmployeesButton.setOnAction(e -> DataStorage.saveEmployees(new ArrayList<>(employees)));
        savePayrollsButton.setOnAction(e -> DataStorage.savePayrolls(new ArrayList<>(payrolls)));

        footer.getChildren().addAll(saveEmployeesButton, savePayrollsButton);

        root.setTop(titleLabel);
        root.setCenter(tabPane);
        root.setBottom(footer);

        // Add Moving Shapes
        addMovingShapes(root);

        Scene scene = new Scene(root, 600, 500);
        scene.getStylesheets().add(Objects.requireNonNull(getClass().getResource("style.css")).toExternalForm());
        primaryStage.setScene(scene);
        primaryStage.setTitle("HR Management System");
        primaryStage.show();
    }
    private void showLoginScreen(Stage primaryStage) {
        // Login screen setup
        VBox loginLayout = new VBox(10);
        loginLayout.setPadding(new Insets(20));
        loginLayout.setAlignment(Pos.CENTER);

        TextField usernameField = new TextField();
        usernameField.setPromptText("Username");

        PasswordField passwordField = new PasswordField();
        passwordField.setPromptText("Password");

        Button loginButton = new Button("Login");
        Button registerButton = new Button("Register");

        loginButton.setOnAction(e -> handleLogin(usernameField, passwordField, primaryStage));
        registerButton.setOnAction(e -> handleRegister(usernameField, passwordField));

        loginLayout.getChildren().addAll(usernameField, passwordField, loginButton, registerButton);

        Scene loginScene = new Scene(loginLayout, 300, 250);
        primaryStage.setScene(loginScene);
        primaryStage.setTitle("Login");
        primaryStage.show();
    }

    private void handleLogin(TextField usernameField, PasswordField passwordField, Stage primaryStage) {
        String username = usernameField.getText();
        String password = passwordField.getText();

        if (userManager.login(username, password)) {
            // If login is successful, proceed to the HR Management System
            showHRMInterface(primaryStage);
        } else {
            showErrorAlert("Login failed! Please check your credentials.");
        }
    }

    private void handleRegister(TextField usernameField, PasswordField passwordField) {
        String username = usernameField.getText();
        String password = passwordField.getText();

        // Validate input
        if (username.isEmpty() || password.isEmpty()) {
            showErrorAlert("Please fill in both username and password fields.");
            return;
        }

        boolean isRegistered = userManager.register(username, password);

        if (isRegistered) {
            showErrorAlert("Registration successful! You can now log in.");
        } else {
            showErrorAlert("Registration failed! Username may already exist.");
        }
    }


    private void showHRMInterface(Stage primaryStage) {
        // Your existing HRM application code here (showing employee and payroll tabs)
        BorderPane root = new BorderPane();
        Label titleLabel = new Label("HR Management System");
        titleLabel.setFont(Font.font("Arial", FontWeight.BOLD, 24));
        titleLabel.setStyle("-fx-text-fill: #6200EE;");
        titleLabel.setAlignment(Pos.CENTER);

        TabPane tabPane = new TabPane();
        Tab employeeTab = new Tab("Employees");
        Tab payrollTab = new Tab("Payroll");

        // Employee Management Layout
        VBox employeeLayout = new VBox(10);
        employeeLayout.setPadding(new Insets(20));
        Button addEmployeeButton = createButton("Add Employee", "add.png");
        Button updateEmployeeButton = createButton("Update Employee", "update.png");
        Button deleteEmployeeButton = createButton("Delete Employee", "delete.png");
        Button viewEmployeeButton = createButton("View Employees", "view.png");

        addEmployeeButton.setOnAction(e -> showAddEmployeeDialog());
        updateEmployeeButton.setOnAction(e -> updateEmployeeDialog());
        deleteEmployeeButton.setOnAction(e -> deleteEmployeeDialog());
        viewEmployeeButton.setOnAction(e -> showEmployeeList());

        employeeLayout.getChildren().addAll(addEmployeeButton, updateEmployeeButton, deleteEmployeeButton, viewEmployeeButton);
        employeeTab.setContent(employeeLayout);

        // Payroll Management Layout
        VBox payrollLayout = new VBox(10);
        payrollLayout.setPadding(new Insets(20));
        Button generatePayrollButton = createButton("Generate Payroll", "generate.png");
        Button viewPayrollButton = createButton("View Payrolls", "view.png");
        Button deletePayrollButton = createButton("Delete Payroll", "delete.png");

        generatePayrollButton.setOnAction(e -> showPayrollAddDialog());
        viewPayrollButton.setOnAction(e -> showPayrollList());
        deletePayrollButton.setOnAction(e -> deletePayrollDialog());

        payrollLayout.getChildren().addAll(generatePayrollButton, viewPayrollButton, deletePayrollButton);
        payrollTab.setContent(payrollLayout);

        tabPane.getTabs().addAll(employeeTab, payrollTab);

        HBox footer = new HBox(10);
        footer.setPadding(new Insets(10));
        footer.setAlignment(Pos.CENTER);
        footer.setStyle("-fx-background-color: #f0f0f0;");

        Button logAttendanceButton = createButton("Log Attendance", "attendance.png");
        Button requestLeaveButton = createButton("Request Leave", "leave.png");

        logAttendanceButton.setOnAction(e -> logAttendanceDialog());
        requestLeaveButton.setOnAction(e -> requestLeaveDialog());

        employeeLayout.getChildren().addAll(logAttendanceButton, requestLeaveButton);

        Button saveEmployeesButton = createButton("Save Employees", "save.png");
        Button savePayrollsButton = createButton("Save Payrolls", "save.png");

        saveEmployeesButton.setOnAction(e -> DataStorage.saveEmployees(new ArrayList<>(employees)));
        savePayrollsButton.setOnAction(e -> DataStorage.savePayrolls(new ArrayList<>(payrolls)));

        footer.getChildren().addAll(saveEmployeesButton, savePayrollsButton);

        root.setTop(titleLabel);
        root.setCenter(tabPane);
        root.setBottom(footer);

        Scene hrmScene = new Scene(root, 600, 500);
        primaryStage.setScene(hrmScene);
        primaryStage.setTitle("HR Management System");
        primaryStage.show();
    }



    private void addMovingShapes(BorderPane root) {
        // Create a Circle
        Circle circle = new Circle(20, Color.BLUE);
        circle.setCenterX(50);
        circle.setCenterY(100);
        root.getChildren().add(circle);

        // Create a Rectangle
        Rectangle rectangle = new Rectangle(50, 30, Color.RED);
        rectangle.setX(200);
        rectangle.setY(150);
        root.getChildren().add(rectangle);

        // Create a Line
        Line line = new Line(400, 200, 450, 250);
        line.setStroke(Color.GREEN);
        root.getChildren().add(line);

        // Animation for Circle (Move)
        TranslateTransition circleTransition = new TranslateTransition(Duration.seconds(2), circle);
        circleTransition.setToX(400);
        circleTransition.setToY(300);
        circleTransition.setCycleCount(Timeline.INDEFINITE);
        circleTransition.setAutoReverse(true);
        circleTransition.play();

        // Animation for Rectangle (Move)
        TranslateTransition rectTransition = new TranslateTransition(Duration.seconds(3), rectangle);
        rectTransition.setToX(450);
        rectTransition.setToY(50);
        rectTransition.setCycleCount(Timeline.INDEFINITE);
        rectTransition.setAutoReverse(true);
        rectTransition.play();

        // Animation for Line (Move)
        TranslateTransition lineTransition = new TranslateTransition(Duration.seconds(4), line);
        lineTransition.setToX(500);
        lineTransition.setToY(200);
        lineTransition.setCycleCount(Timeline.INDEFINITE);
        lineTransition.setAutoReverse(true);
        lineTransition.play();
    }


    private Button createButton(String text, String iconPath) {
        Button button = new Button(text);
        button.setFont(Font.font("Arial", FontWeight.NORMAL, 14));
        button.setStyle("-fx-background-color: #6200EE; -fx-text-fill: white; -fx-background-radius: 10;");
        button.setPrefWidth(180);


        button.setOnMouseEntered(e -> button.setStyle("-fx-background-color: #3700B3; -fx-text-fill: white; -fx-background-radius: 10;"));
        button.setOnMouseExited(e -> button.setStyle("-fx-background-color: #6200EE; -fx-text-fill: white; -fx-background-radius: 10;"));

        return button;
    }

    private void addButtonHoverAnimation(Button button) {
        ScaleTransition scaleTransition = new ScaleTransition(Duration.seconds(0.3), button);
        scaleTransition.setFromX(1);
        scaleTransition.setToX(1.1);
        scaleTransition.setFromY(1);
        scaleTransition.setToY(1.1);

        button.setOnMouseEntered(e -> scaleTransition.play());
        button.setOnMouseExited(e -> scaleTransition.stop());
    }

    private void showAddEmployeeDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Add Employee");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        TextField firstNameField = new TextField();
        firstNameField.setPromptText("First Name");

        TextField lastNameField = new TextField();
        lastNameField.setPromptText("Last Name");

        TextField departmentField = new TextField();
        departmentField.setPromptText("Department");

        TextField hourlyRateField = new TextField();
        hourlyRateField.setPromptText("Hourly Rate");

        Button addButton = new Button("Add");
        addButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                String firstName = firstNameField.getText();
                String lastName = lastNameField.getText();
                String department = departmentField.getText();
                double hourlyRate = Double.parseDouble(hourlyRateField.getText());

                employees.add(new Employee(id, firstName, lastName, department, hourlyRate));
                dialog.close();
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, firstNameField, lastNameField, departmentField, hourlyRateField, addButton);
        dialog.setScene(new Scene(layout, 300, 300));
        dialog.showAndWait();
    }

    private void updateEmployeeDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Update Employee");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        TextField firstNameField = new TextField();
        firstNameField.setPromptText("First Name");

        TextField lastNameField = new TextField();
        lastNameField.setPromptText("Last Name");

        TextField departmentField = new TextField();
        departmentField.setPromptText("Department");

        TextField hourlyRateField = new TextField();
        hourlyRateField.setPromptText("Hourly Rate");


        Button updateButton = new Button("Update");
        updateButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                String firstName = firstNameField.getText();
                String lastName = lastNameField.getText();
                String department = departmentField.getText();
                double hourlyRate = Double.parseDouble(hourlyRateField.getText());

                for (Employee employee : employees) {
                    if (employee.getId() == id) {
                        employee.setFname(firstName);
                        employee.setLname(lastName);
                        employee.setDepartment(department);
                        employee.setHoursRate(hourlyRate);
                        break;
                    }
                }

                dialog.close();
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, firstNameField, lastNameField, departmentField, hourlyRateField, updateButton);
        dialog.setScene(new Scene(layout, 300, 300));
        dialog.showAndWait();
    }

    private void deleteEmployeeDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Delete Employee");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        Button deleteButton = new Button("Delete");
        deleteButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                employees.removeIf(employee -> employee.getId() == id);
                dialog.close();
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, deleteButton);
        dialog.setScene(new Scene(layout, 300, 200));
        dialog.showAndWait();
    }

    private void showEmployeeList() {
        StringBuilder list = new StringBuilder();
        for (Employee employee : employees) {
            list.append(employee).append("\n");
        }

        Alert alert = new Alert(Alert.AlertType.INFORMATION);
        alert.setTitle("Employee List");
        alert.setHeaderText("Employees:");
        alert.setContentText(list.toString());
        alert.showAndWait();
    }
    private void showPayrollAddDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Add Payroll");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");
        idField.setMaxWidth(200);

        TextField hoursField = new TextField();
        hoursField.setPromptText("Worked Hours");
        hoursField.setMaxWidth(200);

        TextField overtimeField = new TextField();
        overtimeField.setPromptText("Overtime Hours");
        overtimeField.setMaxWidth(200);

        TextField bonusesField = new TextField();
        bonusesField.setPromptText("Bonuses");
        bonusesField.setMaxWidth(200);

        TextField deductionField = new TextField();
        deductionField.setPromptText("Deductions");
        deductionField.setMaxWidth(200);

        Button addButton = new Button("Generate Payroll");
        addButton.setMaxWidth(200);
        addButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                double workedHours = Double.parseDouble(hoursField.getText());
                double overtimeHours = Double.parseDouble(overtimeField.getText());
                double bonuses = Double.parseDouble(bonusesField.getText());
                double deductions = Double.parseDouble(deductionField.getText());

                for (Employee employee : employees) {
                    if (employee.getId() == id) {
                        double hourlyRate = employee.getHoursRate();
                        // Calculate net pay
                        double basePay = workedHours * hourlyRate;
                        double overtimePay = overtimeHours * hourlyRate * 1.5; // Assuming overtime is 1.5x
                        double netpay = basePay + overtimePay + bonuses - deductions;

                        Payroll payroll = new Payroll(employee, workedHours, overtimeHours, bonuses, deductions, netpay);
                        payrolls.add(payroll);
                        dialog.close();
                        return;
                    }
                }
                showErrorAlert("Employee not found.");
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, hoursField, overtimeField, bonusesField, deductionField, addButton);
        dialog.setScene(new Scene(layout, 300, 300));
        dialog.showAndWait();
    }


    private void showPayrollList() {
        Stage dialog = new Stage();
        dialog.setTitle("Payroll List");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.TOP_LEFT);

        for (Payroll payroll : payrolls) {
            layout.getChildren().add(new Label(payroll.toString()));
        }

        dialog.setScene(new Scene(layout, 400, 400));
        dialog.showAndWait();
    }

    private void deletePayrollDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Delete Payroll");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        Button deleteButton = new Button("Delete Payroll");
        deleteButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                payrolls.removeIf(payroll -> payroll.getEmployee().getId() == id);
                dialog.close();
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid ID!");
            }
        });

        layout.getChildren().addAll(idField, deleteButton);
        dialog.setScene(new Scene(layout, 300, 200));
        dialog.showAndWait();
    }

    private void showLoginDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("User Login");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField usernameField = new TextField();
        usernameField.setPromptText("Username");

        PasswordField passwordField = new PasswordField();
        passwordField.setPromptText("Password");

        Button loginButton = new Button("Login");
        loginButton.setOnAction(e -> {
            String username = usernameField.getText();
            String password = passwordField.getText();

            UserManager userManager = new UserManager();
            if (userManager.login(username, password)) {
                dialog.close();
            } else {
                showErrorAlert("Login failed! Please try again.");
            }
        });

        layout.getChildren().addAll(usernameField, passwordField, loginButton);
        dialog.setScene(new Scene(layout, 300, 200));
        dialog.showAndWait();
    }
    private void logAttendanceDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Log Attendance");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        Button logButton = new Button("Log");
        logButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                for (Employee employee : employees) {
                    if (employee.getId() == id) {
                        employee.logAttendance();
                        dialog.close();
                        return;
                    }
                }
                showErrorAlert("Employee not found.");
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, logButton);
        dialog.setScene(new Scene(layout, 300, 200));
        dialog.showAndWait();
    }

    private void requestLeaveDialog() {
        Stage dialog = new Stage();
        dialog.setTitle("Request Leave");

        VBox layout = new VBox(10);
        layout.setPadding(new Insets(20));
        layout.setAlignment(Pos.CENTER);

        TextField idField = new TextField();
        idField.setPromptText("Employee ID");

        TextField leaveDaysField = new TextField();
        leaveDaysField.setPromptText("Number of Leave Days");

        Button requestButton = new Button("Request");
        requestButton.setOnAction(e -> {
            try {
                int id = Integer.parseInt(idField.getText());
                int leaveDays = Integer.parseInt(leaveDaysField.getText());
                for (Employee employee : employees) {
                    if (employee.getId() == id) {
                        employee.requestLeave(leaveDays);
                        dialog.close();
                        return;
                    }
                }
                showErrorAlert("Employee not found.");
            } catch (NumberFormatException ex) {
                showErrorAlert("Invalid input! Please fill all fields correctly.");
            }
        });

        layout.getChildren().addAll(idField, leaveDaysField, requestButton);
        dialog.setScene(new Scene(layout, 300, 200));
        dialog.showAndWait();
    }


    private void showErrorAlert(String message) {
        Alert alert = new Alert(Alert.AlertType.ERROR);
        alert.setTitle("Error");
        alert.setContentText(message);
        alert.showAndWait();
    }
}
package com.example.demo3;

import java.io.Serializable;

public class Payroll implements Serializable {
    private Employee employee;
    private double workedHours;
    private double overtimeHours;
    private double bonuses;
    private double deduction;
    private double netPay;

    public Payroll(Employee employee, double workedHours, double overtimeHours, double bonuses, double deduction, double netPay) {
        this.employee = employee;
        this.workedHours = workedHours;
        this.overtimeHours = overtimeHours;
        this.bonuses = bonuses;
        this.deduction = deduction;
        this.netPay = netPay;
    }
    public Payroll(Employee employee, double workedHours, double overtimeHours, double bonuses, double deductions) {
        this.employee = employee;
        this.workedHours = workedHours;
        this.overtimeHours = overtimeHours;
        this.bonuses = bonuses;
        this.deduction = deductions;
        calculateNetPay();
    }


    public Employee getEmployee() {
        return employee;
    }

    public void setEmployee(Employee employee) {
        this.employee = employee;
    }

    public double getOvertimeHours() {
        return overtimeHours;
    }

    public void setOvertimeHours(double overtimeHours) {
        this.overtimeHours = overtimeHours;
    }

    public double getWorkedHours() {
        return workedHours;
    }

    public void setWorkedHours(double workedHours) {
        this.workedHours = workedHours;
    }

    public double getNetPay() {
        return netPay;
    }

    public void setNetPay(double netPay) {
        this.netPay = netPay;
    }

    public double getDeduction() {
        return deduction;
    }

    public void setDeduction(double deduction) {
        this.deduction = deduction;
    }

    public double getBonuses() {
        return bonuses;
    }

    public void setBonuses(double bonuses) {
        this.bonuses = bonuses;
    }

    public void calculateNetPay(){
        double hoursRate = employee.getHoursRate();
        double regularPay = hoursRate * workedHours;
        double overtimePay = overtimeHours * (1.5*hoursRate);
        netPay = overtimePay + regularPay + bonuses - deduction;
    }
}
package com.example.demo3;

import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ReportGenerator {

    private final List<Employee> employees;
    private final List<Payroll> payrolls;

    public ReportGenerator(List<Employee> employees, List<Payroll> payrolls) {
        this.employees = employees;
        this.payrolls = payrolls;
    }

    // Generate payroll report for a specific employee
    public void generateEmployeeReport(int employeeId) {
        boolean found = false;
        StringBuilder reportContent = new StringBuilder();
        reportContent.append("Employee Payroll Report\n");
        reportContent.append("-----------------------\n");

        for (Payroll payroll : payrolls) {
            if (payroll.getEmployee().getId() == employeeId) {
                found = true;
                reportContent.append("Employee Name: ").append(payroll.getEmployee().getFname())
                        .append(" ").append(payroll.getEmployee().getLname()).append("\n");
                reportContent.append("Department: ").append(payroll.getEmployee().getDepartment()).append("\n");
                reportContent.append("Worked Hours: ").append(payroll.getWorkedHours()).append("\n");
                reportContent.append("Overtime Hours: ").append(payroll.getOvertimeHours()).append("\n");
                reportContent.append("Bonuses: ").append(payroll.getBonuses()).append("\n");
                reportContent.append("Deductions: ").append(payroll.getDeduction()).append("\n");
                reportContent.append("Net Pay: ").append(payroll.getNetPay()).append("\n");
                reportContent.append("-----------------------\n");
            }
        }

        if (!found) {
            reportContent.append("No payroll records found for the employee with ID: ").append(employeeId).append("\n");
        }

        saveReportToFile("employee_report_" + employeeId + ".txt", reportContent.toString());
    }

    // Generate payroll report for an entire department
    public void generateDepartmentReport(String departmentName) {
        boolean found = false;
        double totalNetPay = 0;
        int employeeCount = 0;
        StringBuilder reportContent = new StringBuilder();

        reportContent.append("Department Payroll Report\n");
        reportContent.append("--------------------------\n");
        reportContent.append("Department: ").append(departmentName).append("\n");

        for (Payroll payroll : payrolls) {
            if (payroll.getEmployee().getDepartment().equalsIgnoreCase(departmentName)) {
                found = true;
                employeeCount++;
                totalNetPay += payroll.getNetPay();
                reportContent.append("Employee Name: ").append(payroll.getEmployee().getFname())
                        .append(" ").append(payroll.getEmployee().getLname()).append("\n");
                reportContent.append("Net Pay: ").append(payroll.getNetPay()).append("\n");
                reportContent.append("-----------------------\n");
            }
        }

        if (found) {
            reportContent.append("Total Employees: ").append(employeeCount).append("\n");
            reportContent.append("Total Net Pay for Department: ").append(totalNetPay).append("\n");
        } else {
            reportContent.append("No payroll records found for the department: ").append(departmentName).append("\n");
        }

        saveReportToFile("department_report_" + departmentName + ".txt", reportContent.toString());
    }

    // Generate summary report for all departments
    public void generateSummaryReport() {
        Map<String, Double> departmentTotalPay = new HashMap<>();
        Map<String, Integer> departmentEmployeeCount = new HashMap<>();
        StringBuilder reportContent = new StringBuilder();

        reportContent.append("Company-Wide Payroll Summary Report\n");
        reportContent.append("-----------------------------------\n");

        for (Payroll payroll : payrolls) {
            String department = payroll.getEmployee().getDepartment();
            departmentTotalPay.put(department, departmentTotalPay.getOrDefault(department, 0.0) + payroll.getNetPay());
            departmentEmployeeCount.put(department, departmentEmployeeCount.getOrDefault(department, 0) + 1);
        }

        for (Map.Entry<String, Double> entry : departmentTotalPay.entrySet()) {
            String department = entry.getKey();
            double totalPay = entry.getValue();
            int employeeCount = departmentEmployeeCount.get(department);

            reportContent.append("Department: ").append(department).append("\n");
            reportContent.append("Total Employees: ").append(employeeCount).append("\n");
            reportContent.append("Total Net Pay: ").append(totalPay).append("\n");
            reportContent.append("-----------------------\n");
        }

        saveReportToFile("company_summary_report.txt", reportContent.toString());
    }

    // Save generated report to a file
    private void saveReportToFile(String filename, String content) {
        try {
            File file = new File(filename);
            FileWriter writer = new FileWriter(file);
            writer.write(content);
            writer.close();
            System.out.println("Report saved to " + filename);
        } catch (IOException e) {
            System.out.println("Error saving report: " + e.getMessage());
        }
    }
}
package com.example.demo3;

public class User {
    private String username;
    private String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                '}';
    }
}
package com.example.demo3;

import java.util.HashMap;
import java.util.Map;

public class UserManager {
    private Map<String, String> userDatabase = new HashMap<>();

    // Register a new user
    public boolean register(String username, String password) {
        if (userDatabase.containsKey(username)) {
            System.out.println("Username already exists!");
            return false;
        }
        userDatabase.put(username, password);
        System.out.println("User registered successfully!");
        return true;
    }

    // Log in an existing user
    public boolean login(String username, String password) {
        if (!userDatabase.containsKey(username)) {
            System.out.println("User not found!");
            return false;
        }
        if (userDatabase.get(username).equals(password)) {
            System.out.println("Login successful!");
            return true;
        }
        System.out.println("Invalid password!");
        return false;
    }

    // Display all registered users (for debugging or admin use)
    public void displayUsers() {
        System.out.println("Registered Users:");
        userDatabase.forEach((key, value) -> System.out.println(key));
    }
}
/* General Root Styling */
.root {
    -fx-font-family: "Arial";
    -fx-alignment: center;
    -fx-pref-width: 100%;
    -fx-pref-height: 100%;
    -fx-padding: 20px;
    -fx-background-color: #f0f0f0;
}

/* Center the title */
.title {
    -fx-font-size: 24px;
    -fx-font-weight: bold;
    -fx-text-fill: #333;
    -fx-padding: 20px;
    -fx-alignment: center;
}

/* Center the navbar and other buttons */
.navbar, .button, .button:hover {
    -fx-background-color: #6200EE;
    -fx-text-fill: white;
    -fx-background-radius: 10;
    -fx-padding: 10px 20px;
    -fx-alignment: center;
}

/* Button Hover Effect */
.button:hover {
    -fx-background-color: #3700B3;
}

/* Center the content within the VBox */
.vbox {
    -fx-alignment: center;
}

/* Center the content within the HBox */
.hbox {
    -fx-alignment: center;
}

/* Center the content within the GridPane */
.grid-pane {
    -fx-alignment: center;
}
