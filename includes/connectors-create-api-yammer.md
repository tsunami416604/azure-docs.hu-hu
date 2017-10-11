### <a name="prerequisites"></a>Előfeltételek
* A [Yammer](https://www.yammer.com/) fiók 

A Yammer-fiókjába a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Yammer-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást Yammer-fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer Yammer, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Yammer-* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-yammer/yammer-1.png)
2. Ha még nem hozott létre előtt Yammer fennálló kapcsolatokat, a rendszer Yammer hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a Yammer-fiókjába adatok eléréséhez:  
   ![](./media/connectors-create-api-yammer/yammer-2.png)
3. A Yammer-felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![](./media/connectors-create-api-yammer/yammer-3.png)   
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-yammer/yammer-4.png)   

