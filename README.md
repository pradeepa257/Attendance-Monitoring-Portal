# Attendance-Monitoring-Portal
<!DOCTYPE html>
<html lang="en">
<img src="C:\Users\Dell\Downloads\image.png" style="width: 190px; height: 100px; alt="description of image">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Attendance Portal</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

    <header>
        <div class="logo">Attendance Monitoring Portal</div>
        <nav>
            <a href="#">Home</a>
            <a href="#" class="active">Add Attendance</a>
            <a href="#">View Table</a>
            <a href="#">About</a>
        </nav>
    </header>

    <main>
        <section class="attendance-section">
            <h2> Mark Attendance for Today</h2>

            <div class="date-header">
                <label for="attendanceDate">Date:</label>
                <input type="date" id="attendanceDate" value="2025-12-07">
            </div>

            <table id="attendanceTable">
                <thead>
                    <tr>
                        <th>Roll No.</th>
                        <th>Student Name</th>
                        <th class="status-col">Status</th>
                    </tr>
                </thead>
                <tbody>
                    </tbody>
            </table>

            <button id="submitAttendance">Submit Today's Attendance</button>
        </section>
    </main>

    <script src="script.js"></script>
<p>Submitted by: Pradeepa, Dheeraj, Waseem, Prasanthi</p>

</body>
</html>
