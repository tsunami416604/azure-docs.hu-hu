#### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [Office 365](https://office365.com) fiók  

A logikai alkalmazást az Office 365-fiók használatához engedélyezni, a logikai alkalmazást az Office 365-fiókjához. Ehhez egyszerűen a logikai alkalmazásban, az Azure portálon.  

Engedélyezze a Logic Apps alkalmazást csatlakozni az Office 365-fiókra, az alábbi lépéseket követve:

1. Logikai alkalmazás létrehozása. Válassza ki a Logic Apps-tervezőben **megjelenítése Microsoft felügyelt API-k** a legördülő listában, és írja be a keresőmezőbe "office 365". Az eseményindítók és műveletek közül választhat:  
    ![Az Office 365 kapcsolat létrehozását lépést](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Ha korábban még nem létrehozott Office 365 fennálló kapcsolatokat, az Office 365 hitelesítő adataival bejelentkezni kéri:  
    ![Az Office 365 kapcsolat létrehozását lépést](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Válassza ki **bejelentkezés**, és írja be a felhasználónevet és jelszót. Válassza ki **bejelentkezés**:  
    ![Az Office 365 kapcsolat létrehozását lépést](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Ezek a hitelesítő adatok segítségével engedélyezik a Logic Apps alkalmazást, és Office 365-fiókját. 
4. Figyelje meg, a kapcsolat létrejött. Most folytassa a Logic Apps alkalmazást más lépéseket:   
    ![Az Office 365 kapcsolat létrehozását lépést](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

