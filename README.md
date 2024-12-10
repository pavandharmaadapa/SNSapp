# FastAPI and Android App Integration Tutorial

This CodeChat file provides a comprehensive guide for setting up a FastAPI server and an Android application.

---

## Section 1: FastAPI Server Setup

```plaintext
# Step 1.1: Install Python
Download Python from https://www.python.org/ and verify installation:
python --version
pip --version

# Step 1.2: Navigate to the server directory
cd D:\ztx-android\sns_server
# Step 1.3: Set up a virtual environment
python -m venv venv

# Activate virtual environment:
# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

# Step 1.4: Install FastAPI and Uvicorn
pip install fastapi uvicorn

# Step 1.5: Create a FastAPI server file (__main__.py)
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

# Step 1.6: Run the FastAPI server
uvicorn sns_server.__main__:app --reload

# Step 2.1: Install Android Studio
Download from https://developer.android.com/ and install it.

# Step 2.2: Create a new Android project
1. Open Android Studio.
2. File > New > New Project.
3. Select Empty Compose Activity.
4. Set project name, package name, and save location, then click Finish.

# Step 2.3: Add Retrofit dependencies
# In build.gradle (Module: app):
dependencies {
    implementation 'com.squareup.retrofit2:retrofit:2.9.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
}
# Sync the project by clicking 'Sync Now.'

// Step 2.4: Replace MainActivity.kt content
package com.example.snsapp

import android.os.Bundle
import android.util.Log
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.*
import androidx.compose.material3.*
import androidx.compose.runtime.*
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
        .baseUrl("http://10.0.2.2:8000/") // Use localhost for emulator
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

# Step 3.1: Start the FastAPI server
uvicorn sns_server.__main__:app --reload

# Step 3.2: Run the Android application
1. Use an Android emulator or connect a physical device.
2. Enter username (e.g., "admin") and password ("password").
3. Check for:
   - Valid credentials: "Login successful."
   - Invalid credentials: "Invalid credentials."
#Add to GitHub
# Initialize Git
git init

# Add files and commit
git add .
git commit -m "Initial commit for FastAPI and Android app integration"

# Push to GitHub
git remote add origin https://github.com/<your-username>/<repo-name>.git
git branch -M main
git push -u origin main

