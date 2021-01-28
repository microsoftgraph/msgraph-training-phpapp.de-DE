<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph abzurufen. In diesem Schritt integrieren Sie die [oauth2-client-Bibliothek](https://github.com/thephpleague/oauth2-client) in die Anwendung.

1. Öffnen Sie **die env-Datei** im Stammverzeichnis Ihrer PHP-Anwendung, und fügen Sie am Ende der Datei den folgenden Code hinzu.

    :::code language="ini" source="../demo/graph-tutorial/example.env" range="51-57":::

1. Ersetzen `YOUR_APP_ID_HERE` Sie sie durch die Anwendungs-ID aus dem Anwendungsregistrierungsportal, und ersetzen Sie sie durch das `YOUR_APP_SECRET_HERE` kennwort, das Sie generiert haben.

    > [!IMPORTANT]
    > Wenn Sie Quellcodeverwaltung wie Git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei aus der Quellcodeverwaltung auszuschließen, um zu verhindern, dass versehentlich Ihre App-ID und Ihr Kennwort durch lecks `.env` geht.

1. Erstellen Sie eine neue Datei im Ordner **"./config"** mit dem `azure.php` Namen, und fügen Sie den folgenden Code hinzu.

    :::code language="php" source="../demo/graph-tutorial/config/azure.php":::

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

1. Erstellen Sie eine neue Datei im Verzeichnis **./app/Http/Controllers** mit dem `AuthController.php` Namen, und fügen Sie den folgenden Code hinzu.

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
          'clientId'                => config('azure.appId'),
          'clientSecret'            => config('azure.appSecret'),
          'redirectUri'             => config('azure.redirectUri'),
          'urlAuthorize'            => config('azure.authority').config('azure.authorizeEndpoint'),
          'urlAccessToken'          => config('azure.authority').config('azure.tokenEndpoint'),
          'urlResourceOwnerDetails' => '',
          'scopes'                  => config('azure.scopes')
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
            'clientId'                => config('azure.appId'),
            'clientSecret'            => config('azure.appSecret'),
            'redirectUri'             => config('azure.redirectUri'),
            'urlAuthorize'            => config('azure.authority').config('azure.authorizeEndpoint'),
            'urlAccessToken'          => config('azure.authority').config('azure.tokenEndpoint'),
            'urlResourceOwnerDetails' => '',
            'scopes'                  => config('azure.scopes')
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

    Dadurch wird ein Controller mit zwei Aktionen `signin` definiert: und `callback` .

    Die Aktion generiert die Azure AD-Anmelde-URL, speichert den vom OAuth-Client generierten Wert und leitet dann den Browser zur `signin` `state` Azure AD-Anmeldeseite um.

    Die `callback` Aktion leitet Azure um, nachdem die Anmeldung abgeschlossen ist. Diese Aktion stellt sicher, dass der Wert mit dem gespeicherten Wert und dann den von Azure gesendeten Autorisierungscode zum Anfordern eines `state` Zugriffstokens entspricht. Anschließend wird sie zurück zur Startseite mit dem Zugriffstoken im temporären Fehlerwert umgeleitet. Sie können damit überprüfen, ob die Anmeldung funktioniert, bevor Sie weiter arbeiten.

1. Fügen Sie die Routen zu **./routes/web.php hinzu.**

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. Starten Sie den Server, und navigieren Sie zu `https://localhost:8000` . Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden. Melden Sie sich mit Ihrem Microsoft-Konto an.

1. Überprüfen Sie die Zustimmungsaufforderung. Die Liste der Berechtigungen entspricht der Liste der Berechtigungsbereiche, die in **.env konfiguriert sind.**

    - **Behalten Sie den Zugriff auf Daten bei,** auf die Sie zugriffen haben: ( ) Diese Berechtigung wird von MSAL angefordert, um Aktualisierungstoken `offline_access` abzurufen.
    - **Melden Sie sich an, und lesen** Sie Ihr Profil: ( ) Mit dieser Berechtigung kann die App das Profil und Profilfoto des angemeldeten `User.Read` Benutzers erhalten.
    - **Lesen Der Postfacheinstellungen:** ( ) Mit dieser Berechtigung kann die App die Postfacheinstellungen des Benutzers lesen, einschließlich Zeitzone `MailboxSettings.Read` und Zeitzone.
    - **Haben Vollzugriff auf** Ihre Kalender: ( ) Diese Berechtigung ermöglicht der App, Ereignisse im Kalender des Benutzers zu lesen, neue Ereignisse hinzuzufügen und `Calendars.ReadWrite` vorhandene zu ändern.

1. Stimmen Sie den angeforderten Berechtigungen zu. Der Browser leitet zur App um, in der Sie das Token sehen.

### <a name="get-user-details"></a>Benutzerdetails abrufen

In diesem Abschnitt aktualisieren Sie die Methode, um das Profil des Benutzers `callback` aus Microsoft Graph zu erhalten.

1. Fügen Sie die folgenden Anweisungen am oberen Rand von `use` **/app/Http/Controllers/AuthController.php** unter der Zeile `namespace App\Http\Controllers;` hinzu.

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. Ersetzen Sie `try` den Block in der Methode durch den folgenden `callback` Code.

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

Der neue Code erstellt ein Objekt, weist das Zugriffstoken zu und verwendet es dann zum Anfordern des `Graph` Benutzerprofils. Der Anzeigename des Benutzers wird der temporären Ausgabe zu Testzwecken hinzufügt.

## <a name="storing-the-tokens"></a>Speichern des Tokens

Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern. Da es sich um eine Beispiel-App handelt, speichern Sie sie der Einfachheit halber in der Sitzung. Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.

1. Erstellen Sie ein neues Verzeichnis im **Verzeichnis ./app** mit dem Namen, erstellen Sie dann eine neue Datei in diesem Verzeichnis namens , und fügen Sie `TokenStore` den folgenden Code `TokenCache.php` hinzu.

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

1. Fügen Sie die folgende Anweisung am oberen Rand `use` von **./app/Http/Controllers/AuthController.php** unter der Zeile `namespace App\Http\Controllers;` hinzu.

    ```php
    use App\TokenStore\TokenCache;
    ```

1. Ersetzen Sie `try` den Block in der `callback` vorhandenen Funktion durch Folgendes.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a>Implementieren des Abmeldens

Fügen Sie vor dem Testen dieses neuen Features eine Möglichkeit zum Abmelden hinzu.

1. Fügen Sie der Klasse die folgende Aktion `AuthController` hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. Fügen Sie diese Aktion zu **./routes/web.php hinzu.**

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. Starten Sie den Server neu, und gehen Sie den Anmeldevorgang durch. Sie sollten wieder auf der Startseite landen, aber die Benutzeroberfläche sollte geändert werden, um anzugeben, dass Sie angemeldet sind.

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den Link **"Abmelden" zu** klicken. Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Aktualisieren von Token

An diesem Punkt verfügt Ihre Anwendung über ein Zugriffstoken, das im `Authorization` Header von API-Aufrufen gesendet wird. Dies ist das Token, mit dem die App im Namen des Benutzers auf Microsoft Graph zugreifen kann.

Dieses Token ist jedoch nur kurzzeitig verfügbar. Das Token läuft eine Stunde nach seiner Entsprechung ab. An dieser Stelle kommt das Aktualisierungstoken ins Spiel. Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss. Aktualisieren Sie den Tokenverwaltungscode, um die Tokenaktualisierung zu implementieren.

1. Öffnen **Sie ./app/TokenStore/TokenCache.php,** und fügen Sie der Klasse die folgende Funktion `TokenCache` hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. Ersetzen Sie die vorhandene `getAccessToken`-Funktion durch Folgendes.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

Diese Methode überprüft zunächst, ob das Zugriffstoken abgelaufen oder kurz vor dem Ablauf steht. Wenn dies der Wert ist, verwendet er das Aktualisierungstoken, um neue Token abzurufen, aktualisiert dann den Cache und gibt das neue Zugriffstoken zurück.
