---
title: Építse újra a Contoso helyszíni alkalmazást az Azure-bA |} A Microsoft Docs
description: Ismerje meg, hogyan ismét létrehozza a Contoso-alkalmazást az Azure App Services, a Kubernetes szolgáltatás, CosmosDB, Azure Functions és a Cognitive services használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: raynew
ms.openlocfilehash: 58ea0859af42f7614e69d1693bbd9f8e3a17ccb8
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300545"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso áttelepítése: az Azure-bA a helyszíni alkalmazás újraépítése

Ez a cikk bemutatja, hogyan Contoso áttelepíti, és ismét létrehozza a SmartHotel360 alkalmazást az Azure-ban. Contoso az Azure App Services Web apps az alkalmazás előtérrendszerét virtuális Gépet áttelepíti. Az alkalmazás háttérrendszere mikroszolgáltatások üzembe helyezett Azure Kubernetes Service (AKS) által felügyelt tárolók használatával lett összeállítva. A hely kommunikál az Azure Functions kisállat fénykép funkciók biztosításához. 

Ez a dokumentum az egyik, a cikkeket, amelyek megmutatják, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait. A sorozat része a háttér-információkat és forgatókönyvek, amelyek a migrálás infrastruktúra beállításával, az áttelepítéshez a helyszíni erőforrások értékelése és áttelepítések különböző típusú futtató mutatják be. Forgatókönyvek egyre összetettebbé válnak. Idővel további cikkek adunk hozzá.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
[2. cikk: Egy Azure-infrastruktúra üzembe helyezése](contoso-migration-infrastructure.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md)  | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel360 alkalmazás értékelése. Contoso alkalmazás rendelkező virtuális gépek értékeli a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési egy alkalmazást az Azure virtuális gépeken és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso fut egy lift-and-shift-migrálás az Azure-bA a SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás-adatbázis SQL felügyelt példányra, használja a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető
[5. cikk: Áthelyezési egy alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át a SmartHotel360 app virtuális gépek csak a Site Recovery használatával. | Elérhető
[A cikk 6: Egy alkalmazás Újratárolása az Azure virtuális gépek és az SQL Server Always On rendelkezésre állási csoport](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel360 alkalmazást. Contoso számára, hogy az alkalmazás virtuális gépeit és a Database Migration service, az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használja. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure-beli virtuális gépeken](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso hajtja végre a Linux osTicket alkalmazás lift-and-shift áttelepítés Azure virtuális gépekre, a Site Recovery | Elérhető
[A cikk 8: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépeken és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure-beli virtuális gépek Site Recovery használatával, és az alkalmazás-adatbázis áttelepítése Azure MySQL Server-példány, a MySQL Workbench használatával. | Elérhető
[9. cikk: Újrabontás egy alkalmazást az Azure Web Apps és az Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Bemutatja, hogyan Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése az Azure SQL Server-példány | Elérhető
[10. cikk: Újrabontás egy Linux-alkalmazás Azure Web Apps és az Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux-osTicket alkalmazás Azure Web Apps több helyen, a folyamatos készregyártás a GitHub integrálva. Az alkalmazás-adatbázis nekik át egy Azure-beli MySQL-példányt. | Elérhető
[11. cikk: Újrabontás a TFS-t az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Bemutatja, hogyan telepíti át a Contoso a a Team Foundation Server (TFS) a helyszíni üzembe helyezés, migrálás, hogy az Azure-ban az Azure DevOps-szolgáltatásokkal. | Elérhető
[12. cikk: Azure-tárolók és az SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Bemutatja, hogyan Contoso áttelepíti, és rearchitects SmartHotel alkalmazás az Azure-bA. Az alkalmazás webes réteg egy Windows-tárolót, és a egy Azure SQL Database-ben az alkalmazás-adatbázis újratervezése azokat. | Elérhető
Cikk 13: Építse újra az alkalmazást az Azure-bA | Bemutatja, hogyan építse újra a Contoso SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az App Services, Azure-beli Kubernetes, az Azure Functions, a Cognitive services és a Cosmos DB használatával. | Ez a cikk.

Ez a cikk a Contoso áttelepíti a kétrétegű Windows. NET SmartHotel360 alkalmazás VMware virtuális gépeken futó Azure-bA. Ha szeretné használni ezt az alkalmazást, nyílt forráskódú nyújtja, és töltheti le a [GitHub](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>A stratégiai

Az informatikai vezetőségi szorosan együttműködik üzleti partnerek megértéséhez, amit szeretnének való áttérés eléréséhez:

- **Üzleti növekedés cím**: Contoso nő, és biztosíthatja a differenciált élményt az ügyfeleknek a Contoso webhelyeken szeretne, és.
- **A gyorsaság**: Contoso reagáljon gyorsabban a módosításokat a Marketplace-en, a versenyképes sikeres engedélyezése képesnek kell lennie. 
- **Méretezési csoport**: a vállalat növekedésével sikeres, a Contoso informatikai csapat kell megadnia a rendszerek, amelyek képesek a ugyanolyan ütemben nő.
- **Költségek**: a Contoso biztosítani szeretné a licencelési költségek csökkentése érdekében.

## <a name="migration-goals"></a>Áttelepítési célok

A Contoso felhőalapú csapat le az áttelepítés alkalmazáskövetelmények van rögzítve. Ezeknek a követelményeknek leginkább megfelelő áttelepítési módszer meghatározásához használt:
 - Az alkalmazás az Azure-ban marad, kritikus fontosságú, mivel még ma. Ez a kell jól, és könnyedén méretezheti a rendszert.
 - Az alkalmazás ne használja az IaaS-összetevőket. Minden kell kialakítani, PaaS, vagy a kiszolgáló nélküli szolgáltatások használatához.
 - Az app build futhat a cloud services, és a tárolókat a felhőben saját vállalati szintű tároló-beállításjegyzék kell tárolni.
 - Az API-szolgáltatás, amely kisállat fényképek kell lennie a pontos és megbízható a való világból, mivel a döntések az alkalmazás által a saját szállodák kell figyelembe. Bármely kisállat kapnak a hozzáférés engedélyezett a "Hotels" maradjon.
 - Fejlesztési és üzemeltetési folyamatok követelményeinek, Contoso fogja használni a Visual Studio Team Services (VSTS) az adatforrás kód Management (SCM), a Git-Adattárral.  Automatizált buildekig és kiadások használandó kód felépítéséhez és üzembe helyezése az Azure Web Apps, az Azure Functions és az AKS az.
 - Különböző CI/CD-folyamatok háttérbeli mikroszolgáltatások, és a webhely az előtér van szükség.
 - A háttérszolgáltatások és az előtérbeli webes alkalmazás közötti váltás másik verzió van.  Ez a követelmény teljesítéséhez két különböző fejlesztési és üzemeltetési folyamatot telepíti azokat.
 - Contoso minden előtér webhely-üzembehelyezési jóváhagyásra van szüksége, és a CI/CD-folyamat meg kell adnia ezt.


## <a name="solution-design"></a>Megoldásterv

Után rögzíthet célokat és követelményeket állapította meg, a Contoso tervez és a egy üzembehelyezési megoldással tekintse át, és azonosítja az áttelepítési folyamat, beleértve az Azure-szolgáltatások, az áttelepítés használandó.

### <a name="current-app"></a>Aktuális alkalmazás

- A SmartHotel360 helyszíni alkalmazás többszintű (WEBVM és SQLVM) két virtuális gép között.
- VMware ESXi-gazdagépen található virtuális gépek **contosohost1.contoso.com** (6.5-ös verzió)
- A VMware-környezet kezeli a vCenter Server 6.5-ös (**vcenter.contoso.com**), egy virtuális gépen futó.
- Contoso rendelkezik egy helyszíni adatközpont (contoso-datacenter), egy helyszíni tartományvezérlővel (**contosodc1**).
- A helyszíni virtuális gépeket a Contoso-datacenter elvesznek az áttelepítés befejezése után.


### <a name="proposed-architecture"></a>Javasolt architektúra

- Az alkalmazás előtér webalkalmazásként Azure App Services, az elsődleges Azure-régióban van telepítve.
- Egy Azure-függvényt biztosít a kisállat fényképek feltöltése, és ez a funkció együttműködik a hely.
- A kisállat fénykép függvény a Cognitive Services Látástechnológia API és a cosmos DB modul.
- A webhely háttérrendszere mikroszolgáltatások használatával lett összeállítva. Ezek kezelése pedig az Azure Kubernetes service (AKS) tárolókat fog települni.
- Tárolók az Azure DevOps használatával jönnek létre, és az Azure Container Registry (ACR) leküldött.
- Most Contoso manuális telepítése a webes alkalmazás és a függvény kódját a Visual Studio használatával.
- Mikroszolgáltatások telepíti egy PowerShell-parancsfájl, amely meghívja a Kubernetes parancssori eszközök használatával.

    ![Forgatókönyv-architektúra](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Megoldás áttekintése

Contoso kiértékeli a javasolt tervezési által bármik lehetnek, és hátrányai listáját.

**Szempontok** | **Részletek**
--- | ---
**Szakemberek számára** | PaaS- és kiszolgáló nélküli megoldások teljes körű központi telepítésére vonatkozó használata jelentősen csökkenti a felügyeleti időt, amely a Contoso meg kell adnia.<br/><br/> Áthelyezése a mikroszolgáltatási architektúrát lehetővé teszi, hogy a Contoso, könnyedén kiterjesztheti a megoldásokat az idő függvényében.<br/><br/> Új funkciók online állapotba helyezhetők a már meglévő megoldások kódbázissal bármelyikét megszakítása nélkül.<br/><br/> A webalkalmazás több példányával, és nem rendszerkritikus meghibásodási pontot lesz konfigurálva.<br/><br/> Az automatikus skálázás engedélyezve lesz, hogy az alkalmazás válaszolhat a különböző forgalommal képes kezelni.<br/><br/> A PaaS-szolgáltatások helyezze át a Contoso kivonhatja az elavult megoldások Windows Server 2008 R2 operációs rendszeren.<br/><br/> Cosmos DB, amely nem igényel konfigurálást a Contoso beépített hibatűrő képességgel rendelkezik. Ez azt jelenti, hogy az adatréteg már nem feladatátvételi hibaérzékeny pont.
**Hátrányai** | Tárolók összetettebbek, mint más áttelepítési lehetőségek. Gyorsan elsajátítható contoso problémát okozhatja.  Egy új szint által biztosított nagy mennyiségű értéket a görbe érték összetettségi vezetnek.<br/><br/> Az üzemeltetési csapat, contoso kell számára és Azure-tárolók és mikroszolgáltatások az alkalmazás támogatja.<br/><br/> Contoso még nem teljes körűen megvalósítva fejlesztési és üzemeltetési a teljes megoldás. Gondolja át, amely a központi telepítés az AKS, a functions és az App Services-szolgáltatások a Contoso cégnek szüksége van.



### <a name="migration-process"></a>Áttelepítési folyamat

1. Contoso az ACR, az AKS és a cosmos DB üzembe helyezése.
2. Az üzembe helyezéshez, beleértve az Azure Web App, a storage-fiók, a függvény és az API az infrastruktúra kiépítése azokat. 
3. Után az infrastruktúra van beállítva, azok fog létrehozni a mikroszolgáltatás-alapú Azure DevOps, amely leküldi azokat az ACR használatával a tárolórendszerképeket.
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

Íme, mi Contoso cégnek szüksége van ehhez a forgatókönyvhöz:

**Követelmények** | **Részletek**
--- | ---
**Azure-előfizetés** | Contoso előfizetések létrehozása közben egy korábbi cikkben. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Ha ingyenes fiókot hoz létre, Ön lesz az előfizetés rendszergazdája, és minden műveletet végrehajthat.<br/><br/> Ha egy meglévő előfizetést használ, és Ön nem a rendszergazda, kérjen a rendszergazdától tulajdonosi vagy közreműködői jogosultságot rendelhet, szeretne.
**Azure-infrastruktúra** | [Ismerje meg, hogyan](contoso-migration-infrastructure.md) Contoso beállítása az Azure-infrastruktúrát.
**Fejlesztői Előfeltételek** | Contoso cégnek szüksége van a következő eszközök egy fejlesztői munkaállomáson:<br/><br/> - [A Visual Studio 2017 Community Edition: Verzió 15.5](https://www.visualstudio.com/)<br/><br/> .NET-munkaterhelés engedélyezve van.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [A docker CE (Windows 10 esetén) vagy a Docker VM (Windows Server) –](https://docs.docker.com/docker-for-windows/install/) be a Windows-tárolók használatára.



## <a name="scenario-steps"></a>A forgatókönyv lépései

Itt látható, hogyan Contoso fog futni az áttelepítési:

> [!div class="checklist"]
> * **1. lépés: Üzembe AKS és ACR**: Contoso látja el a felügyelt AKS-fürt és a PowerShell-lel az Azure container registrybe
> * **2. lépés: A Docker-tárolók létrehozása**: folyamatos Integráció beállítása az Azure DevOps segítségével Docker-tárolókat, és azok leküldése az ACR-REL.
> * **3. lépés: A háttér-mikroszolgáltatások üzembe helyezéséhez**: azokat a többi fog javítható a háttér-mikroszolgáltatás-alapú infrastruktúra üzembe.
> * **4. lépés: Előtér-infrastruktúra üzembe helyezése**: azok az előtér-infrastruktúra, állatokért blob storage a kisállat-telefonokon, a Cosmos DB és a Vision API üzembe helyezése.
> * **5. lépés: A háttér áttelepítése**: mikroszolgáltatások üzembe helyezéséhez, és a futtatása az aks-en, a háttéralkalmazás áttelepíteni.
> * **6. lépés: Közzététel a kezelőfelület**: azok a SmartHotel360 alkalmazás közzététele az Azure App Service-ben, és a Függvényalkalmazást, amely a kisállat szolgáltatás fogja meghívni.



## <a name="step-1-provision-back-end-resources"></a>1. lépés: A háttér-erőforrások kiépítése

AKS és az Azure Container Registry (ACR) segítségével felügyelt Kubernetes-fürt létrehozása az üzembe helyezési parancsfájl futtatása a Contoso rendszergazdák.

- Az utasításokat, ez a szakasz használata a **SmartHotel360 – Azure-háttérrendszer** tárház.
- A **SmartHotel360 – Azure-háttérrendszer** GitHub-adattárat tartalmazza az összes szoftver esetében ez az üzembe helyezés részeként.

### <a name="prerequisites"></a>Előfeltételek

1. Ahhoz, hogy, Contoso rendszergazdák győződjön meg arról, hogy az összes prerequisitie szoftver telepítve van az üzemelő példány használatát a fejlesztési gépen.
2. Ezek a fejlesztői gépen, Git használatával helyi tárház klónozása: **git-klón https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>AKS és ACR kiépítése

A Contoso-rendszergazdák az üzembe helyezés végrehajtása:

1.  Nyissa meg a mappát a Visual Studio Code, és áthelyezi a **/üzembe helyezése/k8s** könyvtár, amely tartalmazza a parancsfájl **általános – aks-env.ps1**.
2. Ezek a parancsprogrammal hozzon létre a felügyelt Kubernetes-fürtöt, az AKS és ACR használatával.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  Nyissa meg a fájl, a $location paraméter frissítése **eastus2**, és mentse a fájlt.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. Kattintanak **nézet** > **beépített terminál** az integrált terminálon megnyitásához a kódban.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. A PowerShell integrált terminálon bejelentkeznek az Azure-bA a Connect-AzureRmAccount paranccsal. [További](https://docs.microsoft.com/powershell/azure/get-started-azureps) PowerShell használatának első lépéseivel kapcsolatos.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. Azure CLI-vel hitelesítéshez futtassa a **az bejelentkezési** parancsot, és a következő utasításokat a webböngésző használatával történő hitelesítéshez. [További](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) készül az Azure CLI bejelentkezéskor.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. A következő parancsot, és átadja a ContosoRG, az AKS fürt smarthotel-aks-eus2 nevét és az új beállításjegyzék-nevet az erőforráscsoport neve futnak.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Az Azure létrehozza az AKS-fürt erőforrásainak tartalmazó egy másik erőforráscsoportot.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. Az üzembe helyezés befejezése után telepíteniük a **kubectl** parancssori eszköz. Az eszköz már telepítve van az Azure cloud Shell.

    **az aks install-cli**

10. Futtassa a fürthöz való csatlakozás, ellenőrizze a **kubectl get csomópontok** parancsot. A csomópont a neve megegyezik a virtuális Gépet, az automatikusan létrehozott erőforráscsoportban.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. Ezek a következő parancsot a Kubernetes-irányítópult indításához: 

    **az aks browse--resource-group ContosoRG--name smarthotelakseus2**

12. Az irányítópult egy böngészőlapon nyílik meg. Ez a bújtatott kapcsolatot az Azure CLI használatával. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>2. lépés: A háttér-folyamat konfigurálásához.

### <a name="create-an-azure-devops-project-and-build"></a>Az Azure DevOps-projekt létrehozása és létrehozása

Contoso hoz létre az Azure DevOps-projekt, és konfigurálja a tároló létrehozása egy Konfigurációelem létrehozása, és majd leküldi azt az ACR-REL. Az utasításokat a jelen szakasz használata a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) repository.r

1. A visualstudio.com webhelyen, azok hozzon létre új szervezetet (**contosodevops360.visualstudio.com**), és állítsa be úgy a Git használatával.

2. Akkor hozzon létre egy új projektet (**SmartHotelBackend**) készítése a Git használatával a verziókezeléshez és Agile-munkafolyamat.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. A GitHub-adattárat importálja azokat.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. A **készítése és kiadása**, hoznak létre egy új folyamatot az Azure Git-Adattárakkal használatával, az importált adatforrásként **smarthotel** tárház. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. Egy üres folyamatot használ első lépésként válassza ki.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. Kiválasztják **üzemeltetett Linux előzetes** a buildelési folyamat.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. A **1. fázis**, adnak hozzá egy **Docker Compose** feladat. Ez a feladat buildek a Docker compose.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. Ismételje meg a, és a adjon hozzá egy másik **Docker Compose** feladat. Ez egy leküldéses értesítések a tárolókat az ACR-be.

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. Válassza ki az első tevékenység (a hozhat létre), és a build konfigurálása az Azure-előfizetést, engedélyezési és az ACR-REL. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. Az elérési útját kell adnia a **docket-compose.yaml** fájlban, az a **src** mappát a tárház. Szolgáltatás-lemezképeket, és a legújabb címke kiválasztásával. Mikor változik a művelet **szolgáltatás lemezképeket**, az Azure DevOps-feladat neve módosul a **automatikusan-szolgáltatások létrehozásához**

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. Most, a második Docker feladat (leküldés) konfigurálása. Akkor válassza ki az előfizetést és a **smarthotelacreus2** ACR. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. Újra, akkor adja meg a fájl a docker-compose.yaml fájlt, és válassza **szolgáltatás rendszerképeket** , és tartalmazzák a legutóbbi címkét. Mikor változik a művelet **szolgáltatás rendszerképeket**, az Azure DevOps-feladat neve módosul a **automatikusan leküldési szolgáltatásokat**

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. Az Azure fejlesztési és üzemeltetési feladatokat, konfigurált, a Contoso a buildelési folyamat menti, és a létrehozási folyamat elindul.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. A build folyamatának ellenőrzéséhez feladat a kattintanak.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. A létrehozás befejezése után az ACR-REL az új adattárakkal, amely fel van töltve a tárolók, a mikroszolgáltatások által használt jeleníti meg.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>A háttér-infrastruktúra üzembe helyezése

Az AKS-fürtöt létrehozni, és a beépített Docker-rendszerképeket, a Contoso rendszergazdák már a többi fog javítható a háttér-mikroszolgáltatás-alapú infrastruktúra üzembe helyezheti.

- A szakaszban használt utasítások a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) adattárat.
- Az a **/deploy/k8s/arm** mappában van az összes elem létrehozása egy parancsfájl. 

Telepítse a következő:

1. Nyissa meg a fejlesztői parancssort, és a parancs az bejelentkezés használata az Azure-előfizetést.
2. A Deploy.cmd fájl fájl segítségével, üzembe helyezése az Azure-erőforrások ContosoRG erőforráscsoportban és régióban EUS2, írja be a következő parancsot:

    **.\deploy.cmd azuredeploy - c eastus2 ContosoRG**

    ![Háttérbeli üzembe helyezése](./media/contoso-migration-rebuild/backend1.png)

2. Az Azure Portalon a kapcsolati karakterlánc az egyes adatbázisokra, hogy később rögzítése.

    ![Háttérbeli üzembe helyezése](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>A háttér-kibocsátási folyamatok létrehozása

Most a Contoso rendszergazdák tegye a következőket:

- Az NGINX bejövőforgalom-vezérlőt a engedélyezik a bejövő forgalmat a szolgáltatások üzembe helyezése.
- A mikroszolgáltatások üzembe az AKS-fürtöt.
- Első lépésként, a VSTS használatával mikroszolgáltatásokat, frissítse a kapcsolati karakterláncokat. Ezután konfigurálja egy új VSTS-kiadási folyamatot, a mikroszolgáltatások üzembe helyezéséhez.
- Az utasításokat a jelen szakasz használata a [SmartHotel360 – Azure-háttérrendszer](https://github.com/Microsoft/SmartHotel360-Azure-backend) adattárat.
- Vegye figyelembe, hogy a konfigurációs beállításokat (például az Active Directory B2C-vel) némelyike nem terjed ki ebben a cikkben. Olvassa el ezeket a beállításokat a tárházon belüli további információt.

Ezek a folyamat létrehozása:

1. Frissítse a Visual Studio használatával a **/deploy/k8s/config_local.yml** a korábban feljegyzett adatbázis kapcsolati adatait tartalmazó fájl.

    ![Adatbázis-kapcsolatok](./media/contoso-migration-rebuild/back-pipe1.png)

2. Nyissa meg a vsts-ben, és a a SmartHotel360 projektet, a **kiadásokban**, kattintanak **+ új adatcsatorna**.

    ![Új adatcsatorna](./media/contoso-migration-rebuild/back-pipe2.png)

3. Kattintanak **üres feladat** elindítani a folyamatot sablon nélkül.

    ![Üres projekt](./media/contoso-migration-rebuild/back-pipe3.png)

4. Ezek biztosítanak a környezet és a folyamat nevét.

      ![Környezet neve](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Folyamat neve](./media/contoso-migration-rebuild/back-pipe5.png)

5. Adnak hozzá egy összetevő.

     ![Összetevő hozzáadása](./media/contoso-migration-rebuild/back-pipe6.png)

6. Kiválasztják **Git** forrásaként írja be, és adja meg a projekt, a forrás és a master ággal a SmartHotel360 alkalmazás.

    ![Összetevő-beállítások](./media/contoso-migration-rebuild/back-pipe7.png)

7. A feladat hivatkozásra kattintanak.

    ![Tevékenység hivatkozás](./media/contoso-migration-rebuild/back-pipe8.png)

8. Új bevezetésének PowerShell feladat adnak hozzá, így futtathatók az Azure-környezet egy PowerShell-parancsfájlt.

    ![PowerShell az Azure-ban](./media/contoso-migration-rebuild/back-pipe9.png)

9. Válassza ki a tevékenység az Azure-előfizetést, és válassza ki a **deploy.ps1** parancsfájlt a Git-adattárból.

    ![Parancsfájl futtatása](./media/contoso-migration-rebuild/back-pipe10.png)


10. A parancsfájl argumentumai adnak hozzá. a parancsfájl törli az összes fürt tartalom (kivéve a **bejövő** és **bejövőforgalom-vezérlőjéhez**), és a mikroszolgáltatások üzembe helyezéséhez.

    ![Szkript argumentumai](./media/contoso-migration-rebuild/back-pipe11.png)

11. Az előnyben részesített Azure PowerShell-verzió beállítása a legújabb, és mentse a folyamatot.
12. Lépjen vissza a **kiadási** lapon, és az új kiadás manuális létrehozásához.

    ![Új kiadása](./media/contoso-migration-rebuild/back-pipe12.png)

13. Létrehozása után, illetve a kattintanak a kiadási **műveletek**, kattintanak **telepítés**.

      ![Kiadási üzembe helyezése](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Az üzembe helyezés befejeződése után futnak-e szolgáltatások használata az Azure Cloud Shell állapotának ellenőrzéséhez a következő parancsot: **kubectl szolgáltatások beolvasása**.


## <a name="step-3-provision-front-end-services"></a>3. lépés: Előtér-szolgáltatások üzembe helyezése

Contoso rendszergazdák kell üzembe helyeznie az infrastruktúra, amely az előtér-alkalmazás által használható. Ezek a kisállat rendszerképek; tárolására szolgáló blob storage-tároló létrehozása a kisállat információkkal; dokumentumok tárolására Cosmos-adatbázis és a Vision API, a webhelyhez. 

Ehhez útmutatást szakasz használata a [SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) tárház.

### <a name="create-blob-storage-containers"></a>Blob Storage-tárolók létrehozása

1.  Az Azure Portalon, a storage-fiók létrejött, és rákattint megnyitják **Blobok**.
2.  Akkor hozzon létre egy új tárolót (**Kisállatok**) tároló beállítása a nyilvános hozzáférési szinttel. Felhasználók saját kisállat fényképeket feltölteni ezt a tárolót.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob1.png)

3. Akkor hozzon létre egy második új tárolót **beállítások**. Egy fájl az előtér alkalmazásbeállításokat kerülnek ebben a tárolóban.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob2.png)

4. Rögzítése a hozzáféréshez szükséges adatokat a tárfiók egy szövegfájlba későbbi felhasználásra.

    ![Storage-blobba](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Cosmos-adatbázis kiépítése

Rendszergazdák contoso kisállat információ használandó Cosmos-adatbázis kiépítése.

1. Akkor hozzon létre egy **Azure Cosmos DB** az Azure piactéren.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. Ezek adjon meg egy nevet (**contosomarthotel**), válassza ki az SQL API és az éles erőforráscsoport ContosoRG, a fő USA keleti RÉGIÓJA 2 régióban helyezze.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. Új gyűjtemény az adatbázist, megnövelik a alapértelmezett és az átviteli sebesség.

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. Vegye figyelembe a későbbiekben az adatbázis kapcsolati adatait. 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Számítógépes Látástechnológia üzembe helyezése

Contoso-rendszergazdák a Computer Vision API üzembe helyezése. A függvény kiértékelése a felhasználók által feltöltött képek fogja meghívni az API-t.

1. Akkor hozzon létre egy **Computer Vision** példány az Azure piactéren.

     ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision1.png)

2. Ezek az API-t üzembe (**smarthotelpets**) az éles üzemű erőforrás csoportosítás ContosoRG, a fő USA keleti RÉGIÓJA 2 régióban.

    ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision2.png)

3. Az API-t a biztonságoskapcsolat-beállításainak mentése egy szövegfájlba későbbi felhasználás céljából.

     ![Számítógépes látástechnológia](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Az Azure-webalkalmazás üzembe helyezése

Contoso-rendszergazdák a webalkalmazás az Azure portal használatával üzembe helyezhető.


1. Kiválasztják **webalkalmazás** a portálon.

    ![Webalkalmazás](media/contoso-migration-rebuild/web-app1.png)

2. Biztosítanak az alkalmazás nevét (**smarthotelcontoso**), futtassa a Windows és a termékcsoport erőforrásokat helyezze **ContosoRG**. Hoznak létre egy új Application Insights-példányt az alkalmazás figyelése...

    ![Webalkalmazás neve](media/contoso-migration-rebuild/web-app2.png)

3. Miután végzett, akkor keresse meg az alkalmazásnak, és ellenőrizze, hogy sikeresen létrejött a címet.

4. Most az Azure Portalon, hozzon létre egy előkészítési pontot a kódot. a folyamat telepíti ezt a tárolóhelyet. Ez biztosítja, hogy kódot nem üzembe éles mindaddig, amíg a rendszergazdák, hajtsa végre egy kiadást.

    ![Webes alkalmazás előkészítési ponton](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Az Azure-függvényalkalmazás üzembe helyezése

Az Azure Portalon a Contoso-rendszergazdák a Függvényalkalmazás üzembe helyezhető.

1. Kiválasztják **Függvényalkalmazás**.

    ![Függvényalkalmazás létrehozása](./media/contoso-migration-rebuild/function-app1.png)

2. Biztosítanak az alkalmazás nevét (**smarthotelpetchecker**). Azok az éles erőforráscsoport helyezze az alkalmazás **ContosoRG**. A üzemeltetési helyet beállítása **Használatalapú csomagban**, és az USA keleti RÉGIÓJA 2 régióban helyezze el az alkalmazást. Egy új storage-fiók létrejön, és a figyelés az Application Insights-példányt.

    ![Függvényalkalmazás beállításai](./media/contoso-migration-rebuild/function-app2.png)


3. Az alkalmazás telepítése után, keresse meg az alkalmazás címére, ellenőrizze, hogy sikeresen létrejött.


## <a name="step-4-set-up-the-front-end-pipeline"></a>4. lépés: Az előtér-folyamat beállítása

Contoso-rendszergazdák az előtér-webhely két különböző projektek létrehozása. 

1. A vsts-ben, akkor hozzon létre egy projektet **SmartHotelFrontend**.

    ![Előtér-projekt](./media/contoso-migration-rebuild/function-app1.png)

2. Importálják az [előtér SmartHotel360](https://github.com/Microsoft/SmartHotel360-public-web.git) Git-adattár az új projektbe.
3. A függvényalkalmazás, egy másik (SmartHotelPetChecker) VSTS-projekt létrehozása és importálása a [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) Git-adattárat a projektbe.

### <a name="configure-the-web-app"></a>A webalkalmazás konfigurálása

Most a Contoso-rendszergazdák a webes alkalmazások, a Contoso erőforrások konfigurálását.

1. Kapcsolódjon a vsts-ben, és klónozza az adattárat helyileg a fejlesztői gépen.
2. A Visual Studióban akkor nyissa meg a mappát az adattárban lévő összes fájl megjelenítéséhez.

    ![Tárház-fájlok](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Szükség szerint a konfigurációs módosítások frissíteni azokat.

    - A webes alkalmazás indulásakor megkeresi a **SettingsUrl** alkalmazásbeállítást.
    - A változónak tartalmaznia kell egy konfigurációs fájl mutató URL-címet.
    - Alapértelmezés szerint használt beállítás egy nyilvános végpontot.

4.  Ezek a /config-sample.json/sample.json fájl frissítése.

    - Ez a webes konfigurációs fájl használata esetén a nyilvános végponthoz.
    - Szerkessze azokat a **URL-címek** és **pets_config** , az értékeket az AKS API-végpontokat, a storage-fiókok és a Cosmos-adatbázis szakaszokat.
    - Az URL-címeket meg kell egyeznie az új webalkalmazást, amely létrehozza a Contoso DNS-nevét.
    - Ez a contoso, **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![JSON-beállításfájl](./media/contoso-migration-rebuild/configure-webapp2.png)

5. A fájl frissítése után, nevezze át az **smarthotelsettingsurl**, és töltse fel azt a korábban létrehozott tároló blogon.

    ![Nevezze át és feltöltése](./media/contoso-migration-rebuild/configure-webapp3.png)

6. A fájl az URL-Címének lekéréséhez kattintanak. Az URL-címet használják az alkalmazást, amikor, lekéri a konfigurációs fájlok.

    ![Alkalmazás URL-címe](./media/contoso-migration-rebuild/configure-webapp4.png)

7. Az a **appsettings. Production.JSON** fájlt, frissítse a **SettingsURL** URL-címét az új fájlt.

    ![Frissítési URL-címe](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>A webhelyet üzembe helyezni az Azure App Service szolgáltatásban

Contoso-rendszergazdák mostantól teheti közzé a webhelyén.


1. Megnyitják a vsts-ben, majd a a **SmartHotelFrontend** projektre a **műveletek**, kattintanak **+ új adatcsatorna**.
2. Kiválasztják **VSTS Git** forrásként.

    ![Új adatcsatorna](./media/contoso-migration-rebuild/vsts-publishfront1.png)

3. Akkor válassza ki a **ASP.NET Core** sablont.
4. Tekintse át a folyamat, és ellenőrizze, hogy **webes projektek közzététele** és **közzétett projektek Zip** ki van jelölve.

    ![Folyamat beállításai](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. A **eseményindítók**, engedélyezze a folyamatos integrációt, és adja hozzá a master ágról. Ez biztosítja, hogy minden egyes tim a megoldás a főágba véglegesített új kódot tartalmaz, a létrehozási folyamat elindul.

    ![Folyamatos integráció](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Kattintanak **várólistára & mentése** build elindításához.
7. A létrehozás befejezése után ezek a kibocsátási folyamat használatával konfigurálása a **Azure App Service üzembe helyezési**.
8. Egy környezet neve nyújtanak **átmeneti**.

    ![Környezet neve](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Adjon hozzá egy összetevő, és válassza ki az imént konfigurált build.

     ![Összetevő hozzáadása](./media/contoso-migration-rebuild/vsts-publishfront5.png)

6. Kattintson a artifcat villámgyors bolt ikonjára, és folyamatos üzembe helyezés engedélyezése.

    ![Folyamatos üzembe helyezés](./media/contoso-migration-rebuild/vsts-publishfront6.png)

7. A **környezet**, kattintanak **1. fázis, 1. feladat** alatt **átmeneti**.
8. Miután kijelölte az előfizetés, és az alkalmazás neve, megnyitja a **üzembe helyezése az Azure App Service** feladat. Az üzemelő példány használatára van konfigurálva a **átmeneti** üzembe helyezési pont. Ez a kód áttekintésre és jóváhagyásra tárolóhelyen található automatikusan létrehozza.

     ![Üzembe helyezési pont](./media/contoso-migration-rebuild/vsts-publishfront7.png)

9. Az a **új kibocsátásában**, adnak hozzá egy új környezetben.

    ![Új környezet](./media/contoso-migration-rebuild/vsts-publishfront8.png)

10. Kiválasztják **Azure App Service üzembe helyezési tárolóhellyel**, és nevezze el a ellenőrzésével **éles**.

    ![Környezet neve](./media/contoso-migration-rebuild/vsts-publishfront9.png)

11. Kattintson a **fázis 1, 2 feladatok**, és válassza ki az előfizetés, az app service-neve, és **átmeneti** tárolóhely.

    ![Környezet neve](./media/contoso-migration-rebuild/vsts-publishfront10.png)

12. Eltávolítja azokat a **üzembe helyezése az Azure App Service-tárolóhely** a folyamatból. Ez volt során ide kerülnek az előző lépésekben.

    ![Folyamat eltávolítása](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. A folyamat mentése. A folyamat kattintanak a **üzembe helyezés utáni feltételek**.

    ![Üzembe helyezés utáni](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. A használatukkal **üzembe helyezés utáni jóváhagyásokat**, és a egy fejlesztési érdeklődő hozzáadása a jóváhagyó.

    ![Üzembe helyezés utáni jóváhagyása](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. A Buildelési folyamat azokat manuálisan elindít egy buildet. Ez elindítja az új kiadási folyamatot, amely telepíti a helyet az előkészítési pont. A Contoso, az üzembe helyezési pont URL-je **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Miután a létrehozás befejezése, és a tárolóhely kiadását telepíti, a VSTS a fejlesztési vezető jóváhagyási e-mailek.
17. A fejlesztési vezető kattintással **jóváhagyás megtekintése**, és jóváhagyhatja vagy elutasíthatja a kérelmet a VSTS-portálon.

    ![Jóváhagyó e-mailt](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Az érdeklődő lehetővé teszi egy megjegyzést, és hagyja jóvá. Ekkor elindul a lapozófájl-kapacitás, a **átmeneti** és **prod** tárhelyek és a build helyezi át a termelési.

    ![Hagyja jóvá, és cseréje](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. A folyamat végén a lapozófájl-kapacitás.

    ![Felcserélés befejezése](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. A csapata ellenőrzi a **prod** annak ellenőrzéséhez, hogy a webalkalmazás a termelés tárolóhely **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>A PetChecker Függvényalkalmazás üzembe helyezése

Contoso-rendszergazdák a következő telepíteni az alkalmazást.

1. A VSTS-projektben való csatlakozással helyileg az adattárat a fejlesztői gépen, klónozza.
2. A Visual Studióban akkor nyissa meg a mappát az adattárban lévő összes fájl megjelenítéséhez.
3. Megnyitja a **src/PetCheckerFunction/local.settings.json** fájlt, és adja hozzá a tárolás, a Cosmos-adatbázis és a Computer Vision API app-beállításokat.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function5.png)

4. A kódot, és szinkronizálhatja a vsts-ben, a módosításokat küld vissza.
5. Adjon hozzá egy új Buildelési folyamat, és válassza ki **VSTS Git** a forrás.
6. Akkor válassza ki a **ASP.NET Core (.NET Framework)** sablont.
7. Az alapértelmezett értékeket, a sablon elfogadja őket.
8. A **eseményindítók**, majd válassza a **engedélyezze a folyamatos integrációt**, és kattintson a **várólistára & mentése** build indítása.
9. A build sikeres lesz, miután azok hozhat létre egy kibocsátási folyamatok hozzáadása a **Azure App Service üzembe helyezési tárolóhellyel**.
10. Ezek a környezet neve **éles**, és válassza ki az előfizetést. Függetlenül a **alkalmazástípus** való **függvény Ap**, és az app service néven **smarthotelpetchecker**.

    ![Függvényalkalmazás](./media/contoso-migration-rebuild/petchecker2.png)

11. Adnak hozzá egy összetevő **összeállítása**.

    ![Összetevő](./media/contoso-migration-rebuild/petchecker3.png)

12. A használatukkal **a folyamatos készregyártás eseményindítója**, és kattintson a **mentése**.
13. Kattintanak **új build várólistára** a teljes CI/CD-folyamatok futtatásához.
14. A funkció üzembe helyezését követően megjelenik az Azure Portalon, az a **futó** állapotát.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function6.png)


7. Ezek ellenőrzéséhez, hogy a Kisállat-ellenőrző alkalmazás a várt módon működik-e az alkalmazás megkeresése tallózással [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. A kép feltöltése a profilkép kattintanak.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function7.png)

9. Az első fénykép szeretne ellenőrzése egy kis kutya van.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function8.png)

10. Az alkalmazás által megjelenített üzenetben az elfogadási.

    ![A függvény üzembe helyezése](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Tekintse át a központi telepítés

Az áttelepített erőforrások az Azure-ban, a Contoso most már teljes mértékben üzembe helyezése és kell biztonságossá tétele az új infrastruktúra.

### <a name="security"></a>Biztonság

- Contoso meg kell győződnie arról, hogy az új adatbázisok biztonságosak. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- SSL-tanúsítványokkal használandó frissíteni kell az alkalmazást. A tárolópéldány újratelepítése kell a 443-as választ.
- Contoso érdemes megfontolni KeyVault titkos kulcsok a Service Fabric-alkalmazások védelmére. [További információk](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás

- Contoso cégnek szüksége van, tekintse át az Azure SQL Database biztonsági mentés követelményeinek. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso érdemes fontolóra SQL feladatátvételi csoportok regionális feladatátvételt biztosít az adatbázishoz. [További információk](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso kihasználhatja az ACR prémium Termékváltozatokban georeplikációt. [További információk](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- A cosmos DB automatikusan menti. Contoso is [további](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) erről a folyamatról.

### <a name="licensing-and-cost-optimization"></a>Licencelési és a költségek optimalizálása

- Erőforrások üzembe helyezését követően Contoso rendelje hozzá a az Azure címkék alapján saját [infrastruktúrák tervezése](contoso-migration-infrastructure.md#set-up-tagging).
- Az összes licencelése a költség, a Contoso fogyaszt PaaS-szolgáltatások be van építve. Ez a program levonja a nagyvállalati szerződés.
- Contoso lehetővé teszi a Cloudyn, a Microsoft egy leányvállalata által licencelt Azure Cost Managementbe. Egy többfelhős költségkezelő felügyeleti megoldás, amely segítséget nyújt az Azure és egyéb felhőerőforrások kezelése, és a.  [További](https://docs.microsoft.com/azure/cost-management/overview) kapcsolatos Azure Cost Managementbe.

## <a name="conclusion"></a>Összegzés

Ez a cikk a Contoso újraépíti a SmartHotel360 alkalmazást az Azure-ban. Az előtérbeli virtuális gép az Azure App Services Web Apps újraépítésekor a helyszíni alkalmazás. Az alkalmazás háttérrendszere mikroszolgáltatások üzembe helyezett Azure Kubernetes Service (AKS) által felügyelt tárolók használatával lett összeállítva. Továbbfejlesztett contoso alkalmazás működéséhez kisállat fénykép alkalmazás használatával.




