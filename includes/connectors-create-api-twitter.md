### <a name="prerequisites"></a>Előfeltételek
* Twitter-fiók 

A Twitter-fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást, a Twitter-fiók való kapcsolódáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást, a Twitter-fiók való csatlakozáshoz engedélyezni lépései a következők:

1. A logic app designer Twitter, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Twitter* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Kép: a 0 Twitter kapcsolat](./media/connectors-create-api-twitter/twitter-0.png)
2. Ha még nem hozott létre előtt Twitter fennálló kapcsolatokat, a rendszer Twitter hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó, és a Twitter-fiók adatok eléréséhez:  
   ![Kép: Csatlakozás 1 Twitter](./media/connectors-create-api-twitter/twitter-1.png)  
3. A Twitter-felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![Kép: Csatlakozás 2 Twitter](./media/connectors-create-api-twitter/twitter-2.png)  
4. Erősítse meg az Ön jogosultságait:  
   ![Kép: csatlakozás 3 Twitter](./media/connectors-create-api-twitter/twitter-3.png)  
5. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![Kép: csatlakozás 4 Twitter](./media/connectors-create-api-twitter/twitter-4.png)

