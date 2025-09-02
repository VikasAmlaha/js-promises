# JAVASCRIPT PROMISES

### The 80/20 Approach to Learning Promises
The 80/20 principle suggests we focus on the most impactful Promise features and patterns:
- **Core Concepts**: Creating and using Promises (`.then()`, `.catch()`, `.finally()`).
- **Async/Await**: The modern, readable way to handle Promises.
- **Handling Multiple Promises**: Using `Promise.all()` for parallel execution.
- **Error Handling**: Ensuring robust code with `try/catch` or `.catch()`.
- **Real-World Use**: Applying Promises to common tasks like API calls.

These cover the majority of use cases (e.g., fetching data, handling async operations) while avoiding niche topics like `Promise.race()`, `Promise.any()`, or manual promisification unless you need them later.

---

### 1. Core Concepts: Creating and Using Promises
A Promise represents an asynchronous operation that will eventually resolve with a value or reject with an error.

#### Creating a Promise
```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const success = Math.random() > 0.3; // 70% chance of success
    if (success) resolve("Operation worked!");
    else reject("Operation failed!");
  }, 1000);
});
```

#### Using a Promise
Use `.then()` for success, `.catch()` for errors, and `.finally()` for cleanup.

```javascript
myPromise
  .then((result) => {
    console.log(result); // Operation worked!
  })
  .catch((error) => {
    console.error(error); // Operation failed!
  })
  .finally(() => {
    console.log("Done!");
  });
```

**Key 20%**: 
- `.then()` handles resolved values.
- `.catch()` catches errors.
- `.finally()` runs regardless of outcome.
- Promises are great for one-off async tasks like timeouts or API calls.

---

### 2. Async/Await: A Cleaner Syntax
`async/await` is syntactic sugar for Promises, making code look synchronous and easier to read. An `async` function always returns a Promise, and `await` pauses execution until a Promise resolves.

#### Example
```javascript
async function fetchData() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/posts/1");
    if (!response.ok) throw new Error("Network error");
    const data = await response.json();
    console.log("Post title:", data.title);
  } catch (error) {
    console.error("Error:", error.message);
  } finally {
    console.log("Fetch complete");
  }
}

fetchData();
```

**Key 20%**:
- Use `async` to define a function that uses `await`.
- Use `try/catch` for error handling.
- `await` only works inside `async` functions.
- Prefer `async/await` for readability in most cases.

---

### 3. Handling Multiple Promises with Promise.all()
`Promise.all()` takes an array of Promises and resolves when all succeed, returning an array of results. If any Promise rejects, it rejects immediately.

#### Example
```javascript
async function fetchMultiplePosts() {
  try {
    const promises = [
      fetch("https://jsonplaceholder.typicode.com/posts/1").then((res) => res.json()),
      fetch("https://jsonplaceholder.typicode.com/posts/2").then((res) => res.json()),
    ];
    const posts = await Promise.all(promises);
    posts.forEach((post) => console.log("Post title:", post.title));
  } catch (error) {
    console.error("Error fetching posts:", error);
  }
}

fetchMultiplePosts();
```

**Key 20%**:
- Use `Promise.all()` for parallel execution of independent Promises.
- Wrap in `try/catch` to handle errors from any Promise.
- Ideal for fetching multiple resources at once.

---

### 4. Error Handling
Proper error handling is critical to avoid uncaught Promise rejections, which can crash your app or cause unexpected behavior.

#### Example
```javascript
async function riskyOperation() {
  try {
    const response = await fetch("https://jsonplaceholder.typicode.com/posts/999"); // Non-existent post
    if (!response.ok) throw new Error("Failed to fetch");
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Caught error:", error.message); // Caught error: Failed to fetch
  }
}

riskyOperation();
```

**Key 20%**:
- Always use `.catch()` or `try/catch` to handle errors.
- Check for conditions like `response.ok` in API calls.
- Unhandled rejections can cause warnings or crashes, so be proactive.

---

### 5. Real-World Use: API Calls
Promises are most commonly used for API requests via `fetch`. Here’s a practical example combining everything.

