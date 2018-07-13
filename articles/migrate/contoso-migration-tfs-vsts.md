---
title: Refaktorovat a Team Foundation Server központi telepítés a Visual Studio Team Services (VSTS) az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso refactors telepítse át a helyszíni TFS telepítéshez, a Visual Studio Team Services (VSTS) az Azure-ban.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: 05340c8504150ed568e0d5ce5c8250127e59bca0
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003237"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-visual-studio-team-services-vsts"></a>Contoso áttelepítési: Újrabontás a Team Foundation Server üzembe helyezés a Visual Studio Team Services (VSTS)

Ez a cikk bemutatja, hogyan Contoso vannak újrabontás azok helyszíni Team Foundation Server (TFS) központi telepítésének áttelepítése révén azt a Visual Studio Team Services (VSTS) az Azure-ban. A Contoso fejlesztői csapat használta TFS együttműködést és verziókövetési az elmúlt öt év során. Most azok szeretne áthelyezni egy felhőalapú megoldás fejlesztési és tesztelési munka, valamint a verziókövetés. Helyezze át a fejlesztési és üzemeltetési modell, és új natív felhőalapú alkalmazásokat hozhat létre szerepet játszik a vsts-ben.

Ez a dokumentum a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait a tizenegyedik. A sorozat tartalmazza a háttér-információkat, és a egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes Contoso áttelepítési forgatókönyvek ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Mérje fel az alkalmazás virtuális gépek a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Az alkalmazás webes virtuális gépet át [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az adatbázis használatával a [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás migrálása a felügyelt SQL-példányt. | Elérhető
[Cikk 5: Az Azure virtuális gépeken áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso azok SmartHotel áttelepítése Azure IaaS virtuális gépeket, a Site Recovery szolgáltatással.
[A cikk 6: Újratárolás az Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. A Site Recovery számára, hogy az alkalmazás virtuális gépeit és a egy SQL Server rendelkezésre állási csoportot az alkalmazás-adatbázis áttelepítése a Database Migration service használnak. | Elérhető
[7. cikk: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux alkalmazás az Azure IaaS virtuális gépek Azure Site Recovery használatával.
[A cikk 8: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan telepíti át a Contoso osTicket Linux-alkalmazás. A Site Recovery a virtuális gépek migrálása, és a MySQL Workbench használata áttelepítése az Azure MySQL Server-példány. | Elérhető
[9. cikk: Újrabontás az alkalmazások az Azure Web App és az Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure container-alapú webalkalmazásba, és az alkalmazás-adatbázis áttelepítése az Azure SQL-kiszolgálóra. | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazást az Azure App Service és az Azure MySQL-kiszolgáló](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti az Azure App Service segítségével PHP 7.0 Docker-tárolót a osTicket Linux-alkalmazás. A központi telepítés kódbázis a Githubra áttelepítése. Az alkalmazás-adatbázis áttelepítése az Azure MySQL-hez. | Elérhető
11. cikk: Újrabontás a TFS üzembe helyezés a vsts-ben | A fejlesztői TFS alkalmazás migrálása a vsts-ben az Azure-ban | Ez a cikk
[A cikk 12: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[Cikk 13: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető


## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik a jövőbeli célok azonosításához üzleti. Üzleti partnerek nem túlságosan érintett, a fejlesztői eszközöket és technológiákat, de az ezeken a pontokon rögzített:

- **Szoftver**: az alapvető üzleti függetlenül minden cégek immár szoftvervállalat, beleértve a Contoso. Üzleti vezetői hogyan van érdekelné informatikai segíthet a vállalat vezethet új munkamódszereket felhasználók és ügyfelek számára, akik élményt.
- **Hatékonyság**: Contoso cégnek szüksége van a folyamat hatékonyabbá, és távolítsa el a felesleges eljárások a fejlesztők és a felhasználók számára. Ez lehetővé teszi számukra, hogy az ügyfelek igényei hatékonyabban. Az üzleti igényeknek megfelelő IT a gyors, pazarolni arra időt és pénzt nélkül.
- **A gyorsaság**: Contoso IT kell reagálni az üzleti igények, és a Marketplace-en engedélyezése sikeres versenyképes gyorsabban reagálhatnak. Informatikai nem lehet a vállalat egy blokkoló.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat rendelkezik rögzített le a vsts-ben az áttelepítés célok:

- Szükségük van egy olyan eszközt, az adatok migrálása a felhőbe. Néhány manuális folyamatokat, érdemes lehet szükség.
- Munkaelem-adatokat, és az elmúlt év előzmények kell áttelepíteni.
- Nem szeretnének új felhasználónevek és jelszavak beállítása. Az összes jelenlegi rendszer-hozzárendelések fenn kell tartani.
- Git a verziókövetéshez távolabbi Team Foundation verzió Control (TFVC) áthelyezése szeretnének.
- Az átállás a Git "Tipp áttelepítés", amely csak a legújabb verziót a forráskód importálja lesz. Ha az összes munkahelyi fog le, mint a kódbázis műszakok, fog történni a leállások során. Ezek ismerje meg, hogy csak az aktuális "master" ága előzmények elérhető lesz az áthelyezés után.
- Ezek aggódik a módosítást, és egy teljes lépés végrehajtása előtt tesztelésre. Szeretné TFS a vsts-ben az áthelyezés után is hozzáférjen.
- Több gyűjteményei, és szeretné a kezdéshez válasszon egyet, amely segít jobban megérteni, a folyamat csak néhány projektek rendelkezik.
- Ezek megismeréséhez, hogy TFS-gyűjteményt a VSTS-fiókok esetében egy-az-egyhez kapcsolat így több URL-cím rendelkeznek. Azonban ez megegyezik az aktuális modell elkülönítésének kódbázissal és projektek.


## <a name="proposed-architecture"></a>Javasolt architektúra

- Contoso a felhőbe helyezheti át a TFS-projektekhez, és már nem üzemeltetésére, projektek vagy a forrás vezérlő helyszíni.
- TFS áttelepíthetők a vsts-ben.
- Jelenleg a Contoso nevű egy TFS-gyűjtemény rendelkezik **ContosoDev**, amely nevű VSTS-fiók migrálása **contosodevmigration.visualstudio.com**.
- A projektek, a munkaelemek, a hibák és a tavalyi évhez képest az ismétlések átkerülnek a vsts-ben.
- Contoso használja az Azure Active Directory, amely akkor állítsa be, amikor azok [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md) , az áttelepítés megtervezése elején. 


![Forgatókönyv-architektúra](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Áttelepítési folyamat

Contoso befejezi az áttelepítési folyamat a következő:

1. Nincs érintett elkészítése rengeteg. Első lépésként Contoso cégnek szüksége van, a TFS-implementáció frissítése egy támogatott szintre. TFS 2017 Update 3 jelenleg futnak, de használandó adatbázis-migrálás támogatott 2018-as verziója fut a legújabb frissítésekkel kell.
2. A frissítés után azok fogja futtatni a TFS-áttelepítési eszköz, és ellenőrizze a gyűjtemény.
3. Azok fog hozhat létre egy készletét adatelőkészítési fájlt, és a egy áttelepítési tesztfuttatás végrehajtani a tesztelés.
4. Majd kiszámíthatja, egy másik áttelepítési, ezúttal a teljes migrálás, amely tartalmazza a munkaelemeket, hibák elhárítása, sprintekben, kód.
5. Az áttelepítés után a akkor kell áthelyezni kódját TFVC a Git.

![Áttelepítési folyamat](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható az Azure hogyan hajtsa végre az áttelepítést:

> [!div class="checklist"]
> * **1. lépés: Hozzon létre egy Azure storage-fiók**: ezt a tárfiókot fogja használni az áttelepítési folyamat során.
> * **2. lépés: Frissítés TFS**: TFS 2018-as frissítés 2 lesz frissítik azokat üzembe helyezésükben. 
> * **3. lépés: Ellenőrizze a gyűjtemény**: migrálásra való felkészüléskor a TFS-gyűjtemény, fogja érvényesíteni.
> * **4. lépés: Build adatelőkészítési fájlt**: fog létrehozni, akkor az áttelepítési fájlokat, a TFS-áttelepítési eszköz használata. 


## <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása

1. Az Azure Portalon, a Contoso tárfiókot hoz létre (**contosodevmigration**).
2. A másodlagos régió használata a feladatátvételhez – USA középső RÉGIÓJA, helyezze a fiókot. Helyileg redundáns tárolással egy általános célú standard szintű fiók használata.

    ![Tárfiók](./media/contoso-migration-tfs-vsts/storage1.png) 


**További segítségre van szüksége?**

- [Az Azure storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>2. lépés: Frissítés TFS

Contoso TFS 2018. 2. frissítés frissíti a TFS-kiszolgálónak. Mielőtt elkezdené:

- Letöltik [TFS 2018. 2. frissítés](https://visualstudio.microsoft.com/downloads/)
- Ellenőrizze, hogy a [hardverkövetelmények](https://docs.microsoft.com/tfs/server/requirements), és olvasási keresztül a [kibocsátási megjegyzések](https://docs.microsoft.com/visualstudio/releasenotes/tfs2018-relnotes) és [frissítése buktatóinak elkerülése](https://docs.microsoft.com/tfs/server/upgrade/get-started#before-you-upgrade-to-tfs-2018).

Frissítés módja:

1. Indításához, a TFS-kiszolgáló (a VMware virtuális gépeken futó) biztonsági mentése és VMware pillanatképet.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade1.png) 

2. A TFS-telepítő elindul, és kiválasztják a telepítési helyet. A telepítőnek internetkapcsolatra van szüksége.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade2.png) 

3. A telepítés befejezése után elindítja a kiszolgálót konfiguráló varázslót.

    ![TFS](./media/contoso-migration-tfs-vsts/upgrade3.png) 

4. Ellenőrzés után a varázsló befejezi a frissítés.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade4.png) 

5. Contoso ellenőrzi a TFS-telepítési projektek, a munkaelemek és a kód vizsgálatával.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Egyes TFS frissítéseket kell futtatnia a szolgáltatások konfigurálása varázsló a frissítés befejezése után. [További információk](https://docs.microsoft.com/vsts/work/customize/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**További segítségre van szüksége?**

Ismerje meg [TFS frissítése](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>3. lépés: Ellenőrizze a TFS-gyűjtemény

Contoso futtatja a TFS-áttelepítési eszköz az áttelepítés előtt érvényesítéséhez ContosoDev gyűjtemény adatbázison.

1. A Contoso letölti és unzips a [TFS áttelepítési eszköz](https://www.microsoft.com/download/details.aspx?id=54274). Fontos, hogy fut-e a TFS-frissítés letöltéséhez. A verzió a felügyeleti konzolon ellenőrizhető.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. Az eszköz a csapatprojekt-gyűjtemény URL-Címének megadásával az érvényesítés végrehajtásához futnak:

        **TfsMigrator validate /collection:http://contosotfs:8080/tfs/ContosoDev**


3. Az eszköz akkor jelez hibát.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Ezek a fájlok találhatók, a napló található a **naplók** mappába, az eszköz helye előtt. A naplófájl minden jelentősebb érvényesítéshez jön létre. **TfsMigration.log** tartalmazza a fő adatait.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Contoso bejegyzéshez, identitáshoz kapcsolódó keresi meg.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Contoso fut **TfsMigration ellenőrzése/help** a parancssorból, és azt látja, hogy a parancs **/tenantDomainName** úgy tűnik, hogy az identitás igazolásához szükséges.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Futtassa újra a érvényesítési parancsot, és tartalmazzák ezt az értéket, és az Azure AD nevük: **TfsMigrator /collection ellenőrzése:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Egy Azure AD bejelentkezési képernyő jelenik meg, és a egy globális rendszergazdai felhasználó hitelesítő adatainak megadása.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Az érvényesítés továbbítja, és megerősítik az eszköz által.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>4. lépés: Az áttelepítési fájlok létrehozása

Az érvényesítés befejeződött, a Contoso hozhat létre az áttelepítési fájlokat használhatja a TFS-áttelepítési eszköz.

1. Az előkészítési lépés, futtassa az eszközt.

    **TfsMigrator előkészítése /collection:http://contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Előkészítés](./media/contoso-migration-tfs-vsts/prep1.png)

    Készítse elő a következőket:
    - Az identitás térkép napló tölti fel a gyűjteményt, amelyben az összes olyan felhasználó keresése vizsgálatok (**IdentityMapLog.csv**])
    - Előkészíti a kapcsolatot az Azure Active Directoryhoz való minden egyes identitás.
    - Contoso már üzembe helyezett Azure ad-ben és szinkronizálása az AD Connect használatával, így előkészítése keresse meg a megfelelő identitásokat és aktív megjelölni képesnek kell lennie.

2. Az Azure AD bejelentkezési képernyő jelenik meg, és a Contoso megadja a hitelesítő adatokat egy globális rendszergazda

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep2.png)

3. Előkészítése befejeződött, és az eszköz jelenti, hogy az importálás fájlok sikeresen létrejöttek-e.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep3.png)

4. Contoso ekkor láthatja, hogy a IdentityMapLog.csv, mind a import.json fájl jött létre egy új mappát.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep4.png)

5. A import.json fájl importálási beállításokat biztosít. Tartalmaz adatokat, például a kívánt fiók nevét és a storage-fiók adatait. A mezők a rendszer automatikusan kitölti. Egyes mezők a felhasználói bevitel szükséges. Contoso megnyitja a fájlt, és hozzáadja a VSTS-fiók nevét a létrehozandó: **contosodevmigration**. Ezzel a névvel, saját VSTS URL-címe lesz **contosodevmigration.visualstudio.com**.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > A fióknak léteznie kell az áttelepítés előtt, a migrálás befejezése után is módosítható.

6. Tekintse át az identitás térkép naplófájl jeleníti meg a fiókok, az importálás során kerül be a vsts-ben. 

    - Aktív identitások tekintse meg, hogy az importálás után válik a felhasználók a vsts-ben identitások.
    - A vsts-en ezeket az identitásokat fogja licencbe venni, és az áttelepítés után a felhasználó a fiókban lévő megjelenjen.
    - Ezeket az identitásokat vannak megjelölve **aktív** a a **várt importálás állapota** oszlop a fájlban.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-vsts"></a>5. lépés: A vsts-ben áttelepítése

Az előkészítési helyen a Contoso most összpontosíthat a migrálás. Miután az áttelepítés, térnek fogja TFVC a Git használatával a verziókezeléshez.

Ahhoz, hogy, Contoso ütemezi a fejlesztői csapatot, a gyűjtemény offline áttelepítéshez az állásidő. Az áttelepítési folyamat lépései az alábbiak:

1. **A gyűjtemény leválasztása**: azonosító adatok gyűjtésére a TFS-kiszolgáló konfigurációs adatbázisában található, miközben a gyűjtése a csatlakoztatott és online állapotban. Ha egy gyűjteményt a TFS-kiszolgáló le van választva, a azonosító adatok másolatot tart, és csomagok azt a gyűjteményt a szállítási. Ezen adatok nélkül az importálás identitás részének nem hajtható végre. Javasoljuk, hogy a gyűjtemény felfüggeszti az leválasztani az importálás befejezéséig nem lehet importálni a módosításokat, amelyek az importálás során történt.
2. **Biztonsági másolat készítése**: az áttelepítési folyamat a következő lépés az, hogy hozza létre a biztonsági mentést, amely importálható a vsts-ben. Adatrétegbeli alkalmazás összetevő csomagok (DACPAC), az SQL Server egy funkciója, amely lehetővé teszi egyetlen fájlba csomagolva, és telepíteni kell a többi példány az SQL database módosításait. Közvetlenül a vsts-ben is visszaállíthatók, és ezért szolgál a csomagolási módszer a gyűjtemény adatait a felhőbe. Contoso az SqlPackage.exe eszköz segítségével az adatrétegbeli ALKALMAZÁSCSOMAG létrehozásához. Ez az eszköz szerepel az SQL Server Data Tools.
3. **Feltöltése a storage-**: után DACPAC jön létre, feltöltése az Azure Storage. Miután a feltöltés, kapnak egy közös hozzáférésű jogosultságkód (SAS), a TFS Migrálási eszköz a tároló hozzáférésének engedélyezéséhez.
4. **Töltse ki az importálás**: Contoso majd is az importfájl, beleértve az adatrétegbeli ALKALMAZÁSCSOMAGOT beállítás hiányzó mezők kitöltéséhez. A következővel kell kezdődnie, adja meg, hogy szeretné-e ehhez a **tesztfuttatási hiba** importálása, ellenőrizze, hogy minden megfelelően működik a teljes áttelepítés előtt.
5. **Hajtsa végre a tesztfuttatás**: tesztfuttatás importálja a gyűjtemény áttelepítési tesztelése érdekében. Száraz futtatások életre korlátozott, és törlődnek, mielőtt egy éles áttelepítést futtat. Ezek Ön automatikusan után törli egy megadott ideig. A sikeres műveletet jelző e-mail érkezett az importálás befejezése után a tesztfuttatás törlésekor kapcsolatos megjegyzés tartalmazza. Jegyezze fel, és ennek megfelelően tervezze meg.
6. **Az éles áttelepítésének befejezéséhez**: a tesztfuttatás migrálása kész, a Contoso elvégzi az elvégzendő végső áttelepítési a import.json frissítése, majd futtassa újra a importálása.



### <a name="detach-the-collection"></a>A gyűjtemény leválasztása

Mielőtt hozzákezdene, Contoso vesz igénybe egy helyi SQL Server biztonsági másolat, és a TFS-kiszolgáló a VMware-pillanatkép leválasztása előtt.

1.  A TFS-rendszergazdai konzolján, a leválasztani kívánt gyűjtemény kiválasztása (**ContosoDev**).

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate1.png)

2. A **általános**, kiválasztják **gyűjtemény leválasztása**

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate2.png)

3. A projekt leválasztása csapat gyűjtemény varázslóban > **karbantartási üzenet**, adja meg egy üzenetet, a felhasználók számára, akik próbálnak kapcsolódni a projektek a gyűjteményben.

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate3.png)

4. A **leválasztása folyamatban**, azok előrehaladásának figyeléséhez, és kattintson a **tovább** amikor a folyamat befejeződése után.

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate4.png)

5. A **készültség-ellenőrzés**, amikor ellenőrzi azok befejezéséhez kattintson a **leválasztási**.

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate5.png)

6. Kattintanak **Bezárás** befejeződik.

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate6.png)

6. A gyűjtemény már nem hivatkoznak a TFS-rendszergazdai konzol.

    ![Migrate (Áttelepítés)](./media/contoso-migration-tfs-vsts/migrate7.png)


### <a name="generate-a-dacpac"></a>Hozzon létre egy adatrétegbeli ALKALMAZÁSCSOMAG

Contoso (DACPAC) biztonsági másolatot készít a vsts-ben történő importáláshoz.

- Az SQL Server Data Tools SqlPackage.exe az adatrétegbeli ALKALMAZÁSCSOMAGOT létrehozására szolgál. Nincsenek telepítve az SQL Server Data Tools, található mappák nevei, mint 120 130 és 140 az SqlPackage.exe több verzióját. Fontos a megfelelő verzió használatához az adatrétegbeli ALKALMAZÁSCSOMAG előkészítése.
- Importálja a TFS 2018 kell SqlPackage.exe használata a 140 mappából vagy újabb verziója.  Ez a fájl CONTOSOTFS, a mappában található: **C:\Program Files (x86) \Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso az adatrétegbeli ALKALMAZÁSCSOMAGOT a következőképpen hoz létre:

1. Nyisson meg egy parancssort, és keresse meg az SQLPackage.exe helyet. Írja be a következő parancsot az adatrétegbeli ALKALMAZÁSCSOMAG:

    **SqlPackage.exe /sourceconnectionstring: "adatforrás SQLSERVERNAME\INSTANCENAME; = Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: I gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = memória** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. A következő üzenet jelenik meg, a parancs futtatása után.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Ezek a cégek ellenőrzik a DACPACfile tulajdonságai

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Frissítse a fájlt a tárolóba

Az ALKALMAZÁSCSOMAG létrehozása után Contoso feltölti az Azure Storage.

1. Ezek [töltse le és telepítse](https://azure.microsoft.com/features/storage-explorer/) Azure Storage Explorerben.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup5.png)

4. Csatlakozzon az előfizetéséhez, és a keresse meg a storage-fiókot létrehozza őket az áttelepítés (**contosodevmigration**). Akkor hozzon létre egy új blobtárolót, **vstsmigration**.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup6.png)

5. Ezek a adatrétegbeli ALKALMAZÁSCSOMAG-fájl feltöltéshez blokkblobként adja meg.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup7.png)
    
7. A fájl feltöltése után a fájlnév kattintanak > **SAS létrehozása**. Bontsa ki a storage-fiókban a blob-tárolók, válassza ki a tárolót az importálás fájlok, és kattintson **közös hozzáférési jogosultságkód igénylése**.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup8.png)

8. Elfogadhatja az alapértelmezett beállításokat, majd a kattintson **létrehozás**. Ez lehetővé teszi hozzáférési 24 órán keresztül.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup9.png)

9. A megosztott hozzáférési Jogosultságkód URL-t, akkor másolja, hogy a TFS-áttelepítési eszköz használható.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup10.png)

