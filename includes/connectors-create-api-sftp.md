### <a name="prerequisites"></a>Előfeltételek
* Egy [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) fiók  

SFTP-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazás SFTP-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást SFTP fiókjához történő engedélyezéséhez lépései a következők:  

1. Válassza ki az SFTP, VPN-kapcsolat létrehozásához a logic app tervezőben, **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *SFTP* be a keresőmezőbe. Válassza ki a **SFTP - amikor egy fájl hozzáadása vagy módosítása** eseményindító:  
   ![Kép: SFTP online kapcsolat 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Ha még nem hozott létre a kapcsolatokat az SFTP előtt, a rendszer SFTP hitelesítő adatok megadása fog kérni. Ezeket a hitelesítő adatokat engedélyezése a logikai alkalmazás való kapcsolódáshoz használandó, és az SFTP fiók adatok eléréséhez:  
   ![Kép: SFTP online kapcsolat 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:   
   ![Kép: a 3 SFTP online kapcsolat](./media/connectors-create-api-sftp/sftp-3.png) 

