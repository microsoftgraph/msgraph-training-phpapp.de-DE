<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erstellen Sie eine neue Azure AD-Webanwendungs Registrierung mithilfe des Azure Active Directory Admin Center.

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich über ein **persönliches Konto** (aka: Microsoft-Konto) oder ein Geschäfts- **oder Schulkonto**an.

1. Wählen Sie **Azure Active Directory** in der linken Navigationsleiste aus, und wählen Sie dann **App-Registrierungen (Vorschau)** unter **Manage**aus.

    ![Screenshot der APP-Registrierungen ](./images/aad-portal-app-registrations.png)

1. Wählen Sie **neue Registrierung**aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen **** Sie Name `PHP Graph Tutorial`auf fest.
    - Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis und persönlichen Microsoft-Konten**fest.
    - Legen Sie unter umLeitungs- **URI**die erste Dropdown `Web` Liste auf fest, und `http://localhost:8000/callback`legen Sie den Wert auf fest.

    ![Screenshot der Seite "Registrieren einer Anwendung"](./images/aad-register-an-app.png)

1. Wählen Sie **registrieren**aus. Kopieren Sie auf der Seite mit dem **php Graph-Lernprogramm** den Wert der **Anwendungs-ID (Client)** , und speichern Sie ihn, dann benötigen Sie ihn im nächsten Schritt.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](./images/aad-application-id.png)

1. Wählen Sie unter **Manage**die Option **Certificates & Secrets** aus. Klicken Sie auf die Schaltfläche **neuen geheimen Client Schlüssel** . Geben Sie einen Wert in **Description** ein, und wählen Sie eine der Optionen für **Expires** und wählen Sie **Hinzufügen**aus.

    ![Screenshot des Dialogfelds zum Hinzufügen eines geheimen Clients](./images/aad-new-client-secret.png)

1. Kopieren Sie den Client geheimen Wert, bevor Sie diese Seite verlassen. Sie benötigen Sie im nächsten Schritt.

    > [!IMPORTANT]
    > Dieser geheime Client Schlüssel wird nie wieder angezeigt, stellen Sie daher sicher, dass Sie ihn jetzt kopieren.

    ![Screenshot des neu hinzugefügten geheimen Clients](./images/aad-copy-client-secret.png)