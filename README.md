
import java.awt.*;
import java.awt.event.*;
import java.net.*;
import java.util.regex.*;

public class PhishingDetector extends Frame implements ActionListener {

    Label title, urlLabel, resultLabel;
    TextField urlField;
    Button checkButton, clearButton;

    PhishingDetector() {

        setTitle("Phishing Website Detector");
        setSize(500, 350);
        setLayout(null);
        setBackground(Color.LIGHT_GRAY);

        title = new Label("PHISHING WEBSITE DETECTOR");
        title.setBounds(100, 50, 300, 30);
        title.setFont(new Font("Arial", Font.BOLD, 18));
        add(title);

        urlLabel = new Label("Enter Website URL:");
        urlLabel.setBounds(50, 120, 150, 25);
        add(urlLabel);

        urlField = new TextField();
        urlField.setBounds(200, 120, 240, 25);
        add(urlField);

        checkButton = new Button("Check URL");
        checkButton.setBounds(120, 180, 100, 35);
        checkButton.addActionListener(this);
        add(checkButton);

        clearButton = new Button("Clear");
        clearButton.setBounds(250, 180, 100, 35);
        clearButton.addActionListener(this);
        add(clearButton);

        resultLabel = new Label("Result will appear here.");
        resultLabel.setBounds(50, 250, 400, 30);
        resultLabel.setFont(new Font("Arial", Font.BOLD, 14));
        add(resultLabel);

        addWindowListener(new WindowAdapter() {
            public void windowClosing(WindowEvent e) {
                dispose();
            }
        });

        setVisible(true);
    }

    public void actionPerformed(ActionEvent e) {

        if (e.getSource() == checkButton) {

            String url = urlField.getText().trim().toLowerCase();

            if (url.isEmpty()) {
                resultLabel.setText("Please enter a URL!");
                resultLabel.setForeground(Color.ORANGE);
                return;
            }

            boolean phishing = false;

            // Check HTTPS
            if (!url.startsWith("https://")) {
                phishing = true;
            }

            // Check suspicious symbols
            if (url.contains("@")) {
                phishing = true;
            }

            // Check IP address instead of domain
            Pattern ipPattern = Pattern.compile(
                "https?://(\\d{1,3}\\.){3}\\d{1,3}"
            );

            if (ipPattern.matcher(url).find()) {
                phishing = true;
            }

            // Check suspicious keywords
            String[] keywords = {
                "login", "verify", "update",
                "banking", "password", "free",
                "account", "secure"
            };

            for (String word : keywords) {
                if (url.contains(word)) {
                    phishing = true;
                    break;
                }
            }

            // Check excessive URL length
            if (url.length() > 75) {
                phishing = true;
            }

            // Check URL shortening services
            String[] shorteners = {
                "bit.ly", "tinyurl.com",
                "t.co", "is.gd"
            };

            for (String site : shorteners) {
                if (url.contains(site)) {
                    phishing = true;
                    break;
                }
            }

            if (phishing) {
                resultLabel.setText("WARNING: Potentially Phishing Website!");
                resultLabel.setForeground(Color.RED);
            } else {
                resultLabel.setText("URL appears safe (not verified).");
                resultLabel.setForeground(new Color(0, 130, 0));
            }
        }

        if (e.getSource() == clearButton) {
            urlField.setText("");
            resultLabel.setText("Result will appear here.");
            resultLabel.setForeground(Color.BLACK);
        }
    }

    public static void main(String[] args) {
        new PhishingDetector();
    }
}
