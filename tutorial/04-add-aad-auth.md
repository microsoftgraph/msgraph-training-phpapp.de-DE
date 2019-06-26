<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="d515a-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d515a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="d515a-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="d515a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="d515a-103">In diesem Schritt werden Sie die [oauth2-Client](https://github.com/thephpleague/oauth2-client) Bibliothek in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="d515a-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

<span data-ttu-id="d515a-104">Öffnen Sie `.env` die Datei im Stammverzeichnis Ihrer PHP-Anwendung, und fügen Sie den folgenden Code am Ende der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-104">Open the `.env` file in the root of your PHP application, and add the following code to the end of the file.</span></span>

```text
OAUTH_APP_ID=YOUR_APP_ID_HERE
OAUTH_APP_PASSWORD=YOUR_APP_PASSWORD_HERE
OAUTH_REDIRECT_URI=http://localhost:8000/callback
OAUTH_SCOPES='openid profile offline_access user.read calendars.read'
OAUTH_AUTHORITY=https://login.microsoftonline.com/common
OAUTH_AUTHORIZE_ENDPOINT=/oauth2/v2.0/authorize
OAUTH_TOKEN_ENDPOINT=/oauth2/v2.0/token
```

<span data-ttu-id="d515a-105">Ersetzen `YOUR APP ID HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR APP SECRET HERE` und ersetzen Sie durch das Kennwort, das Sie generiert haben.</span><span class="sxs-lookup"><span data-stu-id="d515a-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d515a-106">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `.env` aus der Quellcodeverwaltung auszuschließen, damit versehentlich keine APP-ID und Ihr Kennwort verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="d515a-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="d515a-107">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="d515a-107">Implement sign-in</span></span>

<span data-ttu-id="d515a-108">Erstellen Sie eine neue Datei im `./app/Http/Controllers` Verzeichnis mit `AuthController.php` dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-108">Create a new file in the `./app/Http/Controllers` directory named `AuthController.php` and add the following code.</span></span>

```PHP
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;

class AuthController extends Controller
{
  public function signin()
  {
    // Initialize the OAuth client
    $oauthClient = new \League\OAuth2\Client\Provider\GenericProvider([
      'clientId'                => env('OAUTH_APP_ID'),
      'clientSecret'            => env('OAUTH_APP_PASSWORD'),
      'redirectUri'             => env('OAUTH_REDIRECT_URI'),
      'urlAuthorize'            => env('OAUTH_AUTHORITY').env('OAUTH_AUTHORIZE_ENDPOINT'),
      'urlAccessToken'          => env('OAUTH_AUTHORITY').env('OAUTH_TOKEN_ENDPOINT'),
      'urlResourceOwnerDetails' => '',
      'scopes'                  => env('OAUTH_SCOPES')
    ]);

    $authUrl = $oauthClient->getAuthorizationUrl();

    // Save client state so we can validate in callback
    session(['oauthState' => $oauthClient->getState()]);

    // Redirect to AAD signin page
    return redirect()->away($authUrl);
  }

  public function callback(Request $request)
  {
    // Validate state
    $expectedState = session('oauthState');
    $request->session()->forget('oauthState');
    $providedState = $request->query('state');

    if (!isset($expectedState) || !isset($providedState) || $expectedState != $providedState) {
      return redirect('/')
        ->with('error', 'Invalid auth state')
        ->with('errorDetail', 'The provided auth state did not match the expected value');
    }

    // Authorization code should be in the "code" query param
    $authCode = $request->query('code');
    if (isset($authCode)) {
      // Initialize the OAuth client
      $oauthClient = new \League\OAuth2\Client\Provider\GenericProvider([
        'clientId'                => env('OAUTH_APP_ID'),
        'clientSecret'            => env('OAUTH_APP_PASSWORD'),
        'redirectUri'             => env('OAUTH_REDIRECT_URI'),
        'urlAuthorize'            => env('OAUTH_AUTHORITY').env('OAUTH_AUTHORIZE_ENDPOINT'),
        'urlAccessToken'          => env('OAUTH_AUTHORITY').env('OAUTH_TOKEN_ENDPOINT'),
        'urlResourceOwnerDetails' => '',
        'scopes'                  => env('OAUTH_SCOPES')
      ]);

      try {
        // Make the token request
        $accessToken = $oauthClient->getAccessToken('authorization_code', [
          'code' => $authCode
        ]);

        // TEMPORARY FOR TESTING!
        return redirect('/')
          ->with('error', 'Access token received')
          ->with('errorDetail', $accessToken->getToken());
      }
      catch (League\OAuth2\Client\Provider\Exception\IdentityProviderException $e) {
        return redirect('/')
          ->with('error', 'Error requesting access token')
          ->with('errorDetail', $e->getMessage());
      }
    }

    return redirect('/')
      ->with('error', $request->query('error'))
      ->with('errorDetail', $request->query('error_description'));
  }
}
```

