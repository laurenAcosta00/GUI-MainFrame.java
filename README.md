/*
Lauren Acosta, CEN 3024C, 3/22/2024
class name: Main
The Main class encapsulates the core functionality of the library management system,
which includes managing books, facilitating user interactions, and ensuring the smooth execution of the program
to achieve the efficiency of managing a library's catalog and borrowing system.
 */

import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.util.Scanner;

import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Welcome to the Library Management System!");

        // Instantiate BookCatalog
        BookCatalog catalog = new BookCatalog();

        // task 1: Ask user for file name and add books from the file
        System.out.print("Enter the file name: ");
        String fileName = scanner.nextLine();
        catalog.addBooksFromFile(fileName);

        // Task 2: Print the database with a message indicating printing is taking place
        System.out.println("Printing the database...");
        catalog.displayBooks();
        System.out.println("Database printing completed.");

        // Task 3: Remove a book by barcode number
        System.out.print("Enter barcode number to remove: ");
        String barcodeToRemove = scanner.nextLine();
        boolean removedByBarcode = catalog.removeBookByBarcode(barcodeToRemove);
        if (removedByBarcode) {
            System.out.println("Book with barcode '" + barcodeToRemove + "' removed successfully.");
        } else {
            System.out.println("Book with barcode '" + barcodeToRemove + "' not found.");
        }

        //  Reprint the updated library
        System.out.println("Updated library after removal:");
        catalog.displayBooks();

        //scanner.close();

        // Task 4: Remove a book by title
        System.out.print("Enter title to remove: ");
        String titleToRemove = scanner.nextLine();
        boolean removedByTitle = catalog.removeBookByTitle(titleToRemove);
        if (removedByTitle) {
            System.out.println("Book '" + titleToRemove + "' removed successfully.");
        } else {
            System.out.println("Book '" + titleToRemove + "' not found.");
        }

        //  Reprint the updated library
        System.out.println("Updated library after removal:");
        catalog.displayBooks();

        // Task 5: Check out a book
        System.out.print("Enter title to check out: ");
        String titleToCheckout = scanner.nextLine();
        catalog.checkOutBook(titleToCheckout);

        System.out.println("Updated library after removal:");
        catalog.displayBooks();

        // Task 6: Check in a book
        System.out.print("Enter title to check in: ");
        String titleToCheckin = scanner.nextLine();
        catalog.checkInBook(titleToCheckin);

        // Display all books after modifications
        System.out.println("Updated Book Catalog:");
        catalog.displayBooks();

        scanner.close();
    }
}
/*
Lauren Acosta, CEN 3024C, 3/22/2024
class name: Book
The Book class represents individual books within the library management system and plays a crucial role in managing the attributes and behaviors of each book.
 */


class Book {
    private int bookID;
    private String title;
    private String author;
    private String barcode;
    private String genre;
    private String status;
    private String dueDate;

    //The purpose of this constructor method is to initialize a new Book object with the provided attributes such as book ID, title, author, genre, and barcode.
    // It creates a new instance of a book with the specified characteristics.
    //int bookID: Represents the unique identifier for the book.
    //String title: Represents the title of the book.
    //String author: Represents the author of the book.
    //String genre: Represents the genre or category of the book.
    //String barcode: Represents the barcode number associated with the book.

    public Book(int bookID, String title, String author, String genre, String barcode) {
        this.bookID = bookID;
        this.title = title;
        this.author = author;
        this.genre = genre;
        this.barcode = barcode;
        this.status = "available"; // Initialize status to "available"
    }

    // Getters
    public int getBookID() {
        return bookID;
    }

    public String getTitle() {
        return title;
    }

    public String getAuthor() {
        return author;
    }

    public String getGenre() {
        return genre;
    }

    public String getBarcode() {
        return barcode;
    }

    public String getStatus() {
        return status;
    }

    public String getDueDate() {
        return dueDate;
    }

