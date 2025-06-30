# CodeAlpha_HotelReservationSystem
#This is my internship assignment which is a console based Hotel Reservation System made using Java in IntelliJ IDEA. It is made very basic knowledge by a beginner.import java.io.*;
import java.util.*;

class Room {
    int roomNumber;
    String category;
    boolean isBooked;

    Room(int roomNumber, String category) {
        this.roomNumber = roomNumber;
        this.category = category;
        this.isBooked = false;
    }

    public String toString() {
        return "Room " + roomNumber + " [" + category + "] - " + (isBooked ? "Booked" : "Available");
    }
}

class Booking {
    String guestName;
    int roomNumber;
    String paymentStatus;

    Booking(String guestName, int roomNumber, String paymentStatus) {
        this.guestName = guestName;
        this.roomNumber = roomNumber;
        this.paymentStatus = paymentStatus;
    }

    public String toString() {
        return "Guest: " + guestName + ", Room: " + roomNumber + ", Payment: " + paymentStatus;
    }
}

public class HotelReservationSystem {
    private final static Scanner sc = new Scanner(System.in);
    private final ArrayList<Room> rooms = new ArrayList<>();
    private final ArrayList<Booking> bookings = new ArrayList<>();
    private final String FILE_NAME = "bookings.txt";

    public static void main(String[] args) {
        HotelReservationSystem system = new HotelReservationSystem();
        system.loadData();
        system.menu();
        system.saveData();
    }

    void menu() {
        int choice;
        do {
            System.out.println("\n==== Hotel Reservation System ====");
            System.out.println("1. Add Room");
            System.out.println("2. View All Rooms");
            System.out.println("3. Search Available Rooms");
            System.out.println("4. Make Reservation");
            System.out.println("5. Cancel Reservation");
            System.out.println("6. View All Bookings");
            System.out.println("0. Exit");
            System.out.print("Enter choice: ");
            choice = Integer.parseInt(sc.nextLine());

            switch (choice) {
                case 1 -> addRoom();
                case 2 -> viewRooms();
                case 3 -> searchAvailableRooms();
                case 4 -> makeReservation();
                case 5 -> cancelReservation();
                case 6 -> viewBookings();
                case 0 -> System.out.println("Exiting...");
                default -> System.out.println("Invalid choice.");
            }
        } while (choice != 0);
    }

    void addRoom() {
        System.out.print("Enter Room Number: ");
        int number = Integer.parseInt(sc.nextLine());
        System.out.print("Enter Room Category (Single/Double/Suite): ");
        String category = sc.nextLine();
        rooms.add(new Room(number, category));
        System.out.println("Room added successfully.");
    }

    void viewRooms() {
        if (rooms.isEmpty()) {
            System.out.println("No rooms added yet.");
            return;
        }
        rooms.forEach(System.out::println);
    }

    void searchAvailableRooms() {
        System.out.print("Enter category to search: ");
        String cat = sc.nextLine();
        boolean found = false;
        for (Room r : rooms) {
            if (r.category.equalsIgnoreCase(cat) && !r.isBooked) {
                System.out.println(r);
                found = true;
            }
        }
        if (!found) System.out.println("No available rooms found in this category.");
    }

    void makeReservation() {
        System.out.print("Enter your name: ");
        String name = sc.nextLine();
        System.out.print("Enter desired room number: ");
        int number = Integer.parseInt(sc.nextLine());

        Room room = findRoom(number);
        if (room == null) {
            System.out.println("Room does not exist.");
            return;
        }
        if (room.isBooked) {
            System.out.println("Room already booked.");
            return;
        }

        System.out.println("Simulating payment... Payment successful!");
        room.isBooked = true;
        bookings.add(new Booking(name, number, "Paid"));
        System.out.println("Booking confirmed for " + name + " in room " + number);
    }

    void cancelReservation() {
        System.out.print("Enter room number to cancel: ");
        int number = Integer.parseInt(sc.nextLine());

        Booking bookingToRemove = null;
        for (Booking b : bookings) {
            if (b.roomNumber == number) {
                bookingToRemove = b;
                break;
            }
        }

        if (bookingToRemove != null) {
            bookings.remove(bookingToRemove);
            Room room = findRoom(number);
            if (room != null) room.isBooked = false;
            System.out.println("Reservation canceled.");
        } else {
            System.out.println("No booking found for this room.");
        }
    }

    void viewBookings() {
        if (bookings.isEmpty()) {
            System.out.println("No bookings found.");
            return;
        }
        bookings.forEach(System.out::println);
    }

    Room findRoom(int number) {
        for (Room r : rooms) {
            if (r.roomNumber == number) return r;
        }
        return null;
    }

    void saveData() {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(FILE_NAME))) {
            for (Booking b : bookings) {
                writer.write(b.guestName + "," + b.roomNumber + "," + b.paymentStatus);
                writer.newLine();
            }
        } catch (IOException e) {
            System.out.println("Error saving data.");
        }
    }

    void loadData() {
        File file = new File(FILE_NAME);
        if (!file.exists()) return;

        try (BufferedReader reader = new BufferedReader(new FileReader(file))) {
            String line;
            while ((line = reader.readLine()) != null) {
                String[] parts = line.split(",");
                String guest = parts[0];
                int roomNum = Integer.parseInt(parts[1]);
                String payment = parts[2];

                Room room = findRoom(roomNum);
                if (room == null) {
                    room = new Room(roomNum, "Unknown");
                    room.isBooked = true;
                    rooms.add(room);
                } else {
                    room.isBooked = true;
                }

                bookings.add(new Booking(guest, roomNum, payment));
            }
        } catch (IOException e) {
            System.out.println("Error loading data.");
        }
    }
}
