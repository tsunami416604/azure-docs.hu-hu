### <a name="prerequisites"></a>Előfeltételek
* A [Trello](http://trello.com) fiók 

A Trello fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Trello fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást Trello fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer Trello, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Trello* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-trello/trello-1.png)
2. Ha még nem hozott létre a Trello előtt fennálló kapcsolatokat, a rendszer Trello hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezése a logikai alkalmazás való kapcsolódáshoz használandó, és a Trello fiók adatok eléréséhez:  
   ![](./media/connectors-create-api-trello/trello-2.png) 
3. Tartózkodási Trello való csatlakozáshoz:  
   ![](./media/connectors-create-api-trello/trello-3.png)   
4. A Trello felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![](./media/connectors-create-api-trello/trello-4.png)  
5. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-trello/trello-5.png)