<span data-ttu-id="d515a-109">Dadurch wird ein Controller mit zwei Aktionen definiert `signin` : `callback`und.</span><span class="sxs-lookup"><span data-stu-id="d515a-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

<span data-ttu-id="d515a-110">Durch `signin` die Aktion wird die Azure AD SignIn-URL generiert `state` , der vom OAuth-Client generierte Wert gespeichert und anschließend der Browser an die Azure AD SignIn-Seite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d515a-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

<span data-ttu-id="d515a-111">In `callback` der Aktion wird Azure weitergeleitet, nachdem das SignIn abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="d515a-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="d515a-112">Durch diese Aktion wird sicher `state` gestellt, dass der Wert mit dem gespeicherten Wert übereinstimmt, und dann Benutzer der Autorisierungscode, der von Azure gesendet wurde, um ein Zugriffstoken anzufordern.</span><span class="sxs-lookup"><span data-stu-id="d515a-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="d515a-113">Anschließend wird mit dem Zugriffstoken im temporären Fehlerwert zurück zur Startseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d515a-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="d515a-114">Wir verwenden dies, um zu überprüfen, ob unsere Anmeldung funktionsfähig ist, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="d515a-114">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="d515a-115">Bevor wir testen, müssen wir die Routen zu `./routes/web.php`hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d515a-115">Before we test, we need to add the routes to `./routes/web.php`.</span></span>

```PHP
Route::get('/signin', 'AuthController@signin');
Route::get('/callback', 'AuthController@callback');
```

<span data-ttu-id="d515a-116">Starten Sie den Server, und `https://localhost:8000`navigieren Sie zu.</span><span class="sxs-lookup"><span data-stu-id="d515a-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="d515a-117">Klicken Sie auf die Anmeldeschaltfläche, und Sie sollten zu `https://login.microsoftonline.com`umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="d515a-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="d515a-118">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="d515a-118">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="d515a-119">Der Browser wird an die APP umgeleitet, wobei das Token angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d515a-119">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="d515a-120">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="d515a-120">Get user details</span></span>

<span data-ttu-id="d515a-121">Aktualisieren Sie `callback` die- `/app/Http/Controllers/AuthController.php` Methode in, um das Profil des Benutzers aus Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="d515a-121">Update the `callback` method in `/app/Http/Controllers/AuthController.php` to get the user's profile from Microsoft Graph.</span></span>

<span data-ttu-id="d515a-122">Fügen Sie zunächst die folgenden `use` Anweisungen am Anfang der Datei unterhalb der `namespace App\Http\Controllers;` -Codezeile ein.</span><span class="sxs-lookup"><span data-stu-id="d515a-122">First, add the following `use` statements to the top of the file, beneath the `namespace App\Http\Controllers;` line.</span></span>

```php
use Microsoft\Graph\Graph;
use Microsoft\Graph\Model;
```

<span data-ttu-id="d515a-123">Ersetzen Sie `try` den Block in `callback` der-Methode durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="d515a-123">Replace the `try` block in the `callback` method with the following code.</span></span>

```php
try {
  // Make the token request
  $accessToken = $oauthClient->getAccessToken('authorization_code', [
    'code' => $authCode
  ]);

  $graph = new Graph();
  $graph->setAccessToken($accessToken->getToken());

  $user = $graph->createRequest('GET', '/me')
    ->setReturnType(Model\User::class)
    ->execute();

  // TEMPORARY FOR TESTING!
  return redirect('/')
    ->with('error', 'Access token received')
    ->with('errorDetail', 'User:'.$user->getDisplayName().', Token:'.$accessToken->getToken());
}
```

<span data-ttu-id="d515a-124">Der neue Code erstellt ein `Graph` -Objekt, weist das Zugriffstoken zu und verwendet es dann, um das Profil des Benutzers anzufordern.</span><span class="sxs-lookup"><span data-stu-id="d515a-124">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="d515a-125">Der Anzeigename des Benutzers wird der temporären Ausgabe zu Testzwecken hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d515a-125">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="d515a-126">Speichern der Token</span><span class="sxs-lookup"><span data-stu-id="d515a-126">Storing the tokens</span></span>

