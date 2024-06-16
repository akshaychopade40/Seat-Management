<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Seat Management System</title>
    <style>
        .seat 
        {
            width: 50px;
            height: 50px;
            margin: 5px;
            background-color: green;
            display: inline-block;
            cursor: pointer;
        }
        .locked 
        {
            background-color: yellow;
        }
        .booked 
        {
            background-color: red;
            cursor: not-allowed;
        }
    </style>
</head>
<body>
    <h1>Seat Management System</h1>
    <div id="seats"></div>
    <button id="confirmBooking">Confirm Booking</button>
    <script>
        const seats = Array.from({ length: 30 }, (_, i) => ({
            id: i + 1,
            status: 'available'
        }));

        const seatContainer = document.getElementById('seats');

        seats.forEach(seat => 
        {
            const seatElement = document.createElement('div');
            seatElement.classList.add('seat');
            seatElement.dataset.id = seat.id;
            seatContainer.appendChild(seatElement);

            seatElement.addEventListener('click', () => {
                if (seat.status === 'available') 
                {
                    seat.status = 'locked';
                    seatElement.classList.add('locked');
                    lockSeat(seat.id);
                } else if (seat.status === 'locked') 
                {
                    seat.status = 'available';
                    seatElement.classList.remove('locked');
                    releaseSeat(seat.id);
                }
            });
        });

        function lockSeat(seatId) 
       {
            fetch(`/lock-seat/${seatId}`, { method: 'POST' })
                .then(response => response.json())
                .then(data => {
                    if (!data.success) 
                    {
                        alert('Failed to lock seat.');
                        document.querySelector(`.seat[data-id="${seatId}"]`).classList.remove('locked');
                        seats.find(seat => seat.id === seatId).status = 'available';
                    }
                });
        }

        function releaseSeat(seatId) 
        {
            fetch(`/release-seat/${seatId}`, { method: 'POST' });
        }

        document.getElementById('confirmBooking').addEventListener('click', () => {
            const lockedSeats = seats.filter(seat => seat.status === 'locked');
            if (lockedSeats.length === 0) 
            {
                alert('No seats selected.');
                return;
            }

            const seatIds = lockedSeats.map(seat => seat.id);

            fetch('/confirm-booking', 
                {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({ seatIds })
            })
                .then(response => response.json())
                .then(data => 
                    {
                    if (data.success) 
                    {
                        alert('Booking confirmed.');
                        lockedSeats.forEach(seat => 
                           {
                            seat.status = 'booked';
                            document.querySelector(`.seat[data-id="${seat.id}"]`).classList.add('booked');
                            document.querySelector(`.seat[data-id="${seat.id}"]`).classList.remove('locked');
                        });
                    } 
                    else 
                    {
                        alert('Failed to confirm booking.');
                    }
                });
        });
    </script>
</body>
</html>
