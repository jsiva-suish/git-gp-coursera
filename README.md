# Git for developers: managing workflows and conflicts

Codebase for Guided Project "Git for developers: managing workflows and conflicts" on Coursera platform

## Basic project setup

- Install virtual enviroment with its dependencies: `poetry install`
- Activate it: `poetry shell`
- To run the API, use the command: `uvicorn src.app:app --reload`

## Ready-to-use code

- Update items API route
```python
@app.put("/items/{item_id}")
def update_item(item_id:int, item:Item):
    """API route to update an item from database trought the item id.

    Parameters
    ----------
    item_id : int
        Item unique identification
    item : Item
        The item itself following the Item model

    Returns
    -------
    Item
        The item after it has been added to database
    """
    conn = get_db()
    conn.execute(
        "UPDATE items SET name = ?, price = ?, is_offer = ? WHERE id = ?",
        (item.name, item.price, int(item.is_offer) 
        if item.is_offer 
        else None, item_id),)
    conn.commit()
    return item
```
- Delete items API route
```python
@app.delete("/items/{item_id}")
def delete_item(item_id: int):
    """
    API route to delete an item from the database.

    Parameters
    ----------
    item_id : int
        The id of the item to be deleted.

    Returns
    -------
    dict
        A message indicating the deletion was successful.
    """
    conn = get_db()
    conn.execute("DELETE FROM items WHERE id = ?", (item_id,))
    conn.commit()
    return {"message": "Item deleted"}
```
- Read items API route
```python
@app.get("/items/")
def read_items():
    conn = get_db()
    items = conn.execute("SELECT * FROM items").fetchall()
    return [
        dict(item) for item in items]
```
- Read item API route
```python
@app.get("/items/{item_id}")
def read_item(item_id:int):
    conn = get_db()
    item = conn.execute("SELECT * FROM items WHERE id = ?", (item_id,)).fetchone()
    if item is None:
        raise HTTPException(
            status_code=404, 
    detail="Item not found"
            )
    return dict(item)
```
- Count offers API route
```python
@app.get("/items/count/")
def count_items():
    """
    API route to count the total number of items.

    Returns
    -------
    dict
        A dictionary containing the total count of items.
    """
    conn = get_db()
    cursor = conn.cursor()
    cursor.execute("SELECT COUNT(*) FROM items")
    count = cursor.fetchone()[0]
    return {"count": count}
```
- Retrieve offers API route
```python
@app.get("/items/offers/")
def get_offer_items():
    """
    API route to get all items that are offers.

    Returns
    -------
    List[Item]
        A list of items that are offers.
    """
    conn = get_db()
    cursor = conn.cursor()
    cursor.execute("SELECT id, name, price, is_offer FROM items WHERE is_offer = 1")
    rows = cursor.fetchall()
    return [Item(id=row[0], name=row[1], price=row[2], is_offer=bool(row[3])) for row in rows]
```
- Validation error
```python
from fastapi.exceptions import RequestValidationError
from fastapi.responses import JSONResponse
...
@app.exception_handler(RequestValidationError)
def validation_exception_handler(request, exc):
    return JSONResponse(status_code=400, content={"message": "Validation error", "errors": exc.errors()})
```
- Run script src/app.py to start API
```python
# Add this to end of your code
if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

## Conventional commit styles

![commit styles](./img/comimt-style.png)

# Cumulative activity

The steps to build a dice simulator using Python to learners work on as final cumulative activity.

Guidance:

- Always use conventional commit style within your commit messages.
- Use the code provided in each step.

## Step 1: Initial setup
**Features**
- Set up a basic Python project structure with a script containing no code (check content below).

**Git tasks**
- Start a repository in GitHub and clone it to your local machine.
- Create an initial commit with the basic project structure: the `dice_simulator.py` script.
- Push changes to GitHub.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
"""Dice simulator."""
```
</details>

## Step 2: Basic dice roll functionality
**Features**
- Implement a function to simulate rolling a single six-sided die.
- Print the result of the die roll.

