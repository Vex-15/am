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
package com.smarthome.devices;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Switchable { void turnOn(); void turnOff(); }
interface Configurable { void configure(String setting, String value); }
abstract class Device implements Switchable, Configurable {
    protected final String deviceId, deviceName;
    protected boolean isOn = false;
    protected final Map&lt;String,String&gt; config = new HashMap&lt;&gt;();
    public Device(String deviceId, String deviceName) {
        this.deviceId = deviceId; this.deviceName = deviceName;
    }
    @Override public void turnOn() {
        if (isOn) throw new IllegalStateException(deviceName + " is already ON.");
        isOn = true;
    }
    @Override public void turnOff() {
        if (!isOn) throw new IllegalStateException(deviceName + " is already OFF.");
        isOn = false;
    }
    @Override public void configure(String setting, String value) {
        if (!isOn) throw new IllegalStateException("Cannot configure device while offline.");
        if (setting == null || value == null)
            throw new IllegalArgumentException("Invalid configuration values.");
        config.put(setting, value);
    }
    @Override public String toString() {
        return deviceName + "[" + deviceId + "] " + (isOn ? "ON" : "OFF") + " | Config: " + config;
    }
}
class Light extends Device {
    public Light(String id, String name) { super(id, name); }
    public void setBrightness(int level) {
        if (level &lt; 0 || level &gt; 100)
            throw new IllegalArgumentException("Brightness must be 0-100.");
        configure("brightness", String.valueOf(level));
    }
}
class Fan extends Device {
    public Fan(String id, String name) { super(id, name); }
    public void setSpeed(int speed) {
        if (speed &lt; 1 || speed &gt; 5)
            throw new IllegalArgumentException("Fan speed must be 1-5.");
        configure("speed", String.valueOf(speed));
    }
}
public class Main extends JFrame {
    private JTextField idF, nameF, settingF, valueF;
    private JComboBox&lt;String&gt; typeCombo;
    private JTextArea out;
    private final Map&lt;String,Device&gt; devices = new HashMap&lt;&gt;();
    public Main() {
        super("Smart Home Device Control");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(540,480);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Device Control"));
        form.add(new JLabel("Device ID:")); idF = new JTextField(); form.add(idF);
        form.add(new JLabel("Device Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Type:"));
        typeCombo = new JComboBox&lt;&gt;(new String[]{"Light","Fan"}); form.add(typeCombo);
        form.add(new JLabel("Setting:")); settingF = new JTextField("brightness"); form.add(settingF);
        form.add(new JLabel("Value:")); valueF = new JTextField(); form.add(valueF);
        JButton addBtn = new JButton("Add Device");
        JButton onBtn = new JButton("Turn ON");
        JButton offBtn = new JButton("Turn OFF");
        JButton cfgBtn = new JButton("Configure");
        JButton showBtn = new JButton("Show All");
        JButton exitBtn = new JButton("Cancel / Exit");
        JPanel btnP = new JPanel();
        btnP.add(addBtn); btnP.add(onBtn); btnP.add(offBtn);
        btnP.add(cfgBtn); btnP.add(showBtn); btnP.add(exitBtn);
        out = new JTextArea(10,44);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        addBtn.addActionListener(e -&gt; {
            String id = idF.getText().trim(), name = nameF.getText().trim();
            Device d = "Light".equals(typeCombo.getSelectedItem()) ? new Light(id, name) : new Fan(id, name);
            devices.put(id, d); out.append("Device added: " + d + "
");
"); }
"); }
");
        });
        onBtn.addActionListener(e -&gt; {
            try { devices.get(idF.getText().trim()).turnOn(); out.append("Turned ON.
            catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        offBtn.addActionListener(e -&gt; {
            try { devices.get(idF.getText().trim()).turnOff(); out.append("Turned OFF.
            catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        cfgBtn.addActionListener(e -&gt; {
            try {
                devices.get(idF.getText().trim()).configure(settingF.getText().trim(), valueF.getText().trim());
                out.append("Configured.
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- Devices -
"); devices.values().forEach(d -&gt; out.append(d + "
"));
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q11. Airline Ticketing System

```java
package com.airline.booking;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface CheckIn {
    void checkIn(int ticketId);
}
class Passenger {
    private final String name;
    private final String passportNo;
    public Passenger(String name, String passportNo) {
        if (name == null || passportNo == null || name.isBlank() || passportNo.isBlank())
            throw new IllegalArgumentException("Invalid passenger details.");
        this.name = name; this.passportNo = passportNo;
    }
    public String getName() { return name; }
    @Override public String toString() { return name + " (" + passportNo + ")"; }
}
class Ticket {
    private static int counter = 100;
    private final int ticketId;
    private final Passenger passenger;
    private final String seatClass;
    private boolean checkedIn = false;
    public Ticket(Passenger p, String seatClass) {
        this.ticketId = ++counter; this.passenger = p; this.seatClass = seatClass;
    }
    public int getTicketId() { return ticketId; }
    public void doCheckIn() {
        if (checkedIn) throw new IllegalStateException("Already checked in: Ticket #" + ticketId);
        checkedIn = true;
    }
    @Override public String toString() {
        return "Ticket#" + ticketId + " | " + passenger + " | " + seatClass
               + " | " + (checkedIn ? "Checked-In" : "Not Checked-In");
    }
}
class Flight implements CheckIn {
    private final String flightNo;
    private final int capacity;
    private final List&lt;Ticket&gt; tickets = new ArrayList&lt;&gt;();
    private final Set&lt;Integer&gt; ticketIds = new HashSet&lt;&gt;();
    private final double fareEconomy, fareBusiness;
    public Flight(String flightNo, int capacity, double fareE, double fareB) {
        this.flightNo = flightNo; this.capacity = capacity;
        this.fareEconomy = fareE; this.fareBusiness = fareB;
    }
    public Ticket book(Passenger p, String seatClass) {
        if (tickets.size() &gt;= capacity)
            throw new IllegalStateException("Flight overbooked.");
        Ticket t = new Ticket(p, seatClass);
        if (!ticketIds.add(t.getTicketId()))
            throw new IllegalStateException("Double booking detected.");
        tickets.add(t);
        return t;
    }
    @Override public void checkIn(int ticketId) {
        tickets.stream().filter(t -&gt; t.getTicketId() == ticketId).findFirst()
                .orElseThrow(() -&gt; new NoSuchElementException("Ticket not found."))
                .doCheckIn();
    }
    public String listPassengers() {
        StringBuilder sb = new StringBuilder("Flight: " + flightNo + "
");
"));
    }
        tickets.forEach(t -&gt; sb.append(t).append("
        return sb.toString();
    public double getFare(String cls) { return "Business".equals(cls) ? fareBusiness : fareEconomy; }
}
public class Main extends JFrame {
    private JTextField nameF, passportF, ticketIdF;
    private JComboBox&lt;String&gt; classCombo;
    private JTextArea out;
    private final Flight flight = new Flight("AI-202", 5, 5000, 12000);
    public Main() {
        super("Airline Ticketing System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,460);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Flight AI-202"));
        form.add(new JLabel("Passenger Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Passport No:")); passportF = new JTextField(); form.add(passportF);
        form.add(new JLabel("Seat Class:"));
        classCombo = new JComboBox&lt;&gt;(new String[]{"Economy","Business"}); form.add(classCombo);
        form.add(new JLabel("Ticket ID (Check-In):")); ticketIdF = new JTextField(); form.add(ticketIdF);
        JButton bookBtn = new JButton("Book");
        JButton checkBtn = new JButton("Check-In");
        JButton showBtn = new JButton("Show All");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(bookBtn); form.add(checkBtn);
        out = new JTextArea(12,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(showBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        bookBtn.addActionListener(e -&gt; {
            try {
                Passenger p = new Passenger(nameF.getText().trim(), passportF.getText().trim());
                String cls = (String) classCombo.getSelectedItem();
                Ticket t = flight.book(p, cls);
                out.append("Booked: " + t + " | Fare: Rs." + flight.getFare(cls) + "
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        checkBtn.addActionListener(e -&gt; {
            try {
                flight.checkIn(Integer.parseInt(ticketIdF.getText().trim()));
                out.append("Check-in successful.
");
}
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; out.setText(flight.listPassengers()));
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q12. Online Quiz Management System

```java
package com.quiz.app;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Scorable {
    int calculateScore();
}
class Question {
    private final String text;
    private final String[] options;
    private final int correctOption; // 0-indexed
    public Question(String text, String[] options, int correct) {
        if (options == null || options.length &lt; 2)
            throw new IllegalArgumentException("At least 2 options needed.");
        if (correct &lt; 0 || correct &gt;= options.length)
            throw new IllegalArgumentException("Invalid correct option index.");
        this.text = text; this.options = options; this.correctOption = correct;
    }
    public String getText() { return text; }
    public String[] getOptions() { return options; }
    public boolean isCorrect(int chosen) { return chosen == correctOption; }
}
class Quiz implements Scorable {
    private final List&lt;Question&gt; questions;
    private final int[] answers;
    private int currentQ = 0;
    private static final int MAX_SCORE = 100;
    public Quiz(List&lt;Question&gt; questions) {
        this.questions = questions;
        this.answers = new int[questions.size()];
        Arrays.fill(answers, -1);
    }
    public Question currentQuestion() { return questions.get(currentQ); }
    public boolean hasNext() { return currentQ &lt; questions.size() - 1; }
    public void answer(int choice) {
        if (choice &lt; 0) throw new IllegalArgumentException("Invalid answer format.");
        answers[currentQ] = choice;
        if (hasNext()) currentQ++;
    }
    public boolean allAnswered() {
        for (int a : answers) if (a == -1) return false;
        return true;
    }
    @Override public int calculateScore() {
        if (!allAnswered()) throw new IllegalStateException("Unanswered questions remain.");
        int score = 0;
        for (int i = 0; i &lt; questions.size(); i++) {
            if (questions.get(i).isCorrect(answers[i])) score++;
        }
        int result = (score * MAX_SCORE) / questions.size();
        if (result &gt; MAX_SCORE) throw new ArithmeticException("Score overflow.");
        return result;
}
    }
public class Main extends JFrame {
    private JTextArea out;
    private JPanel optionsPanel;
    private Quiz quiz;
    private final ButtonGroup bg = new ButtonGroup();
    public Main() {
        super("Online Quiz Management System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,480);
        setLayout(new BorderLayout(8,8));
        List&lt;Question&gt; qs = Arrays.asList(
            new Question("What is 2+2?", new String[]{"3","4","5","6"}, 1),
            new Question("Java is platform ___?", new String[]{"Dependent","Independent","Both","None"}, 1),
            new Question("JVM stands for?", new String[]{"Java Visual Machine","Java Virtual Machine","Just VM","None"},
 1)
        );
        quiz = new Quiz(qs);
        out = new JTextArea(3,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 13));
        optionsPanel = new JPanel(new GridLayout(5,1,4,4));
        optionsPanel.setBorder(BorderFactory.createTitledBorder("Question"));
        JButton nextBtn = new JButton("Submit Answer / Next");
        JButton scoreBtn = new JButton("Get Score");
        JButton exitBtn = new JButton("Cancel / Exit");
        JPanel btnP = new JPanel(); btnP.add(nextBtn); btnP.add(scoreBtn); btnP.add(exitBtn);
        add(new JScrollPane(out), BorderLayout.NORTH);
        add(optionsPanel, BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        showQuestion();
        nextBtn.addActionListener(e -&gt; {
            try {
                int chosen = getSelected();
                quiz.answer(chosen);
                if (!quiz.hasNext()) out.append("
All questions answered. Click Get Score.
");
                else showQuestion();
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        scoreBtn.addActionListener(e -&gt; {
            try { out.setText("Your Score: " + quiz.calculateScore() + "/100"); }
            catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    private void showQuestion() {
        optionsPanel.removeAll(); bg.clearSelection();
        Question q = quiz.currentQuestion();
        optionsPanel.setBorder(BorderFactory.createTitledBorder(q.getText()));
        for (int i = 0; i &lt; q.getOptions().length; i++) {
            JRadioButton rb = new JRadioButton(q.getOptions()[i]);
            rb.setActionCommand(String.valueOf(i));
            bg.add(rb); optionsPanel.add(rb);
        }
        optionsPanel.revalidate(); optionsPanel.repaint();
    }
    private int getSelected() {
        ButtonModel sel = bg.getSelection();
        if (sel == null) throw new IllegalArgumentException("Please select an answer.");
        return Integer.parseInt(sel.getActionCommand());
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q13. Customer Feedback and Rating System

```java
package com.feedback.system;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Reviewable {
    void addFeedback(String customerId, int rating, String review);
    double getAverageRating();
}
class Feedback {
    private final String customerId;
    private final int rating;
    private final String review;
    public Feedback(String customerId, int rating, String review) {
        if (rating &lt; 1 || rating &gt; 5)
            throw new IllegalArgumentException("Rating must be between 1 and 5.");
        if (review == null) throw new NullPointerException("Review cannot be null.");
        this.customerId = customerId; this.rating = rating; this.review = review;
    }
    public String getCustomerId() { return customerId; }
    public int getRating() { return rating; }
    @Override public String toString() { return customerId + " | " + rating + "/5 | " + review; }
}
class Product implements Reviewable {
    private final String productName;
    private final List&lt;Feedback&gt; feedbacks = new ArrayList&lt;&gt;();
    private final Set&lt;String&gt; reviewedBy = new HashSet&lt;&gt;();
    public Product(String productName) { this.productName = productName; }
    @Override public void addFeedback(String customerId, int rating, String review) {
        if (!reviewedBy.add(customerId))
            throw new IllegalStateException("Duplicate feedback from " + customerId);
        feedbacks.add(new Feedback(customerId, rating, review));
    }
    @Override public double getAverageRating() {
        return feedbacks.stream().mapToInt(f -&gt; f.getRating()).average().orElse(0.0);
    }
    public String allFeedback() {
        StringBuilder sb = new StringBuilder("Product: " + productName + "
");
"));
    }
}
        feedbacks.forEach(f -&gt; sb.append(f).append("
        sb.append("Avg Rating: ").append(String.format("%.2f", getAverageRating()));
        return sb.toString();
public class Main extends JFrame {
    private JTextField custF, ratingF, reviewF;
    private JTextArea out;
    private final Product product = new Product("Java Textbook");
    public Main() {
        super("Customer Feedback and Rating System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(4,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Submit Feedback - Java Textbook"));
        form.add(new JLabel("Customer ID:")); custF = new JTextField(); form.add(custF);
        form.add(new JLabel("Rating (1-5):")); ratingF = new JTextField(); form.add(ratingF);
        form.add(new JLabel("Review:")); reviewF = new JTextField(); form.add(reviewF);
        JButton submitBtn = new JButton("Submit");
        JButton showBtn = new JButton("Show All Feedback");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(submitBtn); form.add(showBtn);
        out = new JTextArea(12,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        submitBtn.addActionListener(e -&gt; {
            try {
                product.addFeedback(custF.getText().trim(),
                        Integer.parseInt(ratingF.getText().trim()),
                        reviewF.getText().trim());
                out.append("Feedback submitted.
");
}
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; out.setText(product.allFeedback()));
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q14. Food Delivery App Simulation

```java
package com.fooddelivery.app;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Deliverable {
    void deliver(int orderId);
}
class Restaurant {
    private final String name;
    private final Map&lt;String,Double&gt; menu = new HashMap&lt;&gt;();
    public Restaurant(String name) { this.name = name; }
    public void addMenuItem(String item, double price) {
        if (price &lt; 0) throw new IllegalArgumentException("Price cannot be negative.");
        menu.put(item, price);
    }
    public double getPrice(String item) {
        if (!menu.containsKey(item)) throw new NoSuchElementException("Item not in menu: " + item);
        return menu.get(item);
    }
    public String getName() { return name; }
}
class Order {
    private static int counter = 0;
    private final int orderId;
    private final String itemName;
    private final double totalPrice;
    private String status;
    public Order(String itemName, double totalPrice) {
        if (itemName == null || itemName.isBlank())
            throw new IllegalArgumentException("Invalid order format: item name missing.");
        this.orderId = ++counter;
        this.itemName = itemName; this.totalPrice = totalPrice;
        this.status = "PLACED";
    }
    public int getOrderId() { return orderId; }
    public void setStatus(String s) { this.status = s; }
    @Override public String toString() {
        return "Order#" + orderId + " | " + itemName + " | Rs." + totalPrice + " | " + status;
    }
}
class DeliveryPerson implements Deliverable {
    private final String name;
    private boolean available = true;
    private final List&lt;Order&gt; delivered = new ArrayList&lt;&gt;();
    public DeliveryPerson(String name) { this.name = name; }
    @Override public void deliver(int orderId) {
        if (!available)
            throw new IllegalStateException("Delivery person " + name + " is unavailable.");
        available = false;
    }
    public void complete(Order o) { o.setStatus("DELIVERED"); delivered.add(o); available = true; }
    public boolean isAvailable() { return available; }
    public String getName() { return name; }
}
public class Main extends JFrame {
    private JTextField itemF, dpF;
    private JTextArea out;
    private final Restaurant resto = new Restaurant("Spice Garden");
    private final List&lt;Order&gt; orders = new ArrayList&lt;&gt;();
    private final List&lt;DeliveryPerson&gt; dps = new ArrayList&lt;&gt;();
    public Main() {
        super("Food Delivery App");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,480);
        setLayout(new BorderLayout(8,8));
        // Pre-fill menu
        resto.addMenuItem("Biryani", 200); resto.addMenuItem("Pizza", 350);
        resto.addMenuItem("Burger", 150); resto.addMenuItem("Pasta", 180);
        JPanel form = new JPanel(new GridLayout(3,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Spice Garden - Place Order"));
        form.add(new JLabel("Item (Biryani/Pizza/Burger/Pasta):")); itemF = new JTextField(); form.add(itemF);
        form.add(new JLabel("Delivery Person Name:")); dpF = new JTextField(); form.add(dpF);
        JButton orderBtn = new JButton("Place Order");
        JButton showBtn = new JButton("Show Orders");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(orderBtn); form.add(showBtn);
        out = new JTextArea(14,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        orderBtn.addActionListener(e -&gt; {
            try {
                String item = itemF.getText().trim();
                String dpName = dpF.getText().trim();
                double price = resto.getPrice(item);
                Order o = new Order(item, price);
                orders.add(o);
                DeliveryPerson dp = dps.stream().filter(d -&gt; d.getName().equals(dpName) &amp;&amp; d.isAvailable())
                        .findFirst().orElse(null);
                if (dp == null) { dp = new DeliveryPerson(dpName); dps.add(dp); }
                dp.deliver(o.getOrderId());
                o.setStatus("OUT FOR DELIVERY");
                dp.complete(o);
                out.append(o + "
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- Orders -
"); orders.forEach(o -&gt; out.append(o + "
"));
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q15. Parcel Tracking System

```java
package com.parcel.tracking;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Trackable {
    void updateLocation(String location);
    String getStatus();
}
class Location {
    private final String city;
    private final long timestamp;
    public Location(String city) {
        if (city == null || city.isBlank())
            throw new IllegalArgumentException("Location city cannot be empty.");
        this.city = city; this.timestamp = System.currentTimeMillis();
    }
    @Override public String toString() { return city + " @ " + new java.util.Date(timestamp); }
}
class Parcel implements Trackable {
    private final String trackingNo;
    private final List&lt;Location&gt; trail = new ArrayList&lt;&gt;();
    private String status = "REGISTERED";
    public Parcel(String trackingNo) {
        if (trackingNo == null || !trackingNo.matches("[A-Z]{2}\d{6}"))
            throw new IllegalArgumentException("Invalid tracking number. Use format: AB123456");
        this.trackingNo = trackingNo;
    }
    @Override public void updateLocation(String city) {
        if (city == null) throw new NullPointerException("Location update cannot be null.");
        trail.add(new Location(city));
        status = "IN TRANSIT - " + city;
    }
    @Override public String getStatus() { return trackingNo + " | " + status; }
    public String fullTrail() {
        StringBuilder sb = new StringBuilder("Parcel: " + trackingNo + "
");
");
"));
}
        if (trail.isEmpty()) sb.append("No location updates yet.
        else trail.forEach(l -&gt; sb.append("  -&gt; ").append(l).append("
        sb.append("Status: ").append(status);
        return sb.toString();
    }
public class Main extends JFrame {
    private JTextField trackF, cityF;
    private JTextArea out;
    private final Map&lt;String,Parcel&gt; parcels = new HashMap&lt;&gt;();
    public Main() {
        super("Parcel Tracking System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(3,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Parcel Tracking (Tracking No: AB123456 format)"));
        form.add(new JLabel("Tracking No:")); trackF = new JTextField(); form.add(trackF);
        form.add(new JLabel("Update City:")); cityF = new JTextField(); form.add(cityF);
        JButton regBtn = new JButton("Register Parcel");
        JButton updateBtn = new JButton("Update Location");
        JButton statusBtn = new JButton("Track");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(regBtn); form.add(updateBtn);
        out = new JTextArea(12,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(statusBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        regBtn.addActionListener(e -&gt; {
            try {
                Parcel p = new Parcel(trackF.getText().trim().toUpperCase());
                parcels.put(p.getStatus().split(" ")[0], p);
                out.append("Parcel registered: " + trackF.getText().trim().toUpperCase() + "
");
");
}
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        updateBtn.addActionListener(e -&gt; {
            try {
                Parcel p = parcels.get(trackF.getText().trim().toUpperCase());
                if (p == null) throw new NoSuchElementException("Parcel not found.");
                p.updateLocation(cityF.getText().trim());
                out.append("Location updated.
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        statusBtn.addActionListener(e -&gt; {
            Parcel p = parcels.get(trackF.getText().trim().toUpperCase());
            out.setText(p != null ? p.fullTrail() : "Parcel not found.");
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```

---

## Q16. Electricity Bill Generator

```java
package com.utility.billing;
import javax.swing.*;
import java.awt.*;
import java.sql.*;
interface Billable {
    double calculateBill();
}
class Meter {
    private final String meterId;
    private final double previousReading, currentReading;
    public Meter(String meterId, double prev, double curr) {
        if (prev &lt; 0 || curr &lt; 0) throw new IllegalArgumentException("Meter readings cannot be negative.");
        if (curr &lt; prev) throw new IllegalArgumentException("Invalid meter readings: current &lt; previous.");
        this.meterId = meterId; this.previousReading = prev; this.currentReading = curr;
    }
    public double unitsConsumed() { return currentReading - previousReading; }
    public String getMeterId() { return meterId; }
}
class Bill implements Billable {
    private final Meter meter;
    public Bill(Meter meter) { this.meter = meter; }
    @Override public double calculateBill() {
        double units = meter.unitsConsumed();
        if (units &lt; 0) throw new IllegalStateException("Negative units — slab mismatch.");
        double bill = 0;
        if (units &lt;= 100) bill = units * 2.0;
        else if (units &lt;= 300) bill = 100*2.0 + (units-100)*4.0;
        else bill = 100*2.0 + 200*4.0 + (units-300)*6.0;
        return bill + 50; // fixed charges
    }
    public String generateBill(String customerName) {
        double units = meter.unitsConsumed();
        return "Customer: " + customerName + "
Meter ID: " + meter.getMeterId()
               + "
Units Consumed: " + units
               + "
Total Bill: Rs." + String.format("%.2f", calculateBill());
    }
}
class Customer {
    private final String name, customerId;
    public Customer(String customerId, String name) {
        this.customerId = customerId; this.name = name;
    }
    public String getName() { return name; }
}
public class Main extends JFrame {
    private JTextField custF, meterF, prevF, currF;
    private JTextArea out;
    public Main() {
        super("Electricity Bill Generator");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(5,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Bill Details"));
        form.add(new JLabel("Customer Name:")); custF = new JTextField(); form.add(custF);
        form.add(new JLabel("Meter ID:")); meterF = new JTextField(); form.add(meterF);
        form.add(new JLabel("Previous Reading:")); prevF = new JTextField(); form.add(prevF);
        form.add(new JLabel("Current Reading:")); currF = new JTextField(); form.add(currF);
        JButton genBtn = new JButton("Generate Bill");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(genBtn); form.add(exitBtn);
        out = new JTextArea(11,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 13));
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        genBtn.addActionListener(e -&gt; {
            try {
                Meter m = new Meter(meterF.getText().trim(),
                        Double.parseDouble(prevF.getText().trim()),
                        Double.parseDouble(currF.getText().trim()));
                Bill b = new Bill(m);
                out.setText(b.generateBill(custF.getText().trim()));
                // JDBC save
                try (Connection con = DriverManager.getConnection(
                        "jdbc:mysql://localhost:3306/utility","root","password");
                     PreparedStatement ps = con.prepareStatement(
                        "INSERT INTO bills(customer,units,amount) VALUES(?,?,?)")) {
                    ps.setString(1, custF.getText().trim());
                    ps.setDouble(2, m.unitsConsumed());
                    ps.setDouble(3, b.calculateBill());
                    ps.executeUpdate();
                } catch (SQLException ex) { out.append("
[DB] " + ex.getMessage()); }
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
Note: JDBC: Create the DB table manually. For Q1: CREATE TABLE students(name VARCHAR(50), roll_no INT, gpa
DOUBLE, attendance INT); Update DB URL/credentials in the code
```

---

## Q17. Event Management System

```java
package com.events.manager;
import javax.swing.*;
import java.awt.*;
import java.time.LocalDate;
import java.util.*;
interface Schedulable {
    void schedule(String eventName, String date, int capacity);
}
class Venue {
    private final String venueName;
    private final int maxCapacity;
    public Venue(String name, int cap) { this.venueName = name; this.maxCapacity = cap; }
    public String getVenueName() { return venueName; }
    public int getMaxCapacity() { return maxCapacity; }
}
class Attendee {
    private final String name, email;
    public Attendee(String name, String email) {
        this.name = name; this.email = email;
    }
    public String getName() { return name; }
}
class Event implements Schedulable {
    private final String eventId;
    private String eventName, date;
    private int capacity;
    private final List&lt;Attendee&gt; attendees = new ArrayList&lt;&gt;();
    private final Set&lt;String&gt; bookedDates = new HashSet&lt;&gt;();
    public Event(String eventId) { this.eventId = eventId; }
    @Override public void schedule(String name, String date, int capacity) {
        if (bookedDates.contains(date))
            throw new IllegalStateException("Double booking on date: " + date);
        try { LocalDate.parse(date); } catch (Exception ex) {
            throw new IllegalArgumentException("Invalid date format. Use YYYY-MM-DD.");
        }
        bookedDates.add(date);
        this.eventName = name; this.date = date; this.capacity = capacity;
    }
    public void register(Attendee a) {
        if (attendees.size() &gt;= capacity)
            throw new IllegalStateException("Event overbooking: no slots left.");
        attendees.add(a);
    }
    @Override public String toString() {
        return "[" + eventId + "] " + eventName + " on " + date
               + " | Registered: " + attendees.size() + "/" + capacity;
    }
}
public class Main extends JFrame {
    private JTextField eidF, enameF, dateF, capF, attNameF;
    private JTextArea out;
    private final Map&lt;String,Event&gt; events = new HashMap&lt;&gt;();
    public Main() {
        super("Event Management System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,480);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(6,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Manage Events"));
        form.add(new JLabel("Event ID:")); eidF = new JTextField(); form.add(eidF);
        form.add(new JLabel("Event Name:")); enameF = new JTextField(); form.add(enameF);
        form.add(new JLabel("Date (YYYY-MM-DD):")); dateF = new JTextField(); form.add(dateF);
        form.add(new JLabel("Capacity:")); capF = new JTextField(); form.add(capF);
        form.add(new JLabel("Attendee Name:")); attNameF = new JTextField(); form.add(attNameF);
        JButton schedBtn = new JButton("Schedule Event");
        JButton regBtn = new JButton("Register Attendee");
        JButton showBtn = new JButton("Show Events");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(schedBtn); form.add(regBtn);
        out = new JTextArea(11,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(showBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        schedBtn.addActionListener(e -&gt; {
            try {
                Event ev = events.computeIfAbsent(eidF.getText().trim(), Event::new);
                ev.schedule(enameF.getText().trim(), dateF.getText().trim(),
                        Integer.parseInt(capF.getText().trim()));
                out.append("Event scheduled: " + ev + "
");
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        regBtn.addActionListener(e -&gt; {
            try {
                Event ev = events.get(eidF.getText().trim());
                if (ev == null) throw new NoSuchElementException("Event not found.");
                ev.register(new Attendee(attNameF.getText().trim(), ""));
                out.append("Attendee registered.
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; {
            out.setText("-- Events -
"); events.values().forEach(ev -&gt; out.append(ev + "
"));
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}

```

---

## Q18. Hotel Room Booking System

```java
package com.hotel.booking;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Bookable {
    boolean book(String customerId, int nights);
    boolean cancel(String customerId);
}
class Room implements Bookable {
    private final int roomNo;
    private final String type;
    private final double pricePerNight;
    private boolean occupied = false;
    private String bookedBy = null;
    public Room(int roomNo, String type, double price) {
        this.roomNo = roomNo; this.type = type; this.pricePerNight = price;
    }
    public boolean isOccupied() { return occupied; }
    public int getRoomNo() { return roomNo; }
    public String getType() { return type; }
    @Override public boolean book(String customerId, int nights) {
        if (customerId == null || customerId.isBlank())
            throw new IllegalArgumentException("Invalid customer info.");
        if (occupied) throw new IllegalStateException("Room " + roomNo + " is already booked.");
        if (nights &lt;= 0) throw new IllegalArgumentException("Nights must be positive.");
        occupied = true; bookedBy = customerId;
        return true;
    }
    @Override public boolean cancel(String customerId) {
        if (!occupied || !customerId.equals(bookedBy))
            return false;
        occupied = false; bookedBy = null; return true;
    }
    public double totalCost(int nights) { return nights * pricePerNight; }
    @Override public String toString() {
        return "Room " + roomNo + " [" + type + "] Rs." + pricePerNight + "/night | "
               + (occupied ? "Occupied by " + bookedBy : "Available");
    }
}
public class Main extends JFrame {
    private JTextField roomF, custF, nightsF;
    private JTextArea out;
    private final List&lt;Room&gt; rooms = new ArrayList&lt;&gt;();
    public Main() {
        super("Hotel Room Booking System");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(540,480);
        setLayout(new BorderLayout(8,8));
        // Initialize rooms
        rooms.add(new Room(101,"Standard",1500));
        rooms.add(new Room(102,"Deluxe",3000));
        rooms.add(new Room(103,"Suite",6000));
        rooms.add(new Room(104,"Standard",1500));
        JPanel form = new JPanel(new GridLayout(4,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Hotel Booking"));
        form.add(new JLabel("Room No:")); roomF = new JTextField(); form.add(roomF);
        form.add(new JLabel("Customer ID:")); custF = new JTextField(); form.add(custF);
        form.add(new JLabel("Nights:")); nightsF = new JTextField(); form.add(nightsF);
        JButton bookBtn = new JButton("Book");
        JButton cancelBtn = new JButton("Cancel Booking");
        JButton showBtn = new JButton("Show Rooms");
        JButton exitBtn = new JButton("Exit");
        form.add(bookBtn); form.add(cancelBtn);
        out = new JTextArea(12,44);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        JPanel btnP = new JPanel(); btnP.add(showBtn); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        bookBtn.addActionListener(e -&gt; {
            try {
                int rno = Integer.parseInt(roomF.getText().trim());
                int nights = Integer.parseInt(nightsF.getText().trim());
                Room r = rooms.stream().filter(x -&gt; x.getRoomNo()==rno).findFirst()
                        .orElseThrow(() -&gt; new NoSuchElementException("Room not found."));
                r.book(custF.getText().trim(), nights);
                out.append("Booked Room " + rno + " for " + nights + " nights | Total: Rs."
                        + r.totalCost(nights) + "
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        cancelBtn.addActionListener(e -&gt; {
            try {
                int rno = Integer.parseInt(roomF.getText().trim());
                Room r = rooms.stream().filter(x -&gt; x.getRoomNo()==rno).findFirst()
                        .orElseThrow(() -&gt; new NoSuchElementException("Room not found."));
                boolean ok = r.cancel(custF.getText().trim());
                out.append(ok ? "Booking cancelled.
" : "Cancellation failed.
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        showBtn.addActionListener(e -&gt; { out.setText("-- Rooms -
"); rooms.forEach(r -&gt; out.append(r+"
")); });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}

```

---

## Q19. Loan Eligibility Checker

```java
package com.finance.loans;
import javax.swing.*;
import java.awt.*;
interface Eligible {
    boolean checkEligibility();
}
class CreditScore {
    private final int score;
    public CreditScore(int score) {
        if (score &lt; 300 || score &gt; 900)
            throw new IllegalArgumentException("Invalid credit score. Range: 300-900.");
        this.score = score;
    }
    public int getScore() { return score; }
}
class Applicant implements Eligible {
    private final String name;
    private final int age;
    private final double annualIncome;
    private final CreditScore creditScore;
    public Applicant(String name, int age, double income, CreditScore cs) {
        if (name == null || name.isBlank())
            throw new IllegalArgumentException("Incomplete applicant data: name missing.");
        if (age &lt; 18 || age &gt; 65)
            throw new IllegalArgumentException("Age must be between 18 and 65.");
        if (income &lt;= 0)
            throw new IllegalArgumentException("Income must be positive.");
        this.name = name; this.age = age; this.annualIncome = income; this.creditScore = cs;
    }
    @Override public boolean checkEligibility() {
        return age &gt;= 21 &amp;&amp; annualIncome &gt;= 300000 &amp;&amp; creditScore.getScore() &gt;= 650;
    }
    public String report() {
        return "Applicant: " + name + "
Age: " + age
               + "
Annual Income: Rs." + annualIncome
               + "
Credit Score: " + creditScore.getScore()
               + "
Eligibility: " + (checkEligibility() ? "ELIGIBLE" : "NOT ELIGIBLE");
    }
}
class Loan {
    private final double amount;
    private final int tenureYears;
    private final double interestRate;
    public Loan(double amount, int tenure, double rate) {
        this.amount = amount; this.tenureYears = tenure; this.interestRate = rate;
    }
    public double calculateEMI() {
        double r = interestRate / (12 * 100);
        int n = tenureYears * 12;
        return (amount * r * Math.pow(1+r,n)) / (Math.pow(1+r,n) - 1);
    }
    @Override public String toString() {
        return "Loan: Rs." + amount + " | " + tenureYears + " yrs @ " + interestRate
               + "% | EMI: Rs." + String.format("%.2f", calculateEMI());
    }
}
public class Main extends JFrame {
    private JTextField nameF, ageF, incomeF, creditF, amtF, tenureF, rateF;
    private JTextArea out;
    public Main() {
        super("Loan Eligibility Checker");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,500);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(8,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Applicant &amp; Loan Details"));
        form.add(new JLabel("Name:")); nameF = new JTextField(); form.add(nameF);
        form.add(new JLabel("Age:")); ageF = new JTextField(); form.add(ageF);
        form.add(new JLabel("Annual Income (Rs):")); incomeF = new JTextField(); form.add(incomeF);
        form.add(new JLabel("Credit Score (300-900):")); creditF = new JTextField(); form.add(creditF);
        form.add(new JLabel("Loan Amount (Rs):")); amtF = new JTextField(); form.add(amtF);
        form.add(new JLabel("Tenure (Years):")); tenureF = new JTextField(); form.add(tenureF);
        form.add(new JLabel("Interest Rate (%):")); rateF = new JTextField(); form.add(rateF);
        JButton checkBtn = new JButton("Check Eligibility");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(checkBtn); form.add(exitBtn);
        out = new JTextArea(9,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 12));
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        checkBtn.addActionListener(e -&gt; {
            try {
                CreditScore cs = new CreditScore(Integer.parseInt(creditF.getText().trim()));
                Applicant ap = new Applicant(nameF.getText().trim(),
                        Integer.parseInt(ageF.getText().trim()),
                        Double.parseDouble(incomeF.getText().trim()), cs);
                Loan loan = new Loan(Double.parseDouble(amtF.getText().trim()),
                        Integer.parseInt(tenureF.getText().trim()),
                        Double.parseDouble(rateF.getText().trim()));
                out.setText(ap.report() + "
" + loan);
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}

```

---

## Q20. Weather Forecasting Application

```java
package com.weather.app;
import javax.swing.*;
import java.awt.*;
import java.util.*;
interface Forecastable {
    double averageTemperature();
    double averageHumidity();
    String generateForecast();
}
class WeatherData {
    private final double temperature;
    private final double humidity;
    public WeatherData(double temperature, double humidity) {
        if (Double.isNaN(temperature) || Double.isInfinite(temperature))
            throw new IllegalArgumentException("Invalid temperature format.");
        if (humidity &lt; 0 || humidity &gt; 100)
            throw new IllegalArgumentException("Humidity must be 0-100%.");
        this.temperature = temperature; this.humidity = humidity;
    }
    public double getTemperature() { return temperature; }
    public double getHumidity() { return humidity; }
    @Override public String toString() {
        return "Temp=" + temperature + "°C, Humidity=" + humidity + "%";
    }
}
class City {
    private final String cityName;
    private final List&lt;WeatherData&gt; data = new ArrayList&lt;&gt;();
    public City(String name) {
        if (name == null) throw new NullPointerException("City name cannot be null.");
        this.cityName = name;
    }
    public void addData(WeatherData wd) {
        if (wd == null) throw new NullPointerException("Weather data null reference.");
        data.add(wd);
    }
    public String getCityName() { return cityName; }
    public List&lt;WeatherData&gt; getData() { return data; }
}
class Forecast implements Forecastable {
    private final City city;
    public Forecast(City city) { this.city = city; }
    @Override public double averageTemperature() {
        if (city.getData().isEmpty()) throw new IllegalStateException("Missing data entries for temperature.");
        return city.getData().stream().mapToDouble(WeatherData::getTemperature).average().orElse(0);
    }
    @Override public double averageHumidity() {
        if (city.getData().isEmpty()) throw new IllegalStateException("Missing data entries for humidity.");
        return city.getData().stream().mapToDouble(WeatherData::getHumidity).average().orElse(0);
    }
    @Override public String generateForecast() {
        double avgT = averageTemperature(), avgH = averageHumidity();
        String condition = avgT &gt; 35 ? "Hot &amp; Sunny" : avgT &gt; 25 ? "Warm" : avgT &gt; 15 ? "Pleasant" : 
"Cold";
        String rain = avgH &gt; 80 ? "High chance of rain." : avgH &gt; 60 ? "Moderate humidity." : "Dry conditions.";
        return "City: " + city.getCityName()
               + "
Avg Temp: " + String.format("%.1f", avgT) + "°C"
               + "
Avg Humidity: " + String.format("%.1f", avgH) + "%"
               + "
Condition: " + condition + " | " + rain;
    }
}
public class Main extends JFrame {
    private JTextField cityF, tempF, humF;
    private JTextArea out;
    private final Map&lt;String,City&gt; cities = new HashMap&lt;&gt;();
    public Main() {
        super("Weather Forecasting Application");
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setSize(520,440);
        setLayout(new BorderLayout(8,8));
        JPanel form = new JPanel(new GridLayout(4,2,6,6));
        form.setBorder(BorderFactory.createTitledBorder("Weather Data Entry"));
        form.add(new JLabel("City:")); cityF = new JTextField(); form.add(cityF);
        form.add(new JLabel("Temperature (°C):")); tempF = new JTextField(); form.add(tempF);
        form.add(new JLabel("Humidity (%):")); humF = new JTextField(); form.add(humF);
        JButton addBtn = new JButton("Add Reading");
        JButton forecastBtn = new JButton("Generate Forecast");
        JButton exitBtn = new JButton("Cancel / Exit");
        form.add(addBtn); form.add(forecastBtn);
        out = new JTextArea(12,42);
        out.setEditable(false);
        out.setFont(new Font("Monospaced", Font.PLAIN, 13));
        JPanel btnP = new JPanel(); btnP.add(exitBtn);
        add(form, BorderLayout.NORTH);
        add(new JScrollPane(out), BorderLayout.CENTER);
        add(btnP, BorderLayout.SOUTH);
        addBtn.addActionListener(e -&gt; {
            try {
                String cname = cityF.getText().trim();
                cities.putIfAbsent(cname, new City(cname));
                WeatherData wd = new WeatherData(
                        Double.parseDouble(tempF.getText().trim()),
                        Double.parseDouble(humF.getText().trim()));
                cities.get(cname).addData(wd);
                out.append("Data added: " + wd + "
");
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        forecastBtn.addActionListener(e -&gt; {
            try {
                City c = cities.get(cityF.getText().trim());
                if (c == null) throw new NoSuchElementException("No data for this city.");
                out.setText(new Forecast(c).generateForecast());
            } catch (Exception ex) { JOptionPane.showMessageDialog(this, ex.getMessage()); }
        });
        exitBtn.addActionListener(e -&gt; System.exit(0));
        setLocationRelativeTo(null); setVisible(true);
    }
    public static void main(String[] args) { SwingUtilities.invokeLater(Main::new); }
}
```