<span data-ttu-id="d515a-127">Nachdem Sie nun Token erhalten können, ist es an der Zeit, eine Möglichkeit zum Speichern in der APP zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="d515a-127">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="d515a-128">Da es sich um eine Beispiel-App handelt, speichern Sie Sie aus Gründen der Einfachheit in der Sitzung.</span><span class="sxs-lookup"><span data-stu-id="d515a-128">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="d515a-129">Eine reale APP würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="d515a-129">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="d515a-130">Erstellen Sie ein neues Verzeichnis im `./app` Verzeichnis mit `TokenStore`dem Namen, erstellen Sie dann eine neue Datei in `TokenCache.php`dem Verzeichnis mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-130">Create a new directory in the `./app` directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

```php
<?php

namespace App\TokenStore;

class TokenCache {
  public function storeTokens($accessToken, $user) {
    session([
      'accessToken' => $accessToken->getToken(),
      'refreshToken' => $accessToken->getRefreshToken(),
      'tokenExpires' => $accessToken->getExpires(),
      'userName' => $user->getDisplayName(),
      'userEmail' => null !== $user->getMail() ? $user->getMail() : $user->getUserPrincipalName()
    ]);
  }

  public function clearTokens() {
    session()->forget('accessToken');
    session()->forget('refreshToken');
    session()->forget('tokenExpires');
    session()->forget('userName');
    session()->forget('userEmail');
  }

  public function getAccessToken() {
    // Check if tokens exist
    if (empty(session('accessToken')) ||
        empty(session('refreshToken')) ||
        empty(session('tokenExpires'))) {
      return '';
    }

    return session('accessToken');
  }
}
```

<span data-ttu-id="d515a-131">Aktualisieren Sie dann die `callback` -Funktion in `AuthController` der-Klasse, um die Token in der Sitzung zu speichern und zur Hauptseite zurückzuleiten.</span><span class="sxs-lookup"><span data-stu-id="d515a-131">Then, update the `callback` function in the `AuthController` class to store the tokens in the session and redirect back to the main page.</span></span>

<span data-ttu-id="d515a-132">Fügen Sie zunächst die folgende `use` Anweisung am oberen Rand von `./app/Http/Controllers/AuthController.php`hinzu, unterhalb `namespace App\Http\Controllers;` der-gerade.</span><span class="sxs-lookup"><span data-stu-id="d515a-132">First, add the following `use` statement to the top of `./app/Http/Controllers/AuthController.php`, beneath the `namespace App\Http\Controllers;` line.</span></span>

```php
use App\TokenStore\TokenCache;
```

<span data-ttu-id="d515a-133">Ersetzen Sie dann `try` den Block in der `callback` vorhandenen Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d515a-133">Then replace the `try` block in the existing `callback` function with the following.</span></span>

```php
try {
  // Make the token request
  $accessToken = $oauthClient->getAccessToken('authorization_code', [
    'code' => $authCode
  ]);

  $graph = new Graph();
  $graph->setAccessToken($accessToken->getToken());

  $user = $graph->createRequest('GET', '/me')
    ->setReturnType(Model\User::class)
    ->execute();

  $tokenCache = new TokenCache();
  $tokenCache->storeTokens($accessToken, $user);

  return redirect('/');
}
```

## <a name="implement-sign-out"></a><span data-ttu-id="d515a-134">Implementieren der Abmeldung</span><span class="sxs-lookup"><span data-stu-id="d515a-134">Implement sign-out</span></span>

<span data-ttu-id="d515a-135">Bevor Sie dieses neue Feature testen, fügen Sie eine Möglichkeit zum Abmelden hinzu. Fügen Sie der `AuthController` Klasse die folgende Aktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-135">Before you test this new feature, add a way to sign out. Add the following action to the `AuthController` class.</span></span>

```PHP
public function signout()
{
  $tokenCache = new TokenCache();
  $tokenCache->clearTokens();
  return redirect('/');
}
```

<span data-ttu-id="d515a-136">Fügen Sie diese Aktion `./routes/web.php`zu hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-136">Add this action to `./routes/web.php`.</span></span>

```PHP
Route::get('/signout', 'AuthController@signout');
```

