# Java Practicals – All 20 Programs

## Q1. University Student Management System

```java
package com.university.management;

import javax.swing.*;
import java.awt.*;
import java.sql.*;

interface ExamEligibility {
    boolean isEligibleForExam();
}

interface ScholarshipEligibility {
    boolean isEligibleForScholarship();
}

class Department {
    private final String deptName;
    private static int deptCount = 0;

    public Department(String deptName) {
        this.deptName = deptName;
        deptCount++;
    }

    public String getDeptName() {
        return deptName;
    }

    public static int getDeptCount() {
        return deptCount;
    }
}

class Course {
    private final String courseName;
    private final int credits;

    public Course(String courseName, int credits) {
        if (credits < 0)
            throw new IllegalArgumentException("Credits cannot be negative.");

        this.courseName = courseName;
        this.credits = credits;
    }

    public String getCourseName() {
        return courseName;
    }

    public int getCredits() {
        return credits;
    }
}

class Student implements ExamEligibility, ScholarshipEligibility {

    private final String name;
    private final int rollNo;
    private double gpa;
    private final int attendancePercent;

    private static int studentCount = 0;

    public Student(String name, int rollNo, double gpa, int attendancePercent) {

        if (gpa < 0 || gpa > 10)
            throw new IllegalArgumentException("GPA must be between 0 and 10.");

        if (attendancePercent < 0 || attendancePercent > 100)
            throw new IllegalArgumentException("Attendance must be 0-100%.");

        this.name = name;
        this.rollNo = rollNo;
        this.gpa = gpa;
        this.attendancePercent = attendancePercent;

        studentCount++;
    }

    public String getName() {
        return name;
    }

    public int getRollNo() {
        return rollNo;
    }

    public double getGpa() {
        return gpa;
    }

    public static int getStudentCount() {
        return studentCount;
    }

    public double calculateGPA(double[] grades) {

        if (grades == null || grades.length == 0)
            throw new IllegalArgumentException("Grades array cannot be empty.");

        double sum = 0;

        for (double g : grades) {

            if (g < 0 || g > 100)
                throw new IllegalArgumentException("Grade out of range: " + g);

            sum += g;
        }

        this.gpa = (sum / grades.length) / 10.0;
        return this.gpa;
    }

    @Override
    public boolean isEligibleForExam() {
        return attendancePercent >= 75;
    }

    @Override
    public boolean isEligibleForScholarship() {
        return gpa >= 8.5 && attendancePercent >= 90;
    }

    @Override
    public String toString() {

        return "Student[name=" + name +
                ", rollNo=" + rollNo +
                ", GPA=" + String.format("%.2f", gpa) +
                ", ExamEligible=" + isEligibleForExam() +
                ", Scholarship=" + isEligibleForScholarship() +
                "]";
    }
}

public class Main extends JFrame {

    private JTextField nameField, rollField, gpaField, attendField;
    private JTextArea outputArea;

    public Main() {

        super("University Student Management System");

        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLayout(new BorderLayout(10, 10));
        setSize(550, 480);

        JPanel form = new JPanel(new GridLayout(5, 2, 8, 8));

        form.setBorder(BorderFactory.createTitledBorder("Student Details"));

        form.add(new JLabel("Name:"));
        nameField = new JTextField();
        form.add(nameField);

        form.add(new JLabel("Roll No:"));
        rollField = new JTextField();
        form.add(rollField);

        form.add(new JLabel("GPA (0-10):"));
        gpaField = new JTextField();
        form.add(gpaField);

        form.add(new JLabel("Attendance (%):"));
        attendField = new JTextField();
        form.add(attendField);

        JButton submit = new JButton("Submit");
        JButton cancel = new JButton("Cancel / Exit");

        form.add(submit);
        form.add(cancel);

        outputArea = new JTextArea(10, 40);
        outputArea.setEditable(false);

        outputArea.setFont(new Font("Monospaced", Font.PLAIN, 12));

        add(form, BorderLayout.NORTH);
        add(new JScrollPane(outputArea), BorderLayout.CENTER);

        submit.addActionListener(e -> handleSubmit());

        cancel.addActionListener(e -> System.exit(0));

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void handleSubmit() {

        try {

            String name = nameField.getText().trim();

            int roll = Integer.parseInt(rollField.getText().trim());

            double gpa = Double.parseDouble(gpaField.getText().trim());

            int attend = Integer.parseInt(attendField.getText().trim());

            Student s = new Student(name, roll, gpa, attend);

            saveToDatabase(s);

            outputArea.setText(s.toString());

        } catch (IllegalArgumentException ex) {

            JOptionPane.showMessageDialog(
                    this,
                    ex.getMessage(),
                    "Input Error",
                    JOptionPane.ERROR_MESSAGE
            );
        }
    }

    private void saveToDatabase(Student s) {

        try (
                Connection con = DriverManager.getConnection(
                        "jdbc:mysql://localhost:3306/university",
                        "root",
                        "password"
                );

                PreparedStatement ps = con.prepareStatement(
                        "INSERT INTO students(name,roll_no,gpa,attendance) VALUES(?,?,?,?)"
                )
        ) {

            ps.setString(1, s.getName());
            ps.setInt(2, s.getRollNo());
            ps.setDouble(3, s.getGpa());

            ps.executeUpdate();

        } catch (SQLException ex) {

            outputArea.append("\n[DB] " + ex.getMessage());
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(Main::new);
    }
}
```

