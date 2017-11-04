### <a name="prerequisites"></a>Előfeltételek
* A [Facebook](https://www.facebook.com/) fiók 

A Facebook-fiókkal a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a Facebook-fiókhoz való csatlakozáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást, a Facebook-fiókhoz való csatlakozáshoz engedélyezni lépései a következők:

1. A Logic app designer Facebook, a kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Facebook* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![1. lépés Facebook-on](./media/connectors-create-api-facebook/facebook-1.png)
2. Ha még nem hozott létre a Facebook előtt fennálló kapcsolatokat, a rendszer Facebook hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a Facebook-fiókkal adatok eléréséhez:  
   ![2. lépés Facebook-on](./media/connectors-create-api-facebook/facebook-2.png)
3. A Facebook-felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![3. lépés Facebook-on](./media/connectors-create-api-facebook/facebook-3.png)   
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![4. lépés Facebook-on](./media/connectors-create-api-facebook/facebook-4.png)   

