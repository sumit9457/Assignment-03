# Assignment-03

<?php
// Database connection setup
$servername = "localhost";
$username = "root";
$password = ""; // Update if you have a DB password
$dbname = "DomainReminderDB";

// Create connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Create database if not exists
$conn->query("CREATE DATABASE IF NOT EXISTS $dbname");
$conn->select_db($dbname);

// Create table if not exists
$createTable = "
CREATE TABLE IF NOT EXISTS Domains (
    Domain_Id INT AUTO_INCREMENT PRIMARY KEY,
    Domain_Name VARCHAR(255) NOT NULL,
    Vendor_Name VARCHAR(255) NOT NULL,
    Domain_Purchase_Date DATE NOT NULL,
    Domain_Expiry_Date DATE NOT NULL
)";
$conn->query($createTable);

// Handle form submission
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $domain_name = $_POST['domain_name'];
    $vendor_name = $_POST['vendor_name'];
    $purchase_date = $_POST['purchase_date'];
    $expiry_date = $_POST['expiry_date'];

    $stmt = $conn->prepare("INSERT INTO Domains (Domain_Name, Vendor_Name, Domain_Purchase_Date, Domain_Expiry_Date)
                            VALUES (?, ?, ?, ?)");
    $stmt->bind_param("ssss", $domain_name, $vendor_name, $purchase_date, $expiry_date);

    if ($stmt->execute()) {
        $message = "Domain successfully saved!";
    } else {
        $message = "Error: " . $stmt->error;
    }

    $stmt->close();
}
?>

<!DOCTYPE html>
<html>
<head>
    <title>Domain Reminder Master Form</title>
    <style>
        body { font-family: Arial; margin: 40px; }
        form { max-width: 500px; padding: 20px; border: 1px solid #ccc; border-radius: 8px; }
        input[type=text], input[type=date] {
            width: 100%; padding: 10px; margin: 8px 0; box-sizing: border-box;
        }
        input[type=submit] {
            padding: 10px 20px; background-color: #28a745; color: white; border: none; border-radius: 5px;
            cursor: pointer;
        }
        input[type=submit]:hover { background-color: #218838; }
        .message { margin-top: 20px; font-weight: bold; color: green; }
    </style>
</head>
<body>

<h2>Domain Master Entry Form</h2>

<form method="POST" action="">
    <label>Domain Name:</label>
    <input type="text" name="domain_name" required>

    <label>Vendor Name:</label>
    <input type="text" name="vendor_name" required>

    <label>Domain Purchase Date:</label>
    <input type="date" name="purchase_date" required>

    <label>Domain Expiry Date:</label>
    <input type="date" name="expiry_date" required>

    <input type="submit" value="Save Domain">
</form>

<?php if (isset($message)) echo "<div class='message'>$message</div>"; ?>

</body>
</html>
