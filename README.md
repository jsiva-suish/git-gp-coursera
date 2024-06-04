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