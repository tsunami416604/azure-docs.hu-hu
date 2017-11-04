### <a name="prerequisites"></a>Előfeltételek
* Egy [Office 365-felhasználók](https://office365.com) fiók  

Az Office 365 felhasználói fiókot a logikai alkalmazás használata előtt engedélyeznie kell az Office 365 felhasználói fiók kapcsolódni a logikai alkalmazást. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást az Office 365 felhasználói fiók kapcsolódni engedélyezésére lépései a következők:  

1. A Logic app tervezőben kapcsolat Office 365-felhasználók létrehozásához válasszon **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Office 365-felhasználók* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Az Office 365 felhasználók kapcsolat létrehozását lépést](./media/connectors-create-api-office365users/office365users-1.png)  
2. Ha még nem hozott létre a kapcsolatokat az Office 365-felhasználók előtt, a rendszer adja meg az Office 365-felhasználók hitelesítő adatait fogja kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és elérni az Office 365 felhasználói fiók adatait:  
   ![Az Office 365 felhasználók kapcsolat létrehozását lépést](./media/connectors-create-api-office365users/office365users-2.png)  
3. Az Office 365-felhasználók felhasználónevet és jelszót kell megadniuk engedélyezik a Logic Apps alkalmazást:  
   ![Az Office 365 felhasználók kapcsolat létrehozását lépést](./media/connectors-create-api-office365users/office365users-3.png)  
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![Az Office 365 felhasználók kapcsolat létrehozását lépést](./media/connectors-create-api-office365users/office365users-4.png)  

