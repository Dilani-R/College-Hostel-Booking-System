    import java.awt.*;
    import java.awt.event.*;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;

    class CollegeHostelBookingSystem extends Frame {
    CardLayout card;
    Panel mainPanel;

    // Global fields for user data
    TextField nameField, rollField, usernameField, passwordField, rePasswordField;
    Checkbox specialChildCheckbox;
    Choice roomChoice, acChoice;

    // Special and general rooms
    ArrayList<String> specialRooms = new ArrayList<>();
    ArrayList<String> generalRooms = new ArrayList<>();

    // Login fields
    TextField loginUsername, loginPassword, adminUsername, adminPassword;

    // List to store booking details
    ArrayList<String> bookings = new ArrayList<>();

    // Constructor
    public CollegeHostelBookingSystem() {
        setTitle("College Hostel Booking System");
        setSize(600, 400);
        setLayout(new BorderLayout());

        // Initialize room lists
        initializeRooms();

        card = new CardLayout();
        mainPanel = new Panel(card);

        // Home Page
        Panel homePanel = new Panel(new GridLayout(3, 1, 10, 10));
        Button studentButton = new Button("Student Registration/Login");
        Button adminButton = new Button("Admin Login");
        homePanel.add(new Label("Welcome to the College Hostel Booking System"));
        homePanel.add(studentButton);
        homePanel.add(adminButton);

        studentButton.addActionListener(e -> card.show(mainPanel, "Registration"));
        adminButton.addActionListener(e -> card.show(mainPanel, "AdminLogin"));

        mainPanel.add(homePanel, "Home");

        // Registration Page
        Panel registrationPanel = new Panel(new GridLayout(8, 2, 10, 10));
        registrationPanel.add(new Label("Name:"));
        nameField = new TextField();
        registrationPanel.add(nameField);

        registrationPanel.add(new Label("Roll Number:"));
        rollField = new TextField();
        registrationPanel.add(rollField);

        registrationPanel.add(new Label("Username:"));
        usernameField = new TextField();
        registrationPanel.add(usernameField);

        registrationPanel.add(new Label("Password:"));
        passwordField = new TextField();
        passwordField.setEchoChar('*');
        registrationPanel.add(passwordField);

        registrationPanel.add(new Label("Re-enter Password:"));
        rePasswordField = new TextField();
        rePasswordField.setEchoChar('*');
        registrationPanel.add(rePasswordField);

        registrationPanel.add(new Label("Special Child (Yes/No):"));
        specialChildCheckbox = new Checkbox();
        registrationPanel.add(specialChildCheckbox);

        Button registerButton = new Button("Submit");
        registrationPanel.add(registerButton);

        registerButton.addActionListener(e -> {
            if (passwordField.getText().equals(rePasswordField.getText())) {
                card.show(mainPanel, "Login");
            } else {
                showMessage("Passwords do not match!");
            }
        });

        mainPanel.add(registrationPanel, "Registration");

        // Login Page for Students
        Panel loginPanel = new Panel(new GridLayout(4, 2, 10, 10));
        loginPanel.add(new Label("Username:"));
        loginUsername = new TextField();
        loginPanel.add(loginUsername);

        loginPanel.add(new Label("Password:"));
        loginPassword = new TextField();
        loginPassword.setEchoChar('*');
        loginPanel.add(loginPassword);

        Button loginButton = new Button("Login");
        loginPanel.add(loginButton);

        loginButton.addActionListener(e -> {
            if (loginUsername.getText().equals(usernameField.getText())
                    && loginPassword.getText().equals(passwordField.getText())) {
                card.show(mainPanel, "RoomAllocation");
            } else {
                showMessage("Invalid login credentials!");
            }
        });

        mainPanel.add(loginPanel, "Login");

        // Room Allocation Page
        Panel roomAllocationPanel = new Panel(new GridLayout(6, 2, 10, 10));
        roomAllocationPanel.add(new Label("Select Room:"));

        roomChoice = new Choice();
        roomAllocationPanel.add(roomChoice);

        roomAllocationPanel.add(new Label("AC or Non-AC:"));
        acChoice = new Choice();
        acChoice.add("AC");
        acChoice.add("Non-AC");
        roomAllocationPanel.add(acChoice);

        Button allocateButton = new Button("Allocate Room");
        roomAllocationPanel.add(allocateButton);

        allocateButton.addActionListener(e -> {
            String room = roomChoice.getSelectedItem();
            String studentName = nameField.getText();
            String date = new SimpleDateFormat("dd/MM/yyyy HH:mm:ss").format(new Date());

            // Add booking
            bookings.add("Name: " + studentName + ", Room: " + room + ", Date: " + date);

            // Remove the allocated room from the available list
            if (specialChildCheckbox.getState()) {
                specialRooms.remove(room);
            } else {
                generalRooms.remove(room);
            }

            // Notify the user
            showMessage("Room Allocated: " + room);
        });

        Button logoffButton = new Button("Log Off");
        roomAllocationPanel.add(logoffButton);

        logoffButton.addActionListener(e -> card.show(mainPanel, "Home"));

        mainPanel.add(roomAllocationPanel, "RoomAllocation");

        // Admin Login Page
        Panel adminLoginPanel = new Panel(new GridLayout(4, 2, 10, 10));
        adminLoginPanel.add(new Label("Admin Username:"));
        adminUsername = new TextField();
        adminLoginPanel.add(adminUsername);

        adminLoginPanel.add(new Label("Admin Password:"));
        adminPassword = new TextField();
        adminPassword.setEchoChar('*');
        adminLoginPanel.add(adminPassword);

        Button adminLoginButton = new Button("Login");
        adminLoginPanel.add(adminLoginButton);

        adminLoginButton.addActionListener(e -> {
            if (adminUsername.getText().equals("admin") && adminPassword.getText().equals("admin123")) {
                card.show(mainPanel, "AdminPanel");
            } else {
                showMessage("Invalid admin credentials!");
            }
        });

        mainPanel.add(adminLoginPanel, "AdminLogin");

        // Admin Panel
        Panel adminPanel = new Panel(new BorderLayout());
        TextArea adminArea = new TextArea("Room Bookings:\n", 20, 50);
        adminPanel.add(adminArea, BorderLayout.CENTER);

        Button refreshButton = new Button("Refresh");
        adminPanel.add(refreshButton, BorderLayout.SOUTH);

        refreshButton.addActionListener(e -> {
            adminArea.setText("Room Bookings:\n");
            for (String booking : bookings) {
                adminArea.append(booking + "\n");
            }
        });

        mainPanel.add(adminPanel, "AdminPanel");

        // Adjust available rooms based on user type
        roomAllocationPanel.addComponentListener(new ComponentAdapter() {
            @Override
            public void componentShown(ComponentEvent e) {
                roomChoice.removeAll();
                if (specialChildCheckbox.getState()) {
                    for (String room : specialRooms) {
                        roomChoice.add(room);
                    }
                } else {
                    for (String room : generalRooms) {
                        roomChoice.add(room);
                    }
                }
            }
        });

        card.show(mainPanel, "Home");

        add(mainPanel, BorderLayout.CENTER);

        addWindowListener(new WindowAdapter() {
            public void windowClosing(WindowEvent e) {
                dispose();
            }
        });

        setVisible(true);
    }

    private void initializeRooms() {
        // Initialize room lists
        for (int i = 201; i <= 203; i++) {
            specialRooms.add(String.valueOf(i));
        }
        for (int i = 101; i <= 104; i++) {
            generalRooms.add(String.valueOf(i));
        }
    }

    private void showMessage(String message) {
        Dialog dialog = new Dialog(this, "Message", true);
        dialog.setLayout(new FlowLayout());
        dialog.add(new Label(message));
        Button okButton = new Button("OK");
        dialog.add(okButton);
        okButton.addActionListener(e -> dialog.dispose());
        dialog.setSize(300, 150);
        dialog.setVisible(true);
    }

    public static void main(String[] args) {
        new CollegeHostelBookingSystem();
    }
}

