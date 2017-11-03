### <a name="prerequisites"></a>Előfeltételek
* A [mezőben](http://box.com) fiók  

Be fiókjába a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás bezárásához fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást be fiókjába való csatlakozáshoz engedélyezni lépései a következők:  

1. A Logic app designer mezőbe kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *mezőben* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![mezőbe kapcsolat létrehozását lépést](./media/connectors-create-api-box/box-1.png)  
2. Ha még nem hozott létre fennálló kapcsolatokat előtt mezőben, a rendszer be hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és be fiókja adatok eléréséhez:  
   ![mezőbe kapcsolat létrehozását lépést](./media/connectors-create-api-box/box-2.png)  
3. A mezőben felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![mezőbe kapcsolat létrehozását lépést](./media/connectors-create-api-box/box-3.png)  
4. Tartózkodási mezőbe való csatlakozáshoz:  
   ![mezőbe kapcsolat létrehozását lépést](./media/connectors-create-api-box/box-4.png)  
5. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![mezőbe kapcsolat létrehozását lépést](./media/connectors-create-api-box/box-5.png)  