#### Example: Fetch User and Their Posts
```javascript
async function getUserAndPosts(userId) {
  try {
    // Fetch user
    const userResponse = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
    if (!userResponse.ok) throw new Error("Failed to fetch user");
    const user = await userResponse.json();
    console.log("User:", user.name);

    // Fetch posts in parallel
    const postIds = [1, 2, 3].map((id) => userId * 10 + id); // Example: posts 11, 12, 13 for user 1
    const postPromises = postIds.map((id) =>
      fetch(`https://jsonplaceholder.typicode.com/posts/${id}`).then((res) => res.json())
    );
    const posts = await Promise.all(postPromises);
    console.log("Posts:", posts.map((post) => post.title));
  } catch (error) {
    console.error("Error:", error.message);
  } finally {
    console.log("Operation complete");
  }
}

getUserAndPosts(1);
```

**Key 20%**:
- Use `fetch` for API calls, which returns a Promise.
- Combine sequential (`await`) and parallel (`Promise.all()`) operations as needed.
- Always validate responses and handle errors.

---

### 80/20 Exercises
Here are three focused exercises to practice the 20% of Promise concepts that cover 80% of use cases. Try them yourself, then check the solutions.

#### Exercise 1: Create and Handle a Promise
Create a Promise that resolves with a random string after 1 second or rejects if a condition fails. Use `.then()` and `.catch()` to handle it.

**Solution**:
```javascript
const randomStringPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    const strings = ["Apple", "Banana", "Orange"];
    const randomString = strings[Math.floor(Math.random() * strings.length)];
    if (randomString !== "Orange") resolve(randomString);
    else reject("Got Orange!");
  }, 1000);
});

randomStringPromise
  .then((result) => console.log("Got:", result)) // e.g., Got: Apple
  .catch((error) => console.error("Error:", error)); // Error: Got Orange!
```

#### Exercise 2: Async/Await with Error Handling
Write an `async` function to fetch a post by ID and log its title. Handle errors if the post doesn’t exist.

**Solution**:
```javascript
async function fetchPost(id) {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`);
    if (!response.ok) throw new Error("Post not found");
    const post = await response.json();
    console.log("Title:", post.title);
  } catch (error) {
    console.error("Error:", error.message);
  }
}

fetchPost(999); // Error: Post not found
fetchPost(1); // Title: sunt aut facere...
```

#### Exercise 3: Fetch Multiple Resources
Use `Promise.all()` to fetch three posts in parallel and log their titles. Handle errors gracefully.

**Solution**:
```javascript
async function fetchThreePosts() {
  try {
    const promises = [1, 2, 3].map((id) =>
      fetch(`https://jsonplaceholder.typicode.com/posts/${id}`).then((res) => {
        if (!res.ok) throw new Error(`Failed to fetch post ${id}`);
        return res.json();
      })
    );
    const posts = await Promise.all(promises);
    posts.forEach((post) => console.log("Title:", post.title));
  } catch (error) {
    console.error("Error:", error.message);
  }
}

fetchThreePosts();
```

---

### Why This is the 80/20
- **Creating Promises**: You’ll rarely need to create custom Promises, but understanding them helps with APIs like `fetch`.
- **Async/Await**: Most modern JavaScript uses `async/await` for its clarity.
- **Promise.all()**: Essential for parallel tasks, like fetching multiple resources.
- **Error Handling**: Critical for robust apps, especially with network requests.
- **API Calls**: The most common Promise use case in web development.

This covers 80% of what you’ll encounter in real-world JavaScript (e.g., React, Node.js, or vanilla JS apps). Less common topics like `Promise.race()`, `Promise.any()`, or cancellation can be learned later if needed.

---

### Next Steps
To solidify your learning:
1. **Practice**: Run the examples and exercises in a browser console or Node.js.
2. **Build Something**: Create a small app that fetches data from a public API (e.g., JSONPlaceholder, OpenWeather) and displays it.
3. **Experiment**: Try modifying the exercises (e.g., add timeouts, handle partial failures in `Promise.all()`).

If you want to dive into a specific area (e.g., a mini-project, debugging Promises, or a particular use case), let me know, and we can focus there! Alternatively, I can provide a chart visualizing Promise execution timing if you’re curious about how multiple Promises run concurrently. Just say the word!