> [!NOTE]
> Az áttelepítés előtt ablakban vagy engedélyek lejár az előírt időn belül meg kell történnie.
> Ne készítsen egy SAS-kulcsot az Azure Portalról. Ehhez hasonló generált kulcsok fiók hatókörű, és az importálás nem fog működni.

### <a name="fill-in-the-import-settings"></a>Adja meg a beállítások importálása

Contoso korábban részlegesen tölti ki a specifikáció fájl (import.json). Most azokat hozzá kell adnia a többi beállítást.

Nyissa meg a import.json fájlt, és töltse ki az alábbi mezőket: • helye: a fent létrehozott SAS-kulcs helyét.
• Adatrétegbeli alkalmazáscsomagot: állítsa a nevet az adatrétegbeli ALKALMAZÁSCSOMAGOT feltöltött fájlra, a storage-fiókba. Például a ".dacpac" kiterjesztést.
• ImportType: most tesztfuttatási hiba beállítása.


![Importálási beállítások](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Egy tesztfuttatás áttelepítés

Contoso első tesztfuttatás áttelepítést, győződjön meg arról, hogy minden a várt módon működik.

1. Nyisson meg egy parancssort, és keresse meg a TfsMigration helyre (C:\TFSMigrator).
2. Első lépésként, ellenőrizze a fájl. Szeretnék, hogy a fájl megfelelően van formázva, és az SAS-kulcs működik.

    **TfsMigrator /importFile:C:\TFSMigrator\import.json /validateonly importálása**

3. Az érvényesítési hibát, amely a SAS-kulcsot kell hosszabb lejárati idővel adja vissza.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test1.png)

