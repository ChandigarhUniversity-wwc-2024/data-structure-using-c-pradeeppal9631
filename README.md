<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Disaster Relief Fund</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f4f7fa;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            background-color: #ffffff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
            width: 300px;
            text-align: center;
        }
        h2 {
            margin-bottom: 20px;
        }
        input[type="text"] {
            padding: 10px;
            width: 100%;
            margin-bottom: 20px;
            border-radius: 5px;
            border: 1px solid #ccc;
        }
        button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
            font-size: 16px;
        }
        button:hover {
            background-color: #45a049;
        }
        .status {
            margin-top: 20px;
            color: green;
            font-weight: bold;
        }
        .error {
            color: red;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div class="container">
        <h2>Donate to the Fund(Disaster)</h2>
        <input type="text" id="donationAmount" placeholder="Enter donation amount in ETH" />
        <button id="donateButton">Donate</button>
        <div id="status" class="status"></div>
    </div>

    <!-- Load Web3.js -->
    <script src="https://cdn.jsdelivr.net/npm/web3@1.6.1/dist/web3.min.js"></script>

    <script>
        // Check if Web3 (MetaMask) is available
        let web3;
        if (typeof window.ethereum !== 'undefined') {
            web3 = new Web3(window.ethereum);  // Using the provider from MetaMask
        } else {
            alert('Please install MetaMask!');
            throw new Error('MetaMask is required to interact with this application.');
        }

        // Define contract ABI and address (replace with your actual contract)
        const contractABI = [
  {
    "constant": false,
    "inputs": [],
    "name": "donate",
    "outputs": [],
    "payable": true,
    "stateMutability": "payable",
    "type": "function"
  },
  // Include other functions and events of your contract...
];

      const contractAddress = '0x1234567890abcdef1234567890abcdef12345678';  // Your actual contract address here



        // Event listener for the Donate button
        document.getElementById('donateButton').addEventListener('click', async () => {
            const amountInEther = parseFloat(document.getElementById('donationAmount').value);  // Get donation amount
            const statusDiv = document.getElementById('status');
            
            // Validate input
            if (isNaN(amountInEther) || amountInEther <= 0) {
                statusDiv.innerHTML = 'Please enter a valid donation amount.';
                statusDiv.classList.add('error');
                return;
            }

            const amountInWei = web3.utils.toWei(amountInEther.toString(), 'ether');  // Convert to Wei

            try {
                // Request MetaMask accounts
                const accounts = await web3.eth.requestAccounts();  
                if (accounts.length === 0) {
                    statusDiv.innerHTML = 'No MetaMask account detected!';
                    statusDiv.classList.add('error');
                    return;
                }

                const disasterReliefFund = new web3.eth.Contract(contractABI, contractAddress);  // Initialize contract

                // Show the user the donation status
                statusDiv.innerHTML = 'Sending transaction...';
                statusDiv.classList.remove('error');
                statusDiv.classList.add('status');

                // Sending the donation
                await disasterReliefFund.methods.donate().send({
                    from: accounts[0],  // The first account in MetaMask
                    value: amountInWei  // Donation amount in Wei
                });

                statusDiv.innerHTML = 'Donation successful! Thank you for your support.';
                statusDiv.classList.remove('error');
                statusDiv.classList.add('status');
            } catch (error) {
                console.error('Transaction failed:', error);
                // Display more detailed error messages
                statusDiv.innerHTML = 'Donation failed: ' + error.message;
                statusDiv.classList.add('error');
            }
        });
    </script>
</body>
</html>
