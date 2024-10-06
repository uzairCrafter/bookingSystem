# bookingSystem
//This is cinema booking system
import java.util.*;

class CinemaBookingSystem {
    private static Scanner scanner = new Scanner(System.in);
    private static List<User> users = new ArrayList<>();
    private static List<Movie> movies = new ArrayList<>();
    private static List<Booking> bookings = new ArrayList<>();
    private static User currentUser = null;

    public static void main(String[] args) {
        initializeMovies();
        mainMenu();
    }

    private static void mainMenu() {
        while (true) {
            System.out.println("Welcome to the Cinema Booking System");
            System.out.println("1. Register");
            System.out.println("2. Login");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    register();
                    break;
                case 2:
                    login();
                    break;
                case 3:
                    System.exit(0);
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private static void register() {
        System.out.print("Enter username: ");
        String username = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();
        users.add(new User(username, password));
        System.out.println("Registration successful!");
    }

    private static void login() {
        System.out.print("Enter username: ");
        String username = scanner.nextLine();
        System.out.print("Enter password: ");
        String password = scanner.nextLine();

        for (User user : users) {
            if (user.getUsername().equals(username) && user.getPassword().equals(password)) {
                currentUser = user;
                userMenu();
                return;
            }
        }
        System.out.println("Invalid username or password.");
    }

    private static void userMenu() {
        while (true) {
            System.out.println("1. View Movies");
            System.out.println("2. Book Tickets");
            System.out.println("3. View Booking History");
            System.out.println("4. Logout");
            System.out.print("Choose an option: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // Consume newline

            switch (choice) {
                case 1:
                    viewMovies();
                    break;
                case 2:
                    bookTickets();
                    break;
                case 3:
                    viewBookingHistory();
                    break;
                case 4:
                    currentUser = null;
                    return;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
    }

    private static void viewMovies() {
        System.out.println("Movies currently showing:");
        for (Movie movie : movies) {
            System.out.println(movie);
        }
    }

    private static void bookTickets() {
        System.out.print("Enter movie title: ");
        String title = scanner.nextLine();
        Movie selectedMovie = null;

        for (Movie movie : movies) {
            if (movie.getTitle().equalsIgnoreCase(title)) {
                selectedMovie = movie;
                break;
            }
        }

        if (selectedMovie == null) {
            System.out.println("Movie not found.");
            return;
        }

        System.out.println("Available showtimes: " + selectedMovie.getShowtimes());
        System.out.print("Enter showtime: ");
        String showtime = scanner.nextLine();

        if (!selectedMovie.getShowtimes().contains(showtime)) {
            System.out.println("Invalid showtime.");
            return;
        }

        System.out.print("Enter number of tickets: ");
        int numTickets = scanner.nextInt();
        scanner.nextLine(); // Consume newline

        List<String> selectedSeats = new ArrayList<>();
        for (int i = 0; i < numTickets; i++) {
            System.out.print("Enter seat number (e.g., A1): ");
            String seat = scanner.nextLine();
            selectedSeats.add(seat);
        }

        double totalPrice = calculateTotalPrice(selectedSeats);
        System.out.println("Total price: $" + totalPrice);
        System.out.print("Confirm booking? (yes/no): ");
        String confirm = scanner.nextLine();

        if (confirm.equalsIgnoreCase("yes")) {
            String bookingID = UUID.randomUUID().toString();
            Booking booking = new Booking(bookingID, currentUser, selectedMovie, showtime, selectedSeats, totalPrice);
            bookings.add(booking);
            currentUser.addBooking(booking);
            System.out.println("Booking confirmed! Your booking ID is " + bookingID);
        } else {
            System.out.println("Booking cancelled.");
        }
    }

    private static double calculateTotalPrice(List<String> seats) {
        double totalPrice = 0;
        for (String seat : seats) {
            if (seat.startsWith("A") || seat.startsWith("B")) {
                totalPrice += 15; // Front seats
            } else if (seat.startsWith("C") || seat.startsWith("D")) {
                totalPrice += 10; // Mid rows
            } else {
                totalPrice += 5; // Back seats
            }
        }
        return totalPrice;
    }

    private static void viewBookingHistory() {
        System.out.println("Your booking history:");
        for (Booking booking : currentUser.getBookings()) {
            System.out.println(booking);
        }
    }

    private static void initializeMovies() {
        movies.add(new Movie("Inception", "Sci-Fi", 148, Arrays.asList("10:00 AM", "1:00 PM", "4:00 PM")));
        movies.add(new Movie("Titanic", "Romance", 195, Arrays.asList("11:00 AM", "2:00 PM", "5:00 PM")));
    }
}

class User {
    private String username;
    private String password;
    private List<Booking> bookings = new ArrayList<>();

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

    public void addBooking(Booking booking) {
        bookings.add(booking);
    }

    public List<Booking> getBookings() {
        return bookings;
    }
}

class Movie {
    private String title;
    private String genre;
    private int duration;
    private List<String> showtimes;

    public Movie(String title, String genre, int duration, List<String> showtimes) {
        this.title = title;
        this.genre = genre;
        this.duration = duration;
        this.showtimes = showtimes;
    }

    public String getTitle() {
        return title;
    }

    public List<String> getShowtimes() {
        return showtimes;
    }

    @Override
    public String toString() {
        return "Title: " + title + ", Genre: " + genre + ", Duration: " + duration + " mins, Showtimes: " + showtimes;
    }
}

class Booking {
    private String bookingID;
    private User user;
    private Movie movie;
    private String showtime;
    private List<String> selectedSeats;
    private double totalPrice;

    public Booking(String bookingID, User user, Movie movie, String showtime, List<String> selectedSeats, double totalPrice) {
        this.bookingID = bookingID;
        this.user = user;
        this.movie = movie;
        this.showtime = showtime;
        this.selectedSeats = selectedSeats;
        this.totalPrice = totalPrice;
    }

    @Override
    public String toString() {
        return "Booking ID: " + bookingID + ", Movie: " + movie.getTitle() + ", Showtime: " + showtime + ", Seats: " + selectedSeats + ", Total Price: $" + totalPrice;
    }
}
