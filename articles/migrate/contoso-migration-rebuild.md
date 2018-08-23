---
title: Építse újra a Contoso helyszíni alkalmazást az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan ismét létrehozza a Contoso-alkalmazást az Azure App Services, a Kubernetes szolgáltatás, CosmosDB, Azure Functions és a Cognitive services használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 3d835a7bd93426e57c5ab204d277faca22ae0638
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2018
ms.locfileid: "42061371"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso áttelepítése: az Azure-bA a helyszíni alkalmazás újraépítése

Ez a cikk bemutatja, hogyan Contoso áttelepíti, és hogy SmartHotel az alkalmazás az Azure-ban újraépíti. Az Azure App Services Web apps, az alkalmazás előtérbeli virtuális gép át. Az alkalmazás háttérrendszere mikroszolgáltatások üzembe helyezett Azure Kubernetes Service (AKS) által felügyelt tárolók használatával lett összeállítva. A hely kisállat fénykép funkciókat biztosít az Azure Functions kommunikál. 

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a saját helyszíni erőforrásokat a Microsoft Azure felhőbe. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak, és idővel további cikkek adunk hozzá.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési egy alkalmazást az Azure virtuális gépeken és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA az SmartHotel alkalmazás. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át a SmartHotel app virtuális gépek csak a Site Recovery használatával. | Elérhető
[A cikk 6: Egy alkalmazás Újratárolása az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[A cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépeken és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure Web Apps és az Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t a vsts-ben](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a a Team Foundation Server (TFS) a helyszíni üzembe helyezés, migrálás, a Visual Studio Team Services (VSTS) az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
Cikk 13: Építse újra az alkalmazást az Azure-bA | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Ez a cikk.

Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel alkalmazás VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik az üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő. Differenciált élményt biztosít az ügyfelek számára, akik a saját webhelyek szeretnének.
- **A gyorsaság**: Contoso reagáljon gyorsabban a módosításokat a Marketplace-en, a versenyképes sikeres engedélyezése képesnek kell lennie. 
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai kell megadnia rendszerek, amelyek képesek a ugyanolyan ütemben nő.
- **Költségek**: a Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat le az áttelepítés alkalmazáskövetelmények van rögzítve. Ezeknek a követelményeknek leginkább megfelelő áttelepítési módszer meghatározásához használt:
 - Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma. Ez a kell jól, és könnyedén méretezheti a rendszert.
 - Az alkalmazás ne használja az IaaS-összetevőket. Minden kell kialakítani, PaaS, vagy a kiszolgáló nélküli szolgáltatások használatához.
 - Az app build futhat a cloud services, és a tárolókat a felhőben saját vállalati szintű tároló-beállításjegyzék kell tárolni.
 - Az API-szolgáltatás, amely kisállat fényképek kell lennie a pontos és megbízható a való világból, mivel a döntések az alkalmazás által a saját szállodák kell figyelembe. Bármely kisállat kapnak a hozzáférés engedélyezett a "Hotels" maradjon.

## <a name="solution-design"></a>Megoldásterv

Után rögzíthet a célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, amelyek az áttelepítés használatának mikéntjét.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-architecture"></a>Javasolt architektúra

- Az alkalmazás előtér webalkalmazásként Azure App Services, az elsődleges régióban telepíti.
- A kisállat fényképek feltöltése egy Azure-függvényt biztosít, és ez a funkció a webhely használni fog.
- A kisállat fénykép függvény a Cognitive Services Látástechnológia API és a cosmos DB modul.
- A webhely háttérrendszere mikroszolgáltatások használatával lett összeállítva. Ezek kezelése pedig az Azure Kubernetes service (AKS) tárolókat fog települni.
- Tárolók VSTS használatával jönnek létre, és az Azure Container Registry (ACR) leküldött.
- Most Contoso manuális telepítése a webes alkalmazás és a függvény kódját a Visual Studio használatával.
- Mikroszolgáltatások telepíti egy PowerShell-parancsfájl, amely meghívja a Kubernetes parancssori eszközök használatával.

    ![Forgatókönyv-architektúra](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Megoldás áttekintése
Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** | PaaS- és kiszolgáló nélküli megoldások teljes körű központi telepítésére vonatkozó használata jelentősen csökkenti a felügyeleti időt, amely a Contoso meg kell adnia.<br/><br/> Áthelyezése a mikroszolgáltatási architektúrát lehetővé teszi, hogy a Contoso, könnyedén kiterjesztheti a megoldást idővel.<br/><br/> Új funkciók online állapotba helyezhetők a már meglévő megoldások kódbázissal bármelyikét megszakítása nélkül.<br/><br/> A webalkalmazás több példányával, és nem rendszerkritikus meghibásodási pontot lesz konfigurálva.<br/><br/> Az automatikus skálázás engedélyezve lesz, hogy az alkalmazás válaszolhat a különböző forgalommal képes kezelni.<br/><br/> A PaaS-szolgáltatások helyezze át a Contoso kivonhatja az elavult megoldások Windows Server 2008 R2 operációs rendszeren.<br/><br/> Cosmos DB, amely nem igényel konfigurálást a Contoso beépített hibatűrő képességgel rendelkezik. Ez azt jelenti, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Tárolók összetettebbek, mint más áttelepítési lehetőségek. Gyorsan elsajátítható contoso problémát okozhatja.  Egy új szint által biztosított nagy mennyiségű értéket a görbe érték összetettségi vezetnek.<br/><br/> Az üzemeltetési csapat, contoso megértéséhez, és az alkalmazás támogatja az Azure, tárolók és mikroszolgáltatások felpörög kell.<br/><br/> Contoso még nem teljes körűen megvalósítva fejlesztési és üzemeltetési a teljes megoldás. Gondolja át, amely a központi telepítés az AKS, a functions és az App Services-szolgáltatások van szükségük.



### <a name="migration-process"></a>Áttelepítési folyamat

1. Contoso az ACR, az AKS és a cosmos DB üzembe helyezése.
2. Az üzembe helyezéshez, beleértve az Azure Web App, a storage-fiók, a függvény és az API az infrastruktúra kiépítése azokat. 
3. Után az infrastruktúra van beállítva, azok fog létrehozni a mikroszolgáltatások vsts-ben, amely leküldi azokat az ACR használatával a tárolórendszerképeket.
4. Contoso telepíti ezeket a mikroszolgáltatások kérje meg egy PowerShell-parancsfájl használatával.
5. Végül azok helyezünk üzembe az Azure-függvény és egy webalkalmazást.

    ![Áttelepítési folyamat](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure-szolgáltatások

**Szolgáltatás** | **Leírás** | **Költségek**
--- | --- | ---
[AZ AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Leegyszerűsíti a Kubernetes felügyeleti, üzembe helyezési és műveleteket. Egy teljes körűen felügyelt Kubernetes tárolószervező szolgáltatást biztosít.  | Az AKS szolgáltatás ingyenes.  Csak a virtuális gépeket, és a hozzájuk kapcsolódó tárterületért és a felhasznált hálózati erőforrásokért kell fizetnie. [További információk](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Gyorsítja egy eseményvezérelt, kiszolgáló nélküli számítási környezettel történő fejlesztést. Igény szerint skálázhatja.  | Fizetés csak a felhasznált erőforrásokat. Csomag / másodperc erőforrás-használat és a végrehajtás alapján számítjuk fel. [További információk](https://azure.microsoft.com/pricing/details/functions/).
[Az Azure Container Registrybe](https://azure.microsoft.com/services/container-registry/) | Tárolja a lemezképeket a tárolópéldányok összes típusára vonatkozóan. | A Funkciók, a storage és a használati időtartama alapján költsége. [További információk](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Gyorsan létrehozhat, üzembe helyezhet és méretezhet nagyvállalati szintű webes, mobil- és API-alkalmazásokat, amelyek bármilyen platformon képesek futni. | Az App Service-csomagok díjszabása másodpercalapú. [További információk](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Előfeltételek

Itt látható, milyen meg (és Contoso) kell futtatni az ebben a forgatókönyvben:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Kell már létrehozott egy előfizetést az értékelés az oktatóanyag-sorozatban az első cikkben végrehajtását. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.
**Fejlesztői Előfeltételek** | Contoso cégnek szüksége van a következő eszközök egy fejlesztői munkaállomáson:<br/><br/> - [A Visual Studio 2017 Community Edition: Verzió 15.5](https://www.visualstudio.com/)<br/><br/> .NET-munkaterhelés engedélyezve van.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [A docker CE (Windows 10 esetén) vagy a Docker VM (Windows Server) –](https://docs.docker.com/docker-for-windows/install/) be a Windows-tárolók használatára.



## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Üzembe AKS és ACR**: Contoso látja el a felügyelt AKS-fürt és a PowerShell-lel az Azure container registrybe
> * **2. lépés: A Docker-tárolók létrehozása**: folyamatos Integráció beállítása a VSTS használatával Docker-tárolókat, és azok leküldése az ACR-REL.
> * **3. lépés: A háttér-mikroszolgáltatások üzembe helyezéséhez**: azokat a többi fog javítható a háttér-mikroszolgáltatás-alapú infrastruktúra üzembe.
> * **4. lépés: Előtér-infrastruktúra üzembe helyezése**: azok az előtér-infrastruktúra, állatokért blob storage a kisállat-telefonokon, a Cosmos DB és a Vision API üzembe helyezése.
> * **5. lépés: A háttér áttelepítése**: mikroszolgáltatások üzembe helyezéséhez, és a futtatása az aks-en, a háttéralkalmazás áttelepíteni.
> * **6. lépés: Közzététel a kezelőfelület**: azok a SmartHotel alkalmazás közzététele az Azure App Service-ben, és a Függvényalkalmazást, amely a kisállat szolgáltatás fogja meghívni.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>1. lépés: Egy AKS-fürt és az ACR kiépítése

Contoso AKS és az Azure Container Registry használatával felügyelt Kubernetes-fürt létrehozása az üzembe helyezési parancsfájlok futtatására szolgál.

- Az utasításokat, ez a szakasz használata a **SmartHotel360 – Azure-háttérrendszer** tárház.
- A **SmartHotel360 – Azure-háttérrendszer** GitHub-adattárat tartalmazza az összes szoftver esetében ez az üzembe helyezés részeként.

Azok az üzembe helyezés végrehajtása:

1. Előtt indítsa el, a Contoso biztosítja, hogy az összes előfeltételként szükséges szoftver használatát a fejlesztési számítógépen telepítve. 
2. Ezek klónozza az adattárat a fejlesztői gép készítése a Git használatával helyileg.

    **git-klón https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso megnyitja azt a mappát a Visual Studio Code, és áthelyezi a **/üzembe helyezése/k8s** könyvtár, amely tartalmazza a parancsfájl **általános – aks-env.ps1**.
4. Ezek a parancsprogrammal hozzon létre a felügyelt Kubernetes-fürtöt, az AKS és a Container Registry használatával.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  Nyissa meg a fájl, a $location paraméter frissítése **eastus2**, és mentse a fájlt.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. Kattintanak **nézet** > **beépített terminál** az integrált terminálon megnyitásához a kódban.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. A PowerShell integrált terminálon bejelentkeznek az Azure-bA a Connect-AzureRmAccount paranccsal. [További](https://docs.microsoft.com/powershell/azure/get-started-azureps) PowerShell használatának első lépéseivel kapcsolatos.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. Azure CLI-vel hitelesítéshez futtassa a **az bejelentkezési** parancsot, és a következő utasításokat a webböngésző használatával történő hitelesítéshez. [További](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) készül az Azure CLI bejelentkezéskor.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. A következő parancsot, és átadja a ContosoRG, az AKS fürt smarthotel-aks-eus2 nevét és az új beállításjegyzék-nevet az erőforráscsoport neve futnak.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Az Azure létrehozza az AKS-fürt erőforrásainak tartalmazó egy másik erőforráscsoportot.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. Az üzembe helyezés befejezése után telepítse-e a Contoso a **kubectl** parancssori eszköz. Az eszköz már telepítve van az Azure cloud Shell.

    **az aks install-cli**

12. Futtassa a fürthöz való csatlakozás, ellenőrizze a **kubectl get csomópontok** parancsot. A csomópont a neve megegyezik a virtuális Gépet, az automatikusan létrehozott erőforráscsoportban.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. Ezek a következő parancsot a Kubernetes-irányítópult indításához: 

    **az aks browse--resource-group ContosoRG--name smarthotelakseus2**

14. Az irányítópult egy böngészőlapon nyílik meg. Ez a bújtatott kapcsolatot az Azure CLI használatával. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>2. lépés: A Docker-tároló létrehozása

### <a name="create-a-vsts-and-build"></a>Hozzon létre egy vsts-ben, és hozhat létre

Contoso létrehoz egy projektet, vsts-ben, és konfigurálja a CI hozhat létre a tároló létrehozásához, és majd leküldi azt az ACR-REL. Az utasításokat a jelen szakasz használata a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) tárház.

1. A visualstudio.com webhelyen, új fiók létrehozása (**contosodevops360.visualstudio.com**), és állítsa be úgy a Git használatával.

2. Akkor hozzon létre egy új projektet (**smarthotelrefactor**) készítése a Git használatával a verziókezeléshez és Agile-munkafolyamat.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. A GitHub-adattárat importálja azokat.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. A **készítése és kiadása**, azok a VSTS Git használatával, az importált adatforrásként új definíciójának létrehozása **smarthotel** tárház. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Egy üres folyamatot használ első lépésként válassza ki.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Kiválasztják **üzemeltetett Linux előzetes** a build-definíciójában.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. A **1. fázis**, adnak hozzá egy **Docker Compose** feladat. Ez a feladat buildek a Docker compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Ismételje meg a, és a adjon hozzá egy másik **Docker Compose** feladat. Ez egy leküldéses értesítések a tárolókat az ACR-be.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Válassza ki az első tevékenység (a hozhat létre), és a build konfigurálása az Azure-előfizetést, engedélyezési és az ACR-REL. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Az elérési útját kell adnia a **docket-compose.yaml** fájlban, az a **src** mappát a tárház. Szolgáltatás-lemezképeket, és a legújabb címke kiválasztásával. Mikor változik a művelet **szolgáltatás lemezképeket**, a VSTS-feladat neve megváltozik **automatikusan-szolgáltatások létrehozásához**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Most a Contoso a második Docker feladat (leküldés) konfigurálja. Akkor válassza ki az előfizetést és a **smarthotelacreus2** ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Újra, akkor adja meg a fájl a docker-compose.yaml fájlt, és válassza **szolgáltatás rendszerképeket** , és tartalmazzák a legutóbbi címkét. Mikor változik a művelet **szolgáltatás rendszerképeket**, a VSTS-feladat neve megváltozik **automatikusan leküldési szolgáltatásokat**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. A VSTS-feladatok konfigurálva a Contoso menti a builddefiníció és a létrehozási folyamat elindul.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. A build folyamatának ellenőrzéséhez feladat a kattintanak.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. A létrehozás befejezése után az ACR-REL az új adattárakkal, amely fel van töltve a tárolók, a mikroszolgáltatások által használt jeleníti meg.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>3. lépés: A háttér-mikroszolgáltatások üzembe helyezéséhez.

Az AKS-fürtöt létrehozni, és a Docker-rendszerképek build, a Contoso most már a többi fog javítható a háttér-mikroszolgáltatás-alapú infrastruktúra üzembe helyezi.

- A szakaszban használt utasítások a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) adattárat.
- Az a **/deploy/k8s/arm** mappában van az összes elem létrehozása egy parancsfájl. 

Telepítse a következő:

1. Contoso használja a Deploy.cmd fájl fájl üzembe helyezése az Azure-erőforrások ContosoRG erőforráscsoportban és régióban EUS2, írja be a következő parancsot:

    **. \deploy azuredeploy - c eastus2 ContosoRG**

    ![Háttérbeli üzembe helyezése](./media/contoso-migration-rebuild/backend1.png)

2. A kapcsolati karakterlánc az egyes adatbázisokra, hogy később rögzítése.

    ![Háttérbeli üzembe helyezése](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>4. lépés: Előtér-infrastruktúra üzembe helyezése

Contoso cégnek szüksége van, az infrastruktúra, amely az előtér-alkalmazás által használható. Ezek a kisállat rendszerképek; tárolására szolgáló blob storage-tároló létrehozása Cosmos-adatbázis, tárolására a kisállat információkkal; dokumentumok és a Vision API, a webhelyhez. 

Ehhez útmutatást szakasz használata a [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) tárház.

### <a name="create-storage-containers"></a>Storage-tárolók létrehozása

1.  Az Azure Portalon, Contoso nyitja meg a tárfiók létrejött, és rákattint **Blobok**.
2.  Akkor hozzon létre egy új tárolót (**Kisállatok**) tároló beállítása a nyilvános hozzáférési szinttel. Felhasználók saját kisállat fényképeket feltölteni ezt a tárolót.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob1.png)

3. Akkor hozzon létre egy második új tárolót **beállítások**. Egy fájl az előtér alkalmazásbeállításokat kerülnek ebben a tárolóban.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob2.png)

4. Rögzítése a hozzáféréshez szükséges adatokat a tárfiók egy szövegfájlba későbbi felhasználásra.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Cosmos-adatbázis kiépítése

Contoso kisállat információ használható egy Cosmos-adatbázis kiépítése.

1. Akkor hozzon létre egy **Azure Cosmos DB** az Azure piactéren.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Ezek adjon meg egy nevet (**contosomarthotel**), válassza ki az SQL API és az éles erőforráscsoport ContosoRG, a fő USA keleti RÉGIÓJA 2 régióban helyezze.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Új gyűjtemény az adatbázist, megnövelik a alapértelmezett és az átviteli sebesség.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Vegye figyelembe a későbbiekben az adatbázis kapcsolati adatait. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Számítógépes Látástechnológia üzembe helyezése

Contoso látja el a Computer Vision API. A függvény kiértékelése a felhasználók által feltöltött képek fogja meghívni az API-t.

1. Akkor hozzon létre egy **Computer Vision** példány az Azure piactéren.

     ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision1.png)

2. Ezek az API-t üzembe (**smarthotelpets**) az éles üzemű erőforrás csoportosítás ContosoRG, a fő USA keleti RÉGIÓJA 2 régióban.

    ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision2.png)

3. Az API-t a biztonságoskapcsolat-beállításainak mentése egy szövegfájlba későbbi felhasználás céljából.

     ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>5. lépés: A háttérszolgáltatásoknak az Azure-beli üzembe helyezése

Most Contoso kell üzembe helyeznie az NGINX engedélyezik a bejövő forgalmat a szolgáltatásokhoz, és majd a mikroszolgáltatások üzembe az AKS-fürt bejövőforgalom-vezérlőt.

Az utasításokat a jelen szakasz használata a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) adattárat.



1. Ezek a Visual Studio Code használatával frissítheti a **/deploy/k8s/config_loal.yml** fájlt. A korábban feljegyzett információkat a különböző adatbázis-kapcsolatok frissíteni azokat.

     ![Mikroszolgáltatások üzembe helyezéséhez](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Bizonyos konfigurációs beállításokat (például az Active Directory B2C-vel) Ez a cikk nem tárgyalja. Nincs további információ a tárházon belüli ezeket a beállításokat.

2. A deploy.ps1 fájlt törli az összes fürt tartalom (kivéve a bejövő és bejövőforgalom-vezérlőjéhez), és üzembe helyezi a mikroszolgáltatásokat. Futnak, a következő:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. A következő parancsot a szolgáltatások állapotának ellenőrzéséhez futnak:

    ```
    kubectl get services
    ```
4. Megnyitja a Kubernetes-irányítópult, tekintse át a központi telepítés:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>6. lépés: Az előtér közzététele

Utolsó lépésként a Contoso a SmartHotel alkalmazást közzéteszi a az Azure App Service, valamint a Függvényalkalmazás a Kisállat-szolgáltatás által meghívott.

Az utasításokat a jelen szakasz használata a [SmartHotel-public-web-tárházban.](https://github.com/Microsoft/SmartHotel360-public-web) tárház.

### <a name="set-up-web-app-to-use-contoso-resources"></a>A Contoso erőforrások webes alkalmazás beállítása

1. Contoso klónozza az adattárat a fejlesztői gép készítése a Git használatával helyileg:

    **git-klón https://github.com/Microsoft/SmartHotel360-public-web.git**

2. A Visual Studióban akkor nyissa meg a mappát az adattárban lévő összes fájl megjelenítéséhez.

    ![Előtérbeli közzététele](./media/contoso-migration-rebuild/front-publish1.png)

3. Azokat a konfigurációs módosítások az alapértelmezett beállítás szükséges.

    - A webes alkalmazás indulásakor megkeresi a **SettingsUrl** alkalmazásbeállítást.
    - A változónak tartalmaznia kell egy konfigurációs fájl URL-CÍMÉT.
    - Alapértelmezés szerint használt beállítás egy nyilvános végpontot.

4. Frissítse a **/config-sample.json/sample.json** fájlt. Ez a webes konfigurációs fájl használata esetén a nyilvános végponthoz.

    - Mindkét azok szerkesztése a **URL-címek** és **pets_config** , az értékeket az AKS API-végpontokat, a storage-fiókok és a Cosmos-adatbázis a szakaszokat. 
    - Az URL-címeket meg kell egyeznie az új webalkalmazást, amely létrehozza a Contoso DNS-nevét.
    - Ez a contoso, **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Előtérbeli közzététele](./media/contoso-migration-rebuild/front-publish2.png)

5. A fájl frissítése után, nevezze át, hogy **smarthotelsettingsurl**, és töltse fel a korábban létrehozott storage-blobba.

     ![Előtérbeli közzététele](./media/contoso-migration-rebuild/front-publish3.png)

6. A fájl az URL-Címének lekéréséhez kattintanak. Az URL-címet az alkalmazás által használt esetén megkezdi a kérje le a konfigurációs fájlban.

    ![Előtérbeli közzététele](./media/contoso-migration-rebuild/front-publish4.png)

7. Azok a frissítés **SettingsUrl** a a **appsettings. Production.JSON** fájlok URL-címét az új fájlt.

    ![Előtérbeli közzététele](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>A webhelyet üzembe helyezni az Azure App Service szolgáltatásban

Most a Contoso szervezet webhelyét teheti közzé.


1. A használatukkal az Application Insights-figyelést a Visual Studio 2017-ben. Ehhez válassza a **PublicWeb** projektre a Megoldáskezelőben, és keressen rá a **Application Insights hozzáadása**. Az Application Insights-példány, az infrastruktúra telepítésekor létrehozott, regisztrálja az alkalmazást.

    ![Webhely közzététele](./media/contoso-migration-rebuild/deploy-website1.png)

2. A Visual Studióban, az App Insights PublicWeb projekt csatlakozhat, és úgy van beállítva megjelenítése érdekében.
 
    ![Webhely közzététele](./media/contoso-migration-rebuild/deploy-website2.png)

3. A Visual Studióban, létrehozása és közzététele a webalkalmazás.

    ![Webhely közzététele](./media/contoso-migration-rebuild/deploy-website3.png)

5. Adja meg az alkalmazás nevét, és helyezze az éles erőforráscsoport **ContosoRG**, a fő USA keleti RÉGIÓJA 2 régióban.

    ![Webhely közzététele](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

1. A Visual Studio létrehozása és közzététele a funkció használata. Ehhez az szükséges, akkor kattintson a jobb gombbal a **PetCheckerFunction** > **közzététel**. Ezután, hozzon létre egy új App Service-funkció.

     ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function1.png)

2. A nevet kell adnia **smarthotelpetchecker**, és helyezze az ContosoRG erőforráscsoportot, és a egy új app service-csomagot.

     ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function2.png)

3. Válassza ki a storage-fiók, és létrehozni a függvényt.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function3.png)

4. Az üzembe helyezés az Azure-bA a függvényalkalmazás üzembe elindul. A **közzététel**, Contoso alkalmazás beállításait a storage, a Cosmos-adatbázis és a Computer Vision API adja.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function4.png)

5. A függvény helyi futtatásához először, ezek a beállítások frissítése **PetCheckerFunction/local.settings.json**.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function5.png)

6. A funkció üzembe helyezését követően megjelenik az Azure Portalon, az a **futó** állapotát.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function6.png)


7. Ezek ellenőrzéséhez, hogy a Kisállat-ellenőrző alkalmazás a várt módon működik-e az alkalmazás megkeresése tallózással [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. A kép feltöltése a profilkép kattintanak.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function7.png)

9. Az első fénykép szeretne ellenőrzése egy kis kutya van.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function8.png)

