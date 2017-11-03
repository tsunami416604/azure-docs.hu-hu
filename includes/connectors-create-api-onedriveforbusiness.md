### <a name="prerequisites"></a>Előfeltételek
* A [OneDrive](http://OneDrive.com) fiók 

A onedrive vállalati verzió fiókjában található logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást a onedrive vállalati verzió fiókjában való kapcsolódáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon. 

A Logic Apps alkalmazást, a onedrive vállalati verzió fiókjában való csatlakozáshoz engedélyezni lépései a következők:

1. Válassza ki a onedrive vállalati verzióhoz, VPN-kapcsolat létrehozásához a Logic app tervezőben, **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *onedrive vállalati verzió* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)
2. Ha még nem hozott létre a kapcsolatokat, mielőtt onedrive vállalati verzióhoz, a rendszer adja meg a OneDrive vállalati hitelesítő adatok fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használandó és a OneDrive vállalati fiók adatait hozzáférési:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)
3. Adja meg a onedrive vállalati verzió üzleti felhasználónevet és jelszót a Logic Apps alkalmazást engedélyezése:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   

