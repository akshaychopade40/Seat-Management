<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seat Management System</title>
    <style>
        body 
        {
            font-family: Arial, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #f7f7f7;
        }
        .container 
        {
            text-align: center;
        }
        .seat 
        {
            width: 50px;
            height: 50px;
            margin: 5px;
            display: inline-block;
            background-color: #ccc;
            cursor: pointer;
        }
        .seat.available 
        {
            background-color: #28a745;
        }
        .seat.locked 
        {
            background-color: #ffc107;
        }
        .seat.booked 
        {
            background-color: #dc3545;
            cursor: not-allowed;
        }
        .seat.selected 
        {
            background-color: #007bff;
        }
        #message 
        {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Select Your Seats</h1>
        <div id="seats"></div>
        <div id="message"></div>
    </div>

    <script>
        const seatCount = 30;
        const seats = [];
        const lockDuration = 30000; 
        function createSeats() 
      {
            const seatsContainer = document.getElementById('seats');
            for (let i = 0; i < seatCount; i++) 
            {
                const seat = document.createElement('div');
                seat.className = 'seat available';
                seat.dataset.index = i;
                seat.addEventListener('click', () => selectSeat(i));
                seatsContainer.appendChild(seat);
                seats.push({
                    element: seat,
                    status: 'available',
                    lockTimer: null
                });
            }
        }

        function selectSeat(index) 
        {
            const seat = seats[index];
            if (seat.status === 'available') 
            {
                lockSeat(index);
            } else if (seat.status === 'selected') 
            {
                releaseSeat(index);
            }
        }

        function lockSeat(index) 
        {
            const seat = seats[index];
            seat.status = 'locked';
            seat.element.className = 'seat locked';
            seat.lockTimer = setTimeout(() => releaseSeat(index), lockDuration);
            displayMessage(`Seat ${index + 1} locked. Complete booking within 15 seconds.`);
        }

        function releaseSeat(index) 
       {
            const seat = seats[index];
            if (seat.status === 'locked') 
            {
                seat.status = 'available';
                seat.element.className = 'seat available';
                clearTimeout(seat.lockTimer);
                seat.lockTimer = null;
                displayMessage(`Seat ${index + 1} is available again.`);
            }
        }

        function displayMessage(message) 
        {
            const messageDiv = document.getElementById('message');
            messageDiv.innerText = message;
        }

        createSeats();
    </script>
</body>
</html>
