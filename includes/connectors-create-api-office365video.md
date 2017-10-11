### <a name="prerequisites"></a>Előfeltételek
* A [Office 365 videó](https://support.office.com/article/Meet-Office-365-Video-ca1cc1a9-a615-46e1-b6a3-40dbd99939a6) fiók  

Office 365 videó fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást az Office 365 videó fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást az Office 365 videó fiókjához történő engedélyezéséhez lépései a következők:  

1. A Logic app designer Office 365 videó-kapcsolat létrehozásához válassza **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *Office 365 videó* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![Az Office 365 videó kapcsolat létrehozását lépést](./media/connectors-create-api-office365video/office365video-1.png)  
2. Ha még nem hozott létre az Office 365 videó előtt a kapcsolatokat, a rendszer adja meg az Office 365 videó hitelesítő adatokat fogja kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és az Office 365 videó fiók adatok eléréséhez:  
   ![Az Office 365 videó kapcsolat létrehozását lépést](./media/connectors-create-api-office365video/office365video-2.png)  
3. Adja meg a hitelesítő adatokat, Office 365 videó való csatlakozáshoz:  
   ![Az Office 365 videó kapcsolat létrehozását lépést](./media/connectors-create-api-office365video/office365video-3.png)  
4. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![Az Office 365 videó kapcsolat létrehozását lépést](./media/connectors-create-api-office365video/office365video-4.png)  