    // Setters
    public void setBookID(int bookID) {
        this.bookID = bookID;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public void setGenre(String genre) {
        this.genre = genre;
    }

    public void setBarcode(String barcode) {
        this.barcode = barcode;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public void setDueDate(String dueDate) {
        this.dueDate = dueDate;
    }

// The purpose of this toString() method is to provide a string representation of a Book object.
// It includes the book's attributes such as book ID, title, author, genre, and barcode, formatted in a specific way.
// The method returns a string containing the concatenation of the book's attributes along with their corresponding values.

    @Override
    public String toString() {
        return "Book{" +
                "bookID=" + bookID +
                ", title=" + title +
                ", author=" + author +
                ", genre=" + genre +
                ", barcode=" + barcode +
                '}';
    }

}
/*
Lauren Acosta, CEN 3024C, 3/22/2024
class name: BookCatalog
the BookCatalog class serves as the backbone for managing the library's collection of books, providing essential functionalities such as adding, removing, checking out, and checking in books.
This class also calculates a checked out books due date and updates the status accordingly.
 */

import java.time.LocalDate;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;
import java.time.format.DateTimeFormatter;

// BookCatalog class managing the collection of books
class BookCatalog {
    private List<Book> books;

    public BookCatalog() {
        this.books = new ArrayList<>();
    }

    // The addBooksFromFile method is responsible for populating the catalog of books by reading book information from a text file.
    // filePath specifies the path to the text file containing book information. The method reads this file to retrieve book details such as book ID, title, author, genre, and barcode.
    // No return value for this method (return type is void). Instead, it populates the catalog of books maintained by the BookCatalog class by adding Book objects created from the data read from the file.
    public void addBooksFromFile(String filePath) {
        try (BufferedReader br = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] bookInfo = line.split(",");
                int bookID = Integer.parseInt(bookInfo[0]);
                String title = bookInfo[1].trim();
                String author = bookInfo[2];
                String genre = bookInfo[3];
                String barcode = bookInfo[4].trim();

                Book book = new Book(bookID, title, author, genre, barcode);
                books.add(book);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // The removeBookByBarcode method serves the purpose of removing a book from the catalog based on its barcode.
    // barcode argument represents the unique identifier of the book that needs to be removed from the catalog. It is used to locate and identify the book entry to be removed.
    // This method returns a boolean value indicating whether the removal operation was successful or not.
    // It returns true if a book with the specified barcode was found and removed successfully, and false if no book with the given barcode was found in the catalog.
    public boolean removeBookByBarcode(String barcode) {
        Iterator<Book> iterator = books.iterator();
        while (iterator.hasNext()) {
            Book book = iterator.next();
            if (book.getBarcode().equals(barcode)) { // Check if the barcode matches
                iterator.remove();
                return true;
            }
        }
        return false; // If no book with the given barcode is found
    }

    // removeBookByTitle method serves the purpose of removing a book from the catalog based on its title.
    // title argument represents the title of the book that needs to be removed from the catalog. It is used to locate and identify the book entry to be removed.
    // The method returns a boolean value indicating whether the removal operation was successful or not.
    // It returns true if a book with the specified title was found and removed successfully, and false if no book with the given title was found in the catalog.
    public boolean removeBookByTitle(String title) {
        Iterator<Book> iterator = books.iterator();
        while (iterator.hasNext()) {
            Book book = iterator.next();
            if (book.getTitle().equals(title.trim())) {
                iterator.remove();
                return true;
            }
        }
        return false;    // If no book with the given title is found
    }

    // The checkOutBook method serves the purpose of checking out a book from the catalog.
    // title argument represents the title of the book that is being checked out. It is used to locate the book entry in the catalog.
    // The method does not have a return value. It performs an action of marking the book as checked out in the catalog. H
    // But it does print a message to determine if it was successful or not.
    public void checkOutBook(String title) {
        Iterator<Book> iterator = books.iterator();
        while (iterator.hasNext()) {
            Book book = iterator.next();
            if (book.getTitle().equalsIgnoreCase(title)) {
                if ("checked out".equals(book.getStatus())) {
                    System.out.println("Error: This book is already checked out.");
                    return;
                }
                // Calculate due date as 4 weeks from the current date
                LocalDate currentDate = LocalDate.now();
                LocalDate dueDate = currentDate.plusWeeks(4);
                book.setStatus("checked out");
                book.setDueDate(dueDate.format(DateTimeFormatter.ofPattern("MMMM dd, yyyy")));
                System.out.println("Book checked out successfully. Due date: " + book.getDueDate());
                System.out.println("Book checked out successfully.");
                return; // Exit the method after successful checkout
            }
        }
        // If the loop completes without finding the book
        System.out.println("Error: Book not found.");
    }

    // The checkInBook method is responsible for marking a book as checked in within the catalog, indicating that it has been returned by the borrower and is available for borrowing again.
    // title argument represents the title of the book that is being checked in. It is used to locate the book entry in the catalog.
    // The method does not have a return value. It performs an action of marking the book as checked in the catalog.
    // it also does print a message to determine if it was successful or not.
    public boolean checkInBook(String title) {
        for (Book book : books) {
            if (book.getTitle().equalsIgnoreCase(title)) {
                if ("checked in".equals(book.getStatus())) {
                    System.out.println("Error: This book is already checked in.");
                    return false;
                }
                // Set status to "checked in"
                book.setStatus("checked in");

                // When a book is checked in, its status is changed to “checked in” and its due date is “null”.
                book.setDueDate(null);

                System.out.println("Book checked in successfully.");
                return true;
            }
        }
        // Book not found in the list, consider it as "checked in"
        System.out.println("Book checked in successfully.");
        return true;
    }

    // displayBooks method is to provide a visual representation of all the books present in the catalog.
    // It allows users or administrators to view the titles, authors, genres, barcodes, and other relevant details of each book.
    // The method does not take any arguments. It operates solely on the internal state of the BookCatalog object.
    // The method does not return any value. It simply prints the details of the books to the output.
    public void displayBooks() {
        for (Book book : books) {
            System.out.println(book);
        }
    }

    // Return all books in the catalog
    public List<Book> getAllBooks() {
        return books;
    }

    // getTotalBooks method simply returns the size of the books list, which represents the total number of books in the catalog.
    public int getTotalBooks() {
        return books.size();
    }

    // The findBookByTitle method iterates through the list of books and returns the book with the matching title.
    //returns NULL if book is not found
    public Book findBookByTitle(String title) {
        for (Book book : books) {
            if (book.getTitle().equalsIgnoreCase(title)) {
                return book;
            }
        }
        return null; // Book not found
    }

    //getBookByTitle provides a convenient way to retrieve a Book object based on its title from a collection of books
    //returns NULL if book is not found
    public Book getBookByTitle(String title) {
        for (Book book : books) {
            if (book.getTitle().equalsIgnoreCase(title)) {
                return book;
            }
        }
        return null; // Return null if the book is not found
    }
}
/*
Lauren Acosta, CEN 3024C, 3/20/2024
class name: MainFrame
My MainFrame.java class serves as the primary interface for users to interact with my LMS application, allowing them to perform various actions related to managing books in the library.
It integrates various functionalities related to managing books in a library, such as loading books from a file, removing books by barcode or title, checking books in and out, and displaying the library database.
 */
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.List;
import javax.swing.JOptionPane;
import java.util.Iterator;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;


public class MainFrame extends JFrame {
    private JTextField fileNameField;
    private JTextArea databaseTextArea;
    private JButton loadFileButton;
    private BookCatalog catalog;

    private JTextField barcodeField;
    private JButton removeByBarcodeButton;
    private JTextField titleField;
    private JButton removeByTitleButton;

    private JTextField checkOutTitleField;
    private JButton checkOutButton;
    private JTextField checkInTitleField;
    private JButton checkInButton;
    private List<Book> books;

    // Method to set preferred sizes for text fields and labels in a container
    private void setPreferredSizes(Container container, int width, int height) {
        for (Component component : container.getComponents()) {
            if (component instanceof JTextField || component instanceof JLabel) {
                component.setPreferredSize(new Dimension(width, height));
            }
        }
    }


    //the layout of the main window of your application using Swing components such as JFrame, JPanel, JButton, JTextField, and JTextArea.
    public MainFrame() {
        setTitle("Library Management System");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        // Initialize book catalog
        catalog = new BookCatalog();

        // Initialize components
        fileNameField = new JTextField(20);
        loadFileButton = new JButton("Load File");
        databaseTextArea = new JTextArea();
        databaseTextArea.setEditable(false);

        // Initialize text fields and buttons for barcode and title removal
        barcodeField = new JTextField(10);
        removeByBarcodeButton = new JButton("Remove by Barcode");
        titleField = new JTextField(20);
        removeByTitleButton = new JButton("Remove by Title");

        // Initialize text fields and buttons for check-out and check-in
        checkOutTitleField = new JTextField(20);
        checkOutButton = new JButton("Check Out");
        checkInTitleField = new JTextField(20);
        checkInButton = new JButton("Check In");

         // Initialize the exit button
        //
        JButton exitButton = new JButton("Exit");
        exitButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int option = JOptionPane.showConfirmDialog(MainFrame.this,
                        "Are you sure you want to exit?", "Exit", JOptionPane.YES_NO_OPTION);
                if (option == JOptionPane.YES_OPTION) {
                    dispose(); // Close the JFrame
                }
            }
        });


        //handles user interactions with the interface components by adding action listeners to buttons and menu items.
        // Add action listener to the load file button
        loadFileButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String fileName = fileNameField.getText();
                loadBooksFromFile(fileName);
            }
        });

        removeByBarcodeButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String barcode = barcodeField.getText();
                removeBookByBarcode(barcode);
            }
        });

        removeByTitleButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String title = titleField.getText();
                removeBookByTitle(title);
            }
        });

        // Add action listeners to the check-out and check-in buttons
        checkOutButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String title = checkOutTitleField.getText();
                checkOutBook(title);
            }
        });

        checkInButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String title = checkInTitleField.getText();
                checkInBook(title);
            }
        });


        // Layout setup for file user input
        JPanel mainPanel = new JPanel(new BorderLayout());
        JPanel inputPanel = new JPanel();

        inputPanel.add(new JLabel("Enter file name: "));
        inputPanel.add(fileNameField);
        inputPanel.add(loadFileButton);
        mainPanel.add(inputPanel, BorderLayout.NORTH);
        mainPanel.add(new JScrollPane(databaseTextArea), BorderLayout.CENTER);
        add(mainPanel);
        // Set main panel to frame content pane
        setContentPane(mainPanel);




        JPanel actionsPanel = new JPanel(new GridLayout(4, 1)); // 4 rows, 2 column

        //actionsPanel.add(new JTextField(10));
        actionsPanel.add(new JLabel("Enter barcode to remove: "));
        actionsPanel.add(barcodeField);
        actionsPanel.add(removeByBarcodeButton);

        actionsPanel.add(new JLabel("Enter title to remove: "));
        actionsPanel.add(titleField);
        actionsPanel.add(removeByTitleButton);


        mainPanel.add(exitButton, BorderLayout.SOUTH);

        // Set main panel to frame content pane
        setContentPane(mainPanel);
        // Make the frame visible
        setVisible(true);

        // Check components
        actionsPanel.add(new JLabel("Enter title to check in: "));
        actionsPanel.add(checkInTitleField);
        actionsPanel.add(checkInButton);

        actionsPanel.add(new JLabel("Enter title to check out: "));
        actionsPanel.add(checkOutTitleField);
        actionsPanel.add(checkOutButton);

        // Set preferred sizes for components
        setPreferredSizes(actionsPanel, 10, 10);

        // Add the actions panel to the main panel
        mainPanel.add(actionsPanel, BorderLayout.WEST);

        // Make the frame visible
        setVisible(true);

        // Layout setup
        JPanel exitPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT)); // Panel to contain the exit button

        // Add the exit button to the exit panel
        exitPanel.add(exitButton);

        // Add the exit panel to the SOUTH position of the main panel
        mainPanel.add(exitPanel, BorderLayout.SOUTH);

        // Add other components to the main panel...

        // Set main panel to frame content pane
        setContentPane(mainPanel);

        // Make the frame visible
        setVisible(true);

    }

    // Method loadBooksFromFile(String fileName) is responsible for reading book information from a file specified by fileName,parsing each line to extract book details, and then adding those books to the catalog.
    // After processing all lines in the file, it updates the databaseTextArea to reflect the current state of the catalog by calling updateDatabaseTextArea().
    // if the file is not found, it displays an error message using a JOptionPane.
    private void loadBooksFromFile(String fileName) {
        try (BufferedReader br = new BufferedReader(new FileReader(fileName))) {
            String line;
            while ((line = br.readLine()) != null) {
                String[] bookInfo = line.split(",");
                int bookID = Integer.parseInt(bookInfo[0]);
                String title = bookInfo[1].trim();
                String author = bookInfo[2];
                String genre = bookInfo[3];
                String barcode = bookInfo[4].trim();

                Book book = new Book(bookID, title, author, genre, barcode);
                catalog.addBooksFromFile("LMS_in.txt");
            }
            // Update databaseTextArea
            updateDatabaseTextArea();
        } catch (IOException ex) {
            JOptionPane.showMessageDialog(this, "Error: File not found!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }


    // Method updateDatabaseTextArea() is responsible for refreshing the databaseTextArea with the current state of the catalog.
    // Each book appends its string representation obtained by calling book.toString()).
    // When all books have been appended to the StringBuilder, it sets the text of the databaseTextArea to the string representation of sb by calling databaseTextArea.setText(sb.toString()).
    private void updateDatabaseTextArea() {
        StringBuilder sb = new StringBuilder();
        List<Book> books = catalog.getAllBooks();
        for (Book book : books) {
            sb.append(book.toString()).append("\n");
        }
        databaseTextArea.setText(sb.toString());
    }

    // method removeBookByBarcode(String barcode) is responsible for removing a book from the catalog based on its barcode.
    //If book was successfully removed, it displays a success message using JOptionPane.showMessageDialog(),
    // If book was not found, it displays an error message using JOptionPane.showMessageDialog() indicating that no book with the specified barcode was found.
    private void removeBookByBarcode(String barcode) {
        boolean removed = catalog.removeBookByBarcode(barcode);
        if (removed) {
            JOptionPane.showMessageDialog(this, "Book with barcode '" + barcode + "' removed successfully.", "Success", JOptionPane.INFORMATION_MESSAGE);
            updateDatabaseTextArea();
        } else {
            JOptionPane.showMessageDialog(this, "Error: Book with barcode '" + barcode + "' not found.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }


    // method removeBookByTitle(String title) is responsible for removing a book from the catalog based on its title.
    private void removeBookByTitle(String title) {
        boolean removed = catalog.removeBookByTitle(title);
        if (removed) {
            JOptionPane.showMessageDialog(this, "Book '" + title + "' removed successfully.", "Success", JOptionPane.INFORMATION_MESSAGE);
            updateDatabaseTextArea();
        } else {
            JOptionPane.showMessageDialog(this, "Error: Book '" + title + "' not found.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // Method to check out a book
    //It retrieves an iterator over all the books in the catalog using catalog.getAllBooks().iterator(). After checking out the book successfully, it constructs a message that includes the due date of the book and displays it using a message dialog.
    // returns no value.
    private void checkOutBook(String title) {
        Iterator<Book> iterator = catalog.getAllBooks().iterator();
        while (iterator.hasNext()) {
            Book book = iterator.next();
            if (book.getTitle().equalsIgnoreCase(title)) {
                if ("checked out".equals(book.getStatus())) {
                    JOptionPane.showMessageDialog(this, "Error: This book is already checked out.", "Error", JOptionPane.ERROR_MESSAGE);
                    return;
                }
                // Calculate due date as 4 weeks from the current date
                LocalDate currentDate = LocalDate.now();
                LocalDate dueDate = currentDate.plusWeeks(4);
                book.setStatus("checked out");
                book.setDueDate(dueDate.format(DateTimeFormatter.ofPattern("MMMM dd, yyyy")));
                String message = "Book '" + title + "' checked out successfully. Due date: " + book.getDueDate();
                JOptionPane.showMessageDialog(this, message, "Success", JOptionPane.INFORMATION_MESSAGE);
                updateDatabaseTextArea(); // Update the database display
                return; // Exit the method after successful checkout
            }
        }
        // If the loop completes without finding the book
        JOptionPane.showMessageDialog(this, "Error: Book not found.", "Error", JOptionPane.ERROR_MESSAGE);
    }



    // checkInBook method is responsible for checking in a book in your library management system.
    //check-in operation (checkedIn) is stored in a boolean variable.
    //If book successfully checked in, it displays a success message using JOptionPane.showMessageDialog().It also updates the database text area to reflect the change.
    // If book is not found or is already checked in (checkedIn is false), it displays an error message using JOptionPane.showMessageDialog()
    // returns no value.
    private void checkInBook(String title) {
        boolean checkedIn = catalog.checkInBook(title);
        if (checkedIn) {
            JOptionPane.showMessageDialog(this, "Book '" + title + "' checked in successfully.", "Success", JOptionPane.INFORMATION_MESSAGE);
            updateDatabaseTextArea();
        } else {
            JOptionPane.showMessageDialog(this, "Error: Book '" + title + "' not found or is already checked in.", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // My main method
    public static void main(String[] args) {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                new MainFrame().setVisible(true);
            }
        });
    }
}



