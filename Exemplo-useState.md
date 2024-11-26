# Exemplo useState

```jsx
"use client"

import React, { useState } from "react"

const SimpleCounterExample = () => {
  const [count, setCount] = useState(0)
  const [text, setText] = useState("")

  const incrementCount = () => setCount(count + 1)
  const decrementCount = () => setCount(count - 1)
  const resetCount = () => setCount(0)

  return (
    <div
      style={{
        maxWidth: "400px",
        margin: "0 auto",
        padding: "20px",
        border: "1px solid #ccc",
        borderRadius: "8px",
      }}
    >
      <h2 style={{ textAlign: "center" }}>Exemplo de useState</h2>

      <div style={{ textAlign: "center", marginBottom: "20px" }}>
        <p style={{ fontSize: "24px", fontWeight: "bold" }}>
          Contador: {count}
        </p>

        <div style={{ display: "flex", gap: "8px", justifyContent: "center" }}>
          <button
            onClick={decrementCount}
            style={{
              padding: "8px 16px",
              border: "1px solid #ccc",
              borderRadius: "4px",
            }}
          >
            Diminuir
          </button>
          <button
            onClick={incrementCount}
            style={{
              padding: "8px 16px",
              border: "1px solid #ccc",
              borderRadius: "4px",
              backgroundColor: "green",
            }}
          >
            Aumentar
          </button>
          <button
            onClick={resetCount}
            style={{
              padding: "8px 16px",
              border: "1px solid #ccc",
              borderRadius: "4px",
              backgroundColor: "red",
            }}
          >
            Resetar
          </button>
        </div>
      </div>

      <div>
        <input
          type="text"
          value={text}
          onChange={(e) => setText(e.target.value)}
          placeholder="Digite algo aqui..."
          style={{
            width: "100%",
            padding: "8px",
            border: "1px solid #ccc",
            borderRadius: "4px",
            marginBottom: "8px",
            color: "gray",
          }}
        />
        <p>Texto digitado: {text ? text : "(vazio)"}</p>
      </div>
    </div>
  )
}

export default SimpleCounterExample
```
