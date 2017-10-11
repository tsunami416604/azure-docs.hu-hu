### <a name="prerequisites"></a>Előfeltételek
* Egy [RSS](https://wikipedia.org/wiki/RSS) fiók  

RSS-fiókját a logikai alkalmazás használata előtt engedélyeznie kell a logikai alkalmazást az RSS-fiókjához. Szerencsére ehhez egyszerűen a a logikai alkalmazásban, az Azure portálon.  

A Logic Apps alkalmazást RSS fiókjához történő engedélyezéséhez lépései a következők:  

1. RSS-, VPN-kapcsolat létrehozásához a Logic app tervezőben, válassza ki a **megjelenítése Microsoft felügyelt API-k** a legördülő listában adja meg *RSS* be a keresőmezőbe. Válassza ki az eseményindító vagy művelet fogjuk használni kívánt:  
   ![RSS kapcsolat létrehozását lépést](./media/connectors-create-api-rss/rss-1.png)  
2. Válassza ki **hozható létre kapcsolat** :  
   ![RSS kapcsolat létrehozását lépést](./media/connectors-create-api-rss/rss-2.png)  
3. Figyelje meg, a kapcsolat létrejött, és szabadon most folytatja a Logic Apps alkalmazást más lépéseket:  
   ![RSS kapcsolat létrehozását lépést](./media/connectors-create-api-rss/rss-3.png)  