---

## Q2. Online Shopping System

```java
// Paste Q2 Java code here
package com.shopping.cart;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
interface Discountable {
    double applyDiscount(double percent);
}
class Product implements Discountable {
    private final String name;
    private double price;
    private int quantity;
    private static int productCount = 0;
    public Product(String name, double price, int quantity) {
        if (price &lt; 0) throw new IllegalArgumentException("Price cannot be negative.");
        if (quantity &lt; 0) throw new IllegalArgumentException("Quantity cannot be negative.");
        this.name = name;
        this.price = price;
        this.quantity = quantity;
        productCount++;
    }
    public String getName() { return name; }
    public double getPrice() { return price; }
    public int getQuantity() { return quantity; }
    public static int getProductCount() { return productCount; }
    @Override
    public double applyDiscount(double percent) {
        if (percent &lt; 0 || percent &gt; 100)
            throw new IllegalArgumentException("Invalid discount percent.");
        price = price - (price * percent / 100);
        return price;
    }
    public double totalCost() { return price * quantity; }
    @Override public String toString() {
        return name + " x" + quantity + " @ Rs." + String.format("%.2f", price)
               + " = Rs." + String.format("%.2f", totalCost());
    }
}
class Cart {
    private final java.util.List&lt;Product&gt; items = new java.util.ArrayList&lt;&gt;();
    public void addProduct(Product p) { items.add(p); }
    public double calculateBill() {
        return items.stream().mapToDouble(Product::totalCost).sum();
    }
    public String checkout() {
        StringBuilder sb = new StringBuilder("---- BILL ---
");
");
        for (Product p : items) sb.append(p).append("
        sb.append("-------------
Total: Rs.").append(String.format("%.2f", calculateBill()));
        return sb.toString();
    }
}
public class Main extends JFrame {
    private JTextField nameF, priceF, qtyF, discF;
    private JTextArea out;
    private final Cart cart = new Cart();
    public Main() {
        super("Online Shopping System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520, 480);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Add Product to Cart"));
        form.add(new JLabel("Product Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Price (Rs):")); priceF = new JTextField(); form.add(priceF);
        form.add(new JLabel("Quantity:")); qtyF = new JTextField(); form.add(qtyF);
        form.add(new JLabel("Discount (%):")); discF = new JTextField("0"); form.add(discF);
        JButton addBtn = new JButton("Add to Cart");
        JButton checkoutBtn = new JButton("Checkout");
        JButton cancelBtn = new JButton("Cancel / Exit");
        form.add(addBtn); form.add(checkoutBtn);
        out = new JTextArea(12,40);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnPanel = new JPanel(); btnPanel.add(cancelBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnPanel, BorderLayout.SOUTH);
        addBtn.addActionListener(e -&gt; {
            try {
                Product p = new Product(nameF.getText().trim(),
                        Double.parseDouble(priceF.getText().trim()),
                        Integer.parseInt(qtyF.getText().trim()));
                double disc = Double.parseDouble(discF.getText().trim());
                if (disc &gt; 0) p.applyDiscount(disc);
                cart.addProduct(p);
                out.append("Added: " + p + "
");
}
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        checkoutBtn.addActionListener(e -&gt; out.setText(cart.checkout()));
        cancelBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null);
        setVisible(true);
    }
    public static void main(String[] args) {
        SwingUtilities.invokeLater(Main::new);
    }

```

---

## Q3. Railway Reservation System

