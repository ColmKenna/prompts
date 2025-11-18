# Generate Your First TDD Test

> **Test-Driven Development (TDD)** begins with a failing test. This prompt helps you create that initial "Red" test to kickstart your development process.

---

> **Note:** Unless you specify otherwise, tests will be generated using **xUnit** and **Moq** (for .NET/C#).  
> If you specify **JavaScript**, tests will use **Jest**.

---

## Instructions

To generate a starter test, please provide the following information. The more detail you provide, the better the test will be.

---

### 1. The Code You Want to Test

*   **Programming Language & Test Framework:** (e.g., Python with Pytest, TypeScript with Jest, Java with JUnit)
*   **Code Snippet:** Please provide the function signature, class definition, or the relevant piece of code. If it doesn't exist yet, describe what it will look like.

### 2. Functionality Details

*   **Primary Purpose:** What is the high-level goal of this code?
*   **Specific Behavior:** Describe the expected input and output. What should happen when the code is executed successfully? (e.g., "Given user ID 123, it should return a UserProfile object with the name 'John Doe'.")
*   **Conditions:** Are there any preconditions (e.g., user must be logged in) or post-conditions (e.g., a record is created in the database)?

### 3. External Dependencies & Mocks

*   **Dependencies:** List any external systems your code interacts with (e.g., a database, a REST API, the file system).
*   **Mocking Behavior:** For each dependency, describe how it should be mocked for this test. What specific data should the mock return? (e.g., "The `db.fetchUser` method should return `{ id: 123, name: 'John Doe' }` when called with `123`.")

---

## What You'll Get Back

*   A complete, ready-to-use failing test case for the "happy path" scenario.
*   Suggestions for other test cases to consider, covering edge cases, invalid inputs, and error conditions.

---

## Example

> **1. Code to Test:**
> - **Language & Framework:** C# with XUnit
> - **Code Snippet:**
>   ```csharp
>   public class UserService
>   {
>       public UserProfile GetUserProfile(int userId)
>       {
>           // Implementation to be added
>       }
>   }
>   ```
>
> **2. Functionality Details:**
> - **Purpose:** Retrieve a user's profile from the database and format it.
> - **Behavior:** It takes a `userId` and returns a `UserProfile` object.
>
> **3. Dependencies & Mocks:**
> - **Dependency:** A database repository `IDatabase`.
> - **Mocking:** The mock `IDatabase.GetUser(userId)` should return a `User` object for a valid ID.
