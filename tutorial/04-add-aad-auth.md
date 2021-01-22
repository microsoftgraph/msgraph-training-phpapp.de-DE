<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a9346-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a9346-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="a9346-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen.</span><span class="sxs-lookup"><span data-stu-id="a9346-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="a9346-103">In diesem Schritt integrieren Sie die [oauth2-client-Bibliothek](https://github.com/thephpleague/oauth2-client) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a9346-103">In this step you will integrate the [oauth2-client](https://github.com/thephpleague/oauth2-client) library into the application.</span></span>

1. <span data-ttu-id="a9346-104">Öffnen Sie **die env-Datei** im Stammverzeichnis Ihrer PHP-Anwendung, und fügen Sie am Ende der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-104">Open the **.env** file in the root of your PHP application, and add the following code to the end of the file.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/example.env" range="48-54":::

1. <span data-ttu-id="a9346-105">Ersetzen `YOUR_APP_ID_HERE` Sie sie durch die Anwendungs-ID aus dem Anwendungsregistrierungsportal, und ersetzen Sie sie durch das `YOUR_APP_PASSWORD_HERE` kennwort, das Sie generiert haben.</span><span class="sxs-lookup"><span data-stu-id="a9346-105">Replace `YOUR_APP_ID_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_PASSWORD_HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="a9346-106">Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass versehentlich Ihre App-ID und Ihr Kennwort durch den Speicherverlust `.env` verfingen.</span><span class="sxs-lookup"><span data-stu-id="a9346-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="a9346-107">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="a9346-107">Implement sign-in</span></span>

1. <span data-ttu-id="a9346-108">Erstellen Sie eine neue Datei im Verzeichnis **./app/Http/Controllers** mit dem `AuthController.php` Namen, und fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-108">Create a new file in the **./app/Http/Controllers** directory named `AuthController.php` and add the following code.</span></span>

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

    <span data-ttu-id="a9346-109">Dadurch wird ein Controller mit zwei Aktionen `signin` definiert: und `callback` .</span><span class="sxs-lookup"><span data-stu-id="a9346-109">This defines a controller with two actions: `signin` and `callback`.</span></span>

    <span data-ttu-id="a9346-110">Die Aktion generiert die Azure AD-Anmelde-URL, speichert den vom OAuth-Client generierten Wert und leitet dann den Browser zur `signin` `state` Azure AD-Anmeldeseite um.</span><span class="sxs-lookup"><span data-stu-id="a9346-110">The `signin` action generates the Azure AD signin URL, saves the `state` value generated by the OAuth client, then redirects the browser to the Azure AD signin page.</span></span>

    <span data-ttu-id="a9346-111">Die `callback` Aktion leitet Azure um, nachdem die Anmeldung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="a9346-111">The `callback` action is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="a9346-112">Diese Aktion stellt sicher, dass der Wert mit dem gespeicherten Wert und dann den von Azure gesendeten Autorisierungscode zum Anfordern eines `state` Zugriffstokens entspricht.</span><span class="sxs-lookup"><span data-stu-id="a9346-112">That action makes sure the `state` value matches the saved value, then users the authorization code sent by Azure to request an access token.</span></span> <span data-ttu-id="a9346-113">Anschließend wird sie mit dem Zugriffstoken im temporären Fehlerwert zurück zur Startseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="a9346-113">It then redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="a9346-114">Sie können damit überprüfen, ob die Anmeldung funktioniert, bevor Sie weiter arbeiten.</span><span class="sxs-lookup"><span data-stu-id="a9346-114">You'll use this to verify that sign-in is working before moving on.</span></span>

1. <span data-ttu-id="a9346-115">Fügen Sie die Routen zu **./routes/web.php hinzu.**</span><span class="sxs-lookup"><span data-stu-id="a9346-115">Add the routes to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. <span data-ttu-id="a9346-116">Starten Sie den Server, und navigieren Sie zu `https://localhost:8000` .</span><span class="sxs-lookup"><span data-stu-id="a9346-116">Start the server and browse to `https://localhost:8000`.</span></span> <span data-ttu-id="a9346-117">Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden.</span><span class="sxs-lookup"><span data-stu-id="a9346-117">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="a9346-118">Melden Sie sich mit Ihrem Microsoft-Konto an.</span><span class="sxs-lookup"><span data-stu-id="a9346-118">Login with your Microsoft account.</span></span>

1. <span data-ttu-id="a9346-119">Überprüfen Sie die Zustimmungsaufforderung.</span><span class="sxs-lookup"><span data-stu-id="a9346-119">Examine the consent prompt.</span></span> <span data-ttu-id="a9346-120">Die Liste der Berechtigungen entspricht der Liste der Berechtigungsbereiche, die in **.env konfiguriert sind.**</span><span class="sxs-lookup"><span data-stu-id="a9346-120">The list of permissions correspond to list of permissions scopes configured in **.env**.</span></span>

    - <span data-ttu-id="a9346-121">**Behalten Sie den Zugriff auf Daten,** auf die Sie zugriffen haben: ( ) Diese Berechtigung wird von MSAL angefordert, um Aktualisierungstoken `offline_access` abzurufen.</span><span class="sxs-lookup"><span data-stu-id="a9346-121">**Maintain access to data you have given it access to:** (`offline_access`) This permission is requested by MSAL in order to retrieve refresh tokens.</span></span>
    - <span data-ttu-id="a9346-122">**Melden Sie sich an, und lesen** Sie Ihr Profil: ( ) Mit dieser Berechtigung kann die App das Profil und Profilfoto des angemeldeten `User.Read` Benutzers erhalten.</span><span class="sxs-lookup"><span data-stu-id="a9346-122">**Sign you in and read your profile:** (`User.Read`) This permission allows the app to get the logged-in user's profile and profile photo.</span></span>
    - <span data-ttu-id="a9346-123">**Lesen Der Postfacheinstellungen:** ( ) Mit dieser Berechtigung kann die App die Postfacheinstellungen des Benutzers lesen, einschließlich Zeitzone `MailboxSettings.Read` und Zeitzone.</span><span class="sxs-lookup"><span data-stu-id="a9346-123">**Read your mailbox settings:** (`MailboxSettings.Read`) This permission allows the app to read the user's mailbox settings, including time zone and time format.</span></span>
    - <span data-ttu-id="a9346-124">**Haben Vollzugriff auf** Ihre Kalender: ( ) Diese Berechtigung ermöglicht der App, Ereignisse im Kalender des Benutzers zu lesen, neue Ereignisse hinzuzufügen und `Calendars.ReadWrite` vorhandene zu ändern.</span><span class="sxs-lookup"><span data-stu-id="a9346-124">**Have full access to your calendars:** (`Calendars.ReadWrite`) This permission allows the app to read events on the user's calendar, add new events, and modify existing ones.</span></span>

1. <span data-ttu-id="a9346-125">Stimmen Sie den angeforderten Berechtigungen zu.</span><span class="sxs-lookup"><span data-stu-id="a9346-125">Consent to the requested permissions.</span></span> <span data-ttu-id="a9346-126">Der Browser leitet zur App um, in der Sie das Token sehen.</span><span class="sxs-lookup"><span data-stu-id="a9346-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="a9346-127">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="a9346-127">Get user details</span></span>

<span data-ttu-id="a9346-128">In diesem Abschnitt aktualisieren Sie die Methode, um das Profil des Benutzers `callback` aus Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="a9346-128">In this section you'll update the `callback` method to get the user's profile from Microsoft Graph.</span></span>

1. <span data-ttu-id="a9346-129">Fügen Sie die folgenden Anweisungen am oberen Rand von `use` **/app/Http/Controllers/AuthController.php** unter der Zeile `namespace App\Http\Controllers;` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-129">Add the following `use` statements to the top of **/app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. <span data-ttu-id="a9346-130">Ersetzen Sie `try` den Block in der Methode durch den folgenden `callback` Code.</span><span class="sxs-lookup"><span data-stu-id="a9346-130">Replace the `try` block in the `callback` method with the following code.</span></span>

    ```php
    try {
      // Make the token request
      $accessToken = $oauthClient->getAccessToken('authorization_code', [
        'code' => $authCode
      ]);

      $graph = new Graph();
      $graph->setAccessToken($accessToken->getToken());

      $user = $graph->createRequest('GET', '/me?$select=displayName,mail,mailboxSettings,userPrincipalName')
        ->setReturnType(Model\User::class)
        ->execute();

      // TEMPORARY FOR TESTING!
      return redirect('/')
        ->with('error', 'Access token received')
        ->with('errorDetail', 'User:'.$user->getDisplayName().', Token:'.$accessToken->getToken());
    }
    ```

<span data-ttu-id="a9346-131">Der neue Code erstellt ein Objekt, weist das Zugriffstoken zu und verwendet es dann zum Anfordern des `Graph` Benutzerprofils.</span><span class="sxs-lookup"><span data-stu-id="a9346-131">The new code creates a `Graph` object, assigns the access token, then uses it to request the user's profile.</span></span> <span data-ttu-id="a9346-132">Der Anzeigename des Benutzers wird der temporären Ausgabe zu Testzwecken hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="a9346-132">It adds the user's display name to the temporary output for testing.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="a9346-133">Speichern des Tokens</span><span class="sxs-lookup"><span data-stu-id="a9346-133">Storing the tokens</span></span>

<span data-ttu-id="a9346-134">Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="a9346-134">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="a9346-135">Da es sich um eine Beispiel-App handelt, speichern Sie sie der Einfachheit halber in der Sitzung.</span><span class="sxs-lookup"><span data-stu-id="a9346-135">Since this is a sample app, for simplicity's sake, you'll store them in the session.</span></span> <span data-ttu-id="a9346-136">Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9346-136">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

1. <span data-ttu-id="a9346-137">Erstellen Sie ein neues Verzeichnis im **Verzeichnis ./app** mit dem Namen, erstellen Sie dann eine neue Datei in diesem Verzeichnis namens , und `TokenStore` fügen Sie den folgenden Code `TokenCache.php` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-137">Create a new directory in the **./app** directory named `TokenStore`, then create a new file in that directory named `TokenCache.php`, and add the following code.</span></span>

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
          'userEmail' => null !== $user->getMail() ? $user->getMail() : $user->getUserPrincipalName(),
          'userTimeZone' => $user->getMailboxSettings()->getTimeZone()
        ]);
      }

      public function clearTokens() {
        session()->forget('accessToken');
        session()->forget('refreshToken');
        session()->forget('tokenExpires');
        session()->forget('userName');
        session()->forget('userEmail');
        session()->forget('userTimeZone');
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

1. <span data-ttu-id="a9346-138">Fügen Sie die folgende Anweisung am oberen Rand von `use` **./app/Http/Controllers/AuthController.php** unter der Zeile `namespace App\Http\Controllers;` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-138">Add the following `use` statement to the top of **./app/Http/Controllers/AuthController.php**, beneath the `namespace App\Http\Controllers;` line.</span></span>

    ```php
    use App\TokenStore\TokenCache;
    ```

1. <span data-ttu-id="a9346-139">Ersetzen Sie `try` den Block in der `callback` vorhandenen Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="a9346-139">Replace the `try` block in the existing `callback` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a><span data-ttu-id="a9346-140">Implementieren des Abmeldens</span><span class="sxs-lookup"><span data-stu-id="a9346-140">Implement sign-out</span></span>

<span data-ttu-id="a9346-141">Fügen Sie vor dem Testen dieses neuen Features eine Möglichkeit zum Abmelden hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-141">Before you test this new feature, add a way to sign out.</span></span>

1. <span data-ttu-id="a9346-142">Fügen Sie der Klasse die folgende Aktion `AuthController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-142">Add the following action to the `AuthController` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. <span data-ttu-id="a9346-143">Fügen Sie diese Aktion zu **./routes/web.php hinzu.**</span><span class="sxs-lookup"><span data-stu-id="a9346-143">Add this action to **./routes/web.php**.</span></span>

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. <span data-ttu-id="a9346-144">Starten Sie den Server neu, und gehen Sie den Anmeldevorgang durch.</span><span class="sxs-lookup"><span data-stu-id="a9346-144">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="a9346-145">Sie sollten wieder auf der Startseite landen, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="a9346-145">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. <span data-ttu-id="a9346-147">Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den Link **"Abmelden" zu** klicken.</span><span class="sxs-lookup"><span data-stu-id="a9346-147">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="a9346-148">Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.</span><span class="sxs-lookup"><span data-stu-id="a9346-148">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="a9346-150">Aktualisieren von Token</span><span class="sxs-lookup"><span data-stu-id="a9346-150">Refreshing tokens</span></span>

<span data-ttu-id="a9346-151">An diesem Punkt verfügt Ihre Anwendung über ein Zugriffstoken, das im `Authorization` Header von API-Aufrufen gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="a9346-151">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="a9346-152">Dies ist das Token, mit dem die App im Namen des Benutzers auf Microsoft Graph zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="a9346-152">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="a9346-153">Dieses Token ist jedoch nur kurzzeitig verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a9346-153">However, this token is short-lived.</span></span> <span data-ttu-id="a9346-154">Das Token läuft eine Stunde nach seiner Entsprechung ab.</span><span class="sxs-lookup"><span data-stu-id="a9346-154">The token expires an hour after it is issued.</span></span> <span data-ttu-id="a9346-155">An dieser Stelle kommt das Aktualisierungstoken ins Spiel.</span><span class="sxs-lookup"><span data-stu-id="a9346-155">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="a9346-156">Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss.</span><span class="sxs-lookup"><span data-stu-id="a9346-156">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span> <span data-ttu-id="a9346-157">Aktualisieren Sie den Tokenverwaltungscode, um die Tokenaktualisierung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="a9346-157">Update the token management code to implement token refresh.</span></span>

1. <span data-ttu-id="a9346-158">Öffnen **Sie ./app/TokenStore/TokenCache.php,** und fügen Sie der Klasse die folgende Funktion `TokenCache` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9346-158">Open **./app/TokenStore/TokenCache.php** and add the following function to the `TokenCache` class.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. <span data-ttu-id="a9346-159">Ersetzen Sie die vorhandene `getAccessToken`-Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="a9346-159">Replace the existing `getAccessToken` function with the following.</span></span>

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

<span data-ttu-id="a9346-160">Diese Methode überprüft zunächst, ob das Zugriffstoken abgelaufen oder kurz vor dem Ablauf steht.</span><span class="sxs-lookup"><span data-stu-id="a9346-160">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="a9346-161">Wenn dies der Wert ist, verwendet er das Aktualisierungstoken, um neue Token abzurufen, aktualisiert dann den Cache und gibt das neue Zugriffstoken zurück.</span><span class="sxs-lookup"><span data-stu-id="a9346-161">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span>