10. Az alkalmazás által megjelenített üzenetben az elfogadási.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso cégnek szüksége van, teljes mértékben üzembe helyezése, és tegye biztonságossá a új infrastruktúrára.

### <a name="security"></a>Biztonság

- Contoso kell arról, hogy az új adatbázisok biztonságos. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- SSL-tanúsítványokkal használandó frissíteni kell az alkalmazást. A tárolópéldány újratelepítése kell a 443-as választ.
- Akkor érdemes megfontolni KeyVault titkos kulcsok a Service Fabric-alkalmazások védelme érdekében. [További információk](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Akkor érdemes megfontolni, regionális feladatátvételt biztosít az adatbázishoz tartozó SQL feladatátvételi csoportok megvalósítását. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Az ACR prémium Termékváltozatokban georeplikációt használhatnak. [További információ](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján saját [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso építse újra a SmartHotel alkalmazást az Azure-ban. Ezek az újonnan létrehozott a helyszíni alkalmazás előtérbeli virtuális gép az Azure App Services Web Apps szolgáltatásban. Az app-háttérrendszert mikroszolgáltatások üzembe helyezett tárolók az Azure Kubernetes Service (AKS) által felügyelt, beépített. Azok a továbbfejlesztett alkalmazás működéséhez kisállat fénykép alkalmazás használatával.




