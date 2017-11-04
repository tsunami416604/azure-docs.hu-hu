### <a name="prerequisites"></a>Előfeltételek
* A [SendGrid](https://www.SendGrid.com/) fiók 

A SendGrid-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a SendGrid-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást SendGrid fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer SendGrid, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *SendGrid* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![1. lépés SendGrid](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Ha még nem hozott létre a SendGrid előtt fennálló kapcsolatokat, a rendszer Sendgridbeli hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a SendGrid fiók adatok eléréséhez:  
   ![2. lépés SendGrid](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![3. lépés SendGrid](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

