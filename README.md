
# Languagenut API Documentation
Documentation for Languagenut's API.

## DISCLAIMER

I am not responsible for any kind of trouble you may get in for reading/using this documentation. I do not condone cheating on any competitive services such as Languagenut, as it defeats the point of it. I do not cheat. If you want to, using this documentation, you can. But you shouldn't.

<sub>Just so we're all on the same page here, every endpoint on this page has the URL removed because, well, its redundant. Just add `https://api.languagenut.com/` to the beginning of any of the mentioned endpoints to use them!
I also don't know where else to put this, but my examples use the function `WriteInfoToConsole();` which I wrote; it just formats things nicely. If you want to just copy and paste my code, feel free to replace them with `Console.WriteLine();`. My examples also reference certain variables; I assigned the token to a string called "newToken", for example.</sub>
## How does the API work?

The API is mostly made up of endpoints which you send GET requests (and occasionally POST requests) to. There's an endpoint for everything (obviously), although some features of the website are pretty hacky.

## How do I do things with it?

First, you need to get an auth token. This obviously requires an account. So go get one of those. Once you have one, open up your IDE and language of choice. Or Postman ~~if you're a coward~~. In my case, I'll be using C# just because it's easy to demonstrate things with. Lets begin!

## So, how do I get an auth token?

It's pretty simple, actually. You'll want to send a GET request to `/loginController/attemptlogin/`with parameters `username` and `pass`	 (like `/loginController/attemptlogin?username=Username&pass=Pass`.  Here's an example in C# (using a deprecated library because I'm so good at C#; also the code sucks, DON'T USE THIS).

```
public static string GetLoginToken(string username, string password)
{

    string url = "https://api.languagenut.com/loginController/attemptlogin?username=" + username + "&pass=" + password + "&languagenutTimeMarker=1653152956557&lastLanguagenutTimeMarker=1653152956557&apiVersion=9";
    System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
    var request = (HttpWebRequest)WebRequest.Create(url);

    request.Method = "GET";
    request.ContentType = "application/x-www-form-urlencoded; charset=UTF-8";
    request.UserAgent = "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:100.0) Gecko/20100101 Firefox/100.0";
    request.Headers["Upgrade-Insecure-Requests"] = "1";
    request.Host = "api.languagenut.com";
    request.Headers["Sec-Fetch-Dest"] = "document";
    request.Headers["Sec-Fetch-Mode"] = "navigate";
    request.Headers["Sec-Fetch-Site"] = "none";
    request.Headers["Sec-Fetch-User"] = "?1";
    request.Headers["Cookie"] = "_fbp=fb.1.1653070949979.270584707; _ga=GA1.2.2141399847.1653150179; _gid=GA1.2.1360687336.1653150179; PHPSESSID=pm0opudd90chhrmqnd10n9f9sh";
    WriteInfoToConsole("Info", "Attempting to send GET request to login endpoint!", ConsoleColor.Blue);
    try
    {
        var response = (HttpWebResponse)request.GetResponse();
        using (Stream stream = response.GetResponseStream())
        {
            StreamReader reader = new StreamReader(stream, Encoding.UTF8);
            String responseString = reader.ReadToEnd();
            var jo = JObject.Parse(responseString);
            newToken = jo["newToken"].ToString();
            if (newToken != "")
            {
                WriteInfoToConsole("Debug", newToken, ConsoleColor.Magenta);
            } else
            {
                WriteInfoToConsole("Debug", "No token received!", ConsoleColor.Magenta);
            }
            if (jo["loginError"].ToString() == "NONE")
            {
                new SoundPlayer(@"C:\Windows\Media\tada.wav").Play();
                WriteInfoToConsole("Info", "Token received successfully: " + jo["liveServiceToken"].ToString(), ConsoleColor.Blue);
                return jo["liveServiceToken"].ToString();
            }
            else
            {
                SystemSounds.Hand.Play();
                WriteInfoToConsole("Error", "Token was not successfully received. This is fatal, and the script cannot continue. The server reported the error: " + jo["loginError"].ToString() + ".", ConsoleColor.Red);
                WriteInfoToConsole("Input", "Press any key to exit.", ConsoleColor.DarkMagenta);
                Console.ReadKey();
                Environment.Exit(40);
            }

        }

    }
    catch
    {
        WriteInfoToConsole("Error", "Token was not successfully received, THIS WILL NOT WORK!", ConsoleColor.Red);
    }
    return "";
}
```
## What can I do now I have authentication?
A lot. Like, a lot a lot.

One example is that you can get all of the users info. All of it. The below code returns a JObject (from Newtonsoft.Json) with all of the JSON data parsed into it. You can access a JObject's items like an array but with nesting (for example: `userData[name].ToString();` returns the full name of the user in a string.) 
```
static List<int> moduleArray = new List<int>();

public static JObject GetUserData(string token)
{
    string url = "https://api.languagenut.com/userDataController/getUserData?token=" + token;
    System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
    var request = (HttpWebRequest)WebRequest.Create(url);

    request.Method = "GET";
    request.ContentType = "application/x-www-form-urlencoded; charset=UTF-8";
    request.UserAgent = "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:100.0) Gecko/20100101 Firefox/100.0";
    request.Headers["Upgrade-Insecure-Requests"] = "1";
    request.Host = "api.languagenut.com";
    request.Headers["Sec-Fetch-Dest"] = "document";
    request.Headers["Sec-Fetch-Mode"] = "navigate";
    request.Headers["Sec-Fetch-Site"] = "none";
    request.Headers["Sec-Fetch-User"] = "?1";
    request.Headers["Cookie"] = "_fbp=fb.1.1653070949979.270584707; _ga=GA1.2.2141399847.1653150179; _gid=GA1.2.1360687336.1653150179; PHPSESSID=pm0opudd90chhrmqnd10n9f9sh";
    WriteInfoToConsole("Info", "Attempting to send GET request to module endpoint!", ConsoleColor.Blue);
    var response = (HttpWebResponse)request.GetResponse();
    using (Stream stream = response.GetResponseStream())
    {
        StreamReader reader = new StreamReader(stream, Encoding.UTF8);
        String responseString = reader.ReadToEnd();
        JObject parsedResponse = JObject.Parse(responseString);
        return parsedResponse;
    }
}
```
The HTTP response will have the following elements:

    ESOLEndDates
    IntroductionVideo
    RWYS
    SRWG
    accountName
    accountSettings
    showSchoolRanking
    accountUid
    account_type
    additionalVocabScreens
    bronzeMedals
    country
    curriculum
    customContentTeacherScreens
    daysLeftOfSubscription
    defined_support
    displayName
    goldMedals
    groups
    homeworkCounter
    isLive
    isMessageDisplay
    is_cefr
    name
    newToken
    overrideLogo
    primaryEndDates
    rank
    secondLoginType
    secondaryEndDates
    silverMedals
    style
    tested
    timeTakenServer
    totalScore
    userName
    userType
    userUid
    wordsLearnt

## Give me the endpoints!!
Well, here's the useful ones.

`/highscoreController/studentsAllAccount?token=(token)` (returns all accounts in order of school ranking, allows output of school ranking and table)

`/screensController/getModuleIds?token=(token)`
Honestly have no idea how this one works or where some of the parameters come from, not that useful

`/gameDataController/addGameScore`
Figure it out yourself, you cheater.


<sub>Footnotes</sub>

<sub><sup>Hey! Thanks for reading all of this. This has been something to occupy myself for the past weeks. There seems to be no documentation for any of this online, so I had to find all of this myself. I don't have a Ko-fi or anything, knowing that you're still reading is enough for me. Thank you <3</sup></sub>
