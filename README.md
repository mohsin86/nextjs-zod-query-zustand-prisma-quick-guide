# 🧠 DEV QUICK GUIDE (Zod + React Query + Zustand + Prisma)

A simple reference for daily development.

---

# 🧩 1. ZOD (Validation)

## 📌 When to use
- Validate forms (login, register, create todo)
- Validate API request body

## ✅ Example

```ts
import { z } from 'zod';

export const userSchema = z.object({
  username: z.string().min(3),
  email: z.string().email(),
  password: z.string().min(6),
});
```

## ✅ Validate data

```ts
const result = userSchema.safeParse(data);

if (!result.success) {
  console.log(result.error);
}
```

## 🔥 With React Hook Form

```ts
useForm({
  resolver: zodResolver(userSchema),
});
```

---

# ⚡ 2. REACT QUERY (Server State)

## 📌 Use for:
- API data (users, todos, profile)
- Fetching + caching
- Mutations (create/update/delete)

---

## ✅ Basic Query

```ts
import { useQuery } from '@tanstack/react-query';

const { data, isLoading } = useQuery({
  queryKey: ['user', username],
  queryFn: () => fetch(`/api/user/${username}`).then(res => res.json()),
});
```

---

## 🔄 Mutation (Update / Create)

```ts
import { useMutation, useQueryClient } from '@tanstack/react-query';

const queryClient = useQueryClient();

const mutation = useMutation({
  mutationFn: (data) =>
    fetch('/api/todo', {
      method: 'POST',
      body: JSON.stringify(data),
    }),

  onSuccess: () => {
    queryClient.invalidateQueries(['todos']);
  },
});
```

---

## 🚨 Rules
- ✅ Use for backend data only
- ❌ Don’t store UI state here

---

# 🧠 3. ZUSTAND (Client State)

## 📌 Use for:
- Auth state
- UI state (modal, sidebar)
- Temporary state

---

## ✅ Store Example

```ts
import { create } from 'zustand';

export const useAuthStore = create((set) => ({
  user: null,

  setUser: (user) => set({ user }),

  logout: () => {
    fetch('/api/logout', { method: 'POST' });
    set({ user: null });
  },
}));
```

---

## ✅ Use in Component

```ts
const { user, logout } = useAuthStore();
```

---

## 🚨 Rules
- ✅ Use for frontend state
- ❌ Don’t store API data here

---

# 🗄️ 4. PRISMA (Database ORM)

## 📌 Use for:
- Database queries
- Relations (User → Todo)

---

## ✅ Example Model

```prisma
model User {
  id       String @id @default(cuid())
  username String @unique
  todos    Todo[]
}

model Todo {
  id     String @id @default(cuid())
  title  String
  userId String
  user   User   @relation(fields: [userId], references: [id])
}
```

---

## ✅ Query Example

```ts
const user = await prisma.user.findUnique({
  where: { username },
  include: { todos: true },
});
```

---

## ✅ Create

```ts
await prisma.todo.create({
  data: {
    title: 'New Task',
    userId,
  },
});
```

---

## 🚨 Common Errors

- ❌ "undefined in where" → param missing
- ❌ relation error → missing opposite relation
- ❌ enum error → duplicate migrations

---

# 🧱 HOW EVERYTHING CONNECTS

```
Frontend (React)
   ↓
React Query → API → Prisma → DB
   ↑
Zustand (UI State)
```

---

# 🔥 BEST PRACTICE SUMMARY

| Tool          | Use for                  |
|---------------|------------------------|
| Zod           | Validation             |
| React Query   | API data (server state)|
| Zustand       | UI / Auth state        |
| Prisma        | Database               |

---

# ⚠️ GOLDEN RULES

- ❌ Don’t use Zustand for API data  
- ❌ Don’t use React Query for UI state  
- ✅ Always validate with Zod  
- ✅ Keep Prisma logic in services  

---

# 🚀 QUICK PATTERN (REAL FLOW)

### Create Todo

1. Validate (Zod)
2. Call API (React Query mutation)
3. Save (Prisma)
4. Refresh UI (invalidateQueries)

---

# 🧪 DEBUG CHECKLIST

- API not working? → check `res.ok`
- Prisma error? → check `where` values
- State not updating? → check query invalidation
- Auth issue? → check cookie/token

---

# 📌 FUTURE EXTENSIONS

- Will Update this doc