```java
package com.railway.reservation;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
interface Reservable {
    boolean book(String passengerName, int age);
    boolean cancel(int ticketId);
}
class Passenger {
    private final String name;
    private final int age;
    public Passenger(String name, int age) {
        if (name == null || name.isBlank()) throw new IllegalArgumentException("Passenger name missing.");
        if (age &lt; 1 || age &gt; 120) throw new IllegalArgumentException("Invalid age: " + age);
        this.name = name; this.age = age;
    }
    public String getName() { return name; }
    public int getAge() { return age; }
}
class Ticket {
    private static int counter = 1000;
    private final int ticketId;
    private final Passenger passenger;
    private final double fare;
    public Ticket(Passenger p, double fare) {
        this.ticketId = ++counter;
        this.passenger = p;
        this.fare = fare;
    }
    public int getTicketId() { return ticketId; }
    @Override public String toString() {
        return "Ticket#" + ticketId + " | " + passenger.getName()
               + " (age " + passenger.getAge() + ") | Fare: Rs." + String.format("%.2f", fare);
    }
}
class Train implements Reservable {
    private final String trainName;
    private final int totalSeats;
    private final java.util.List&lt;Ticket&gt; tickets = new java.util.ArrayList&lt;&gt;();
    private static final double BASE_FARE = 250.0;
    public Train(String trainName, int totalSeats) {
        this.trainName = trainName;
        this.totalSeats = totalSeats;
    }
    public int availableSeats() { return totalSeats - tickets.size(); }
    @Override
    public boolean book(String name, int age) {
        if (availableSeats() &lt;= 0) throw new IllegalStateException("Overbooking: No seats available.");
        Passenger p = new Passenger(name, age);
        double fare = age &lt; 5 ? 0 : age &gt;= 60 ? BASE_FARE * 0.5 : BASE_FARE;
        tickets.add(new Ticket(p, fare));
    }
        return true;
    @Override
    public boolean cancel(int ticketId) {
        return tickets.removeIf(t -&gt; t.getTicketId() == ticketId);
    }
    public String listTickets() {
        if (tickets.isEmpty()) return "No bookings yet.";
        StringBuilder sb = new StringBuilder("Train: " + trainName + "
");
"));
    }
}
        tickets.forEach(t -&gt; sb.append(t).append("
        sb.append("Available Seats: ").append(availableSeats());
        return sb.toString();
public class Main extends JFrame {
    private JTextField nameF, ageF, cancelF;
    private JTextArea out;
    private final Train train = new Train("Deccan Express", 5);
    public Main() {
        super("Railway Reservation System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(540, 500);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(4,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Book / Cancel Ticket"));
        form.add(new JLabel("Passenger Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Age:")); ageF = new JTextField(); form.add(ageF);
        form.add(new JLabel("Cancel Ticket ID:")); cancelF = new JTextField(); form.add(cancelF);
        JButton bookBtn = new JButton("Book");
        JButton cancelBtn = new JButton("Cancel Ticket");
        JButton showBtn = new JButton("Show All");
        JButton exitBtn = new JButton("Exit");
        form.add(bookBtn); form.add(cancelBtn);
        out = new JTextArea(14,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnPanel = new JPanel();
        btnPanel.add(showBtn); btnPanel.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnPanel, BorderLayout.SOUTH);
        bookBtn.addActionListener(e -&gt; {
            try {
                train.book(nameF.getText().trim(), Integer.parseInt(ageF.getText().trim()));
                out.append("Booking successful!
");
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        cancelBtn.addActionListener(e -&gt; {
            try {
                boolean ok = train.cancel(Integer.parseInt(cancelF.getText().trim()));
                out.append(ok ? "Ticket cancelled.
" : "Ticket not found.
");
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        showBtn.addActionListener(e -&gt; out.setText(train.listTickets()));
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null);
        setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q4. Employee Payroll Management System

```java
// Paste Q4 Java code here
package com.company.hr;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
interface Payable {
    double calculateSalary();
}
class Employee implements Payable {
    protected final String name;
    protected final int employeeId;
    protected double basicSalary;
    protected int workingDays;
    private static int empCount = 0;
    public static final int TOTAL_DAYS = 30;
    public Employee(String name, int employeeId, double basicSalary, int workingDays) {
        if (basicSalary &lt; 0) throw new IllegalArgumentException("Salary cannot be negative.");
        if (workingDays &lt; 0 || workingDays &gt; TOTAL_DAYS)
            throw new IllegalArgumentException("Invalid working days: " + workingDays);
        this.name = name; this.employeeId = employeeId;
        this.basicSalary = basicSalary; this.workingDays = workingDays;
        empCount++;
    }
    @Override public double calculateSalary() { return (basicSalary / TOTAL_DAYS) * workingDays; }
    public static int getEmpCount() { return empCount; }
    @Override public String toString() {
        return "Employee[" + name + ", ID=" + employeeId
               + ", NetPay=Rs." + String.format("%.2f", calculateSalary()) + "]";
    }
}
class Manager extends Employee {
    private final double incentive;
    public Manager(String name, int id, double basic, int days, double incentive) {
        super(name, id, basic, days);
        this.incentive = incentive;
    }
    @Override public double calculateSalary() { return super.calculateSalary() + incentive; }
    @Override public String toString() {
        return "Manager[" + name + ", ID=" + employeeId
               + ", NetPay=Rs." + String.format("%.2f", calculateSalary()) + " (incl. incentive Rs." + incentive + ")]";
    }
}
class Clerk extends Employee {
    private static final double OVERTIME_RATE = 150.0;
    private final int overtimeHours;
    public Clerk(String name, int id, double basic, int days, int overtimeHours) {
        super(name, id, basic, days);
        this.overtimeHours = overtimeHours;
    }
    @Override public double calculateSalary() {
        return super.calculateSalary() + (overtimeHours * OVERTIME_RATE);
    }
    @Override public String toString() {
    }
        return "Clerk[" + name + ", ID=" + employeeId
               + ", NetPay=Rs." + String.format("%.2f", calculateSalary()) + "]";
}
public class Main extends JFrame {
    private JTextField nameF, idF, basicF, daysF, extraF;
    private JComboBox&lt;String&gt; typeCombo;
    private JTextArea out;
    public Main() {
        super("Employee Payroll Management System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(540, 500);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(7,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Employee Details"));
        form.add(new JLabel("Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Employee ID:")); idF = new JTextField(); form.add(idF);
        form.add(new JLabel("Basic Salary:")); basicF = new JTextField(); form.add(basicF);
        form.add(new JLabel("Working Days:")); daysF = new JTextField(); form.add(daysF);
        form.add(new JLabel("Incentive/OT Hours:")); extraF = new JTextField("0"); form.add(extraF);
        form.add(new JLabel("Type:"));
        typeCombo = new JComboBox&lt;&gt;(new String[]{"Employee", "Manager", "Clerk"});
        form.add(typeCombo);
        JButton submit = new JButton("Submit");
        JButton cancel = new JButton("Cancel / Exit");
        form.add(submit); form.add(cancel);
        out = new JTextArea(10,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        submit.addActionListener(e -&gt; {
            try {
                String name = nameF.getText().trim();
                int id = Integer.parseInt(idF.getText().trim());
                double basic = Double.parseDouble(basicF.getText().trim());
                int days = Integer.parseInt(daysF.getText().trim());
                double extra = Double.parseDouble(extraF.getText().trim());
                String type = (String) typeCombo.getSelectedItem();
                Employee emp;
                if ("Manager".equals(type)) emp = new Manager(name, id, basic, days, extra);
                else if ("Clerk".equals(type)) emp = new Clerk(name, id, basic, days, (int)extra);
                else emp = new Employee(name, id, basic, days);
                out.setText(emp.toString() + "
Total Employees: " + Employee.getEmpCount());
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        cancel.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null);
        setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}

```

---

## Q5. Library Management System

```java
package com.library.system;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
import java.util.*;
class BookUnavailableException extends Exception {
    public BookUnavailableException(String msg) { super(msg); }
}
class FineCalculationException extends Exception {
    public FineCalculationException(String msg) { super(msg); }
}
interface Borrowable {
    void issueBook(String memberId) throws BookUnavailableException;
    void returnBook(String memberId, int daysLate) throws FineCalculationException;
}
class Book implements Borrowable {
    private final String title, bookId;
    private boolean available = true;
    public static final double FINE_PER_DAY = 2.0;
    public Book(String bookId, String title) {
        this.bookId = bookId; this.title = title;
    }
    public String getBookId() { return bookId; }
    public String getTitle() { return title; }
    public boolean isAvailable() { return available; }
    @Override
    public void issueBook(String memberId) throws BookUnavailableException {
        if (!available) throw new BookUnavailableException("Book '" + title + "' is not available.");
        available = false;
        System.out.println("Issued to " + memberId);
    }
    @Override
    public void returnBook(String memberId, int daysLate) throws FineCalculationException {
        if (daysLate &lt; 0) throw new FineCalculationException("Days late cannot be negative.");
        available = true;
        double fine = daysLate * FINE_PER_DAY;
        System.out.println("Returned. Fine: Rs." + fine);
    }
    @Override public String toString() {
        return "[" + bookId + "] " + title + " | " + (available ? "Available" : "Issued");
    }
}
class Member {
    private final String memberId, memberName;
    private final List&lt;String&gt; borrowedBooks = new ArrayList&lt;&gt;();
    public Member(String memberId, String memberName) {
        if (memberId == null || memberId.isBlank()) throw new IllegalArgumentException("Member ID required.");
        this.memberId = memberId; this.memberName = memberName;
    }
    public String getMemberId() { return memberId; }
    public String getMemberName() { return memberName; }
    public List&lt;String&gt; getBorrowedBooks() { return borrowedBooks; }
}
public class Main extends JFrame {
    private JTextField bookIdF, titleF, memberIdF, memberNameF, daysF;
    private JTextArea out;
    private final Map&lt;String, Book&gt; books = new HashMap&lt;&gt;();
    private final Map&lt;String, Member&gt; members = new HashMap&lt;&gt;();
    public Main() {
        super("Library Management System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(560, 540);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(6,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Library Operations"));
        form.add(new JLabel("Book ID:")); bookIdF = new JTextField(); form.add(bookIdF);
        form.add(new JLabel("Book Title:")); titleF = new JTextField(); form.add(titleF);
        form.add(new JLabel("Member ID:")); memberIdF = new JTextField(); form.add(memberIdF);
        form.add(new JLabel("Member Name:")); memberNameF = new JTextField(); form.add(memberNameF);
        form.add(new JLabel("Days Late (return):")); daysF = new JTextField("0"); form.add(daysF);
        JButton addBook = new JButton("Add Book");
        JButton issue = new JButton("Issue");
        JButton returnBtn = new JButton("Return");
        JButton showBtn = new JButton("Show All Books");
        JButton cancelBtn = new JButton("Cancel / Exit");
        JPanel btnP = new JPanel();
        btnP.add(addBook); btnP.add(issue); btnP.add(returnBtn); btnP.add(showBtn); btnP.add(cancelBtn);
        out = new JTextArea(12,44);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        addBook.addActionListener(e -&gt; {
            try {
                books.put(bookIdF.getText().trim(), new Book(bookIdF.getText().trim(), titleF.getText().trim()));
                out.append("Book added: " + titleF.getText().trim() + "
");
");
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        issue.addActionListener(e -&gt; {
            try {
                Book b = books.get(bookIdF.getText().trim());
                if (b == null) throw new BookUnavailableException("Book not found.");
                b.issueBook(memberIdF.getText().trim());
                out.append("Issued: " + b.getTitle() + " to " + memberIdF.getText().trim() + "
            } catch (BookUnavailableException ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        returnBtn.addActionListener(e -&gt; {
            try {
                Book b = books.get(bookIdF.getText().trim());
                if (b == null) throw new FineCalculationException("Book not found.");
                int days = Integer.parseInt(daysF.getText().trim());
                b.returnBook(memberIdF.getText().trim(), days);
                double fine = days * Book.FINE_PER_DAY;
                out.append("Returned: " + b.getTitle() + " | Fine: Rs." + String.format("%.2f", fine) + "
");
");
"));
}
            } catch (FineCalculationException | NumberFormatException ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- All Books -
            books.values().forEach(b -&gt; out.append(b + "
        });
        cancelBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null);
        setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }

```

---

## Q6. Banking System with Transaction History

```java
package com.bank.accounts;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
import java.util.*;
interface Transactable {
    void deposit(double amount);
    void withdraw(double amount);
    double getBalance();
}
class Transaction {
    private final String type;
    private final double amount;
    private final double balanceAfter;
    private static final Set&lt;String&gt; txIds = new HashSet&lt;&gt;();
    public Transaction(String txId, String type, double amount, double balanceAfter) {
        if (!txIds.add(txId)) throw new IllegalArgumentException("Duplicate transaction: " + txId);
        this.type = type; this.amount = amount; this.balanceAfter = balanceAfter;
    }
    @Override public String toString() {
        return type + " Rs." + String.format("%.2f", amount)
               + " | Balance: Rs." + String.format("%.2f", balanceAfter);
    }
}
class Account implements Transactable {
    protected String accountNumber;
    protected double balance;
    protected final List&lt;Transaction&gt; history = new ArrayList&lt;&gt;();
    private static int txCounter = 0;
    public Account(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber; this.balance = initialBalance;
    }
    @Override public void deposit(double amount) {
        if (amount &lt;= 0) throw new IllegalArgumentException("Deposit must be positive.");
        balance += amount;
        history.add(new Transaction("TX" + (++txCounter), "DEPOSIT", amount, balance));
    }
    @Override public void withdraw(double amount) {
        if (amount &lt;= 0) throw new IllegalArgumentException("Withdrawal must be positive.");
        if (amount &gt; balance) throw new IllegalStateException("Insufficient balance.");
        balance -= amount;
        history.add(new Transaction("TX" + (++txCounter), "WITHDRAW", amount, balance));
    }
    @Override public double getBalance() { return balance; }
    public String getHistory() {
        StringBuilder sb = new StringBuilder("Account: " + accountNumber + "
");
"));
        history.forEach(t -&gt; sb.append(t).append("
        sb.append("Current Balance: Rs.").append(String.format("%.2f", balance));
        return sb.toString();
    }
}
class SavingsAccount extends Account {
    private final double interestRate;
    public SavingsAccount(String accountNumber, double balance, double interestRate) {
        super(accountNumber, balance);
        this.interestRate = interestRate;
    }
    public double applyInterest() {
        double interest = balance * interestRate / 100;
        balance += interest;
        return interest;
    }
}
public class Main extends JFrame {
    private JTextField accF, amtF;
    private JTextArea out;
    private Account account;
    public Main() {
        super("Banking System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(500, 460);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(3,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Banking Operations"));
        form.add(new JLabel("Account No:")); accF = new JTextField("ACC001"); form.add(accF);
        form.add(new JLabel("Amount (Rs):")); amtF = new JTextField(); form.add(amtF);
        JButton depositBtn = new JButton("Deposit");
        JButton withdrawBtn = new JButton("Withdraw");
        JButton histBtn = new JButton("History");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(depositBtn); form.add(withdrawBtn);
        account = new SavingsAccount("ACC001", 5000.0, 4.5);
        out = new JTextArea(13,40);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(histBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        depositBtn.addActionListener(e -&gt; {
            try {
                account.deposit(Double.parseDouble(amtF.getText().trim()));
                out.append("Deposited successfully.
");
");
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        withdrawBtn.addActionListener(e -&gt; {
            try {
                account.withdraw(Double.parseDouble(amtF.getText().trim()));
                out.append("Withdrawn successfully.
            } catch (Exception ex) {
                JOptionPane.showMessageDialog(this, ex.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
            }
        });
        histBtn.addActionListener(e -&gt; out.setText(account.getHistory()));
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null);
        setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
Note: JDBC: Create the DB table manually. For Q1: CREATE TABLE students(name VARCHAR(50), roll_no INT, gpa
DOUBLE, attendance INT); Update DB URL/credentials in the code
```

---

## Q7. Vehicle Rental System

```java
package com.rental.vehicles;
import javax.swing.*;
import java.awt.*;
interface Rentable {
    double calculateRent(int days);
}
abstract class Vehicle implements Rentable {
    protected final String vehicleId, brand;
    protected boolean available;
    public Vehicle(String vehicleId, String brand) {
        this.vehicleId = vehicleId; this.brand = brand; this.available = true;
    }
    public boolean isAvailable() { return available; }
    public void rent() {
        if (!available) throw new IllegalStateException("Vehicle " + vehicleId + " is unavailable.");
        available = false;
    }
    public void returnVehicle() { available = true; }
    @Override public String toString() {
        return brand + " [" + vehicleId + "] " + (available ? "Available" : "Rented");
    }
}
class Car extends Vehicle {
    private static final double RATE_PER_DAY = 1500.0;
    public Car(String id, String brand) { super(id, brand); }
    @Override public double calculateRent(int days) {
        if (days &lt;= 0) throw new IllegalArgumentException("Rental duration must be positive.");
        return days * RATE_PER_DAY;
    }
}
class Bike extends Vehicle {
    private static final double RATE_PER_DAY = 500.0;
    public Bike(String id, String brand) { super(id, brand); }
    @Override public double calculateRent(int days) {
        if (days &lt;= 0) throw new IllegalArgumentException("Rental duration must be positive.");
        return days * RATE_PER_DAY;
    }
}
public class Main extends JFrame {
    private JTextField idF, brandF, daysF;
    private JComboBox&lt;String&gt; typeCombo;
    private JTextArea out;
    private final java.util.List&lt;Vehicle&gt; fleet = new java.util.ArrayList&lt;&gt;();
    public Main() {
        super("Vehicle Rental System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520, 440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Rent a Vehicle"));
        form.add(new JLabel("Vehicle ID:")); idF = new JTextField(); form.add(idF);
        form.add(new JLabel("Brand:")); brandF = new JTextField(); form.add(brandF);
        form.add(new JLabel("Type:"));
        typeCombo = new JComboBox&lt;&gt;(new String[]{"Car","Bike"}); form.add(typeCombo);
        form.add(new JLabel("Rental Days:")); daysF = new JTextField(); form.add(daysF);
        JButton addBtn = new JButton("Add to Fleet");
        JButton rentBtn = new JButton("Rent");
        JButton retBtn = new JButton("Return");
        JButton showBtn = new JButton("Show Fleet");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(addBtn); form.add(rentBtn);
        out = new JTextArea(11,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(retBtn); btnP.add(showBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        addBtn.addActionListener(e -&gt; {
            try {
                Vehicle v = "Car".equals(typeCombo.getSelectedItem())
                        ? new Car(idF.getText().trim(), brandF.getText().trim())
                        : new Bike(idF.getText().trim(), brandF.getText().trim());
                fleet.add(v); out.append("Added: " + v + "
");
");
null);
"); }
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        rentBtn.addActionListener(e -&gt; {
            try {
                int days = Integer.parseInt(daysF.getText().trim());
                Vehicle v = fleet.stream().filter(x -&gt; x.vehicleId.equals(idF.getText().trim())).findFirst()
                        .orElseThrow(() -&gt; new IllegalArgumentException("Vehicle not found."));
                v.rent();
                out.append("Rent for " + days + " days: Rs." + String.format("%.2f", v.calculateRent(days)) + "
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        retBtn.addActionListener(e -&gt; {
            Vehicle v = fleet.stream().filter(x -&gt; x.vehicleId.equals(idF.getText().trim())).findFirst().orElse(
            if (v != null) { v.returnVehicle(); out.append("Vehicle returned.
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- Fleet -
"); fleet.forEach(v -&gt; out.append(v + "
"));
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}```

---

## Q8. Inventory Management System

```java
package com.inventory.stock;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Storable {
    void addItem(Item item);
    void removeItem(String itemCode);
    int getStock(String itemCode);
}
class Item {
    private final String itemCode, itemName;
    private int quantity;
    private static final int REORDER_LEVEL = 5;
    public Item(String itemCode, String itemName, int quantity) {
        if (itemCode == null || itemCode.isBlank())
            throw new NullPointerException("Item code cannot be null/empty.");
        if (quantity &lt; 0) throw new IllegalArgumentException("Quantity cannot be negative.");
        this.itemCode = itemCode; this.itemName = itemName; this.quantity = quantity;
    }
    public String getItemCode() { return itemCode; }
    public String getItemName() { return itemName; }
    public int getQuantity() { return quantity; }
    public void reduceStock(int qty) {
        if (qty &gt; quantity) throw new IllegalStateException("Stock underflow for " + itemCode);
        quantity -= qty;
    }
    public boolean needsReorder() { return quantity &lt;= REORDER_LEVEL; }
    @Override public String toString() {
        return "[" + itemCode + "] " + itemName + " | Qty: " + quantity
               + (needsReorder() ? " *** REORDER ALERT ***" : "");
    }
}
class Warehouse implements Storable {
    private final Map&lt;String, Item&gt; inventory = new HashMap&lt;&gt;();
    @Override public void addItem(Item item) {
        if (inventory.containsKey(item.getItemCode()))
            throw new IllegalArgumentException("Duplicate item code: " + item.getItemCode());
        inventory.put(item.getItemCode(), item);
    }
    @Override public void removeItem(String code) {
        if (!inventory.containsKey(code))
            throw new NoSuchElementException("Item not found: " + code);
        inventory.remove(code);
    }
    @Override public int getStock(String code) {
        Item item = inventory.get(code);
        if (item == null) throw new NoSuchElementException("Item not found: " + code);
        return item.getQuantity();
    }
    public Collection&lt;Item&gt; allItems() { return inventory.values(); }
}
public class Main extends JFrame {
    private JTextField codeF, nameF, qtyF;
    private JTextArea out;
    private final Warehouse wh = new Warehouse();
    public Main() {
        super("Inventory Management System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(4,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Inventory"));
        form.add(new JLabel("Item Code:")); codeF = new JTextField(); form.add(codeF);
        form.add(new JLabel("Item Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Quantity:")); qtyF = new JTextField(); form.add(qtyF);
        JButton addBtn = new JButton("Add Item");
        JButton removeBtn = new JButton("Remove Item");
        JButton showBtn = new JButton("Show All");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(addBtn); form.add(removeBtn);
        out = new JTextArea(11,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(showBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        addBtn.addActionListener(e -&gt; {
            try {
                wh.addItem(new Item(codeF.getText().trim(), nameF.getText().trim(),
                        Integer.parseInt(qtyF.getText().trim())));
                out.append("Item added.
");
"); }
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        removeBtn.addActionListener(e -&gt; {
            try { wh.removeItem(codeF.getText().trim()); out.append("Item removed.
            catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- Inventory -
"); wh.allItems().forEach(i -&gt; out.append(i + "
"));
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q9. Hospital Appointment Booking System

```java
package com.hospital.appointments;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Schedulable {
    void bookAppointment(Patient p, String timeSlot) throws Exception;
}
class Patient {
    private final String patientId, name;
    public Patient(String patientId, String name) {
        if (patientId == null || name == null)
            throw new NullPointerException("Patient info cannot be null.");
        this.patientId = patientId; this.name = name;
    }
    public String getPatientId() { return patientId; }
    public String getName() { return name; }
}
class Appointment {
    private final Patient patient;
    private final String timeSlot;
    public Appointment(Patient p, String slot) { this.patient = p; this.timeSlot = slot; }
    @Override public String toString() {
        return "[" + timeSlot + "] " + patient.getName() + " (ID:" + patient.getPatientId() + ")";
    }
}
class Doctor implements Schedulable {
    private final String name;
    private final List&lt;Appointment&gt; appointments = new ArrayList&lt;&gt;();
    private final Set&lt;String&gt; bookedSlots = new HashSet&lt;&gt;();
    public Doctor(String name) { this.name = name; }
    @Override
    public void bookAppointment(Patient p, String timeSlot) throws Exception {
        if (p == null) throw new NullPointerException("Patient info is null.");
        if (timeSlot == null || !timeSlot.matches("\d{2}:\d{2}"))
            throw new IllegalArgumentException("Invalid time slot format. Use HH:MM.");
        if (bookedSlots.contains(timeSlot))
            throw new IllegalStateException("Overlapping appointment at " + timeSlot);
        bookedSlots.add(timeSlot);
        appointments.add(new Appointment(p, timeSlot));
    }
    public String listAppointments() {
        if (appointments.isEmpty()) return "No appointments for Dr. " + name;
        StringBuilder sb = new StringBuilder("Dr. " + name + "'s Appointments:
");
"));
    }
        appointments.forEach(a -&gt; sb.append(a).append("
        return sb.toString();
}
public class Main extends JFrame {
    private JTextField doctorF, patIdF, patNameF, slotF;
    private JTextArea out;
    private final Map&lt;String, Doctor&gt; doctors = new HashMap&lt;&gt;();
    public Main() {
        super("Hospital Appointment Booking System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,460);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Book Appointment"));
        form.add(new JLabel("Doctor Name:")); doctorF = new JTextField(); form.add(doctorF);
        form.add(new JLabel("Patient ID:")); patIdF = new JTextField(); form.add(patIdF);
        form.add(new JLabel("Patient Name:")); patNameF = new JTextField(); form.add(patNameF);
        form.add(new JLabel("Time Slot (HH:MM):")); slotF = new JTextField(); form.add(slotF);
        JButton bookBtn = new JButton("Book");
        JButton showBtn = new JButton("Show Appointments");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(bookBtn); form.add(showBtn);
        out = new JTextArea(12,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        bookBtn.addActionListener(e -&gt; {
            try {
                String dName = doctorF.getText().trim();
                doctors.putIfAbsent(dName, new Doctor(dName));
                Doctor d = doctors.get(dName);
                Patient p = new Patient(patIdF.getText().trim(), patNameF.getText().trim());
                d.bookAppointment(p, slotF.getText().trim());
                out.append("Appointment booked: " + slotF.getText().trim() + "
");
}
```

---

## Q10. Smart Home Device Control System

```java
// Paste Q10 Java code here
```

---

## Q11. Airline Ticketing System

```java
// Paste Q11 Java code here
```

---

## Q12. Online Quiz Management System

```java
// Paste Q12 Java code here
```

---

## Q13. Customer Feedback and Rating System

```java
// Paste Q13 Java code here
```

---

## Q14. Food Delivery App Simulation

```java
// Paste Q14 Java code here
```

---

## Q15. Parcel Tracking System

```java
// Paste Q15 Java code here
```

---

## Q16. Electricity Bill Generator

```java
// Paste Q16 Java code here
```

---

## Q17. Event Management System

```java
// Paste Q17 Java code here
```

---

## Q18. Hotel Room Booking System

```java
// Paste Q18 Java code here
```

---

## Q19. Loan Eligibility Checker

```java
// Paste Q19 Java code here
```

---

## Q20. Weather Forecasting Application

```java
// Paste Q20 Java code here
```
