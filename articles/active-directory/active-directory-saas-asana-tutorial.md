<properties
	pageTitle="Tutorial: Azure Active Directory-Integration mit Asana | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Asana konfigurieren."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/10/2016"
	ms.author="jeedes"/>


# Tutorial: Azure Active Directory-Integration mit Asana

In diesem Tutorial erfahren Sie, wie Sie Asana in Azure Active Directory (Azure AD) integrieren.

Die Integration von Asana in Azure AD bietet folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Asana haben soll.
- Sie können es Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Asana anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im klassischen Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## Voraussetzungen

Um die Azure AD-Integration mit Asana konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SSO-fähiges **Asana**-Abonnement


> [AZURE.NOTE] Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.


Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Sie sollten keine Produktionsumgebung verwenden, sofern dies nicht erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.


## Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptelementen:

1. Hinzufügen von Asana über den Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD


## Hinzufügen von Asana über den Katalog
Zum Konfigurieren der Integration von Asana in Azure AD müssen Sie Asana über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Asana über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **klassischen Azure-Portals** auf **Active Directory**.

	![Active Directory][1]

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie zum Öffnen der Anwendungsansicht in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

	![Anwendungen][2]

4. Klicken Sie unten auf der Seite auf **Hinzufügen**.

	![Anwendungen][3]

5. Klicken Sie im Dialogfeld **Was möchten Sie tun?** auf **Anwendung aus dem Katalog hinzufügen**.

	![Anwendungen][4]

6. Geben Sie im Suchfeld die Zeichenfolge **Asana** ein.

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)

7. Wählen Sie im Ergebnisbereich die Option **Asana** aus, und klicken Sie dann auf **Abschließen**, um die Anwendung hinzuzufügen.

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

##  Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie anhand eines Testbenutzers namens Britta Simon das einmalige Anmelden von Azure AD mit Asana.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Asana als Gegenpart für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Asana muss eine Linkbeziehung eingerichtet werden. Diese Linkbeziehung wird hergestellt, indem Sie den Benutzernamen in Azure AD dem Benutzernamen in Asana zuweisen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Asana müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren von Azure AD – einmaliges Anmelden](#configuring-azure-ad-single-single-sign-on)**, um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Erstellen eines Asana-Testbenutzers](#creating-an-Asana-test-user)**, um in Asana einen Gegenpart von Britta Simon zu erhalten, der mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### Konfigurieren des einmaligen Anmeldens von Azure AD

Das Ziel dieses Abschnitts besteht darin, das einmalige Anmelden von Azure AD im klassischen Azure-Portal zu aktivieren und in Ihrer Asana-Anwendung zu konfigurieren.

**Führen Sie die folgenden Schritte aus, um das einmalige Anmeldens von Azure AD mit Asana zu konfigurieren:**

1. Klicken Sie im oberen Menü auf **Schnellstart**.

	![Einmaliges Anmelden konfigurieren][6]
2. Klicken Sie im klassischen Portal auf der Anwendungsintegrationsseite für **Asana** auf **Einmaliges Anmelden konfigurieren**, um das Dialogfeld **Einmaliges Anmelden konfigurieren** zu öffnen.

	![Einmaliges Anmelden konfigurieren][7]

3. Wählen Sie auf der Seite **Wie sollen sich Benutzer bei Asana anmelden** die Option **Azure AD – einmaliges Anmelden** aus, und klicken Sie dann auf **Weiter**.
 	
	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)

4. Führen Sie auf der Dialogseite **App-Einstellungen konfigurieren** die folgenden Schritte aus:

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)


    a. Geben Sie im Textfeld für die Anmelde-URL eine URL im folgenden Format ein: `https://app.asana.com`

	c. Klicken Sie auf **Weiter**.

5. Klicken Sie auf der Seite **Einmaliges Anmelden konfigurieren für Asana** auf **Zertifikat herunterladen**, und speichern Sie die Datei auf Ihrem Computer. Kopieren Sie außerdem den Wert für die SAML-SSO-URL.
	
	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)

8. Klicken Sie mit der rechten Maustaste auf das Zertifikat, und öffnen Sie die Zertifikatsdatei mit Ihrem bevorzugten Text-Editor. Kopieren Sie den Inhalt zwischen den Überschriften für Beginn und Ende des Zertifikats. Hierbei handelt es sich um das X.509-Zertifikat zum Konfigurieren von SSO in Asana.

6. Melden Sie sich in einem anderen Browserfenster bei Ihrer Asana-Anwendung als Administrator an. Greifen Sie zum Konfigurieren von SSO in Asana auf die Arbeitsbereichseinstellungen zu, indem Sie in der rechten oben Bildschirmecke auf den Namen des Arbeitsbereichs klicken. Klicken Sie dann auf **Einstellungen für <Name Ihres Arbeitsbereich>**.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)

7. Klicken Sie im Fenster **Organisationseinstellungen** auf **Verwaltung**. Klicken Sie auf **Members must log in via SAML** (Mitglieder müssen sich über SAML anmelden), um die SSO-Konfiguration zu aktivieren. Führen Sie anschließend die folgenden Schritte aus:

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)

	a. Fügen Sie im Textfeld **URL der Anmeldeseite** die SAML-Anmelde-URL aus Azure AD ein.

	b. Fügen Sie im Textfeld **X.509-Zertifikat** das aus Azure AD kopierte X.509-Zertifikat ein.