3. Azok az Azure Storage Explorer használatával hozzon létre egy új SAS-kulcsot a hét nap elteltével.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test2.png)

3. Ezek a import.json fájl frissítése, és futtassa újra az ellenőrzést. Ekkor fejeződik be sikeresen.

    **TfsMigrator /importFile:C:\TFSMigrator\import.json /validateonly importálása**

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test3.png)
    
7. A tesztfuttatás indítása:

    **Importálás /importFile:C:\TFSMigrator\import.json TfsMigrator**

8. Egy üzenet jelenik meg a migrálás megerősítéséhez. Vegye figyelembe a tesztfuttatás fenntartja az előkészített adatokat, amelynek időtartama.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test4.png)

9. Az Azure AD bejelentkezési jelenik meg, és a Contoso-rendszergazdai bejelentkezési befejezése kell lennie.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test5.png)

10. Egy üzenet az importálás vonatkozó információkat tartalmazza.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test6.png)

11. Elteltével 15 perc a Contoso az URL-címre lép, és megkeresheti a következő információkat:

     ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test7.png)

12. Miután az áttelepítés befejezése egy Contoso fejlesztési érdeklődők jelentkezik be a vsts-ben, ellenőrizze, hogy a tesztfuttatás megfelelően működött. A hitelesítés után vsts-ben a fiók megerősítéséhez néhány adatra van szüksége.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test8.png)

