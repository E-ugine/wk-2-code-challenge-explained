document.addEventListener('DOMContentLoaded', () => {
This event listener ensures that the code inside it runs only after the entire DOM is fully loaded. This prevents errors that could occur if the script tries to access elements that haven't been rendered yet.


These lines are fetching the DOM elements that will be interacted with: the input field, add button, clear button, checkout button, and the container for the shopping list items.
    const itemInput = document.getElementById('newItem'); 
    const addButton = document.getElementById('addButton');
    const clearButton = document.getElementById('clearList');
    const checkoutButton = document.getElementById('checkoutButton');
    const listContainer = document.getElementById('shoppingList');



This line retrieves the shopping list from local storage if it exists. If it doesn't, it initializes the shopping list as an empty array.
    let shoppingList = JSON.parse(localStorage.getItem('shoppingList')) || [];



This function below clears the current list in the HTML and re-renders it based on the shoppingList array. It creates a new list item for each entry in the array, adds appropriate event listeners for toggling purchase status and editing the item, and appends each list item to the container.


const renderList = () => {
    listContainer.innerHTML = ''; // Clear the list container

    shoppingList.forEach((item, index) => { // Iterate over each item in the shopping list
        const listItem = document.createElement('li'); // Create a new list item element
        listItem.textContent = item.name; // Set the text content to the name of the item
        listItem.classList.toggle('purchased', item.purchased); // Add or remove the 'purchased' class based on the item's purchased status

        listItem.addEventListener('click', () => togglePurchased(index)); // Adds an event listener to toggle the purchased state when the item is clicked
        listItem.addEventListener('dblclick', () => editItem(index)); // Add an event listener to edit the item when it is double-clicked

        listContainer.appendChild(listItem); // Append the list item to the list container
    });
};


This function adds a new item to the shopping list. It first checks if the item name is not empty, then checks if the item already exists in the list. If it exists, it asks for user confirmation to add the item again. If confirmed or if the item doesn't exist, it adds the item to the list, updates the local storage, and re-renders the list.

    const addItem = () => {
        const itemName = itemInput.value.trim();
        if (itemName) {
            const existingItemIndex = shoppingList.findIndex(item => item.name === itemName);
            if (existingItemIndex > -1) {
                const confirmAdd = confirm(`"${itemName}" is already in the cart. Do you want to add it again?`);
                if (!confirmAdd) {
                    itemInput.value = '';
                    return;
                }
            }
            shoppingList.push({ name: itemName, purchased: false });
            itemInput.value = '';
            updateLocalStorage();
            renderList();
        }
    };



This function toggles the purchased status of an item when it is clicked, updates the local storage, and re-renders the list.
    const togglePurchased = (index) => {
        shoppingList[index].purchased = !shoppingList[index].purchased;
        updateLocalStorage();
        renderList();
    };


This function allows the user to edit an item's name. It prompts the user to enter a new name, updates the item in the list if the new name is valid, updates the local storage, and re-renders the list.
        const editItem = (index) => {
        const newItemName = prompt('Edit item:', shoppingList[index].name);
        if (newItemName && newItemName.trim()) {
            shoppingList[index].name = newItemName.trim();
            updateLocalStorage();
            renderList();
        }
    };



This function clears the entire shopping list, updates the local storage, and re-renders the list.
    const clearList = () => {
        shoppingList = [];
        updateLocalStorage();
        renderList();
    };





This function handles the checkout process. It iterates through the shopping list and asks the user if they want to remove each item. If confirmed, the item is removed from the list. Then, it marks all remaining items as purchased, updates the local storage, re-renders the list, and alerts the user that all items are marked as purchased.

    const proceedToCheckout = () => {
        let itemsToRemove = [];
        shoppingList.forEach((item, index) => {
            const removeItem = confirm(`Do you want to remove "${item.name}" from the cart?`);
            if (removeItem) {
                itemsToRemove.push(index);
            }
        });

        itemsToRemove.reverse().forEach(index => {
            shoppingList.splice(index, 1);
        });

        shoppingList = shoppingList.map(item => ({ ...item, purchased: true }));
        updateLocalStorage();
        renderList();
        alert("All items marked as purchased. You can continue adding new items.");
    };



This function updates the local storage with the current state of the shopping list.
    const updateLocalStorage = () => {
        localStorage.setItem('shoppingList', JSON.stringify(shoppingList));
    };



These lines add event listeners to the add, clear, and checkout buttons, linking them to their respective functions. The last line calls renderList() to render the shopping list when the page is first loaded.
    addButton.addEventListener('click', addItem);
    clearButton.addEventListener('click', clearList);
    checkoutButton.addEventListener('click', proceedToCheckout);

    renderList();
});
