# CoreBase JS Client

A simple yet powerful JavaScript/TypeScript library to connect your app with your CoreBase backend. It works just like Supabase, giving you easy tools for user logins, database management, and more.

## Installation

Getting started is easy! Just run this command in your project folder:

```bash
npm install corebase-js

# If you are using React or React Native, you'll need these too:
npm install --save-dev react @types/react
```

## Setup

First, let's connect the library to your project. You'll need your **Project URL** and **API Key**, which you can find in your CoreBase dashboard.

```typescript
import { createClient } from 'corebase-js';

const CLIENT_URL = 'https://corebase.trivyaa.in'; // Your CoreBase API URL
const API_KEY = 'pk_...'; // Your Project Public API Key

// Initialize the client
const corebase = createClient(CLIENT_URL, API_KEY);
```

## User Accounts (Authentication)

Managing your users is built right in. You can sign them up, log them in, and sign them out securely.

### Sign Up

Create a new account for a user:

```typescript
const { data, error } = await corebase.auth.signUp({
  email: 'user@example.com',
  password: 'securePassword123',
  name: 'John Doe'
});

if (error) {
  console.error('Signup failed:', error);
} else {
  console.log('Welcome!', data.user);
}
```

### Sign In

Log in an existing user. Their session is saved automatically (in your browser storage), so they stay logged in even if they refresh the page.

```typescript
const { data, error } = await corebase.auth.signIn({
  email: 'user@example.com',
  password: 'securePassword123'
});

if (error) {
  console.error('Login failed:', error);
} else {
  console.log('Logged in successfully:', data.user);
}
```

### Get Current User

Check who is currently logged in:

```typescript
const { data, error } = await corebase.auth.getUser();

if (data) {
  console.log('Current user:', data.user);
}
```

### Sign Out

Log out the current user and clear their secure session:

```typescript
await corebase.auth.signOut();
```

## Database Operations

Working with your database is simple. You can read, add, update, and remove data using easy commands.

### Read Data (Select)

**Get everything from a table:**

```typescript
const { data, error, count } = await corebase
  .from('posts')
  .select('*');
```

**Get only specific fields:**

```typescript
const { data } = await corebase
  .from('users')
  .select('id, name, email');
```

**Filter your results:**

Find posts that match specific conditions (like status = 'published'):

```typescript
const { data } = await corebase
  .from('posts')
  .select('*')
  .eq('status', 'published')
  .match({ author_id: 'user_123', category: 'tech' });
```

**Sort and Limit:**

Get the 10 most recent posts:

```typescript
const { data, count } = await corebase
  .from('posts')
  .select('*')
  .order('created_at', { ascending: false }) // Sort by newest first
  .limit(10)                                 // Get only 10
  .page(1);                                  // Page 1
```

**Get a Single Row:**

Fetch just one specific record (useful when searching by ID):

```typescript
const { data: user, error } = await corebase
  .from('users')
  .select('*')
  .eq('id', 1)
  .single();
```

### Add Data (Insert)

Add one or more rows to your table.

```typescript
// Add one row
const { data, error } = await corebase
  .from('posts')
  .insert({ 
    title: 'My New Post', 
    content: 'Hello World', 
    user_id: 'user_123' 
  });

// Add multiple rows at once
const { data, error } = await corebase
  .from('posts')
  .insert([
    { title: 'Post 1', user_id: 'user_123' },
    { title: 'Post 2', user_id: 'user_123' }
  ]);
```

### Update Data

Change existing data that matches certain conditions.

```typescript
const { data, error } = await corebase
  .from('posts')
  .update({ status: 'archived' }) // Set status to 'archived'
  .eq('status', 'draft')          // ...only for drafts
  .match({ user_id: 'user_123' }); // ...belonging to this user
```

### Remove Data (Delete)

Delete rows that match your conditions.

```typescript
const { data, error } = await corebase
  .from('posts')
  .delete()
  .eq('id', 123); // Delete post with ID 123
```

## File Storage

Upload files to your storage buckets easily.

### Upload a File

Upload a file directly from a browser input:

```typescript
// Example: <input type="file" id="fileInput" />
const fileInput = document.getElementById('fileInput') as HTMLInputElement;
const file = fileInput.files?.[0];

if (file) {
  const { data, error } = await corebase.storage.upload(file);

  if (error) {
    console.error('Upload failed:', error);
  } else {
    console.log('Success! File uploaded.');
    console.log('File Key:', data.key);
    console.log('File URL:', data.url);
  }
}
```

## Realtime Data (React / React Native)

Keep your app updated instantly when data changes using our `useQuery` hook.

```typescript
import { useQuery } from 'corebase-js/react';

// Define what you want to listen for
const query = {
  from: 'posts',
  select: ['id', 'title', 'likes'],
  where: { 
    status: 'published' 
  }
};

const MyComponent = () => {
  // Subscribe to updates. The component will re-render automatically when data changes.
  const { data, loading, error } = useQuery(corebase, query);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <ul>
      {data.map(post => (
        <li key={post.id}>
          {post.title} ({post.likes} likes)
        </li>
      ))}
    </ul>
  );
};
```

## TypeScript Support

We love TypeScript! The library comes with full type definitions so your code is safe and easy to write.

```typescript
import { User, AuthSession, ClientResponse } from 'corebase-js';

// Define the shape of your data
interface Post {
  id: number;
  title: string;
  content: string;
}

// Tell the client what to expect
const { data } = await corebase.from<Post>('posts').select('*');
// data is now known to be Post[] | null
```
