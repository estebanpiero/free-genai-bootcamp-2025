# Implementation Plan: POST /api/study-sessions

This document outlines the steps needed to implement the POST route for creating a study session.

## ✅ Pre-requisites
- [ ] Ensure you have access to the database schema (especially the `study_sessions` table) and know which fields are required.
- [ ] Familiarize yourself with the current Flask project structure and how database connections are managed (e.g., `app.db`).

## API Endpoint Requirements
- **Endpoint:** `/api/study-sessions`
- **Method:** POST
- **Request Body:** JSON containing at least:
  - `group_id` (integer)
  - `study_activity_id` (integer)
  - (Optional) Additional fields such as `created_at` if not using server time.
- **Response:** JSON containing the created study session details, including its new `id` and timestamps.

## Implementation Steps

- [ ] **Step 1: Create the Route Function**
  - Create a new function in your routes file (where other `/api/study-sessions` endpoints exist) with the decorator `@app.route('/api/study-sessions', methods=['POST'])`.
  - Add `@cross_origin()` if needed.

- [ ] **Step 2: Parse and Validate Request Data**
  - Use `request.get_json()` to parse the incoming JSON.
  - Validate that `group_id` and `study_activity_id` are provided and are integers.
  - Return a 400 error if the validation fails.
  
  **Example Code:**
  ```python
  data = request.get_json()
  if not data or 'group_id' not in data or 'study_activity_id' not in data:
      return jsonify({"error": "Missing required fields: group_id and study_activity_id"}), 400
  group_id = data['group_id']
  study_activity_id = data['study_activity_id']
  # Optionally use data.get('created_at') or set it to current time
  created_at = datetime.utcnow().strftime('%Y-%m-%d %H:%M:%S')

- [ ] **Step 3: Insert the New Study Session into the Database**

    - Use the database cursor from app.db.cursor() to execute an INSERT statement.
    - Ensure you insert the required fields and use created_at as appropriate.
    - Retrieve the newly inserted session's id (using cursor.lastrowid if supported).

  **Example Code:**

     ```python
    cursor = app.db.cursor()
    insert_query = '''
        INSERT INTO study_sessions (group_id, study_activity_id, created_at)
        VALUES (?, ?, ?)
        '''
    cursor.execute(insert_query, (group_id, study_activity_id, created_at))
    new_session_id = cursor.lastrowid
    app.db.commit()
    
- [ ] **Step 4: Prepare the Response Data**
    
    - Construct a JSON response that includes the new session details.
    - Use the inserted values and the generated session id.
    
    **Example Code:**

    ```python
    
    response = {
        "id": new_session_id,
        "group_id": group_id,
        "study_activity_id": study_activity_id,
        "created_at": created_at,
        "message": "Study session created successfully"
    }
    return jsonify(response), 201
    
-[ ] **Step 5: Add Error Handling**

    -Wrap the database operations in a try-except block.
    -Return a 500 error with the exception message if any unexpected error occurs.

- [ ] **Step 6: Test the New Endpoint Manually**

    - Use Postman or curl to send a POST request with the required JSON payload.
    
    **Example curl Command:**

    ```bash
    curl -X POST http://localhost:5000/api/study-sessions \
    -H "Content-Type: application/json" \
    -d '{"group_id": 1, "study_activity_id": 2}'
    
- [ ] **Step 7: Write Automated Tests**

    - Write a unit test or integration test to verify the endpoint works as expected.
    
    **Example Testing Code (using Python requests):**

    ```python
    Copy
    import requests

    url = 'http://localhost:5000/api/study-sessions'
    payload = {
        "group_id": 1,
        "study_activity_id": 2
    }
    headers = {
        "Content-Type": "application/json"
    }

    response = requests.post(url, json=payload, headers=headers)
    print(response.status_code)
    print(response.json())
    
- [ ] **Step 8: Review and Document**

    - Ensure that the new route is added to your API documentation.
    - Double-check that error messages are clear and useful for debugging.
    
    **Final Steps**
    
    - Commit your changes with a clear commit message.
    - Ask for a code review from a senior developer.
    - Merge the changes once approved.
    
    ```yaml
    
    This plan breaks down the implementation into simple, manageable tasks, ensuring that each step is clear and testabl