13. A vsts-ben a fejlesztési vezető láthatja, hogy áttelepítette-e a projektek a vsts-ben. Nincs egy üzenetet arról, hogy a fiók törlődik a 15 nap.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test9.png)

14. A fejlesztési vezető megnyitja a projektek egyikére, és megnyitja **munkaelemek** > **nekem kiosztott**. Ez azt mutatja, hogy munkahelyi elem adatok migrálása befejeződött, identitására együtt.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test10.png)

15. Azt is ellenőrzi, egyéb projektet és a kódot, győződjön meg arról, hogy a Forráskód és a korábbi lett áttelepítve.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Az éles migrálás futtatása

A tesztfuttatás befejeződött, a Contoso folytassa az éles áttelepítés. Ezek a tesztfuttatás törlése, frissíteni az importálási beállításokat, majd futtassa újra a importálása.

1. A VSTS portál, a tesztfuttatás fiók törlése.
2. A import.json fájl segítségével frissítse a **ImportType** való **ProductionRun**.

    ![Production](./media/contoso-migration-tfs-vsts/full1.png)

3. Az áttelepítés indítása, akárcsak a tesztfuttatás a: **TfsMigrator importálás /importFile:C:\TFSMigrator\import.json**.
4. Egy üzenetet jeleníti meg a migrálás jóváhagyásához, és figyelmezteti, hogy adatokat sikerült fenntartani egy átmeneti területre egy biztonságos helyen legfeljebb 7 napig.

    ![Production](./media/contoso-migration-tfs-vsts/full2.png)

