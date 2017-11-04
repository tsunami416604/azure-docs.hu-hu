### <a name="prerequisites"></a>Előfeltételek
* Egy Wunderlist fiók  

A Wunderlist fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Wunderlist fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást Wunderlist fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer Wunderlist, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Wunderlist* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-wunderlist/wunderlist-0.png)
2. Ha még nem hozott létre előtt Wunderlist fennálló kapcsolatokat, a rendszer Wunderlist hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó, és Wunderlist fiókja adatok eléréséhez:   
   ![](./media/connectors-create-api-wunderlist/wunderlist-1.png)  
3. Adja meg a hitelesítő adatait, majd kattintson a gombra, majd jelentkezzen be  
   ![](./media/connectors-create-api-wunderlist/wunderlist-2.png)  
4. Meg kell majd értesítést Mi a Logic app engedéllyel rendelkezni az Wunderlist fiókját. Ha elfogadja, kattintson a gombra jelzi a szerződést. 
   ![](./media/connectors-create-api-wunderlist/wunderlist-4.png)  
5. Végül válassza ki a **engedélyezés** gomb  
   ![](./media/connectors-create-api-wunderlist/wunderlist-5.png)  

