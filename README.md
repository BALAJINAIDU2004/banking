#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TRANSACTIONS 10

// Structure for storing a transaction record
typedef struct {
    char transaction_type[10];
    float amount;
} Transaction;

// Structure for a bank account
typedef struct {
    char account_holder[50];
    int account_number;
    float balance;
    Transaction transactions[MAX_TRANSACTIONS];
    int transaction_count;
    float interest_rate;  // Interest rate for the account
} BankAccount;

// Function to deposit money
void deposit(BankAccount *account, float amount) {
    account->balance += amount;
    if (account->transaction_count < MAX_TRANSACTIONS) {
        strcpy(account->transactions[account->transaction_count].transaction_type, "Deposit");
        account->transactions[account->transaction_count].amount = amount;
        account->transaction_count++;
    }
    printf("Deposited: %.2f\n", amount);
}

// Function to withdraw money
void withdraw(BankAccount *account, float amount) {
    if (amount <= account->balance) {
        account->balance -= amount;
        if (account->transaction_count < MAX_TRANSACTIONS) {
            strcpy(account->transactions[account->transaction_count].transaction_type, "Withdraw");
            account->transactions[account->transaction_count].amount = amount;
            account->transaction_count++;
        }
        printf("Withdrew: %.2f\n", amount);
    } else {
        printf("Insufficient balance!\n");
    }
}

// Function to transfer money between accounts
void transfer(BankAccount *source, BankAccount *destination, float amount) {
    if (amount <= source->balance) {
        source->balance -= amount;
        destination->balance += amount;

        if (source->transaction_count < MAX_TRANSACTIONS) {
            strcpy(source->transactions[source->transaction_count].transaction_type, "Transfer Out");
            source->transactions[source->transaction_count].amount = amount;
            source->transaction_count++;
        }

        if (destination->transaction_count < MAX_TRANSACTIONS) {
            strcpy(destination->transactions[destination->transaction_count].transaction_type, "Transfer In");
            destination->transactions[destination->transaction_count].amount = amount;
            destination->transaction_count++;
        }

        printf("Transferred %.2f from Account %d to Account %d\n", amount, source->account_number, destination->account_number);
    } else {
        printf("Insufficient balance!\n");
    }
}

// Function to show the account balance
void show_balance(BankAccount *account) {
    printf("Account Holder: %s\n", account->account_holder);
    printf("Account Number: %d\n", account->account_number);
    printf("Balance: %.2f\n", account->balance);
}

// Function to show transaction history
void show_transaction_history(BankAccount *account) {
    printf("Transaction History:\n");
    for (int i = 0; i < account->transaction_count; i++) {
        printf("%s %.2f\n", account->transactions[i].transaction_type, account->transactions[i].amount);
    }
}

// Function to calculate and apply interest (Simple Interest)
void apply_interest(BankAccount *account, float time_in_years) {
    float interest = (account->balance * account->interest_rate * time_in_years) / 100;
    account->balance += interest;

    if (account->transaction_count < MAX_TRANSACTIONS) {
        strcpy(account->transactions[account->transaction_count].transaction_type, "Interest");
        account->transactions[account->transaction_count].amount = interest;
        account->transaction_count++;
    }

    printf("Applied Interest: %.2f\n", interest);
}

// Function to calculate and apply compound interest
void apply_compound_interest(BankAccount *account, float time_in_years) {
    float amount = account->balance * (1 + account->interest_rate / 100);
    for (int i = 1; i < time_in_years; i++) {
        amount *= (1 + account->interest_rate / 100);
    }
    float interest = amount - account->balance;
    account->balance += interest;

    if (account->transaction_count < MAX_TRANSACTIONS) {
        strcpy(account->transactions[account->transaction_count].transaction_type, "Compound Interest");
        account->transactions[account->transaction_count].amount = interest;
        account->transaction_count++;
    }

    printf("Applied Compound Interest: %.2f\n", interest);
}

int main() {
    BankAccount account1 = {"John Doe", 1001, 5000, {{0}}, 0, 5};  // Interest rate 5% for account1 (simple interest)
    BankAccount account2 = {"Jane Smith", 1002, 3000, {{0}}, 0, 5}; // Interest rate 5% for account2 (simple interest)

    int choice;
    float amount, time_in_years;

    while (1) {
        printf("\nBank Account Management System\n");
        printf("1. Check Balance\n");
        printf("2. Deposit Money\n");
        printf("3. Withdraw Money\n");
        printf("4. Transfer Money\n");
        printf("5. View Transaction History\n");
        printf("6. Apply Interest (Simple)\n");
        printf("7. Apply Compound Interest\n");
        printf("8. Exit\n");

        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                show_balance(&account1);
                break;
            case 2:
                printf("Enter deposit amount: ");
                scanf("%f", &amount);
                deposit(&account1, amount);
                break;
            case 3:
                printf("Enter withdrawal amount: ");
                scanf("%f", &amount);
                withdraw(&account1, amount);
                break;
            case 4:
                printf("Enter transfer amount: ");
                scanf("%f", &amount);
                transfer(&account1, &account2, amount);
                break;
            case 5:
                show_transaction_history(&account1);
                break;
            case 6:
                printf("Enter the time in years for interest: ");
                scanf("%f", &time_in_years);
                apply_interest(&account1, time_in_years);
                break;
            case 7:
                printf("Enter the time in years for compound interest: ");
                scanf("%f", &time_in_years);
                apply_compound_interest(&account1, time_in_years);
                break;
            case 8:
                printf("Exiting the system.\n");
                exit(0);
                break;
            default:
                printf("Invalid choice! Please try again.\n");
        }
    }

    return 0;
}