5. Az Azure AD bejelentkezés a Contoso egy Contoso-rendszergazdai bejelentkezési adja meg.

    ![Production](./media/contoso-migration-tfs-vsts/full3.png)

6. Egy üzenet az importálás vonatkozó információkat tartalmazza.

    ![Production](./media/contoso-migration-tfs-vsts/full4.png)

7. Körülbelül 15 perc elteltével Contoso jut el az URL-címet, és megtekinti a következő információkat:

    ![Production](./media/contoso-migration-tfs-vsts/full5.png)

8. Az áttelepítés végeztével egy Contoso fejlesztési érdeklődők jelentkezik be a VSTS, ellenőrizze, hogy az áttelepítés megfelelően működött. Bejelentkezés után he is látja, hogy áttelepítette-e a projekteket.

    ![Production](./media/contoso-migration-tfs-vsts/full6.png)

8. A fejlesztési vezető megnyitja a projektek egyikére, és megnyitja **munkaelemek** > **nekem kiosztott**. Ez azt mutatja, hogy munkahelyi elem adatok migrálása befejeződött, identitására együtt.

    ![Production](./media/contoso-migration-tfs-vsts/full7.png)

9. Phil ellenőrzi, hogy a más munkahelyi elem adatok megerősítését.

    ![Production](./media/contoso-migration-tfs-vsts/full8.png)

