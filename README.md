# WD401 - Level 01 - Workflow using pull-requests

## Handling Code Review Feedback

### Task
- Imagine you're working on a creating a signup form data subbit to api and create a user for the organization using a data like a organization name,userName userEmail and userPassword and after succesfully create a user redirect to page `/dashboard`

- Here's the initial code:
  
```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
    console.log('Sign-up successful');

    const data = await response.json();
    localStorage.setItem('authToken', data.token);
    localStorage.setItem('userData', JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```
### Feedback : 

- Here in given code when you are accesing a property user and token of `data` object first ensure that the data object should conatains that property.

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    if (response.ok) {
      const data = await response.json();
      // Feedback addressed: Validating 'data' before using it
      if (data.token && data.user) {
        localStorage.setItem('authToken', data.token);
        localStorage.setItem('userData', JSON.stringify(data.user));
        navigate("/dashboard");
      } else {
        // Feedback addressed: Improved error logging with more details
        console.error('Sign-up failed: Invalid response data');
      }
    } else {
      // Feedback addressed: Improved error logging with more details
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
  } catch (error) {
    // Feedback addressed: Improved error logging with more details
    console.error('Sign-up failed:', error);
  }
};

```

## Iterative Development Process

- Describe in a workflow.docs file.

## Resolving Merge Conflicts:

Let's think we have an orignal signup function

```javascript

const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    if(email.includes("@")){
      alert("Not valid Email")
      return;
    }
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
    console.log('Sign-up successful');

    const data = await response.json();
    localStorage.setItem('authToken', data.token);
    localStorage.setItem('userData', JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```
Let's take one senario where one develeoper add extra validation on email and match with email regex

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;

    if(userEmail.includes("@") && !emailRegex.test(userEmail)){
      alert("Not valid Email")
      return;
    }
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    // rest of code
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```

Let's take another senario where another developer chech the email should not empty

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;

    if(userEmail === ' ' && userEmail.includes("@") && !emailRegex.test(userEmail)){
      alert("Not valid Email")
      return;
    }
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    // rest of code
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```

When this both developer merge the chnages to develop branch the conflict will occures 

1. **Identify Conflicts:**
   ```bash
   git pull origin develop
   ```

2. **Open Conflicted File (`signup.tsx`):**
   ```javascript
   // Conflicted code   
   const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
  
    try {
      const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;
     <<<<<<< HEAD
       if(userEmail.includes("@") && !emailRegex.test(userEmail)){
     =======
       if(userEmail === ' ' && userEmail.includes("@") && !emailRegex.test(userEmail)){
     >>>>>>> feature-branch
       ...
       // rest of code
   ```

3. **Manually Resolve Conflict:**   
   ```javascript
   // Resolved code
   const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();
  
    try {
      const emailRegex = /^[a-zA-Z0-9._-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,4}$/;
       if(userEmail === ' ' && userEmail.includes("@") && !emailRegex.test(userEmail)){
       ...
       // rest of code
   ```

4. **Mark as Resolved:**
   ```bash
   git add signup.tsx
   ```

5. **Continue with Merge:**
   ```bash
   git merge --continue
   ```


## CI/CD Integration

```javascript
test("Sign Up", async () => {
    var res = await agent.get("/signup");
    var csrfToken = getCsrfToken(res);
    const response = await agent.post("/oragranization").send({
      oraganization: "Dipu",
      username: "rvl",
      email: "dipu@gmail.com",
      password: "dipu",
      _csrf: csrfToken,
    });
    expect(response.statusCode).toBe(302);
  });
```

```yaml
# .github/workflows/main.yml
name: CI/CD Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Setup Node.js
      uses: actions/setup-node@v2
      with:
        node-version: 17

    - name: Install dependencies
      run: npm install

    - name: Run tests
      run: npm test

    - name: Lint Code
      run: npm run lint

    - name: Build and deploy
      run: npm run build && npm run deploy

```
