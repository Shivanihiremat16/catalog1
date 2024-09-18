# catalog1
import org.json.JSONObject;
import java.util.HashMap;
import java.util.Map;

public class ShamirSecretSharing {
    private static int decodeValue(String base, String value) {
        int baseValue = Integer.parseInt(base);
        return Integer.parseInt(value, baseValue);
    }
    private static double lagrangeInterpolation(HashMap<Integer, Integer> points) {
        double secret = 0.0; 
        for (Map.Entry<Integer, Integer> i : points.entrySet()) {
            double li = 1.0;  
            int xi = i.getKey(); 
            int yi = i.getValue(); 

       
            for (Map.Entry<Integer, Integer> j : points.entrySet()) {
                int xj = j.getKey();
                if (xi != xj) {
                    li *= (0.0 - xj) / (xi - xj);  // Multiply the Lagrange basis polynomial
                }
            }
            secret += li * yi;
        }

        return secret;  
    }

    public static void main(String[] args) {
        String jsonString = "{ \"keys\": { \"n\": 4, \"k\": 3, " +
                            "\"1\": { \"base\": \"10\", \"value\": \"4\" }, " +
                            "\"2\": { \"base\": \"2\", \"value\": \"111\" }, " +
                            "\"3\": { \"base\": \"10\", \"value\": \"12\" }, " +
                            "\"6\": { \"base\": \"4\", \"value\": \"213\" } } }";
        JSONObject jsonObject = new JSONObject(jsonString);
        JSONObject keys = jsonObject.getJSONObject("keys");
        int n = keys.getInt("n");
        int k = keys.getInt("k");
        HashMap<Integer, Integer> points = new HashMap<>();
        for (String key : keys.keySet()) {
            if (!key.equals("n") && !key.equals("k")) {
                JSONObject point = keys.getJSONObject(key);
                String base = point.getString("base");
                String value = point.getString("value");
                int decodedValue = decodeValue(base, value);
                points.put(Integer.parseInt(key), decodedValue);
            }
        }
        double secret = lagrangeInterpolation(points);
        System.out.println("The secret (constant term 'c') is: " + secret);
    }
}
