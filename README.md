This project is a full-stack application featuring a Vue.js frontend and a Django backend. 
It provides user registration functionality with a modern interface.


## Installation

Follow these steps to set up the project locally:

### Backend Setup

1. **Navigate to the Backend Directory**:
   Open your terminal (Command Prompt or PowerShell) and change to the backend directory:

      'cd backend'

2. Set Up a Virtual Environment
    Run:
         'python -m venv venv' -- to create a virtual environment
         'venv\Scripts\activate'-- to activate the virtual environment

3. Install Dependencies:
   Ensure you have a requirements.txt file in your backend directory.
   Install the necessary packages: 'pip install -r requirements.txt'

4. Run Migrations: 'python manage.py migrate'

5. Run the Backend Server: 'python manage.py runserver'


### Frontend Setup

1. Navigate to the Frontend Directory:
    Open your terminal (Command Prompt or PowerShell) and change to the frontend directory:

      'cd frontend'
   
2. Install Node.js Dependencies:
Ensure you have a package.json file in your frontend directory,
then install the dependencies: 'npm install'

3. Run the Frontend Development Server: 'npm run dev'

   

Accessing the Application
After starting both servers, you can access the application:

Frontend: Typically accessible at http://localhost:3000
Backend API: Should be accessible at http://localhost:8000



   
