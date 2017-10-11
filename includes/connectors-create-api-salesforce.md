### <a name="prerequisites"></a>Előfeltételek
* A [Salesforce](https://salesforce.com) fiók  

A Salesforce-fiókhoz a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a Salesforce-fiókhoz való csatlakozáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást a Salesforce-fiókhoz való csatlakozás engedélyezése lépései a következők:  

1. A logic app designer Salesforce, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Salesforce* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Kép: a 1 Salesforce kapcsolat](./media/connectors-create-api-salesforce/salesforce-1.png)  
2. Ha még nem hozott létre a Salesforce előtt fennálló kapcsolatokat, a rendszer a Salesforce hitelesítő fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a Salesforce-fiókhoz adatok eléréséhez:  
   ![Kép: Salesforce kapcsolat 2](./media/connectors-create-api-salesforce/salesforce-2.png)  
3. A Salesforce-felhasználónevet és jelszót kell megadniuk engedélyezik a Logic Apps alkalmazást:  
   ![Kép: a 3 Salesforce kapcsolat](./media/connectors-create-api-salesforce/salesforce-3.png)  
4. Tartózkodási Salesforce való csatlakozáshoz:  
   ![Kép: a 4 Salesforce kapcsolat](./media/connectors-create-api-salesforce/salesforce-4.png)  
5. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![Kép: a 5 Salesforce kapcsolat](./media/connectors-create-api-salesforce/salesforce-5.png)  