<span data-ttu-id="d515a-137">Starten Sie den Server neu, und fahren Sie mit dem Anmeldevorgang fort.</span><span class="sxs-lookup"><span data-stu-id="d515a-137">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="d515a-138">Sie sollten wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="d515a-138">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![Ein Screenshot der Startseite nach der Anmeldung](./images/add-aad-auth-01.png)

<span data-ttu-id="d515a-140">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers \*\*\*\* , um auf den Abmeldelink zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d515a-140">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="d515a-141">Durch \*\*\*\* klicken auf Abmelden wird die Sitzung zurückgesetzt, und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="d515a-141">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![Screenshot des Dropdownmenüs mit dem Link zum Abmelden](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="d515a-143">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="d515a-143">Refreshing tokens</span></span>

<span data-ttu-id="d515a-144">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="d515a-144">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="d515a-145">Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d515a-145">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="d515a-146">Dieses Token ist jedoch nur kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="d515a-146">However, this token is short-lived.</span></span> <span data-ttu-id="d515a-147">Das Token läuft eine Stunde nach seiner Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="d515a-147">The token expires an hour after it is issued.</span></span> <span data-ttu-id="d515a-148">Hier wird das Aktualisierungstoken nützlich.</span><span class="sxs-lookup"><span data-stu-id="d515a-148">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="d515a-149">Das Aktualisierungstoken ermöglicht der APP, ein neues Zugriffstoken anzufordern, ohne dass sich der Benutzer erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="d515a-149">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="d515a-150">Aktualisieren Sie den Token-Verwaltungscode, um die Token-Aktualisierung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="d515a-150">Update the token management code to implement token refresh.</span></span>

<span data-ttu-id="d515a-151">Öffnen `./app/TokenStore/TokenCache.php` Sie, und fügen Sie die folgende `TokenCache` Funktion zur-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="d515a-151">Open `./app/TokenStore/TokenCache.php` and add the following function to the `TokenCache` class.</span></span>

```php
public function updateTokens($accessToken) {
  session([
    'accessToken' => $accessToken->getToken(),
    'refreshToken' => $accessToken->getRefreshToken(),
    'tokenExpires' => $accessToken->getExpires()
  ]);
}
```

<span data-ttu-id="d515a-152">Ersetzen Sie dann die `getAccessToken` vorhandene Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="d515a-152">Then replace the existing `getAccessToken` function with the following.</span></span>

```php
public function getAccessToken() {
  // Check if tokens exist
  if (empty(session('accessToken')) ||
      empty(session('refreshToken')) ||
      empty(session('tokenExpires'))) {
    return '';
  }

  // Check if token is expired
  //Get current time + 5 minutes (to allow for time differences)
  $now = time() + 300;
  if (session('tokenExpires') <= $now) {
    // Token is expired (or very close to it)
    // so let's refresh

    // Initialize the OAuth client
    $oauthClient = new \League\OAuth2\Client\Provider\GenericProvider([
      'clientId'                => env('OAUTH_APP_ID'),
      'clientSecret'            => env('OAUTH_APP_PASSWORD'),
      'redirectUri'             => env('OAUTH_REDIRECT_URI'),
      'urlAuthorize'            => env('OAUTH_AUTHORITY').env('OAUTH_AUTHORIZE_ENDPOINT'),
      'urlAccessToken'          => env('OAUTH_AUTHORITY').env('OAUTH_TOKEN_ENDPOINT'),
      'urlResourceOwnerDetails' => '',
      'scopes'                  => env('OAUTH_SCOPES')
    ]);

    try {
      $newToken = $oauthClient->getAccessToken('refresh_token', [
        'refresh_token' => session('refreshToken')
      ]);

      // Store the new values
      $this->updateTokens($newToken);

      return $newToken->getToken();
    }
    catch (League\OAuth2\Client\Provider\Exception\IdentityProviderException $e) {
      return '';
    }
  }

  // Token is still valid, just return it
  return session('accessToken');
}
```

<span data-ttu-id="d515a-153">Diese Methode überprüft zunächst, ob das Zugriffstoken abgelaufen oder nahezu abläuft.</span><span class="sxs-lookup"><span data-stu-id="d515a-153">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="d515a-154">Wenn dies der Fall ist, wird das Aktualisierungstoken verwendet, um neue Token abzurufen, dann wird der Cache aktualisiert und das neue Zugriffstoken zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d515a-154">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
