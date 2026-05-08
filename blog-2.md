# Prevent code duplication with 'Pick' and 'Omit' utility types creating specialized "slices" of a master interface. Following the DRY principle

Developers often work with complex object types or interface that have many properties. But not every function or property is always needed. Manually creating multiple interfaces for subsets leads to code duplication and harder maintenance. This is where TypeScript's `Pick` and `Omit` utility types comes in. A very useful tool to keep the codebase DRY.

## Some Examples of Code Duplication Without Utility Types

Let's consider a real-world scenario: managing user data in an application.

```typescript
interface User {
  id: number;
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  age: number;
  phone: string;
  role: "admin" | "user";
  createdAt: Date;
  updatedAt: Date;
}
```

Now, let's say developer needs to send user data to the frontend (without sensitive fields)

Without utility types:

```typescript
// NOT DRY: Duplicating code
interface UserProfile {
  id: number;
  email: string;
  firstName: string;
  lastName: string;
  age: number;
  phone: string;
  role: "admin" | "user";
  createdAt: Date;
  updatedAt: Date;
}

interface UserUpdate {
  email: string;
  firstName: string;
  lastName: string;
  age: number;
  phone: string;
}

interface UserResponse {
  id: number;
  email: string;
  firstName: string;
  lastName: string;
  age: number;
  phone: string;
  role: "admin" | "user";
}
```

This approach is very repetitive and a nightmare towards maintenance

## Solution 1: Pick - Selecting Properties

The `Pick` utility type creates a new type by **selecting specific properties** from an existing interface.

### Example

```typescript
interface User {
  id: number;
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  age: number;
  phone: string;
  role: "admin" | "user";
  createdAt: Date;
  updatedAt: Date;
}

// Using Pick to create specialized types
type UserProfile = Pick<
  User,
  | "id"
  | "email"
  | "firstName"
  | "lastName"
  | "age"
  | "phone"
  | "role"
  | "createdAt"
  | "updatedAt"
>;

type UserResponse = Pick<
  User,
  "id" | "email" | "firstName" | "lastName" | "role"
>;

type UserPublicInfo = Pick<User, "firstName" | "lastName" | "id">;
```

### Benefits

1. Single Source of Truth
2. Explicit
3. No Duplication
4. Type-Safe

```typescript
// Error: 'nonexistent' is not a property of User
type BadType = Pick<User, "id" | "nonexistent">;
```

## Solution 2: Omit - Excluding Properties

The `Omit` utility type creates a new type by **excluding specific properties** from an existing interface.

### Example

```typescript
// ✓ Using Omit to exclude sensitive fields
type UserUpdate = Omit<
  User,
  "id" | "password" | "createdAt" | "updatedAt" | "role"
>;

type UserInput = Omit<User, "id" | "createdAt" | "updatedAt">;

type PublicUser = Omit<User, "password">;
```

### When to Use Omit vs Pick

```typescript
interface User {
  // 10+ properties...
}

// Omit is cleaner here - only listing what to exclude
type PublicUser = Omit<User, "password" | "ssn" | "internalId">;

// Pick would be verbose - listing all non-sensitive fields
type PublicUser = Pick<User, "id" | "email" | "firstName" | "...">;
```

Rule of thumb is, whichever takes fewer declaration, that one should be used.

## Examples: DRY

### Example 1: API Endpoints

```typescript
interface User {
  id: number;
  email: string;
  password: string;
  firstName: string;
  lastName: string;
  role: "admin" | "user";
  createdAt: Date;
}

// For registration - accept input without id, timestamps
async function registerUser(
  data: Omit<User, "id" | "createdAt">,
): Promise<User> {
  // Process registration
  return { ...data, id: 1, createdAt: new Date() };
}

// For API response - exclude sensitive data
async function getUser(id: number): Promise<Omit<User, "password">> {
  return {
    id: 1,
    email: "user@example.com",
    firstName: "John",
    lastName: "Doe",
    role: "user",
    createdAt: new Date(),
  };
}
```

### Example 2: Form Handling

```typescript
interface Product {
  id: number;
  name: string;
  description: string;
  price: number;
  inventory: number;
  category: string;
  createdAt: Date;
  updatedAt: Date;
}

// Form to create new product - only editable fields
type ProductFormData = Omit<Product, "id" | "createdAt" | "updatedAt">;

function handleProductSubmit(formData: ProductFormData) {
  // TypeScript ensures only valid fields are passed
  console.log(formData.name, formData.price);
}
```

## Conclusion

The `Pick` and `Omit` utility types are essential tools for maintaining DRY code in TypeScript. As the projects grows, utility types becomes very valuable. And as always, Always keep your codebase DRY
