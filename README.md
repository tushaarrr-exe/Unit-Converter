CREATE DATABASE unit_converter;

USE unit_converter;

CREATE TABLE conversion_rates (
    id INT PRIMARY KEY AUTO_INCREMENT,
    from_unit VARCHAR(50),
    to_unit VARCHAR(50),
    rate DOUBLE
);

INSERT INTO conversion_rates (from_unit, to_unit, rate) VALUES
('Kilometers', 'Miles', 0.621371),
('Miles', 'Kilometers', 1.60934),
('Celsius', 'Fahrenheit', 9.0/5.0),
('Fahrenheit', 'Celsius', 5.0/9.0),
('Kilograms', 'Pounds', 2.20462),
('Pounds', 'Kilograms', 1/2.20462);

import java.sql.*;
import java.util.Scanner;

public class UnitConverterWithSQL {

    public static double getConversionRate(Connection conn, String fromUnit, String toUnit) throws SQLException {
        String query = "SELECT rate FROM conversion_rates WHERE from_unit = ? AND to_unit = ?";
        try (PreparedStatement stmt = conn.prepareStatement(query)) {
            stmt.setString(1, fromUnit);
            stmt.setString(2, toUnit);
            ResultSet rs = stmt.executeQuery();
            if (rs.next()) {
                return rs.getDouble("rate");
            } else {
                throw new SQLException("Conversion rate not found for " + fromUnit + " to " + toUnit);
            }
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        
        String url = "jdbc:mysql://localhost:3306/unit_converter";
        String username = "root";
        String password = "password";
        
        try (Connection conn = DriverManager.getConnection(url, username, password)) {
            System.out.println("Unit Converter");
            System.out.println("1. Kilometers to Miles");
            System.out.println("2. Miles to Kilometers");
            System.out.println("3. Celsius to Fahrenheit");
            System.out.println("4. Fahrenheit to Celsius");
            System.out.println("5. Kilograms to Pounds");
            System.out.println("6. Pounds to Kilograms");
            System.out.print("Enter your choice (1-6): ");
            
            int choice = scanner.nextInt();
            
            String fromUnit = "", toUnit = "";
            
            switch (choice) {
                case 1:
                    fromUnit = "Kilometers";
                    toUnit = "Miles";
                    break;
                case 2:
                    fromUnit = "Miles";
                    toUnit = "Kilometers";
                    break;
                case 3:
                    fromUnit = "Celsius";
                    toUnit = "Fahrenheit";
                    break;
                case 4:
                    fromUnit = "Fahrenheit";
                    toUnit = "Celsius";
                    break;
                case 5:
                    fromUnit = "Kilograms";
                    toUnit = "Pounds";
                    break;
                case 6:
                    fromUnit = "Pounds";
                    toUnit = "Kilograms";
                    break;
                default:
                    System.out.println("Invalid choice.");
                    return;
            }
            
            System.out.print("Enter value in " + fromUnit + ": ");
            double value = scanner.nextDouble();
            
            double rate = getConversionRate(conn, fromUnit, toUnit);
            
            double result = value * rate;
            System.out.println(value + " " + fromUnit + " is " + result + " " + toUnit);
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            scanner.close();
        }
    }
}
