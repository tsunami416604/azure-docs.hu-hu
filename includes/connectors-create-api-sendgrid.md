### <a name="prerequisites"></a>Előfeltételek
* A [SendGrid](https://www.SendGrid.com/) fiók 

A SendGrid-fiókjából a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a SendGrid-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül. 

A logikai alkalmazás a SendGrid-fiókhoz való csatlakozás engedélyezése lépései a következők:

1. Hozzon létre egy kapcsolatot a SendGrid, a Logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *SendGrid* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![A SendGrid 1. lépés.](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Ha még nem hozott létre a Sendgridhez előtt kapcsolatokat, első kéri a SendGrid hitelesítő adatokat. Ezekkel a hitelesítő adatokkal kapcsolódni a logikai alkalmazás hitelesítéséhez használható, és a SendGrid-fiókhoz tartozó adatok eléréséhez:  
   ![A SendGrid 2. lépés.](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![A SendGrid 3. lépés.](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

