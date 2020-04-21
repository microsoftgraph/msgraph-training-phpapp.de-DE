<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die [oauth2-Client](https://github.com/thephpleague/oauth2-client) Bibliothek in die Anwendung integrieren.

1. Öffnen Sie `.env` die Datei im Stammverzeichnis Ihrer PHP-Anwendung, und fügen Sie den folgenden Code am Ende der Datei hinzu.

    :::code language="ini" source="../demo/graph-tutorial/.env.example" id="OAuthSettingsSnippet":::

1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID aus dem Anwendungs Registrierungs Portal, `YOUR_APP_PASSWORD_HERE` und ersetzen Sie durch das Kennwort, das Sie generiert haben.

    > [!IMPORTANT]
    > Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei `.env` aus der Quellcodeverwaltung auszuschließen, damit versehentlich keine APP-ID und Ihr Kennwort verloren gehen.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

1. Erstellen Sie eine neue Datei im **./app/http/Controllers** -Verzeichnis `AuthController.php` mit dem Namen, und fügen Sie den folgenden Code hinzu.

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

    Dadurch wird ein Controller mit zwei Aktionen definiert `signin` : `callback`und.

    Durch `signin` die Aktion wird die Azure AD SignIn-URL generiert `state` , der vom OAuth-Client generierte Wert gespeichert und anschließend der Browser an die Azure AD SignIn-Seite umgeleitet.

    In `callback` der Aktion wird Azure weitergeleitet, nachdem das SignIn abgeschlossen wurde. Durch diese Aktion wird sicher `state` gestellt, dass der Wert mit dem gespeicherten Wert übereinstimmt, und dann Benutzer der Autorisierungscode, der von Azure gesendet wurde, um ein Zugriffstoken anzufordern. Anschließend wird mit dem Zugriffstoken im temporären Fehlerwert zurück zur Startseite umgeleitet. Verwenden Sie diese, um zu überprüfen, ob die Anmeldung funktionsfähig ist, bevor Sie fortfahren.

1. Fügen Sie die Routen zu **./routes/Web.php**hinzu.

    ```php
    Route::get('/signin', 'AuthController@signin');
    Route::get('/callback', 'AuthController@callback');
    ```

1. Starten Sie den Server, und `https://localhost:8000`navigieren Sie zu. Klicken Sie auf die Schaltfläche zum Anmelden, um zu `https://login.microsoftonline.com`weitergeleitet zu werden. Melden Sie sich mit Ihrem Microsoft-Konto an, und stimmen Sie den angeforderten Berechtigungen zu. Der Browser leitet zur App um, in der Sie das Token sehen.

### <a name="get-user-details"></a>Benutzerdetails abrufen

In diesem Abschnitt aktualisieren Sie die `callback` -Methode, um das Profil des Benutzers aus Microsoft Graph abzurufen.

1. Fügen Sie die `use` folgenden Anweisungen am oberen Rand von **/App/http/Controllers/AuthController.php**unterhalb der `namespace App\Http\Controllers;` -Kante hinzu.

    ```php
    use Microsoft\Graph\Graph;
    use Microsoft\Graph\Model;
    ```

1. Ersetzen Sie `try` den Block in `callback` der-Methode durch den folgenden Code.

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

Der neue Code erstellt ein `Graph` -Objekt, weist das Zugriffstoken zu und verwendet es dann, um das Profil des Benutzers anzufordern. Der Anzeigename des Benutzers wird der temporären Ausgabe zu Testzwecken hinzugefügt.

## <a name="storing-the-tokens"></a>Speichern des Tokens

Nun, da Sie Token abrufen können, ist es an der Zeit, eine Möglichkeit einzurichten, diese in der App zu speichern. Da es sich um eine Beispiel-App handelt, speichern Sie Sie aus Gründen der Einfachheit in der Sitzung. Eine echte App würde eine zuverlässigere sichere Speicherlösung wie eine Datenbank verwenden.

1. Erstellen Sie ein neues Verzeichnis im **./app** -Verzeichnis `TokenStore`mit dem Namen, erstellen Sie dann eine neue Datei `TokenCache.php`in dem Verzeichnis mit dem Namen, und fügen Sie den folgenden Code hinzu.

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

1. Fügen Sie die `use` folgende Anweisung am oberen Rand von **./app/http/Controllers/AuthController.php**unterhalb der `namespace App\Http\Controllers;` -Leiste hinzu.

    ```php
    use App\TokenStore\TokenCache;
    ```

1. Ersetzen Sie `try` den Block in der `callback` vorhandenen Funktion durch Folgendes.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="StoreTokensSnippet":::

## <a name="implement-sign-out"></a>Implementieren der Abmeldung

Bevor Sie dieses neue Feature testen, fügen Sie eine Möglichkeit zum Abmelden hinzu.

1. Fügen Sie der `AuthController` Klasse die folgende Aktion hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/Http/Controllers/AuthController.php" id="SignOutSnippet":::

1. Fügen Sie diese Aktion zu **./routes/Web.php**.

    ```php
    Route::get('/signout', 'AuthController@signout');
    ```

1. Starten Sie den Server neu, und fahren Sie mit dem Anmeldevorgang fort. Sie sollten wieder auf der Startseite enden, aber die Benutzeroberfläche sollte sich ändern, um anzugeben, dass Sie angemeldet sind.

    ![Screenshot der Startseite nach dem Anmelden](./images/add-aad-auth-01.png)

1. Klicken Sie in der oberen rechten Ecke auf den Avatar des Benutzers, um auf den **Abmelde** Link zuzugreifen. Wenn Sie auf **Abmelden** klicken, wird die Sitzung zurückgesetzt und Sie kehren zur Startseite zurück.

    ![Screenshot des Dropdown-Menüs mit dem Link „Abmelden“](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>Aktualisieren von Token

Zu diesem Zeitpunkt verfügt Ihre Anwendung über ein Zugriffstoken, das in der `Authorization` Kopfzeile von API-aufrufen gesendet wird. Dies ist das Token, das es der App ermöglicht, im Namen des Benutzers auf Microsoft Graph zuzugreifen.

Dieses Token ist jedoch nur kurzzeitig verfügbar. Das Token läuft eine Stunde nach seiner Ausgabe ab. An dieser Stelle kommt das Aktualisierungstoken ins Spiel. Anhand des Aktualisierungstoken ist die App in der Lage, ein neues Zugriffstoken anzufordern, ohne dass der Benutzer sich erneut anmelden muss. Aktualisieren Sie den Token-Verwaltungscode, um die Token-Aktualisierung zu implementieren.

1. Öffnen Sie **/App/TokenStore/TokenCache.php** , und fügen Sie der `TokenCache` -Klasse die folgende Funktion hinzu.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="UpdateTokensSnippet":::

1. Ersetzen Sie die vorhandene `getAccessToken`-Funktion durch Folgendes.

    :::code language="php" source="../demo/graph-tutorial/app/TokenStore/TokenCache.php" id="GetAccessTokenSnippet":::

Diese Methode überprüft zunächst, ob das Zugriffstoken abgelaufen oder nahezu abläuft. Wenn dies der Fall ist, wird das Aktualisierungstoken verwendet, um neue Token abzurufen, dann wird der Cache aktualisiert und das neue Zugriffstoken zurückgegeben.
