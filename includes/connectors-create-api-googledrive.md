### <a name="prerequisites"></a>Előfeltételek
* A [Google Drive-hoz](https://www.google.com/drive/) fiók  

A Google Drive-hoz fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a Google Drive-hoz-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást a Google Drive-hoz fiókjához engedélyezésére lépései a következők:  

1. Válassza ki a Google Drive-hoz, a VPN-kapcsolat létrehozásához a Logic app tervezőben, **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Google Drive-hoz* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-1.png)  
2. Ha még nem hozott létre a Google Drive-hoz előtt fennálló kapcsolatokat, a rendszer adja meg a Google Drive-hoz hitelesítő adatokat fogja kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és a Google Drive-hoz fiók adatok eléréséhez:  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-2.png)  
3. Adja meg a Google Drive-hoz e-mail címét:  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-3.png)  
4. Adja meg a Google Drive-hoz jelszó hitelesítése a Logic Apps alkalmazást:  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-4.png)
5. A Google Drive-hoz való csatlakozás engedélyezése  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-5.png)  
6. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![Google Drive-hoz a kapcsolat létrehozását lépést](./media/connectors-create-api-googledrive/googledrive-6.png)  

