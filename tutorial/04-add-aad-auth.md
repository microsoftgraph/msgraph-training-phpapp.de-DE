<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="58e84-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="58e84-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="58e84-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="58e84-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="58e84-103">In diesem Schritt werden Sie die [oauth2-Client](https://github.com/thephpleague/oauth2-client) Bibliothek in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="58e84-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

1. <span data-ttu-id="58e84-104">Öffnen Sie `.env` die Datei im Stammverzeichnis Ihrer PHP-Anwendung, und fügen Sie den folgenden Code am Ende der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-104">Open the `.env` file in the root of your PHP application, and add the following code to the end of the file.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/.env.example" id="OAuthSettingsSnippet":::

1. <span data-ttu-id="58e84-105">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie durch das Kennwort, das Sie generiert haben.</span><span class="sxs-lookup"><span data-stu-id="58e84-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="58e84-106">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `.env` aus der Quellcodeverwaltung auszuschließen, damit versehentlich keine APP-ID und Ihr Kennwort verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="58e84-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="58e84-107">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="58e84-107">Implement sign-in</span></span>

1. <span data-ttu-id="58e84-108">Erstellen Sie eine neue Datei im **./app/http/Controllers** -Verzeichnis `AuthController.php` mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-108">Create a new file in the **./app/Http/Controllers** directory named `AuthController.php` and add the following code.</span></span>

    ```php
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

        if (!isset($expectedState)) {
          // If there is no expected state in the session,
          // do nothing and redirect to the home page.
          return redirect('/');
        }

        if (!isset($providedState) || $expectedState != $providedState) {
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

    <span data-ttu-id="58e84-109">Dadurch wird ein Controller mit zwei Aktionen definiert `signin` : `callback`und.</span><span class="sxs-lookup"><span data-stu-id="58e84-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

    <span data-ttu-id="58e84-110">Durch `signin` die Aktion wird die Azure AD SignIn-URL generiert `state` , der vom OAuth-Client generierte Wert gespeichert und anschließend der Browser an die Azure AD SignIn-Seite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="58e84-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    <span data-ttu-id="58e84-111">In `callback` der Aktion wird Azure weitergeleitet, nachdem das SignIn abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="58e84-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="58e84-112">Durch diese Aktion wird sicher `state` gestellt, dass der Wert mit dem gespeicherten Wert übereinstimmt, und dann Benutzer der Autorisierungscode, der von Azure gesendet wurde, um ein Zugriffstoken anzufordern.</span><span class="sxs-lookup"><span data-stu-id="58e84-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="58e84-113">Anschließend wird mit dem Zugriffstoken im temporären Fehlerwert zurück zur Startseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="58e84-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="58e84-114">Verwenden Sie diese, um zu überprüfen, ob die Anmeldung funktionsfähig ist, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="58e84-114">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="58e84-115">Fügen Sie die Routen zu **./routes/Web.php**hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-115">Add the routes to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. <span data-ttu-id="58e84-116">Starten Sie den Server, und `https://localhost:8000`navigieren Sie zu.</span><span class="sxs-lookup"><span data-stu-id="58e84-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="58e84-117">Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden.</span><span class="sxs-lookup"><span data-stu-id="58e84-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="58e84-118">Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="58e84-118">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="58e84-119">Der Browser leitet zur App um, in der Sie das Token sehen.</span><span class="sxs-lookup"><span data-stu-id="58e84-119">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="58e84-120">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="58e84-120">Get user details</span></span>

<span data-ttu-id="58e84-121">In diesem Abschnitt aktualisieren Sie die `callback` -Methode, um das Profil des Benutzers aus Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="58e84-121">In this section you'll update the `callback` method to get the user's profile from Microsoft Graph.</span></span>

1. <span data-ttu-id="58e84-122">Fügen Sie die `use` folgenden Anweisungen am oberen Rand von **/App/http/Controllers/AuthController.php**unterhalb der `namespace App\Http\Controllers;` -Kante hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-122">Add the following `use` statements to the top of **/app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. <span data-ttu-id="58e84-123">Ersetzen Sie `try` den Block in `callback` der-Methode durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="58e84-123">Replace the `try` block in the `callback` method with the following code.</span></span>

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

<span data-ttu-id="58e84-124">Der neue Code erstellt ein `Graph` -Objekt, weist das Zugriffstoken zu und verwendet es dann, um das Profil des Benutzers anzufordern.</span><span class="sxs-lookup"><span data-stu-id="58e84-124">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="58e84-125">Der Anzeigename des Benutzers wird der temporären Ausgabe zu Testzwecken hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="58e84-125">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="58e84-126">Speichern des Tokens</span><span class="sxs-lookup"><span data-stu-id="58e84-126">Storing the tokens</span></span>

<span data-ttu-id="58e84-127">Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="58e84-127">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="58e84-128">Da es sich um eine Beispiel-App handelt, speichern Sie Sie aus Gründen der Einfachheit in der Sitzung.</span><span class="sxs-lookup"><span data-stu-id="58e84-128">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="58e84-129">Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="58e84-129">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="58e84-130">Erstellen Sie ein neues Verzeichnis im **./app** -Verzeichnis `TokenStore`mit dem Namen, erstellen Sie dann eine neue Datei `TokenCache.php`in dem Verzeichnis mit dem Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-130">Create a new directory in the **./app** directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

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

1. <span data-ttu-id="58e84-131">Fügen Sie die `use` folgende Anweisung am oberen Rand von **./app/http/Controllers/AuthController.php**unterhalb der `namespace App\Http\Controllers;` -Leiste hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-131">Add the following `use` statement to the top of **./app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use App\TokenStore\TokenCache;
    ```

1. <span data-ttu-id="58e84-132">Ersetzen Sie `try` den Block in der `callback` vorhandenen Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="58e84-132">Replace the `try` block in the existing `callback` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="58e84-133">Implementieren der Abmeldung</span><span class="sxs-lookup"><span data-stu-id="58e84-133">Implement sign-out</span></span>

<span data-ttu-id="58e84-134">Bevor Sie dieses neue Feature testen, fügen Sie eine Möglichkeit zum Abmelden hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-134">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="58e84-135">Fügen Sie der `AuthController` Klasse die folgende Aktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-135">Add the following action to the `AuthController` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. <span data-ttu-id="58e84-136">Fügen Sie diese Aktion zu **./routes/Web.php**.</span><span class="sxs-lookup"><span data-stu-id="58e84-136">Add this action to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. <span data-ttu-id="58e84-137">Starten Sie den Server neu, und fahren Sie mit dem Anmeldevorgang fort.</span><span class="sxs-lookup"><span data-stu-id="58e84-137">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="58e84-138">Sie sollten wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="58e84-138">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. <span data-ttu-id="58e84-140">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="58e84-140">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="58e84-141">Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="58e84-141">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="58e84-143">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="58e84-143">Refreshing tokens</span></span>

<span data-ttu-id="58e84-144">Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="58e84-144">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="58e84-145">Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="58e84-145">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="58e84-146">Dieses Token ist jedoch nur kurzzeitig verfügbar.</span><span class="sxs-lookup"><span data-stu-id="58e84-146">However, this token is short-lived.</span></span> <span data-ttu-id="58e84-147">Das Token läuft eine Stunde nach seiner Ausgabe ab.</span><span class="sxs-lookup"><span data-stu-id="58e84-147">The token expires an hour after it is issued.</span></span> <span data-ttu-id="58e84-148">An dieser Stelle kommt das Aktualisierungstoken ins Spiel.</span><span class="sxs-lookup"><span data-stu-id="58e84-148">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="58e84-149">Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="58e84-149">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="58e84-150">Aktualisieren Sie den Token-Verwaltungscode, um die Token-Aktualisierung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="58e84-150">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="58e84-151">Öffnen Sie **/App/TokenStore/TokenCache.php** , und fügen Sie der `TokenCache` -Klasse die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="58e84-151">Open **./app/TokenStore/TokenCache.php** and add the following function to the `TokenCache` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. <span data-ttu-id="58e84-152">Ersetzen Sie die vorhandene `getAccessToken`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="58e84-152">Replace the existing `getAccessToken` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

<span data-ttu-id="58e84-153">Diese Methode überprüft zunächst, ob das Zugriffstoken abgelaufen oder nahezu abläuft.</span><span class="sxs-lookup"><span data-stu-id="58e84-153">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="58e84-154">Wenn dies der Fall ist, wird das Aktualisierungstoken verwendet, um neue Token abzurufen, dann wird der Cache aktualisiert und das neue Zugriffstoken zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="58e84-154">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
