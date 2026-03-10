# Revisando typescript

# 🛒 Ecommerce TypeScript — Modelagem e Carrinho

Projeto de estudo focado na modelagem de domínio de um e-commerce utilizando **TypeScript**, com ênfase em tipagem estática, orientação a objetos e Higher-Order Functions. Exercício do curso FullStack da IRede/Softex.

---

## Estrutura de arquivos

```
src/
└── model/
    ├── category.model.ts
    ├── product.model.ts
    ├── user.model.ts
    └── cart.model.ts
index.ts
```

---

##  Alterações realizadas

### `category.model.ts`
- Adicionado o atributo `id: number` ao construtor da classe `Category`.

```typescript
export class Category {
  constructor(
    public id: number,
    public name: string
  ) {}
}
```

---

### `product.model.ts`
- Adicionado o atributo `id: number` ao construtor da classe `Product`.
- O parâmetro `discount` permanece **opcional** (`?`), com fallback para `0` no cálculo.

```typescript
export class Product {
  constructor(
    public id: number,
    public name: string,
    public img: string,
    public price: number,
    public category: Category,
    public discount?: number
  ) {}

  priceWithDiscountApplied(): number {
    return this.price * (1 - (this.discount || 0));
  }
}
```

---

### `user.model.ts` *(arquivo novo)*
- Criada a classe `User` com o atributo `role` tipado como **Literal Type**, impedindo valores inválidos em tempo de compilação.

```typescript
type Role = "ADMIN" | "CUSTOMER";

export class User {
  constructor(
    public id: number,
    public username: string,
    public email: string,
    public role: Role
  ) {}
}
```

---

### `cart.model.ts`
- Interface `CartItem` criada para tipar os itens do carrinho.
- Adicionados os métodos `getTotalItems()` e `getFinalPrice()` utilizando `.reduce()`.
- O método `addItem()` usa `.find()` para verificar se o produto já existe — acumulando a quantidade sem duplicar entradas.
- `getFinalPrice()` reutiliza `priceWithDiscountApplied()` do `Product`.

```typescript
interface CartItem {
  product: Product;
  quantity: number;
}

export class Cart {
  constructor(public listproducts: CartItem[] = []) {}

  addItem(product: Product, quantity: number): void {
    const existingItem = this.listproducts.find(
      item => item.product.id === product.id
    );
    if (existingItem) {
      existingItem.quantity += quantity;
    } else {
      this.listproducts.push({ product, quantity });
    }
  }

  getTotalItems(): number {
    return this.listproducts.reduce((total, item) => total + item.quantity, 0);
  }

  getFinalPrice(): number {
    return this.listproducts.reduce((total, item) =>
      total + item.product.priceWithDiscountApplied() * item.quantity, 0
    );
  }
}
```

---

##  Como testar

```typescript
const category = new Category(1, 'Roupa');
const product  = new Product(1, 'Camiseta', 'https://example.com/camiseta.jpg', 29.99, category, 0.2);
const cart     = new Cart([{ product, quantity: 2 }]);

cart.addItem(product, 2);
cart.addItem(product, 1); // acumula — não duplica

console.log(product.category.name);           // "Roupa"
console.log(product.priceWithDiscountApplied()); // 23.992
console.log(cart.getTotalItems());            // 5
console.log(cart.getFinalPrice());            // 119.96

// Usuários
const admin    = new User(1, 'joao', 'joao@email.com', 'ADMIN');
const customer = new User(2, 'maria', 'maria@email.com', 'CUSTOMER');
// const invalid = new User(3, 'x', 'x@x.com', 'GUEST'); // ❌ erro de compilação
```

Execute com:
```bash
npm start
```

---

## 🟦 Conceitos de TypeScript utilizados

| Conceito | Onde foi aplicado |
|---|---|
| **Classes** | `Category`, `Product`, `User`, `Cart` |
| **Interfaces** | `CartItem` — tipagem dos itens do carrinho |
| **Literal Types** | `type Role = "ADMIN" \| "CUSTOMER"` |
| **Parâmetros opcionais** (`?`) | `discount?` em `Product` |
| **Modificador `public`** | Declara e atribui atributos direto no construtor |
| **Tipagem de retorno** | `: number`, `: void` em todos os métodos |
| **`import` / `export`** | Separação de responsabilidades entre arquivos |
| **Higher-Order Functions** | `.find()`, `.reduce()` em `Cart` |
