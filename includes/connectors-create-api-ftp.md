### <a name="prerequisites"></a>Előfeltételek
* Egy [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) fiók  

Az FTP-fiók a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást az FTP-fiókhoz való csatlakozáshoz. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást FTP-fiókhoz való csatlakozáshoz engedélyezni lépései a következők:  

1. FTP-, VPN-kapcsolat létrehozásához a logic app tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *FTP* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![FTP-kapcsolat létrehozása lépés](./media/connectors-create-api-ftp/ftp-1.png)  
2. Ha még nem hozott létre a kapcsolatokat az FTP-előtt, a rendszer FTP hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezik a Logic Apps alkalmazást való kapcsolódáshoz használt, és az FTP-fiók adatok eléréséhez:  
   ![FTP-kapcsolat létrehozása lépés](./media/connectors-create-api-ftp/ftp-2.png)  
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![FTP-kapcsolat létrehozása lépés](./media/connectors-create-api-ftp/ftp-3.png)  

