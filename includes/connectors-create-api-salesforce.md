### <a name="prerequisites"></a>Előfeltételek
* A [Salesforce](https://salesforce.com) fiók  

Salesforce-fiókjához, a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás a Salesforce-fiókhoz való csatlakozáshoz. Szerencsére a ehhez egyszerűen, a logikai alkalmazás az Azure Portal webhelyen belül.  

A logikai alkalmazás a Salesforce-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. Hozzon létre egy kapcsolatot a Salesforce-hoz, a logic app Designerben, jelölje be **megjelenítése a Microsoft által felügyelt API-k** a legördülő listában adja meg *Salesforce* kifejezést a keresőmezőbe. Válassza ki az eseményindítót vagy műveletet fogja kíván használni:  
   ![1. a Salesforce kapcsolat kép](./media/connectors-create-api-salesforce/salesforce-1.png)  
2. Ha még nem hozott létre a Salesforce-hoz, mielőtt kapcsolatokat, első kéri a Salesforce hitelesítő adatokat. Ezeket a hitelesítő adatokat szeretne csatlakozni a logikai alkalmazás hitelesítéséhez használható, és a Salesforce-fiókjához tartozó adatok eléréséhez:  
   ![Salesforce-kapcsolat lemezkép 2](./media/connectors-create-api-salesforce/salesforce-2.png)  
3. Adja meg a Salesforce-hoz felhasználói nevét és jelszavát, a logikai alkalmazás engedélyezése:  
   ![Salesforce-kapcsolat lemezkép 3](./media/connectors-create-api-salesforce/salesforce-3.png)  
4. Lehetővé teszi számunkra, hogy csatlakozzon a Salesforce-hoz:  
   ![Salesforce-kapcsolat lemezkép 4](./media/connectors-create-api-salesforce/salesforce-4.png)  
5. Figyelje meg, hogy a kapcsolat létrejött, és most már szabadon folytassa a további lépésekkel, a logikai alkalmazásban:  
   ![Salesforce-kapcsolat lemezkép 5](./media/connectors-create-api-salesforce/salesforce-5.png)  

