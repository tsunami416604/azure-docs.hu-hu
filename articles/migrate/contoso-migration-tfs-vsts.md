---
title: Refaktorovat a Team Foundation Server központi telepítést, az Azure DevOps-szolgáltatásokkal az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan Contoso refactors telepítse át a helyszíni TFS-telepítési, hogy az Azure-ban az Azure DevOps-szolgáltatásokkal.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 21396a10543d388b6ac360f426272f1841b2f510
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314110"
---
# <a name="contoso-migration--refactor-a-team-foundation-server-deployment-to-azure-devops-services"></a>Contoso áttelepítése:  Team Foundation Server üzemelő példányának újrabontása az Azure DevOps Services-re

Ez a cikk bemutatja, hogyan Contoso vannak újrabontás azok helyszíni Team Foundation Server (TFS) központi telepítésének áttelepítése révén, hogy az Azure-ban az Azure DevOps-szolgáltatásokkal. A Contoso fejlesztői csapat használta TFS együttműködést és verziókövetési az elmúlt öt év során. Most azok szeretne áthelyezni egy felhőalapú megoldás fejlesztési és tesztelési munka, valamint a verziókövetés. Helyezze át az Azure DevOps-modell, és új natív felhőalapú alkalmazásokat hozhat létre szerepet játszik a az Azure DevOps-szolgáltatásokkal.

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat tartalmazza a háttér-információkat, és a egy áttelepítési infrastruktúra beállítását, és futtassa a különböző típusú migrálások bemutató forgatókönyvek. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes Contoso áttelepítési forgatókönyvek ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Mérje fel az alkalmazás virtuális gépek a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Az Azure virtuális gépek és a egy felügyelt SQL-példány áthelyezési](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Az alkalmazás webes virtuális gépet át [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az adatbázis használatával a [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás migrálása a felügyelt SQL-példányt. | Elérhető
[5. cikk: Újratárolás az Azure virtuális gépek](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso azok SmartHotel áttelepítése Azure IaaS virtuális gépeket, a Site Recovery szolgáltatással.
[6. cikk: Újratárolás az Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. A Site Recovery számára, hogy az alkalmazás virtuális gépeit és a egy SQL Server rendelkezésre állási csoportot az alkalmazás-adatbázis áttelepítése a Database Migration service használnak. | Elérhető
[7. cikk: Egy Linux alkalmazás újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux alkalmazás az Azure IaaS virtuális gépek Azure Site Recovery használatával.
[8. cikk: Egy Linux alkalmazás újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan telepíti át a Contoso osTicket Linux-alkalmazás. A Site Recovery a virtuális gépek migrálása, és a MySQL Workbench használata áttelepítése az Azure MySQL Server-példány. | Elérhető
[9. cikk: Az alkalmazások az Azure Web App és az Azure SQL Database újrabontása](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure container-alapú webalkalmazásba, és az alkalmazás-adatbázis áttelepítése az Azure SQL-kiszolgálóra. | Elérhető
[10. cikk: Az Azure App Service és az Azure MySQL-kiszolgáló egy Linux-alkalmazás újrabontása](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti az Azure App Service segítségével PHP 7.0 Docker-tárolót a osTicket Linux-alkalmazás. A központi telepítés kódbázis a Githubra áttelepítése. Az alkalmazás-adatbázis áttelepítése az Azure MySQL-hez. | Elérhető
11. cikk: Refaktorovat a TFS üzembe helyezése az Azure DevOps-szolgáltatásokkal | A fejlesztői TFS alkalmazást át az Azure-ban az Azure DevOps-szolgáltatásokkal | Ez a cikk
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető


## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek jövőbeli célok azonosításához. Partnerek nem túlságosan érintett, a fejlesztői eszközöket és technológiákat, de az ezeken a pontokon rögzített:

- **Szoftver**: Az alapvető üzleti függetlenül minden cégek immár szoftvervállalat, beleértve a Contoso. Üzleti vezetői hogyan van érdekelné informatikai segíthet a vállalat vezethet új munkamódszereket felhasználók és ügyfelek számára, akik élményt.
- **Hatékonyság**: Contoso kell leegyszerűsíthető a folyamat, és távolítsa el a felesleges eljárások a fejlesztők és a felhasználók számára. Ez lehetővé teszi, hogy az ügyfelek igényei hatékonyabban a vállalat. Az üzleti igényeknek megfelelő IT a gyors, pazarolni arra időt és pénzt nélkül.
- **A gyorsaság**:  Contoso IT kell reagálni az üzleti igények, és a Marketplace-en engedélyezése sikeres versenyképes gyorsabban reagálhatnak. Informatikai nem lehet a vállalat egy blokkoló.

## <a name="migration-goals"></a>Migrálási célok

A Contoso felhőalapú csapat van rögzítve le az Azure DevOps-szolgáltatásokkal való migrálásának célok:

- A csapat által igényelt, egy olyan eszközt, az adatok migrálása a felhőbe. Néhány manuális folyamatokat, érdemes lehet szükség.
- Munkaelem-adatokat, és az elmúlt év előzmények kell áttelepíteni.
- Nem szeretnének új felhasználónevek és jelszavak beállítása. Az összes jelenlegi rendszer-hozzárendelések fenn kell tartani.
- Git a verziókövetéshez távolabbi Team Foundation verzió Control (TFVC) áthelyezése szeretnének.
- Az átállás a Git "Tipp áttelepítés", amely csak a legújabb verziót a forráskód importálja lesz. Ha az összes munkahelyi fog le, mint a kódbázis műszakok, fog történni a leállások során. Ezek ismerje meg, hogy csak az aktuális "master" ága előzmények elérhető lesz az áthelyezés után.
- Ezek aggódik a módosítást, és egy teljes lépés végrehajtása előtt tesztelésre. Szeretné TFS hozzáférjen az Azure DevOps-szolgáltatásokkal való áttérés után is.
- Több gyűjteményei, és szeretné a kezdéshez válasszon egyet, amely segít jobban megérteni, a folyamat csak néhány projektek rendelkezik.
- Ezek megismeréséhez, hogy TFS-gyűjteményt egy-az-egyhez kapcsolat az Azure DevOps-szolgáltatásokkal együtt, így több URL-cím rendelkeznek. Azonban ez megegyezik az aktuális modell elkülönítésének kódbázissal és projektek.


## <a name="proposed-architecture"></a>Javasolt architektúra

- Contoso a felhőbe helyezheti át a TFS-projektekhez, és már nem üzemeltetésére, projektek vagy a forrás vezérlő helyszíni.
- TFS átkerülnek az Azure DevOps-szolgáltatásokkal.
- Jelenleg a Contoso nevű egy TFS-gyűjtemény rendelkezik **ContosoDev**, amely egy Azure-fejlesztési és üzemeltetési szolgáltatások szervezet nevű migrálása **contosodevmigration.visualstudio.com**.
- A projektek, a munkaelemek, a hibák és a tavalyi évhez képest az ismétlések átkerülnek az Azure DevOps-szolgáltatásokkal.
- Contoso használja az Azure Active Directory, amely akkor állítsa be, amikor azok [üzembe helyezve az Azure-infrastruktúra](contoso-migration-infrastructure.md) , az áttelepítés megtervezése elején. 


![Forgatókönyv-architektúra](./media/contoso-migration-tfs-vsts/architecture.png) 


## <a name="migration-process"></a>Áttelepítési folyamat

Contoso befejezi az áttelepítési folyamat a következő:

1. Nincs érintett elkészítése rengeteg. Első lépésként Contoso cégnek szüksége van, a TFS-implementáció frissítése egy támogatott szintre. Contoso TFS 2017 Update 3 jelenleg fut, de használandó adatbázis-migrálás, kell támogatott 2018-as verziója fut a legújabb frissítésekkel.
2. A frissítés után a Contoso futtatja a TFS-áttelepítési eszköz, és ellenőrizze a gyűjtemény.
3. Contoso létrehozása előkészítés-fájlokat, és hajtsa végre a migrálás tesztfuttatás teszteléséhez.
4. Contoso fog futni egy másik áttelepítési, ezúttal, amely tartalmazza a hibák, munkatételek, a teljes migrálás sprintekben, kód.
5. Az áttelepítés után a Contoso át kódját a TFVC Git.

![Áttelepítési folyamat](./media/contoso-migration-tfs-vsts/migration-process.png) 


## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso az áttelepítés elvégzéséhez:

> [!div class="checklist"]
> * **1. lépés: Az Azure storage-fiók létrehozása**: Ezt a tárfiókot az áttelepítési folyamat során használható.
> * **2. lépés: Frissítse a TFS**: Contoso üzembe helyezésükben frissíti a TFS 2018-as frissítés 2. 
> * **3. lépés: Ellenőrizze a gyűjtemény**: Ellenőrzi, hogy a Contoso migrálásra való felkészüléskor a TFS-gyűjtemény.
> * **4. lépés: Adatelőkészítési fájlt hozhat létre**: Contoso hoz létre az áttelepítési fájlokat, a TFS-áttelepítési eszköz használata. 


## <a name="step-1-create-a-storage-account"></a>1. lépés: Tárfiók létrehozása

1. Az Azure Portalon, a Contoso-rendszergazdák a storage-fiók létrehozása (**contosodevmigration**).
2. A másodlagos régió használata a feladatátvételhez – USA középső RÉGIÓJA, helyezze a fiókot. Egy általános célú standard szintű fiók a helyileg redundáns tárolást használnak.

    ![Tárfiók](./media/contoso-migration-tfs-vsts/storage1.png) 


**További segítségre van szüksége?**

- [Az Azure storage bemutatása](https://docs.microsoft.com/azure/storage/common/storage-introduction).
- [Hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).


## <a name="step-2-upgrade-tfs"></a>2. lépés: TFS frissítése

Contoso rendszergazdák TFS 2018. 2. frissítés frissítse a TFS-kiszolgálónak. Mielőtt elkezdené:

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

5. A TFS-telepítési projektek, a munkaelemek és a kód vizsgálatával ellenőrizze azokat.

     ![TFS](./media/contoso-migration-tfs-vsts/upgrade5.png) 

> [!NOTE]
> Egyes TFS frissítéseket kell futtatnia a szolgáltatások konfigurálása varázsló a frissítés befejezése után. [További információk](https://docs.microsoft.com/azure/devops/reference/configure-features-after-upgrade?utm_source=ms&utm_medium=guide&utm_campaign=vstsdataimportguide&view=vsts).

**További segítségre van szüksége?**

Ismerje meg [TFS frissítése](https://docs.microsoft.com/tfs/server/upgrade/get-started).

## <a name="step-3-validate-the-tfs-collection"></a>3. lépés: A TFS-gyűjtemény ellenőrzése

Contoso-rendszergazdák a TFS-áttelepítési eszköz futtassa az áttelepítés előtt érvényesítéséhez ContosoDev gyűjtemény adatbázison.

1. Töltse le és csomagolja ki azokat a [TFS áttelepítési eszköz](https://www.microsoft.com/download/details.aspx?id=54274). Fontos, hogy fut-e a TFS-frissítés letöltéséhez. A verzió a felügyeleti konzolon ellenőrizhető.

    ![TFS](./media/contoso-migration-tfs-vsts/collection1.png)

2. A gyűjtemény URL-Címének megadásával az érvényesítés végrehajtásához az eszköz futtatása:

   **TfsMigrator /collection:http ellenőrzése:\//contosotfs:8080/tfs/ContosoDev**


3. Az eszköz akkor jelez hibát.

    ![TFS](./media/contoso-migration-tfs-vsts/collection2.png)

5. Ezek a fájlok találhatók, a napló található a **naplók** mappába, az eszköz helye előtt. A naplófájl minden jelentősebb érvényesítéshez jön létre. **TfsMigration.log** tartalmazza a fő adatait.

    ![TFS](./media/contoso-migration-tfs-vsts/collection3.png)

4. Ez a bejegyzés, identitáshoz kapcsolódó található azokat.

    ![TFS](./media/contoso-migration-tfs-vsts/collection4.png)

5. Azok futtatása **TfsMigration ellenőrzése/help** a parancssorból, és itt látható, hogy a parancs **/tenantDomainName** úgy tűnik, hogy az identitás igazolásához szükséges.

     ![TFS](./media/contoso-migration-tfs-vsts/collection5.png)

6. Futtassa újra a érvényesítési parancsot, és tartalmazzák ezt az értéket, az Azure AD nevük mellett: **TfsMigrator /collection:http ellenőrzése:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com**.

    ![TFS](./media/contoso-migration-tfs-vsts/collection7.png)

7. Egy Azure AD bejelentkezési képernyő jelenik meg, és a egy globális rendszergazdai felhasználó hitelesítő adatainak megadása.

     ![TFS](./media/contoso-migration-tfs-vsts/collection8.png)

8. Az érvényesítés továbbítja, és megerősítik az eszköz által.

    ![TFS](./media/contoso-migration-tfs-vsts/collection9.png)



## <a name="step-4-create-the-migration-files"></a>4. lépés: Az áttelepítési fájlok létrehozása

Az érvényesítés befejeződött, a Contoso is segítségével a rendszergazdák a TFS-áttelepítési eszköz hozhat létre az áttelepítési fájlokat.

1. Az előkészítési lépés, futtassa az eszközt.

    **TfsMigrator előkészítése /collection:http:\//contosotfs:8080/tfs/ContosoDev /tenantDomainName:contosomigration.onmicrosoft.com /accountRegion:cus**

     ![Előkészítés](./media/contoso-migration-tfs-vsts/prep1.png)

    Készítse elő a következőket:
    - Az identitás térkép napló tölti fel a gyűjteményt, amelyben az összes olyan felhasználó keresése vizsgálatok (**IdentityMapLog.csv**])
    - Előkészíti a kapcsolatot az Azure Active Directoryhoz való minden egyes identitás.
    - Contoso már üzembe helyezett Azure ad-ben és szinkronizálása az AD Connect használatával, így előkészítése keresse meg a megfelelő identitásokat és aktív megjelölni képesnek kell lennie.

2. Az Azure AD bejelentkezési képernyő jelenik meg, és ezek a globális rendszergazda hitelesítő adatok megadása

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep2.png)

3. Előkészítése befejeződött, és az eszköz jelenti, hogy az importálás fájlok sikeresen létrejöttek-e.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep3.png)

4. Most már láthatják, hogy a IdentityMapLog.csv, mind a import.json fájl jött létre egy új mappát.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep4.png)

5. A import.json fájl importálási beállításokat biztosít. Tartalmaz adatokat, például a kívánt szervezetnevet és storage-fiók adatait. A mezők a rendszer automatikusan kitölti. Egyes mezők a felhasználói bevitel szükséges. Contoso megnyitja a fájlt, és hozzáadja az Azure DevOps-szolgáltatásokkal szervezetnév hozhatók létre: **contosodevmigration**. Ezen a néven az Azure fejlesztési és üzemeltetési szolgáltatások URL-címe lesz **contosodevmigration.visualstudio.com**.

    ![Előkészítés](./media/contoso-migration-tfs-vsts/prep5.png)

    > [!NOTE]
    > A szervezet léteznie kell az áttelepítés előtt, a migrálás befejezése után is módosítható.

6. Tekintse át az identitás térkép naplófájl jeleníti meg a fiókok, az importálás során kerül az Azure DevOps-szolgáltatásokkal. 

   - Aktív identitások tekintse meg, hogy az importálás után válnak az Azure DevOps-szolgáltatásokkal a felhasználók identitások.
   - Az Azure DevOps-szolgáltatásokkal ezeket az identitásokat fogja licencbe venni, és az áttelepítés után a szervezet egy felhasználója szabályzatként jelenik meg.
   - Ezeket az identitásokat vannak megjelölve **aktív** a a **várt importálás állapota** oszlop a fájlban.

     ![Előkészítés](./media/contoso-migration-tfs-vsts/prep6.png)



## <a name="step-5-migrate-to-azure-devops-services"></a>5. lépés: Migrálás az Azure DevOps Servicesbe

Az előkészítési helyen a Contoso-rendszergazdák mostantól összpontosíthat az áttelepítés. Miután az áttelepítés, térnek fogja TFVC a Git használatával a verziókezeléshez.

Indítsa el, mielőtt a rendszergazdák ütemezése a fejlesztői csapatot, a gyűjtemény offline áttelepítéshez az állásidő. Az áttelepítési folyamat lépései az alábbiak:

1. **A gyűjtemény leválasztása**: Azonosító adatok gyűjtésére a TFS-kiszolgáló konfigurációs adatbázis található, amíg a gyűjtemény csatolt és online állapotban. Ha egy gyűjteményt a TFS-kiszolgáló le van választva, a azonosító adatok másolatot tart, és csomagok azt a gyűjteményt a szállítási. Ezen adatok nélkül az importálás identitás részének nem hajtható végre. Javasoljuk, hogy a gyűjtemény felfüggeszti az leválasztani az importálás befejezéséig nem lehet importálni a módosításokat, amelyek az importálás során történt.
2. **Biztonsági másolat készítése**: Az áttelepítési folyamat a következő lépés, hogy hozza létre a biztonsági mentést, hogy importálni lehessen az Azure DevOps-szolgáltatásokkal. Adatrétegbeli alkalmazás összetevő csomagok (DACPAC), az SQL Server egy funkciója, amely lehetővé teszi egyetlen fájlba csomagolva, és telepíteni kell a többi példány az SQL database módosításait. Közvetlenül az Azure DevOps-szolgáltatásokkal is visszaállíthatók, és ezért szolgál a csomagolási módszer a gyűjtemény adatait a felhőbe. Contoso az SqlPackage.exe eszköz segítségével az adatrétegbeli ALKALMAZÁSCSOMAG létrehozásához. Ez az eszköz szerepel az SQL Server Data Tools.
3. **Feltöltése a storage-**: Az ALKALMAZÁSCSOMAG létrehozása után, töltse fel az Azure Storage. Miután a feltöltés, kapnak egy közös hozzáférésű jogosultságkód (SAS), a TFS Migrálási eszköz a tároló hozzáférésének engedélyezéséhez.
4. **Töltse ki az importálás**: Contoso tudja majd töltse ki az importfájl, beleértve az adatrétegbeli ALKALMAZÁSCSOMAGOT beállítás hiányzó mezők. A következővel kell kezdődnie, adja meg, hogy szeretné-e ehhez a **tesztfuttatási hiba** importálása, ellenőrizze, hogy minden megfelelően működik a teljes áttelepítés előtt.
5. **Hajtsa végre a tesztfuttatás**: Futtatás száraz súgó teszt gyűjteményáttelepítés importálja. Száraz futtatások életre korlátozott, és törlődnek a termelési áttelepítés futtatása előtt. Ezek Ön automatikusan után törli egy megadott ideig. A sikeres műveletet jelző e-mail érkezett az importálás befejezése után a tesztfuttatás törlésekor kapcsolatos megjegyzés tartalmazza. Jegyezze fel, és ennek megfelelően tervezze meg.
6. **Az éles áttelepítésének befejezéséhez**: A tesztfuttatás migrálása kész, a Contoso-rendszergazdák a import.json frissítése, majd futtassa újra a importálás úgy teheti meg az elvégzendő végső áttelepítési.



### <a name="detach-the-collection"></a>A gyűjtemény leválasztása

Mielőtt hozzákezdene, Contoso rendszergazdák igénybe vehet egy helyi SQL Server biztonsági másolat, és a TFS-kiszolgáló a VMware-pillanatkép leválasztása előtt.

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

Contoso DevOps-szolgáltatásokkal az Azure-ba való importálásuk hoz létre egy biztonsági mentés (DACPAC).

- Az SQL Server Data Tools SqlPackage.exe az adatrétegbeli ALKALMAZÁSCSOMAGOT létrehozására szolgál. Nincsenek telepítve az SQL Server Data Tools, található mappák nevei, mint 120 130 és 140 az SqlPackage.exe több verzióját. Fontos a megfelelő verzió használatához az adatrétegbeli ALKALMAZÁSCSOMAG előkészítése.
- Importálja a TFS 2018 kell SqlPackage.exe használata a 140 mappából vagy újabb verziója.  Ez a fájl CONTOSOTFS, a mappában található: **C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\IDE\Extensions\Microsoft\SQLDB\DAC\140**.


Contoso-rendszergazdák a következő generálása az adatrétegbeli ALKALMAZÁSCSOMAG:

1. Nyisson meg egy parancssort, és keresse meg az SQLPackage.exe helyet. Írja be a következő parancsot az adatrétegbeli ALKALMAZÁSCSOMAG:

    **SqlPackage.exe /sourceconnectionstring: "adatforrás SQLSERVERNAME\INSTANCENAME; = Initial Catalog = Tfs_ContosoDev; Integrated Security = True" /targetFile:C:\TFSMigrator\Tfs_ContosoDev.dacpac /action:extract /p:ExtractAllTableData = true /p: I gnoreUserLoginMappings = true /p:IgnorePermissions = true /p:Storage = memória** 

    ![Backup](./media/contoso-migration-tfs-vsts/backup1.png)

2. A következő üzenet jelenik meg, a parancs futtatása után.

    ![Backup](./media/contoso-migration-tfs-vsts/backup2.png)

3. Ezek a cégek ellenőrzik az adatrétegbeli ALKALMAZÁSCSOMAG-fájl tulajdonságait

    ![Backup](./media/contoso-migration-tfs-vsts/backup3.png)

### <a name="update-the-file-to-storage"></a>Frissítse a fájlt a tárolóba

Az ALKALMAZÁSCSOMAG létrehozása után Contoso feltölti az Azure Storage.

1. Ezek [töltse le és telepítse](https://azure.microsoft.com/features/storage-explorer/) Azure Storage Explorerben.

    ![Feltöltés](./media/contoso-migration-tfs-vsts/backup5.png)

4. Csatlakozzon az előfizetéséhez, és a keresse meg a storage-fiókot létrehozza őket az áttelepítés (**contosodevmigration**). Akkor hozzon létre egy új blobtárolót, **azuredevopsmigration**.

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

Contoso-rendszergazdák korábban részlegesen töltötte ki specifikáció importfájl (import.json). Most azokat hozzá kell adnia a többi beállítást.

Nyissa meg a import.json fájlt, és töltse ki az alábbi mezőket: • helye: A fent létrehozott SAS-kulcs helyét.
• Adatrétegbeli alkalmazáscsomag: A storage-fiókhoz feltöltött adatrétegbeli ALKALMAZÁSCSOMAG-fájl nevének megadása Például a ".dacpac" kiterjesztést.
• ImportType: Most állítsuk be tesztfuttatási hiba.


![Importálási beállítások](./media/contoso-migration-tfs-vsts/import1.png)


### <a name="do-a-dry-run-migration"></a>Egy tesztfuttatás áttelepítés

Contoso rendszergazdák első tesztfuttatás áttelepítést, győződjön meg arról, hogy minden a várt módon működik.

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

11. Elteltével 15 perc azok tallózással keresse meg az URL-címet, és tekintse meg a következő információkat:

     ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test7.png)

12. Miután az áttelepítés befejezése egy Contoso fejlesztési érdeklődők jelentkezik be az Azure DevOps-szolgáltatásokkal, ellenőrizze, hogy a tesztfuttatás megfelelően működött. A hitelesítés után az Azure DevOps-szolgáltatásokkal, erősítse meg a szervezet néhány adatra van szüksége.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test8.png)

13. Az Azure DevOps-szolgáltatásokkal a fejlesztési vezető látható, hogy a projektek áttelepítette-e az Azure DevOps-szolgáltatásokkal. Nincs egy üzenetet arról, hogy a szervezet törli a 15 napon keresztül.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test9.png)

14. A fejlesztési vezető megnyitja a projektek egyikére, és megnyitja **munkaelemek** > **nekem kiosztott**. Ez azt mutatja, hogy munkahelyi elem adatok migrálása befejeződött, identitás együtt.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test10.png)

15. Az érdeklődő is ellenőrzi, egyéb projektet és a kódot, győződjön meg arról, hogy a Forráskód és a korábbi lett áttelepítve.

    ![Tesztfuttatás](./media/contoso-migration-tfs-vsts/test11.png)


### <a name="run-the-production-migration"></a>Az éles migrálás futtatása

A tesztfuttatás befejeződött, a Contoso rendszergazdák folytassa az éles áttelepítés. Ezek a tesztfuttatás törlése, frissíteni az importálási beállításokat, majd futtassa újra a importálása.

1. Az Azure DevOps-Services-portálon, törölje a tesztfuttatás szervezetet.
2. A import.json fájl segítségével frissítse a **ImportType** való **ProductionRun**.

    ![Production](./media/contoso-migration-tfs-vsts/full1.png)

3. Az áttelepítés indítása, akárcsak a tesztfuttatás számára: **TfsMigrator importálás /importFile:C:\TFSMigrator\import.json**.
4. Egy üzenetet jeleníti meg a migrálás jóváhagyásához, és figyelmezteti, hogy adatokat sikerült fenntartani egy átmeneti területre egy biztonságos helyen legfeljebb 7 napig.

    ![Production](./media/contoso-migration-tfs-vsts/full2.png)

5. Az Azure AD bejelentkezési akkor adja meg az egy Contoso-rendszergazdai bejelentkezési.

    ![Production](./media/contoso-migration-tfs-vsts/full3.png)

6. Egy üzenet az importálás vonatkozó információkat tartalmazza.

    ![Production](./media/contoso-migration-tfs-vsts/full4.png)

7. Körülbelül 15 perc elteltével, keresse meg az URL-címet, és megtekinti a következő információkat:

    ![Production](./media/contoso-migration-tfs-vsts/full5.png)

8. Az áttelepítés befejezését követően egy Contoso fejlesztési vezető bejelentkezik az Azure DevOps-szolgáltatásokkal, ellenőrizze, hogy az áttelepítés megfelelően működött. Bejelentkezés után azt látja, hogy áttelepítette-e a projekteket.

    ![Production](./media/contoso-migration-tfs-vsts/full6.png)

8. A fejlesztési vezető megnyitja a projektek egyikére, és megnyitja **munkaelemek** > **nekem kiosztott**. Ez azt mutatja, hogy munkahelyi elem adatok migrálása befejeződött, identitás együtt.

    ![Production](./media/contoso-migration-tfs-vsts/full7.png)

9. Az érdeklődő ellenőrzi, hogy más munkahelyi elem adatok megerősítéséhez.

    ![Production](./media/contoso-migration-tfs-vsts/full8.png)

15. Az érdeklődő is ellenőrzi, egyéb projektet és a kódot, győződjön meg arról, hogy a Forráskód és a korábbi lett áttelepítve.

    ![Production](./media/contoso-migration-tfs-vsts/full9.png)


### <a name="move-source-control-from-tfvc-to-git"></a>A GIT verziókövetési válthatnak TFVC

Az áttelepítés befejeződött a Contoso biztosítani szeretné source code Management Git TFVC áthelyezéséhez. Ezek a forráskód Git-tárházak ugyanazon a szervezeten belül, az Azure DevOps-szolgáltatásokkal szervezete jelenleg a importálnia kell.

1. Az Azure DevOps Services portálon megnyitja a TFVC adattárakkal egyik (**$/ PolicyConnect**), és tekintse át azt.

    ![Git](./media/contoso-migration-tfs-vsts/git1.png)

2. Kattintanak a **forrás** legördülő menü > **importálás**.

    ![Git](./media/contoso-migration-tfs-vsts/git2.png)

3. A **adatforrástípust** kiválasztják **TFVC**, és adja meg az elérési útját a tárházban. Ezek korábban úgy döntött, hogy nem az előzmények áttelepítéséhez.

    ![Git](./media/contoso-migration-tfs-vsts/git3.png)

    > [!NOTE]
    > Hogyan TFVC és a Git tárolhatók verzió vezérlő eltérései miatt azt javasoljuk, hogy a Contoso nem települnek át a korábbi. Ez a megközelítés, amely a Microsoft tartott, Windows és más termékekkel való migrálásakor központi verziókezeléshez a Git.

4. Az importálás után a rendszergazdák a kód áttekintése.

    ![Git](./media/contoso-migration-tfs-vsts/git4.png)

5. Ezek ismételje meg a folyamatot, a második tárház (**$/ SmartHotelContainer**).

    ![Git](./media/contoso-migration-tfs-vsts/git5.png)

6. Után tekintse át a forrás, a fejlesztői érdeklődők egyetértenek abban, hogy történt-e az áttelepítést az Azure DevOps-szolgáltatásokkal. Az Azure DevOps-szolgáltatásokkal most már részt vesz a migrálás csapatok belül minden fejlesztési a forrás lesz.

    ![Git](./media/contoso-migration-tfs-vsts/git6.png)



**További segítségre van szüksége?**

[További](https://docs.microsoft.com/azure/devops/repos/git/import-from-TFVC?view=vsts) TFVC importálása.

##  <a name="clean-up-after-migration"></a>Áttelepítés utáni karbantartás

Az áttelepítés befejeződött, a Contoso cégnek szüksége van, a következőket:

- Tekintse át a [utáni importálás](https://docs.microsoft.com/azure/devops/articles/migration-post-import?view=vsts) ismertető cikkben talál további importálási tevékenységek információt.
- Törölje a TFVC adattárakkal, vagy csak olvasható módban helyezze el őket. A kód bázisok nem használják, de azok előzmények lehet rá hivatkozni.

## <a name="next-steps"></a>További lépések

Contoso kell adnia az Azure DevOps-szolgáltatásokkal és a Git képzési megfelelő csapattagok számára.



