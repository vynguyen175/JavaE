package com.example;

import java.io.Serializable;

public class Employee implements Serializable {
    private int id;
    private String fname;
    private String lname;
    private double hoursRate;
    private String department;

    public Employee(int id, String fname, String lname, String department, double hoursRate) {
        this.id = id;
        this.fname = fname;
        this.lname = lname;
        this.department = department;
        this.hoursRate = hoursRate;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getFname() {
        return fname;
    }

    public void setFname(String fname) {
        this.fname = fname;
    }

    public String getLname() {
        return lname;
    }

    public void setLname(String lname) {
        this.lname = lname;
    }

    public double getHoursRate() {
        return hoursRate;
    }

    public void setHoursRate(double hoursRate) {
        this.hoursRate = hoursRate;
    }

    public String getDepartment() {
        return department;
    }
    public void setDepartment(String department) {
        this.department = department;
    }
    public String toString() {
        String s = "------Employee Information-----";
        s+="Employee ID: " + this.id + "\n";
        s+="First Name: " + this.fname + "\n";
        s+="Last Name: " + this.lname + "\n";
        s+="Department: " + this.department + "\n";
        s+="Base Salary: " + this.hoursRate + "\n";
        return s;
    }
}
package com.example;

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
package com.example;

import java.io.Serializable;
import java.util.Scanner;
import java.util.ArrayList;

public class Department implements Serializable {
    private static ArrayList<Employee> employees = new ArrayList<>();
    private static Scanner scanner = new Scanner(System.in);
    public static void EmployeeManager() {
        while (true) {
            System.out.println("\nEmployee Management System");
            System.out.println("1. Add Employee");
            System.out.println("2. View Employees");
            System.out.println("3. Update Employee");
            System.out.println("4. Delete Employee");
            System.out.println("5. Exit");
            System.out.print("Choose an option: ");

            int choice = scanner.nextInt();
            scanner.nextLine();

            switch (choice) {
                case 1:

            }
            }
        }
    private static void addEmployee(){
        System.out.println("Enter Employee ID: ");
        int id = scanner.nextInt();
        scanner.nextLine();
        System.out.println("Enter Employee's First Name: ");
        String firstName = scanner.nextLine();
        System.out.println("Enter Employee's Last Name: ");
        String lastName = scanner.nextLine();
        System.out.println("Enter Employee's Department: ");
        String department = scanner.nextLine();
        System.out.println("Enter Employee's Base Salary: ");
        double hoursRate = scanner.nextDouble();
        Employee employee = new Employee (id,firstName,lastName,department,hoursRate);
        employees.add(employee);
        System.out.println("Employee Added");
    }
    private static void viewEmployees() {
        if (employees.size() == 0) {
            System.out.println("No Employee Found");
        }
        else {
            for (Employee employee : employees) {
                System.out.println(employee);
            }
        }
    }
    private static void updateEmployee() {
        System.out.println("Enter Employee ID to update: ");
        int id = scanner.nextInt();
        scanner.nextLine();
        for (Employee employee : employees) {
            if (employee.getId() == id) {
                System.out.println("Enter Employee's First Name: ");
                String firstName = scanner.nextLine();
                System.out.println("Enter Employee's Last Name: ");
                String lastName = scanner.nextLine();
                System.out.println("Enter Employee's Department: ");
                String department = scanner.nextLine();
                System.out.println("Enter Employee's Base Salary: ");
                double hoursRate = scanner.nextDouble();
                employees.set(id ,new Employee (id,firstName,lastName,department,hoursRate));
                System.out.println("Employee Updated");
                return;
            }
        }
        System.out.println("Employee Not Found");
    }
    private static void deleteEmployee() {
        System.out.println("Enter Employee ID to delete: ");
        int id = scanner.nextInt();
        scanner.nextLine();
        for (Employee employee : employees) {
            if (employee.getId() == id) {
                employees.remove(employee);
                System.out.println("Employee Deleted");
                return;
            }
        }
        System.out.println("Employee Not Found");
    }
    }

package com.example;

public class DataStorage {
}

