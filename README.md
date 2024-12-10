**Step-by-StepProcess**

**1\. Set Upthe FastAPI Server**

**1.1Install Python**

*   Download and install Python from [python.org](https://www.python.org/downloads/).
    

*   Verify installation:
    

bash

Copy code

python--version

pip--version

**1.2Navigate to the Server Directory**

*   Open your terminal or command prompt.
    

*   Navigate to the directory where the server code is located:
    

bash

Copy code

cdD:\\ztx-android\\sns\_server

**1.3 SetUp a Virtual Environment**

*   Create a virtual environment:
    

bash

Copy code

python -mvenv venv

*   Activate the virtual environment:
    

bash

Copy code

venv\\Scripts\\activate

bash

Copy code

source venv/bin/activate

**1.4Install FastAPI and Uvicorn**

*   Install the necessary packages:
    

bash

Copy code

pip installfastapi uvicorn

**1.5Create or Verify the Server File (\_\_main\_\_.py)**

*   Ensure your \_\_main\_\_.py file looks like this:
    

python

Copy code

from fastapiimport FastAPI

frompydantic import BaseModel

app =FastAPI()

classLoginRequest(BaseModel):

    username: str

    password: str

@app.get("/")

async defroot():

    return {"message": "Hello,world!"}

@app.post("/login/")

async deflogin(request: LoginRequest):

    if request.username == "admin"and request.password == "password":

        return {"message":"Login successful"}

    return {"message": "Invalidcredentials"}

**1.6 Runthe Server**

*   Run the server using Uvicorn:
    

bash

Copy code

uvicornsns\_server.\_\_main\_\_:app --reload

*   If successful, you’ll see:
    

vbnet

Copy code

INFO:     Uvicorn running on http://127.0.0.1:8000(Press CTRL+C to quit)

**1.7 Testthe Server**

*   Use a browser or Postman to verify the endpoints:
    

json

Copy code

{"message":"Hello, world!"}

json

Copy code

{

  "username": "admin",

  "password": "password"

}

json

Copy code

{"message":"Login successful"}

**2\. Createthe Android App**

**2.1Install Android Studio**

*   Download and install Android Studio from [developer.android.com](https://developer.android.com/studio).
    

**2.2Create a New Project**

2.  Open Android Studio.
    

4.  Click **File > New > New Project**.
    

6.  Choose **Empty Compose Activity**.
    

8.  Set the project name, package name, and save location. Click **Finish**.
    

**2.3 AddRetrofit Dependency**

2.  Open the build.gradle file in the app module.
    

4.  Add the following dependencies under dependencies:
    

**groovy**

implementation'com.squareup.retrofit2:retrofit:2.9.0'

implementation'com.squareup.retrofit2:converter-gson:2.9.0'

2.  Sync the project by clicking **Sync Now**.
    

**3.Implement the App Code**

**3.1Replace MainActivity.kt**

*   Replace the contents of MainActivity.kt with the following code:
    

**Kotlin**

packagecom.example.snsapp

importandroid.os.Bundle

importandroid.util.Log

importandroidx.activity.ComponentActivity

importandroidx.activity.compose.setContent

importandroidx.compose.foundation.layout.\*

importandroidx.compose.material3.\*

importandroidx.compose.runtime.\*

importandroidx.compose.ui.Modifier

importandroidx.compose.ui.text.input.PasswordVisualTransformation

importandroidx.compose.ui.text.style.TextAlign

importandroidx.compose.ui.unit.dp

importcom.example.snsapp.ui.theme.SNSappTheme

importkotlinx.coroutines.CoroutineScope

importkotlinx.coroutines.Dispatchers

importkotlinx.coroutines.launch

importretrofit2.Retrofit

importretrofit2.converter.gson.GsonConverterFactory

importretrofit2.http.Body

importretrofit2.http.POST

classMainActivity : ComponentActivity() {

    override fun onCreate(savedInstanceState:Bundle?) {

        super.onCreate(savedInstanceState)

        setContent {

            SNSappTheme {

                Surface(

                    modifier =Modifier.fillMaxSize(),

                    color =MaterialTheme.colorScheme.background

                ) {

                    LoginScreen()

                }

            }

        }

    }

}

@Composable

funLoginScreen() {

    var username by remember {mutableStateOf("") }

    var password by remember {mutableStateOf("") }

    var responseText by remember {mutableStateOf("Enter credentials and click Login.") }

    // Retrofit setup

    val retrofit = Retrofit.Builder()

       .baseUrl("http://10.0.2.2:8000/") // Use localhost foremulator

       .addConverterFactory(GsonConverterFactory.create())

        .build()

    val apiService =retrofit.create(ApiService::class.java)

    Column(

        modifier = Modifier

            .fillMaxSize()

            .padding(16.dp),

        verticalArrangement =Arrangement.Center,

        horizontalAlignment =androidx.compose.ui.Alignment.CenterHorizontally

    ) {

        TextField(

            value = username,

            onValueChange = { username = it },

            label = {Text("Username") },

            modifier = Modifier.fillMaxWidth()

        )

        Spacer(modifier =Modifier.height(16.dp))

        TextField(

            value = password,

            onValueChange = { password = it },

            label = {Text("Password") },

            visualTransformation =PasswordVisualTransformation(),

            modifier = Modifier.fillMaxWidth()

        )

        Spacer(modifier =Modifier.height(16.dp))

        Button(

            onClick = {

               CoroutineScope(Dispatchers.IO).launch {

                    try {

                        val request =LoginRequest(username, password)

                        val response =apiService.login(request)

                        responseText ="Response: ${response.message}"

                    } catch (e: Exception) {

                        responseText ="Error: ${e.message}"

                       Log.e("LoginScreen", "Error logging in", e)

                    }

                }

            },

            modifier = Modifier.padding(16.dp)

        ) {

            Text(text = "Login")

        }

        Spacer(modifier =Modifier.height(16.dp))

        Text(

            text = responseText,

            textAlign = TextAlign.Center,

            modifier = Modifier.fillMaxWidth()

        )

    }

}

// RetrofitAPI Service

interfaceApiService {

    @POST("/login/")

    suspend fun login(@Body request: LoginRequest):LoginResponse

}

data classLoginRequest(

    val username: String,

    val password: String

)

data classLoginResponse(

    val message: String

)

**4\. Runthe App**

2.  Start the FastAPI server:
    

bash

Copy code

uvicornsns\_server.\_\_main\_\_:app --reload

2.  Run the app on an emulator or physical device from Android Studio.
    

4.  Enter the username (admin) and password (password) and click **Login**.
    

6.  Observe the response:
    

**5\. Testand Debug**

2.  Use **Logcat** in Android Studio to debug errors.
    

4.  Ensure the baseUrl in the app matches the FastAPI server's address.
    

6.  For network issues, ensure you're using http://10.0.2.2:8000/ when testing on an emulator.