15. Azt is ellenőrzi, egyéb projektet és a kódot, győződjön meg arról, hogy a Forráskód és a korábbi lett áttelepítve.

    ![Production](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>A GIT verziókövetési válthatnak TFVC

Az áttelepítés befejeződött a Contoso biztosítani szeretné source code Management Git TFVC áthelyezéséhez. Importálási jelenleg a saját VSTS-fiók a forráskód Git-tárházak egyazon fiók szükséges.

1. A VSTS-portálon, akkor nyisson meg egy olyan a TFVC adattárakkal (**$/ PolicyConnect**), és tekintse át azt.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Kattintanak a **forrás** legördülő menü > **importálás**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. A **adatforrástípust** kiválasztják **TFVC**, és adja meg az elérési útját a tárházban. Ezek korábban úgy döntött, hogy nem az előzmények áttelepítéséhez.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Hogyan TFVC és a Git tárolhatók verzió vezérlő eltérései miatt javasoljuk, hogy nem az, hogy az előzmények áttelepítéséhez. Ez a megközelítés, amely a Microsoft tartott, Windows és más termékekkel való migrálásakor központi verziókezeléshez a Git.

4. Az importálás után a Contoso áttekinti a kódot.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Ezek ismételje meg a folyamatot, a második tárház (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Után tekintse át a forrás, a fejlesztői érdeklődők egyetértenek abban, hogy az áttelepítés a vsts-ben történik. Vsts-ben mostantól részt vesz a migrálás csapatok belül minden fejlesztési forrása.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**További segítségre van szüksége?**

[További](https://docs.microsoft.com/vsts/git/import-from-tfvc?view=vsts) TFVC importálása.

##  <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött, a Contoso cégnek szüksége van, a következőket:

- Tekintse át a [utáni importálás](https://docs.microsoft.com/vsts/articles/migration-post-import?view=vsts) ismertető cikkben talál további importálási tevékenységek információt.
- Törölje a TFVC adattárakkal, vagy csak olvasható módban helyezze el őket. A kód bázisok nem használják, de azok előzmények lehet rá hivatkozni.

## <a name="next-steps"></a>További lépések

Contoso kell adnia a vsts-ben és a Git képzési megfelelő csapattagok számára.