9. Klicken Sie auf **Speichern**. Weitere Informationen finden Sie im [Asana-Handbuch zum Einrichten von SSO](https://asana.com/guide/help/premium/authentication#gl-saml).

7. Navigieren Sie in Azure AD zur Seite **Einmaliges Anmelden konfigurieren für Asana**, bestätigen Sie die Konfiguration des einmaligen Anmeldens, und klicken Sie anschließend auf **Weiter**.
	
	![Azure AD – einmaliges Anmelden][10]

8. Klicken Sie auf der Seite **Bestätigung zur einmaligen Anmeldung** auf **Fertig stellen**.
  	
	![Azure AD – einmaliges Anmelden][11]


### Erstellen eines Azure AD-Testbenutzers
In diesem Abschnitt erstellen Sie im klassischen Portal einen Testbenutzer mit dem Namen Britta Simon.

![Azure AD-Benutzer erstellen][20]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **klassischen Azure-Portals** auf **Active Directory**.
	
	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png)

2. Wählen Sie in der Liste **Verzeichnis** das Verzeichnis aus, für das Sie die Verzeichnisintegration aktivieren möchten.

3. Klicken Sie im Menü oben auf **Benutzer**, um die Liste der Benutzer anzuzeigen.
	
	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png)

4. Um das Dialogfeld **Benutzer hinzufügen** zu öffnen, klicken Sie auf der Symbolleiste unten auf **Benutzer hinzufügen**.

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png)

5. Führen Sie auf der Dialogfeldseite **Informationen über diesen Benutzer** die folgenden Schritte aus:
 
	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png)

    a. Wählen Sie als „Benutzertyp“ die Option „Neuer Benutzer in Ihrer Organisation“ aus.

    b. Geben Sie in das Textfeld **Benutzername** den Text **BrittaSimon** ein.

    c. Klicken Sie auf **Weiter**.

6.  Führen Sie auf der Dialogfeldseite **Benutzerprofil** die folgenden Schritte aus:

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png)

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.

    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.

    c. Geben Sie in das Textfeld **Anzeigename** den Namen **Britta Simon** ein.

    d. Wählen Sie in der Liste **Rolle** die Option **Benutzer** aus.

    e. Klicken Sie auf **Weiter**.

7. Klicken Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** auf **Erstellen**.

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png)

8. Führen Sie auf der Dialogfeldseite **Vorübergehendes Kennwort abrufen** die folgenden Schritte aus:

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png)

    a. Notieren Sie den Wert von **Neues Kennwort**.

    b. Klicken Sie auf **Fertig stellen**.



### Erstellen eines Asana-Testbenutzers

In diesem Abschnitt erstellen Sie in Asana einen Benutzer namens Britta Simon.

1. Navigieren Sie in **Asana** im linken Bereich zum Abschnitt **Teams**. Klicken Sie auf die Pluszeichen-Schaltfläche.

	![Erstellen eines Azure AD-Testbenutzers](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png)

2. Geben Sie die E-Mail-Adresse „britta.simon@contoso.com“ in das Textfeld ein, und wählen **Invite** (Einladen) aus.
3. Klicken Sie auf **Send Invite** (Einladung senden). Eine E-Mail wird an das E-Mail-Konto des neuen Benutzers gesendet. Der Benutzer muss das Konto erstellen und bestätigen.


### Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Asana gewähren.

![Benutzer zuweisen][200]

**Führen Sie die folgenden Schritte aus, um die Zuweisung von Britta Simon zu Asana durchzuführen:**

1. Klicken Sie zum Öffnen der Anwendungsansicht im klassischen Portal in der oberen Menüleiste der Verzeichnisansicht auf **Anwendungen**.

	![Benutzer zuweisen][201]

2. Wählen Sie in der Anwendungsliste den Eintrag **Asana** aus.

	![Einmaliges Anmelden konfigurieren](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png)

1. Klicken Sie im oberen Menü auf **Benutzer**.

	![Benutzer zuweisen][203]

1. Wählen Sie in der Liste „Alle Benutzer“ den Eintrag **Britta Simon** aus.

2. Klicken Sie auf der Symbolleiste unten auf **Zuweisen**.

	![Benutzer zuweisen][205]


### Testen der einmaligen Anmeldung

In diesem Abschnitt wird das einmalige Anmelden von Azure AD getestet.

Rufen Sie die Asana-Anmeldeseite auf. Geben Sie im Textfeld für die E-Mail-Adresse die E-Mail-Adresse „britta.simon@contoso.com“ ein. Lassen Sie das Textfeld für das Kennwort leer, und klicken Sie dann auf **Log In** (Anmelden). Sie werden zur Azure AD-Anmeldeseite weitergeleitet. Geben Sie Ihre Azure AD-Anmeldeinformationen an. Sie sind nun bei Asana angemeldet.

## Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]: ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->