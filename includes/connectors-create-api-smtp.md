### <a name="prerequisites"></a>Előfeltételek
* A [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) fiók  

Az SMTP-fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás csatlakozni az SMTP-fiók. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást csatlakozni az SMTP-fiók engedélyezése lépései a következők:  

1. Az SMTP-, VPN-kapcsolat létrehozásához a logic app tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *SMTP* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Ha még nem hozott létre előtt SMTP fennálló kapcsolatokat, a rendszer SMTP hitelesítő adatok megadása fog kérni. Ezek a hitelesítő adatok engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és az SMTP-fiókja adatainak eléréséhez:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