**Git tasks**
- Create a new branch for the dice roll feature.
- Implement the dice roll functionality on the new branch.
- Merge the feature branch to `main` branch locally.
- Push changes to remote repository.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random

def roll_die():
    return random.randint(1, 6)

print("Dice simulator")
print(f"Rolled: {roll_die}")
```
</details>

## Step 3: Bug fix
**Features**
- Find and fix the bug within previous code.

**Git tasks**
- Revert commit that introduced the bug (here you might use git reset too).
- Fix the bug and commit it.
- Push changes to remote repository.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random

def roll_die():
    return random.randint(1, 6)

print("Dice simulator")
print(f"Rolled: {roll_die()}") # The function was not called in previous code!
```
</details>

## Step 4: Customizable dice
**Features**
- Allow the user to specify the number of sides on the die.
- Print the result of rolling a die with the specified number of sides.

**Git tasks**
- Create a new branch for the customizable dice feature.
- Implement the feature.
- Commit the changes.
- **Don't merge it to `main` yet!**

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random

def roll_die(sides=6):
    return random.randint(1, sides)

print("Dice simulator")
sides = int(input("Enter the number of sides on the die: "))
print(f"Rolled: {roll_die(sides)}")
```
</details>

## Step 5: Multiple dice roll
**Features**
- Allow the user to roll multiple dice with same side at once.
- Print the results of all the dice rolls.

**Git tasks**
- Emulate another developer by starting from `main` branch.
- Create a new branch for the multiple dice roll feature.
- Implement the feature.
- Commit the changes.
- Checkout to "customizable dice" feature.
- Merge "multiple dice roll" (this one from step 5) feature on "customizable dice" (from step 4) feature using either merge or rebase strategy.
- Solve conflicts.
- Push branch to remote repository.
- Open a Pull Requset and merge changes to main `branch`
- Pull changes to your local repository.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random

def roll_die(sides=6):
    return random.randint(1, sides)

def roll_multiple_dice(num_dice, sides=6):
    return [roll_die(sides) for _ in range(num_dice)]

print("Dice Simulator")
sides = int(input("Enter the number of sides on the die: "))
num_dice = int(input("Enter the number of dice to roll: "))
results = roll_multiple_dice(num_dice, sides)
print(f"Rolled: {results}")
```
</details>

## Step 6: Saving results to a file

From now on, the steps are suggestions for further development!

**Features**
- Save the results of each roll to a file.

**Git tasks**
- Create a new branch for the file saving feature.
- Implement the feature.
- Commit the changes.
- Merge the branch into the main branch.
- Push changes to remote repository.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random

def roll_die(sides=6):
    return random.randint(1, sides)

def roll_multiple_dice(num_dice, sides=6):
    return [roll_die(sides) for _ in range(num_dice)]

def save_results_to_file(results, filename="results.txt"):
    with open(filename, "w") as file:
        file.write("Rolled dice results:\n")
        file.write(", ".join(map(str, results)))
        file.write("\n")

print("Dice Simulator")
sides = int(input("Enter the number of sides on the die: "))
num_dice = int(input("Enter the number of dice to roll: "))
results = roll_multiple_dice(num_dice, sides)
print(f"Rolled: {results}")
save_results_to_file(results)
print("Results saved to results.txt")
print("End of simulation")
print("Thank you for using the simulator")
```
</details>

## Step 7: Command Line Interface (CLI)
**Features**
- Allow users to specify the number of dice and sides through command line arguments.

**Git tasks**
- Create a new branch for the CLI feature.
- Implement the feature.
- Commit the changes.
- Merge the branch into the main branch.
- Push changes to remote repository.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random
import argparse

def roll_die(sides=6):
    return random.randint(1, sides)

def roll_multiple_dice(num_dice, sides=6):
    return [roll_die(sides) for _ in range(num_dice)]

def save_results_to_file(results, filename="results.txt"):
    with open(filename, "w") as file:
        file.write("Rolled dice results:\n")
        file.write(", ".join(map(str, results)))
        file.write("\n")

def main():
    parser = argparse.ArgumentParser(description="Dice Simulator")
    parser.add_argument("--sides", type=int, default=6, help="Number of sides on the die")
    parser.add_argument("--num-dice", type=int, default=1, help="Number of dice to roll")
    parser.add_argument("--output", type=str, default="results.txt", help="Output file for results")
    args = parser.parse_args()

    results = roll_multiple_dice(args.num_dice, args.sides)
    print(f"Rolled: {results}")
    save_results_to_file(results, args.output)
    print(f"Results saved to {args.output}")
    print("End of simulation")
    print("Thank you for using the simulator")

if __name__ == "__main__":
    main()

```
</details>

