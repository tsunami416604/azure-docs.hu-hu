### <a name="prerequisites"></a>Előfeltételek
* A [MailChimp](https://www.MailChimp.com/) fiók 

A MailChimp-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás MailChimp-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást MailChimp-fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer MailChimp, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *MailChimp* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![1. lépés MailChimp](./media/connectors-create-api-mailchimp/mailchimp-1.png)
2. Ha még nem hozott létre a MailChimp előtt fennálló kapcsolatokat, a rendszer MailChimp hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a MailChimp fiók adatok eléréséhez:  
   ![2. lépés MailChimp](./media/connectors-create-api-mailchimp/mailchimp-2.png)
3. A MailChimp-felhasználónevet és jelszót kell megadniuk engedélyezik a Logic Apps alkalmazást:  
   ![3. lépés MailChimp](./media/connectors-create-api-mailchimp/mailchimp-3.png)   
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![4. lépés MailChimp](./media/connectors-create-api-mailchimp/mailchimp-4.png)

