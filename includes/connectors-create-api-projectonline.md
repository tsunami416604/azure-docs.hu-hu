### <a name="prerequisites"></a>Előfeltételek
* A [ProjectOnline](https://products.office.com/Project/project-online-with-project-for-office-365) fiók 

A ProjectOnline fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás ProjectOnline fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást ProjectOnline fiókjához történő engedélyezéséhez lépései a következők:

1. A Logic app designer ProjectOnline, kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *ProjectOnline* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![1. lépés ProjectOnline](./media/connectors-create-api-projectonline/projectonline-1.png)
2. Ha még nem hozott létre előtt ProjectOnline fennálló kapcsolatokat, a rendszer ProjectOnline hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó, és ProjectOnline fiókja adatok eléréséhez:  
   ![2. lépés ProjectOnline](./media/connectors-create-api-projectonline/projectonline-2.png)
3. A ProjectOnline felhasználónevet és jelszót kell megadniuk a Logic Apps alkalmazást engedélyezése:  
   ![3. lépés ProjectOnline](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![4. lépés ProjectOnline](./media/connectors-create-api-projectonline/projectonline-4.png)   

