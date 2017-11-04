### <a name="prerequisites"></a>Előfeltételek
* Az Azure-fiók; létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free)
* Egy [Azure Blob Storage-fiók](../articles/storage/common/storage-create-storage-account.md) például a tárfiók nevét és a hozzáférési kulcsot. Ezt az információt, a storage-fiókot az Azure-portálon tulajdonságainak szerepel. Tudjon meg többet az [Azure Storage](../articles/storage/common/storage-introduction.md).

A logikai alkalmazást az Azure Blob Storage-fiók használatához csatlakozni az Azure Blob Storage-fiók. Ehhez egyszerűen a logikai alkalmazásban, az Azure portálon.  

Csatlakozás az Azure Blob Storage-fiók, az alábbi lépéseket követve:  

1. Logikai alkalmazás létrehozása. A Logic Apps-tervezőben adja hozzá egy eseményindító, és adja hozzá az műveletet. Válassza ki **megjelenítése Microsoft felügyelt API-k** a legördülő listában, majd írja be a "blob" be a keresőmezőbe. A műveletek közül választhat:  
   
    ![Az Azure Blob Storage kapcsolat létrehozását lépést](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. Ha még nem korábban hozott létre az Azure storage fennálló kapcsolatokat, kéri a kapcsolat adatai:   
   
    ![Az Azure Blob Storage kapcsolat létrehozását lépést](./media/connectors-create-api-azureblobstorage/connection-details.png)  
3. Adja meg a tárolási fiók adatait. Tulajdonságok csillaggal szükség.
   
   | Tulajdonság | Részletek |
   | --- | --- |
   | Kapcsolat neve * |Adjon meg egy tetszőleges nevet a kapcsolat. |
   | Az Azure Storage-fiók neve * |Adja meg a tárfiók nevét. A tároló tulajdonságainak az Azure-portálon jelenik meg a tárfiók nevét. |
   | Azure Storage-fiók hozzáférési kulcs * |Adja meg a tárfiók kulcsára. A tárelérési kulcsokat a tároló tulajdonságainak az Azure portálon jelennek meg. |
   
    Ezek a hitelesítő adatok segítségével engedélyezi a Logic Apps alkalmazást, és hozzáférhet az adatokhoz. 
4. Kattintson a **Létrehozás** gombra.
5. Figyelje meg, a kapcsolat létrejött. Most folytassa a Logic Apps alkalmazást más lépéseket: 
   
    ![Az Azure Blob Storage kapcsolat létrehozását lépést](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

