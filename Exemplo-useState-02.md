# Exemplo useState - Carrinho

```jsx
"use client"
import React, { useState } from "react"

const ShoppingCart = () => {
  const [cart, setCart] = useState([])
  const [product, setProduct] = useState("")

  const addItem = () => {
    if (product.trim() !== "") {
      setCart([...cart, product])
      setProduct("")
    }
  }

  const removeItem = (index) => {
    const updatedCart = cart.filter((_, i) => i !== index)
    setCart(updatedCart)
  }

  return (
    <div className="min-h-screen bg-gray-100 flex items-center justify-center">
      <div className="bg-white shadow-md rounded-lg p-6 w-96">
        <h1 className="text-2xl font-bold mb-4 text-gray-800">Shopping Cart</h1>

        <div className="flex gap-2 mb-4">
          <input
            type="text"
            placeholder="Add a product"
            value={product}
            onChange={(e) => setProduct(e.target.value)}
            className="flex-1 p-2 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-500"
          />
          <button
            onClick={addItem}
            className="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600"
          >
            Add
          </button>
        </div>

        {cart.length > 0 ? (
          <ul className="divide-y divide-gray-200">
            {cart.map((item, index) => (
              <li
                key={index}
                className="flex justify-between items-center py-2"
              >
                <span className="text-gray-700">{item}</span>
                <button
                  onClick={() => removeItem(index)}
                  className="text-red-500 hover:underline"
                >
                  Remove
                </button>
              </li>
            ))}
          </ul>
        ) : (
          <p className="text-gray-500 text-center">Your cart is empty.</p>
        )}
      </div>
    </div>
  )
}

export default ShoppingCart
```
