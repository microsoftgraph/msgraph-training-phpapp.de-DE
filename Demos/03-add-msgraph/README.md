# <a name="how-to-run-the-completed-project"></a>Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen des abgeschlossenen Projekts in diesem Ordner benötigen Sie Folgendes:

- [Php](http://php.net/downloads.php) auf dem Entwicklungscomputer installiert. Wenn Sie nicht über PHP verfügen, besuchen Sie den vorherigen Link für Downloadoptionen. (**Hinweis:** dieses Tutorial wurde mit PHP Version 7,2 geschrieben. Die Schritte in diesem Handbuch funktionieren möglicherweise mit anderen Versionen, die jedoch nicht getestet wurden.)
- [Komponist](https://getcomposer.org/) , der auf dem Entwicklungscomputer installiert ist.
- [Laravel](https://laravel.com/) wird auf dem Entwicklungscomputer installiert.
- Entweder ein persönliches Microsoft-Konto mit einem Postfach auf Outlook.com oder ein Microsoft-Geschäfts-oder Schulkonto.

Wenn Sie nicht über ein Microsoft-Konto verfügen, gibt es eine Reihe von Optionen, um ein kostenloses Konto zu erhalten:

- Sie können [sich für ein neues persönliches Microsoft-Konto registrieren](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).
- Sie können [sich für das office 365-Entwicklerprogramm anmelden](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Registrieren einer Webanwendung mit dem Azure Active Directory Admin Center

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie **Azure Active Directory** in der linken Navigationsleiste aus, und wählen Sie dann **App-Registrierungen (Vorschau)** unter **Manage**aus.

    ![Screenshot der APP-Registrierungen ](/tutorial/images/aad-portal-app-registrations.png)

1. Wählen Sie **neue Registrierung**aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen **** Sie Name `PHP Graph Tutorial`auf fest.
    - Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis und persönlichen Microsoft-Konten**fest.
    - Legen Sie unter umLeitungs- **URI**die erste Dropdown `Web` Liste auf fest, und `http://localhost:8000/callback`legen Sie den Wert auf fest.

    ![Screenshot der Seite "Registrieren einer Anwendung"](/tutorial/images/aad-register-an-app.png)

1. Wählen Sie **registrieren**aus. Kopieren Sie auf der Seite mit dem **php Graph-Lernprogramm** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

1. Wählen Sie unter **Manage**die Option **Certificates & Secrets** aus. Klicken Sie auf die Schaltfläche **neuen geheimen Client Schlüssel** . Geben Sie einen Wert in **Description** ein, und wählen Sie eine der Optionen für **Expires** und wählen Sie **Hinzufügen**aus.

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Clients](/tutorial/images/aad-new-client-secret.png)

1. Kopieren Sie den Client geheimen Wert, bevor Sie diese Seite verlassen. Sie benötigen Sie im nächsten Schritt.

    > [!IMPORTANT]
    > Dieser geheime Client Schlüssel wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.

    ![Screenshot des neu hinzugefügten geheimen Clients](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Benennen Sie `.env.example` die Datei `.env`in um.
1. Bearbeiten Sie `.env` die Datei, und nehmen Sie die folgenden Änderungen vor.
    1. Ersetzen `YOUR_APP_ID_HERE` Sie durch die **Anwendungs-ID** , die Sie im App-Registrierungs Portal erhalten haben.
    1. Ersetzen `YOUR_APP_PASSWORD_HERE` Sie durch das Kennwort, das Sie im App-Registrierungs Portal erhalten haben.
1. Navigieren Sie in der Befehlszeilenschnittstelle zu diesem Verzeichnis, und führen Sie den folgenden Befehl aus, um die Anforderungen zu installieren.

    ```Shell
    composer install
    ```

1. Führen Sie in der Befehlszeilenschnittstelle (CLI) den folgenden Befehl aus, um einen Anwendungsschlüssel zu generieren.

    ```Shell
    php artisan key:generate
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Führen Sie den folgenden Befehl in der CLI aus, um die Anwendung zu starten.

    ```Shell
    php artisan serve
    ```

1. Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:8000`.