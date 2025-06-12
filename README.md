# Atm-machine-using-python 
import datetime

class ATM:
    def __init__(self):
        # Initialize with a default account
        self.accounts = {
            "123456": {
                "pin": "1234",
                "balance": 1000.00,
                "transactions": []
            }
        }
        self.current_account = None
        
    def authenticate(self, account_number, pin):
        """Verify account number and PIN"""
        if account_number in self.accounts and self.accounts[account_number]["pin"] == pin:
            self.current_account = account_number
            self.record_transaction("Login")
            return True
        return False
    
    def logout(self):
        """Log out of current account"""
        if self.current_account:
            self.record_transaction("Logout")
            self.current_account = None
    
    def check_balance(self):
        """Return current account balance"""
        if self.current_account:
            balance = self.accounts[self.current_account]["balance"]
            self.record_transaction("Balance Inquiry")
            return balance
        return None
    
    def withdraw(self, amount):
        """Withdraw money from account"""
        if not self.current_account:
            return False
        
        if amount <= 0:
            return False
            
        account = self.accounts[self.current_account]
        if amount > account["balance"]:
            return False
            
        account["balance"] -= amount
        self.record_transaction(f"Withdrawal: ${amount:.2f}")
        return True
    
    def deposit(self, amount):
        """Deposit money into account"""
        if not self.current_account or amount <= 0:
            return False
            
        self.accounts[self.current_account]["balance"] += amount
        self.record_transaction(f"Deposit: ${amount:.2f}")
        return True
    
    def change_pin(self, new_pin):
        """Change account PIN"""
        if not self.current_account or len(new_pin) != 4 or not new_pin.isdigit():
            return False
            
        self.accounts[self.current_account]["pin"] = new_pin
        self.record_transaction("PIN Changed")
        return True
    
    def record_transaction(self, transaction_type):
        """Record a transaction with timestamp"""
        if self.current_account:
            timestamp = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
            self.accounts[self.current_account]["transactions"].append(
                f"{timestamp} - {transaction_type}"
            )
    
    def get_transaction_history(self):
        """Return transaction history"""
        if self.current_account:
            return self.accounts[self.current_account]["transactions"]
        return []
    
    def is_authenticated(self):
        """Check if user is logged in"""
        return self.current_account is not None


def display_menu():
    """Display ATM menu options"""
    print("\nATM Menu:")
    print("1. Check Balance")
    print("2. Withdraw Cash")
    print("3. Deposit Cash")
    print("4. Change PIN")
    print("5. View Transaction History")
    print("6. Exit")


def main():
    atm = ATM()
    
    print("Welcome to the ATM Simulation")
    
    # Login
    while not atm.is_authenticated():
        account_num = input("Enter your account number: ").strip()
        pin = input("Enter your PIN: ").strip()
        
        if not atm.authenticate(account_num, pin):
            print("Invalid account number or PIN. Please try again.")
    
    # Main ATM loop
    while atm.is_authenticated():
        display_menu()
        choice = input("Enter your choice (1-6): ")
        
        try:
            if choice == "1":
                # Balance inquiry
                balance = atm.check_balance()
                print(f"\nYour current balance is: ${balance:.2f}")
                
            elif choice == "2":
                # Withdrawal
                try:
                    amount = float(input("Enter amount to withdraw: "))
                    if atm.withdraw(amount):
                        print(f"\n${amount:.2f} withdrawn successfully.")
                        print(f"New balance: ${atm.check_balance():.2f}")
                    else:
                        print("\nWithdrawal failed. Check your balance or enter a valid amount.")
                except ValueError:
                    print("\nInvalid amount entered.")
                    
            elif choice == "3":
                # Deposit
                try:
                    amount = float(input("Enter amount to deposit: "))
                    if atm.deposit(amount):
                        print(f"\n${amount:.2f} deposited successfully.")
                        print(f"New balance: ${atm.check_balance():.2f}")
                    else:
                        print("\nDeposit failed. Please enter a valid amount.")
                except ValueError:
                    print("\nInvalid amount entered.")
                    
            elif choice == "4":
                # PIN change
                new_pin = input("Enter new 4-digit PIN: ")
                if atm.change_pin(new_pin):
                    print("\nPIN changed successfully.")
                else:
                    print("\nPIN change failed. PIN must be 4 digits.")
                    
            elif choice == "5":
                # Transaction history
                transactions = atm.get_transaction_history()
                print("\nTransaction History:")
                if not transactions:
                    print("No transactions found.")
                else:
                    for t in transactions[-5:]:  # Show last 5 transactions
                        print(t)
                        
            elif choice == "6":
                # Exit
                atm.logout()
                print("\nThank you for using our ATM. Goodbye!")
                
            else:
                print("\nInvalid choice. Please select 1-6.")
                
        except Exception as e:
            print(f"\nAn error occurred: {e}")
    
    print("Session ended.")


if __name__ == "__main__":
    main()