## Step 8: Unit testing
**Features**
- Write unit tests for the dice rolling functions.
- Ensure all tests pass.

**Git tasks**
- Create a new branch for adding unit tests.
- Implement the tests.
- Commit the changes.
- Merge the branch into the main branch.


<details>
<summary>Check script content here</summary>

```python
# test_dice_simulator.py
import unittest
from dice_simulator import roll_die, roll_multiple_dice

class TestDiceSimulator(unittest.TestCase):

    def test_roll_die(self):
        result = roll_die()
        self.assertTrue(1 <= result <= 6)

    def test_roll_die_custom_sides(self):
        result = roll_die(10)
        self.assertTrue(1 <= result <= 10)

    def test_roll_multiple_dice(self):
        results = roll_multiple_dice(5)
        self.assertEqual(len(results), 5)
        for result in results:
            self.assertTrue(1 <= result <= 6)

    def test_roll_multiple_dice_custom_sides(self):
        results = roll_multiple_dice(5, 10)
        self.assertEqual(len(results), 5)
        for result in results:
            self.assertTrue(1 <= result <= 10)

if __name__ == "__main__":
    unittest.main()
```
</details>


## Step 10: Documentation
**Features**
- Create a README file with instructions on how to use the dice simulator.
- Add comments and docstrings to the code for better understanding.

**Git tasks**
- Create a new branch for documentation.
- Ddd comments/docstrings.
- Commit the changes.
- Merge the branch into the main.

<details>
<summary>Check script content here</summary>

```python
# dice_simulator.py
import random
import argparse

def roll_die(sides=6):
    """
    Simulate rolling a single die with a given number of sides.
    
    Parameters:
        sides (int): Number of sides on the die. Default is 6.
        
    Returns:
        int: Result of the die roll.
    """
    return random.randint(1, sides)

def roll_multiple_dice(num_dice, sides=6):
    """
    Simulate rolling multiple dice with a given number of sides.
    
    Parameters:
        num_dice (int): Number of dice to roll.
        sides (int): Number of sides on each die. Default is 6.
        
    Returns:
        list: Results of all the dice rolls.
    """
    return [roll_die(sides) for _ in range(num_dice)]

def save_results_to_file(results, filename="results.txt"):
    """
    Save the results of dice rolls to a file.
    
    Parameters:
        results (list): List of results from dice rolls.
        filename (str): Name of the file to save results. Default is "results.txt".
    """
    with open(filename, "w") as file:
        file.write("Rolled dice results:\n")
        file.write(", ".join(map(str, results)))
        file.write("\n")

def main():
    """
    Main function to handle command line arguments and run the dice simulator.
    """
    parser = argparse.ArgumentParser(description="Dice Simulator")
    parser.add_argument("--sides", type=int, default=6, help="Number of sides on the die")
    parser.add_argument("--num-dice", type=int, default=1, help="Number of dice to roll")
    parser.add_argument("--output", type=str, default="results.txt", help="Output file for results")
    args = parser.parse_args()

    results = roll_multiple_dice(args.num_dice, args.sides)
    print(f"Rolled: {results}")
    save_results_to_file(results, args.output)
    print(f"Results saved to {args.output}")
    print("End of simulation")
    print("Thank you for using the simulator")

if __name__ == "__main__":
    main()
```
</details>
