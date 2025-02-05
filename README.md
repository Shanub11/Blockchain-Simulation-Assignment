# Blockchain-Simulation-Assignment
import hashlib
from datetime import datetime
import json

class Block:
    def __init__(self, index, transactions, previous_hash, difficulty=4):
        self.index = index
        self.timestamp = datetime.now().strftime("%H:%M:%S")
        self.transactions = transactions
        self.previous_hash = previous_hash
        self.nonce = 0
        self.difficulty = difficulty
        self.hash = self.compute_hash()
        
    def compute_hash(self):
        """Generate SHA-256 hash for the block based on its content."""
        block_data = json.dumps({
            "index": self.index,
            "timestamp": self.timestamp,
            "transactions": self.transactions,
            "previous_hash": self.previous_hash,
            "nonce": self.nonce
        }, sort_keys=True)
        return hashlib.sha256(block_data.encode()).hexdigest()
    
    def mine_block(self):
        """Perform Proof-of-Work by finding a hash with a certain number of leading zeros."""
        while not self.hash.startswith('0' * self.difficulty):
            self.nonce += 1
            self.hash = self.compute_hash()
        print(f"Block {self.index} mined with hash: {self.hash}")

class Blockchain:
    def __init__(self):
        self.difficulty = 4
        self.chain = [self.create_genesis_block()]
       
    def create_genesis_block(self):
        """Create the first block in the blockchain."""
        return Block(0, ["Genesis Block"], "0", self.difficulty)

    def add_block(self):
        """Add a new block with user input transactions to the blockchain after mining."""
        transactions = []
        num_transactions = int(input("Enter the number of transactions for the new block: "))
        
        for _ in range(num_transactions):
            transaction = input("Enter transaction: ")
            transactions.append(transaction)
        
        previous_block = self.chain[-1]
        new_block = Block(len(self.chain), transactions, previous_block.hash, self.difficulty)
        new_block.mine_block()
        self.chain.append(new_block)
    
    def is_chain_valid(self):
        """Check the validity of the blockchain by ensuring hashes are correctly linked."""
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i - 1]

            if current_block.hash != current_block.compute_hash():
                return False  # Block data has been changed
            
            if current_block.previous_hash != previous_block.hash:
                return False  # Block linkage is broken
        
        return True

    def tamper_block(self, index, new_transactions):
       # Simulate tampering by modifying block data.
        if index < len(self.chain):
            self.chain[index].transactions = new_transactions
            self.chain[index].hash = self.chain[index].compute_hash()
            print(f"Block {index} tampered!")

    def print_blockchain(self):
        # Print the blockchain with details of each block.
        for block in self.chain:
            print(f"\nBlock {block.index}")
            print(f"Timestamp: {block.timestamp}")
            print(f"Transactions: {block.transactions}")
            print(f"Previous Hash: {block.previous_hash}")
            print(f"Current Hash: {block.hash}")
            print(f"Nonce: {block.nonce}")

# Testing the Blockchain Simulation
def run_blockchain():
    blockchain = Blockchain()

    while True:
    
        print("\n1. Add a new block")
        print("2. Show blockchain")
        print("3. Tamper with a block")
        print("4. Exit\n")
    
        choice = input("Enter your choice: \n")
    
        if choice == "1":
            blockchain.add_block()  # Adding a new block
            blockchain.print_blockchain()
    
        elif choice == "2":    
            blockchain.print_blockchain()  # Displaying the blockchain

        elif choice == "3":
            index = int(input("Enter block index to tamper: "))
            new_transactions = input("Enter new tampered transactions (comma-separated): ").split(",")
            blockchain.tamper_block(index, new_transactions)
            print("\n### Blockchain After Tampering ###")
            blockchain.print_blockchain()
    
        elif choice == "4": 
            break  # Exit 
    
        else:
            print("Invalid choice! Please enter 1, 2, 3, or 4.")
    
        print("Is Blockchain Valid?", blockchain.is_chain_valid())  # Checking blockchain validity
    
run_blockchain()
