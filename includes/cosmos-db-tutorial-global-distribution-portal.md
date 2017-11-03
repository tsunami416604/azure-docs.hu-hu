
Azure Cosmos DB globális terjesztési az Azure-ban videó péntek Scott Hanselman és egyszerű mérnöki Manager Karthik Raman olvashat.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

Hogyan globális adatbázis-replikációval kapcsolatos további információk az Azure Cosmos Adatbázisba működik, a következő témakörben: [adatok globálisan Cosmos DB terjesztése](../articles/cosmos-db/distribute-data-globally.md).

## <a id="addregion"></a>Adja hozzá az Azure portál használatával globális adatbázis területek
Az Azure Cosmos DB érhető el az összes [Azure-régiók] [ azureregions] világszerte. Miután kijelölte az alapértelmezett konzisztencia szint adatbázis fiókjához, társíthatja egy vagy több régióban (attól függően, hogy a választott alapértelmezett konzisztencia szintre, valamint globális terjesztési kell).

1. Az a [Azure-portálon](https://portal.azure.com/), a bal oldali sávon kattintson **Azure Cosmos DB**.
2. Az a **Azure Cosmos DB** panelen válassza az adatbázis-fiók módosítása.
3. A fiók paneljén kattintson **replikálja az adatokat globális** a menüből.
4. Az a **replikálja az adatokat globális** panelen válassza ki a régiók hozzáadása vagy eltávolítása a térkép régiók kattintva, és kattintson **mentése**. A régiók hozzáadása egy költsége van, tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/cosmos-db/) vagy a [adatok globálisan Azure Cosmos DB terjesztése](../articles/cosmos-db/distribute-data-globally.md) cikkében találja.
   
    ![Kattintson a régiók hozzáadásához, vagy távolítsa el őket a térképen][1]
    
Miután hozzáadta a második terület a **kézi feladatátvételt** beállítás engedélyezve van a **replikálja az adatokat globális** a portálon. Ez a beállítás használatával a feladatátvételi folyamat tesztelhet, illetve az elsődleges írási régió módosítása. Miután hozzáadta a harmadik terület a **feladatátvételi prioritások** engedélyezve van a azonos panelen, hogy bármikor módosíthatja a feladatátvételi sorrendnek olvasása.  

### <a name="selecting-global-database-regions"></a>Globális adatbázis területek kiválasztása
Két vagy több régió konfigurálása két gyakori forgatókönyvei van:

1. Alacsony késésű hozzáférést kézbesíti a végfelhasználók számára, függetlenül attól, hol találhatók a világ minden táján adatok
2. Üzleti folytonossági és vészhelyreállítási (BCDR) regionális rugalmassági hozzáadása

Kis késleltetésű kézbesítéséhez legyenek a végfelhasználók számára, ajánlott, mind az alkalmazás központi telepítése és a régiókban, amelyek megfelelnek az Azure Cosmos-adatbázis hozzáadása hol találhatók az alkalmazás felhasználóinak.

A BCDR, javasoljuk, hogy a régió párok leírtak alapján régiók hozzáadása a [üzleti folytonossági és vészhelyreállítási helyreállítási (BCDR): Azure-régiókat párosítva] [ bcdr] cikk.

<!--

## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your Cosmos DB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **Azure Cosmos DB** blade, select the database account to modify.
2. In the account blade, click **Replicate data globally** from the menu.
3. In the **Replicate data globally** blade, click **Manual Failover** from the top bar.
    ![Change the write region under Azure Cosmos DB Account > Replicate data globally > Manual Failover][2]
4. Select a read region to become the new write region, click the checkbox to confirm triggering a failover, and click OK
    ![Change the write region by selecting a new region in list under Azure Cosmos DB Account > Replicate data globally > Manual Failover][3]

--->

<!--Image references-->
[1]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-add-region.png
[2]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-1.png
[3]: ./media/cosmos-db-tutorial-global-distribution-portal/azure-cosmos-db-manual-failover-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: ../articles/cosmos-db/consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
