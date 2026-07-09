import json
from pathlib import Path
from datetime import datetime
from collections import defaultdict

DB_FILE = Path("expenses.json")


class ExpenseTracker:

    def __init__(self):
        if not DB_FILE.exists():
            DB_FILE.write_text("[]")

    def load(self):
        with open(DB_FILE, "r", encoding="utf-8") as f:
            return json.load(f)

    def save(self, data):
        with open(DB_FILE, "w", encoding="utf-8") as f:
            json.dump(data, f, indent=4)

    def add_expense(self):
        amount = float(input("Amount: "))
        category = input(
            "Category (food, transport, etc): "
        )

        description = input("Description: ")

        data = self.load()

        data.append({
            "amount": amount,
            "category": category,
            "description": description,
            "date": datetime.now().strftime("%Y-%m-%d")
        })

        self.save(data)

        print("Expense added.")

    def monthly_report(self):

        current_month = datetime.now().strftime("%Y-%m")

        data = self.load()

        monthly = [
            item for item in data
            if item["date"].startswith(current_month)
        ]

        total = sum(
            item["amount"]
            for item in monthly
        )

        categories = defaultdict(float)

        for item in monthly:
            categories[item["category"]] += item["amount"]

        print("\n=== Monthly Report ===")
        print(f"Total Spending: {total:.2f}\n")

        for cat, value in sorted(
            categories.items(),
            key=lambda x: x[1],
            reverse=True
        ):
            print(f"{cat:<15} {value:.2f}")

    def largest_expenses(self):

        data = self.load()

        expenses = sorted(
            data,
            key=lambda x: x["amount"],
            reverse=True
        )[:5]

        print("\nTop Expenses\n")

        for item in expenses:
            print(
                f"{item['amount']:.2f} | "
                f"{item['category']} | "
                f"{item['description']}"
            )


def main():

    tracker = ExpenseTracker()

    while True:

        print("\n=== Expense Tracker ===")
        print("1. Add Expense")
        print("2. Monthly Report")
        print("3. Top Expenses")
        print("4. Exit")

        choice = input("Select: ")

        if choice == "1":
            tracker.add_expense()

        elif choice == "2":
            tracker.monthly_report()

        elif choice == "3":
            tracker.largest_expenses()

        elif choice == "4":
            break

        else:
            print("Invalid choice")


if __name__ == "__main__":
    main()
