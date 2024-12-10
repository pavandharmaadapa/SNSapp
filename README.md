**Step-by-Step Process**

***

**1. Set Up the FastAPI Server**

**1.1 Install Python**

- Download and install Python from [python.org](https://www.python.org/downloads/).
- Verify installation:

bash

Copy code

python --version

pip --version

**1.2 Navigate to the Server Directory**

- Open your terminal or command prompt.
- Navigate to the directory where the server code is located:

bash

Copy code

cd D:\ztx-android\sns\_server

**1.3 Set Up a Virtual Environment**

- Create a virtual environment:

bash

Copy code

python -m venv venv

- Activate the virtual environment:
  - **Windows**:

bash

Copy code

venv\Scripts\activate

- - **macOS/Linux**:

bash

Copy code

source venv/bin/activate

**1.4 Install FastAPI and Uvicorn**

- Install the necessary packages:

bash

Copy code

pip install fastapi uvicorn

**1.5 Create or Verify the Server File (\_\_main\_\_.py)**

- Ensure your \_\_main\_\_.py file looks like this:

python

Copy code

from fastapi import FastAPI

from pydantic import BaseModel

 

app = FastAPI()

 

class LoginRequest(BaseModel):

    username: str

    password: str

 

@app.get("/")

async def root():

    return {"message": "Hello, world!"}

 

@app.post("/login/")

async def login(request: LoginRequest):

    if request.username == "admin" and request.password == "password":

        return {"message": "Login successful"}

    return {"message": "Invalid credentials"}

**1.6 Run the Server**

- Run the server using Uvicorn:

bash

Copy code

uvicorn sns\_server.\_\_main\_\_:app --reload

- If successful, you’ll see:

vbnet

Copy code

INFO:     Uvicorn running on http\://127.0.0.1:8000 (Press CTRL+C to quit)

**1.7 Test the Server**

- Use a browser or Postman to verify the endpoints:
  - **GET /**:

json

Copy code

{"message": "Hello, world!"}

- - **POST /login/**:
    - Request Body:

json

Copy code

{

  "username": "admin",

  "password": "password"

}

- - - Response:

json

Copy code

{"message": "Login successful"}

***

**2. Create the Android App**

**2.1 Install Android Studio**

- Download and install Android Studio from [developer.android.com](https://developer.android.com/studio).

**2.2 Create a New Project**

1. Open Android Studio.
2. Click **File > New > New Project**.
3. Choose **Empty Compose Activity**.
4. Set the project name, package name, and save location. Click **Finish**.

**2.3 Add Retrofit Dependency**

1. Open the build.gradle file in the app module.
2. Add the following dependencies under dependencies:

**groovy**

implementation 'com.squareup.retrofit2:retrofit:2.9.0'

implementation 'com.squareup.retrofit2:converter-gson:2.9.0'

3. Sync the project by clicking **Sync Now**.

***

**3. Implement the App Code**

**3.1 Replace MainActivity.kt**

- Replace the contents of MainActivity.kt with the following code:

**Kotlin**

 

 

package com.example.snsapp

 

import android.os.Bundle

import android.util.Log

import androidx.activity.ComponentActivity

import androidx.activity.compose.setContent

import androidx.compose.foundation.layout.\*

import androidx.compose.material3.\*

import androidx.compose.runtime.\*

import androidx.compose.ui.Modifier

import androidx.compose.ui.text.input.PasswordVisualTransformation

import androidx.compose.ui.text.style.TextAlign

import androidx.compose.ui.unit.dp

import com.example.snsapp.ui.theme.SNSappTheme

import kotlinx.coroutines.CoroutineScope

import kotlinx.coroutines.Dispatchers

import kotlinx.coroutines.launch

import retrofit2.Retrofit

import retrofit2.converter.gson.GsonConverterFactory

import retrofit2.http.Body

import retrofit2.http.POST

 

class MainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {

        super.onCreate(savedInstanceState)

        setContent {

            SNSappTheme {

                Surface(

                    modifier = Modifier.fillMaxSize(),

                    color = MaterialTheme.colorScheme.background

                ) {

                    LoginScreen()

                }

            }

        }

    }

}

 

@Composable

fun LoginScreen() {

    var username by remember { mutableStateOf("") }

    var password by remember { mutableStateOf("") }

    var responseText by remember { mutableStateOf("Enter credentials and click Login.") }

 

    // Retrofit setup

    val retrofit = Retrofit.Builder()

        .baseUrl("http\://10.0.2.2:8000/") // Use localhost for emulator

        .addConverterFactory(GsonConverterFactory.create())

        .build()

 

    val apiService = retrofit.create(ApiService::class.java)

 

    Column(

        modifier = Modifier

            .fillMaxSize()

            .padding(16.dp),

        verticalArrangement = Arrangement.Center,

        horizontalAlignment = androidx.compose.ui.Alignment.CenterHorizontally

    ) {

        TextField(

            value = username,

            onValueChange = { username = it },

            label = { Text("Username") },

            modifier = Modifier.fillMaxWidth()

        )

 

        Spacer(modifier = Modifier.height(16.dp))

 

        TextField(

            value = password,

            onValueChange = { password = it },

            label = { Text("Password") },

            visualTransformation = PasswordVisualTransformation(),

            modifier = Modifier.fillMaxWidth()

        )

 

        Spacer(modifier = Modifier.height(16.dp))

 

        Button(

            onClick = {

                CoroutineScope(Dispatchers.IO).launch {

                    try {

                        val request = LoginRequest(username, password)

                        val response = apiService.login(request)

                        responseText = "Response: ${response.message}"

                    } catch (e: Exception) {

                        responseText = "Error: ${e.message}"

                        Log.e("LoginScreen", "Error logging in", e)

                    }

                }

            },

            modifier = Modifier.padding(16.dp)

        ) {

            Text(text = "Login")

        }

 

        Spacer(modifier = Modifier.height(16.dp))

 

        Text(

            text = responseText,

            textAlign = TextAlign.Center,

            modifier = Modifier.fillMaxWidth()

        )

    }

}

 

// Retrofit API Service

interface ApiService {

    @POST("/login/")

    suspend fun login(@Body request: LoginRequest): LoginResponse

}

 

data class LoginRequest(

    val username: String,

    val password: String

)

 

data class LoginResponse(

    val message: String

)

***

**4. Run the App**

1. Start the FastAPI server:

bash

Copy code

uvicorn sns\_server.\_\_main\_\_:app --reload

2. Run the app on an emulator or physical device from Android Studio.
3. Enter the username (admin) and password (password) and click **Login**.
4. Observe the response:
   - **Valid Credentials**: Response: Login successful.
   - **Invalid Credentials**: Response: Invalid credentials.

***

**5. Test and Debug**

1. Use **Logcat** in Android Studio to debug errors.
2. Ensure the baseUrl in the app matches the FastAPI server's address.
3. For network issues, ensure you're using http\://10.0.2.2:8000/ when testing on an emulator.

 
