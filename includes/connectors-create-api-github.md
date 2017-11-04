### <a name="prerequisites"></a>Előfeltételek
* A [GitHub](http://GitHub.com) fiók 

A GitHub-fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a GitHub-fiók való kapcsolódáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást csatlakozni a GitHub-fiók engedélyezése lépései a következők:

1. A GitHub, VPN-kapcsolat létrehozásához a Logic app tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *GitHub* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-github/github-1.png)
2. Ha még nem hozott létre a kapcsolatokat a GitHub előtt, a rendszer GitHub hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a GitHub-fiók adatok eléréséhez:  
   ![](./media/connectors-create-api-github/github-2.png)
3. Adja meg a GitHub felhasználói nevét és jelszavát a Logic Apps alkalmazást engedélyezése:  
   ![](./media/connectors-create-api-github/github-3.png)   
4. Szándékai megerősítését:  
   ![](./media/connectors-create-api-github/github-4.png)   
5. Figyelje meg, a kapcsolat létrehozása a portálon. Most már folytathatja a logikai alkalmazás létrehozása és a GitHub használatát:   
   ![](./media/connectors-create-api-github/github-5.png)   

