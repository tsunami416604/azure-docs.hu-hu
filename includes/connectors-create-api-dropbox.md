### <a name="prerequisites"></a>Előfeltételek
* A [Dropbox](https://www.Dropbox.com/) fiók 

A Dropbox-fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás Dropbox-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást csatlakozni a Dropbox-fiók engedélyezése lépései a következők:

1. A dropbox alkalmazásba, VPN-kapcsolat létrehozásához a Logic app tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Dropbox* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Dropbox 1. lépés](./media/connectors-create-api-dropbox/dropbox-1.png)
2. Ha még nem hozott létre a kapcsolatokat a dropbox alkalmazásba előtt, a rendszer Dropbox hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó és hozzáférési a Dropbox-fiók adatait:  
   ![Dropbox 2. lépés](./media/connectors-create-api-dropbox/dropbox-2.png)
3. A Dropbox felhasználónevet és jelszót kell megadniuk engedélyezik a Logic Apps alkalmazást:  
   ![3. lépés dropbox-bA](./media/connectors-create-api-dropbox/dropbox-3.png)   
4. Engedélyezze a logikai alkalmazást a Dropbox-fiók használata:  
   ![4. lépés dropbox-bA](./media/connectors-create-api-dropbox/dropbox-4.png)
5. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![5. lépés dropbox-bA](./media/connectors-create-api-dropbox/dropbox-5.png)   

