import sqlite3

def update_balance(account_id, amount):
    conn = sqlite3.connect('bank.db')
    cursor = conn.cursor()

    try:
        cursor.execute('BEGIN TRANSACTION')

        cursor.execute('SELECT balance FROM accounts WHERE id = ?', (account_id,))
        current_balance = cursor.fetchone()[0]

        new_balance = current_balance + amount
        cursor.execute('UPDATE accounts SET balance = ? WHERE id = ?', (new_balance, account_id))

        conn.commit()
    except Exception as e:
        conn.rollback()
        print(f"Transaction failed: {e}")
    finally:
        conn.close()

def main():
    # Original balance
    original_balance = 1000

    # Account ID and amount to update balance
    account_id = 123
    update_amount = 200

    # Update balance
    update_balance(account_id, update_amount)

    # Check consistency
    conn = sqlite3.connect('bank.db')
    cursor = conn.cursor()
    cursor.execute('SELECT balance FROM accounts WHERE id = ?', (account_id,))
    updated_balance = cursor.fetchone()[0]
    conn.close()

    # Check consistency
    if updated_balance == original_balance + update_amount:
        print("Consistency maintained")
    else:
        print("Consistency violated")

if __name__ == "__main__":
    main()
