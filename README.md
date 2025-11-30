# Currency-converter-
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.Scanner;
import org.json.JSONObject;

public class CurrencyConverter {

    // Method to fetch real-time exchange rates
    public static JSONObject getRates(String baseCurrency) {
        try {
            String urlStr = "https://api.exchangerate-api.com/v4/latest/" + baseCurrency;
            URL url = new URL(urlStr);
            HttpURLConnection conn = (HttpURLConnection) url.openConnection();

            conn.setRequestMethod("GET");
            conn.connect();

            // If response is OK (200)
            int responseCode = conn.getResponseCode();
            if (responseCode != 200) {
                throw new RuntimeException("API request failed!");
            }

            BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream()));
            StringBuilder result = new StringBuilder();
            String line;

            while ((line = reader.readLine()) != null) {
                result.append(line);
            }

            reader.close();
            return new JSONObject(result.toString());

        } catch (Exception e) {
            throw new RuntimeException("Error fetching rates: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // Step 1: Currency selection
        System.out.print("Enter Base Currency (e.g., USD, INR, EUR): ");
        String base = sc.nextLine().toUpperCase();

        System.out.print("Enter Target Currency (e.g., USD, INR, EUR): ");
        String target = sc.nextLine().toUpperCase();

        // Step 2: Amount Input
        System.out.print("Enter Amount to Convert: ");
        double amount = sc.nextDouble();

        // Step 3: Fetch live rates
        JSONObject data = getRates(base);
        JSONObject rates = data.getJSONObject("rates");

        if (!rates.has(target)) {
            System.out.println("Invalid target currency!");
            return;
        }

        // Step 4: Currency conversion
        double rate = rates.getDouble(target);
        double convertedAmount = amount * rate;

        // Step 5: Display result
        System.out.println("\n====== Conversion Result ======");
        System.out.println("Base Currency: " + base);
        System.out.println("Target Currency: " + target);
        System.out.println("Exchange Rate: " + rate);
        System.out.println("Converted Amount: " + convertedAmount + " " + target);
        System.out.println("================================");
    }
}
