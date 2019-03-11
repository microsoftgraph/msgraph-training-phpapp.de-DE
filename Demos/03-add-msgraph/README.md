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

## <a name="register-a-web-application-with-the-application-registration-portal"></a>Registrieren einer Webanwendung mit dem Anwendungs Registrierungs Portal

1. Öffnen Sie einen Browser, und navigieren Sie zum [Anwendungs Registrierungs Portal](https://apps.dev.microsoft.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie oben auf der Seite **eine APP hinzufügen** aus.

    > **Hinweis:** Wenn auf der Seite mehr als eine Schaltfläche **app hinzufügen** angezeigt wird, wählen Sie diejenige aus, die der Liste **konvergierter apps** entspricht.

1. Legen Sie auf der Seite **Anwendung registrieren** den **Anwendungsnamen** auf **php Graph Tutorial** fest, und wählen Sie **Erstellen**aus.

    ![Screenshot des Erstellens einer neuen app in der APP-Registrierungs Portal-Website](/tutorial/images/arp-create-app-01.png)

1. Kopieren Sie auf der Seite mit der Registrierung für das **php Graph-Lernprogramm** im Abschnitt **Eigenschaften** die **Anwendungs-ID** , so wie Sie Sie später benötigen.

    ![Screenshot der neu erstellten Anwendungs-ID](/tutorial/images/arp-create-app-02.png)

1. Scrollen Sie nach unten zum Abschnitt **Anwendungs Geheimnisse** .

    1. Wählen Sie **Neues Kennwort generieren**aus.
    1. Kopieren Sie im Dialogfeld **Neues Kennwort generiert** den Inhalt des Felds, so wie Sie es später benötigen.

        > **Wichtig:** Dieses Kennwort wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie es jetzt kopieren.

    ![Screenshot des Kennworts der neu erstellten Anwendung](/tutorial/images/arp-create-app-03.png)

1. Scrollen Sie nach unten zum Abschnitt **Plattformen** .

    1. Wählen Sie **Plattform hinzufügen**aus.
    1. Wählen Sie im Dialogfeld **Plattform hinzufügen** die Option **Web**aus.

        ![Screenshot Erstellen einer Plattform für die APP](/tutorial/images/arp-create-app-04.png)

    1. Geben Sie **** im Feld Webplattform die URL `http://localhost:8000/callback` für die Umleitungs- **URLs**ein.

        ![Screenshot der neu hinzugefügten Webplattform für die Anwendung](/tutorial/images/arp-create-app-05.png)

1. Scrollen Sie zum unteren Rand der Seite, und wählen Sie **Speichern**aus.

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