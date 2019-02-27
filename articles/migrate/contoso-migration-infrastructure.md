---
title: Contoso – egy áttelepítési infrastruktúra beállítása |} A Microsoft Docs
description: Ismerje meg, hogy beállítja a Contoso egy Azure-infrastruktúra az Azure-ba való migrálásra.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: raynew
ms.openlocfilehash: 6ee05af0391311b4782211807f41ce099a6c24a2
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889936"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - áttelepítési infrastruktúra üzembe helyezése

Ebben a cikkben Contoso előkészíti a helyszíni infrastruktúrát az áttelepítéshez, és beállítja az Azure-infrastruktúrák, az előkészítés, áttelepítés és a hibrid környezetben futó üzleti.

- Egy adott Contoso mintaarchitektúra.
- Az áttelepítési stratégiáját függ, hogy kell-e ebben a cikkben leírt összes elemet. Például ha az Azure-ban csak felhőbeli natív alkalmazásokat fejleszt, szüksége lehet egy kevésbé összetett hálózati struktúrát.

Ez a cikk, amely dokumentálja, hogyan a fiktív Contoso áttelepíti a Microsoft Azure felhőbe helyszíni erőforrásait egy sorozat része. A sorozat háttér-információkat is tartalmaz, és különböző telepítési forgatókönyvek esetén, amelyek bemutatják, hogyan állíthat be egy áttelepítési infrastruktúra egy sorozat felmérheti a a helyszíni erőforrások migrálásra való alkalmasságát, és futtassa a különböző típusú áttelepítéseket. Forgatókönyvek egyre összetettebbé válnak. Cikkek idővel felveszi az adatsorozat.


**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: – Áttekintés](contoso-migration-overview.md) | A cikk sorozat, a Contoso-áttelepítési stratégia és az adatsorozathoz használt mintaalkalmazások áttekintése. | Elérhető
2. cikk: Azure-infrastruktúra üzembe helyezése | Contoso előkészíti a helyszíni infrastruktúra és az Azure-infrastruktúra az áttelepítéshez. A sorozat minden migrálásról szóló cikksorozat ugyanazon az infrastruktúrán használható. | Ez a cikk
[3. cikk: Mérje fel helyszíni erőforrásait az Azure-ba való migráláshoz](contoso-migration-assessment.md)  | A VMware-en futó helyszíni SmartHotel360-alkalmazás értékelése SContoso fut. Contoso értékeli az alkalmazás virtuális gépek az Azure Migrate szolgáltatás és a Data Migration Assistant szolgáltatást használó alkalmazás SQL Server-adatbázis használatával. | Elérhető
[4. cikk: Áthelyezési egy alkalmazást egy Azure virtuális Gépen, és SQL Database felügyelt példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso lift-and-shift az áttelepítés fut az Azure-bA a helyszíni SmartHotel360 alkalmazáshoz. Contoso áttelepíti az alkalmazás előtérbeli virtuális gép használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso az alkalmazás-adatbázis áttelepítése egy Azure SQL Database felügyelt példánya a a [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Elérhető   
[5. cikk: Az Azure virtuális gépein egy alkalmazás újratárolása](contoso-migration-rehost-vm.md) | Contoso a SmartHotel360 alkalmazás virtuális gépeit áttelepíti a Site Recovery szolgáltatással az Azure virtuális gépekhez. | Elérhető
[6. cikk: Egy Azure virtuális gépeken és a egy SQL Server AlwaysOn rendelkezésre állási csoportban található alkalmazás újratárolása](contoso-migration-rehost-vm-sql-ag.md) | Contoso áttelepíti az alkalmazás számára, hogy az alkalmazás virtuális gépek és a Database Migration Service az alkalmazás-adatbázis migrálása az AlwaysOn rendelkezésre állási csoport által védett SQL Server-fürtöt a Site Recovery használatával. | Elérhető
[7. cikk: Egy Azure-beli virtuális gépeken Linux alkalmazás újratárolása](contoso-migration-rehost-linux-vm.md) | Contoso az Azure virtuális gépek, a Site Recovery szolgáltatás használatával Linux osTicket alkalmazás lift-and-shift áttelepítés befejeződött. | Elérhető
[8. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépek és az Azure Database for MySQL-hez](contoso-migration-rehost-linux-vm-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure virtuális gépek Site Recovery használatával. Az áttelepítése az alkalmazás-adatbázis az Azure Database for MySQL-hez a MySQL Workbench használatával. | Elérhető
[9. cikk: Az Azure web App alkalmazásban és az Azure SQL Database alkalmazás újrabontása](contoso-migration-refactor-web-app-sql.md) | Contoso a SmartHotel360 alkalmazást áttelepíti egy Azure-webalkalmazást, és az alkalmazás-adatbázis áttelepítése egy Azure SQL Server-példány az a Database Migration Assistant szolgáltatást. | Elérhető    
[10. cikk: Refaktorovat egy Linux-alkalmazást egy Azure-webalkalmazást és az Azure Database for MySQL-hez](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso áttelepíti a Linux-osTicket alkalmazás Azure-webalkalmazás több helyen is előfordul. A webalkalmazás folyamatos készregyártás a GitHub van integrálva. Az alkalmazás-adatbázis, áttelepíti egy Azure Database for MySQL-példányt. | Elérhető
[11. cikk: Refaktorovat a Team Foundation Serverrel az Azure DevOps-szolgáltatásokkal](contoso-migration-tfs-vsts.md) | Contoso áttelepíti a helyi Team Foundation Server központi Azure DevOps-szolgáltatásokkal az Azure-ban. | Elérhető
[12. cikk: Azure-tárolók és az Azure SQL Database az alkalmazás újratervezése](contoso-migration-rearchitect-container-sql.md) | Contoso annak SmartHotel app áttelepíti az Azure-bA. Ezután azt rearchitects az alkalmazás webes réteg az Azure Service Fabric és az alkalmazás-adatbázis az Azure SQL Database-ban futó Windows tárolójaként. | Elérhető    
[13. cikk: Építse újra az alkalmazást az Azure-ban](contoso-migration-rebuild.md) | Contoso újraépíti a SmartHotel alkalmazás számos Azure-szolgáltatások és szolgáltatások, beleértve az Azure App Service, Azure Kubernetes Service (AKS), az Azure Functions, Azure Cognitive Services és az Azure Cosmos DB használatával. | Elérhető
[14. cikk: Áttelepítés az Azure-bA méretezése](contoso-migration-scale.md) | Után próbálja ki a migrálás kombinációit, Contoso előkészíti a teljes migrálás az Azure-ba való méretezése. | Elérhető 

Ebben a cikkben Contoso állítja be az infrastruktúra-elemeket kell minden áttelepítési forgatókönyveinek végrehajtásához. 


## <a name="overview"></a>Áttekintés

Mielőtt Contoso áttelepíthetők az Azure-ba, rendkívül fontos egy Azure-infrastruktúra előkészítése.  Általában a Contoso cégnek szüksége van, gondolja át az öt széles körű területből áll:

**1. lépés: Az Azure-előfizetések**: Hogyan fogja a Contoso Azure megvásárlása és dolgozhat az Azure-platform és szolgáltatások?  
**2. lépés: Hibrid identitás**: Hogyan fogja azt kezelése és az áttelepítés után a helyszíni és Azure-erőforrásokhoz való hozzáférésének? Hogyan nem Contoso bővítése vagy helyezze át az Identitáskezelés a felhőben?  
**3. lépés: Vészhelyreállítás és a rugalmasság**: Hogyan fogja Contoso ellenőrizze, hogy az alkalmazások és infrastruktúra rugalmas leállások és katasztrófák kezelése fordulhat elő, ha?  
**4. lépés: Hálózatkezelés**: Hogyan kell a Contoso egy hálózati infrastruktúra tervezése és a helyszíni adatközpont és az Azure közötti kapcsolatot?  
**5. lépés: Biztonsági**: Hogyan fogja a biztonságos a hibrid vagy az Azure üzembe helyezési?  
**6. lépés: Cégirányítási**: Hogyan fogja megőrizni a Contoso az üzembe helyezés, biztonsági és cégirányítási követelmények összhangban?

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdjük áttekinteni az infrastruktúra, érdemes olvassa el ebben a cikkben bemutatjuk az Azure-képességek néhány háttér-információkat:

- Nincsenek vásárlási Azure eléréséhez, beleértve a használatalapú fizetés, a nagyvállalati szerződés (EA), az Open licencprogramokon Microsoft-viszonteladók, vagy a Felhőszolgáltatók (CSP) néven ismert Microsoft Partners számos lehetőséget. Ismerje meg [vásárlási lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), és megtudhatja, hogyan lehet [Azure-előfizetések vannak rendszerezve](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Az Azure áttekintése [identitás és hozzáférés-kezelés](https://www.microsoft.com/trustcenter/security/identity). Különösen ismertetése [Azure ad-ben, és kiterjeszti a helyszíni AD a felhőbe](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Nincs információ hasznos letölthető e-könyv [identitás és hozzáférés-kezelés (IAM) hibrid környezetben](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Az Azure robusztus hálózati infrastruktúrát hibrid kapcsolódási lehetőségeket biztosít. Áttekintheti a [hálózatkezelés és a hálózati hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/security-network-overview).
- Ismerkedjen meg [Azure biztonsági](https://docs.microsoft.com/azure/security/azure-security), és olvassa el a terv létrehozása [cégirányítási](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>A helyszíni architektúra

Íme az aktuális Contoso a helyszíni infrastruktúra bemutató ábra.

 ![Contoso-architektúra](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso a az városa New York a kelet-Egyesült Államok található egy fő adatközpont rendelkezik.
- Nincsenek három további helyszíni ágakat Egyesült államokbeli városba.
- A fő adatközpont fiber metro ethernet-kapcsolattal (500 MB/s) az internethez csatlakozik.
- Minden egyes fiókiroda helyi üzleti osztály kapcsolatok, térjen vissza a fő adatközpont IPSec VPN-alagutat az internetes csatlakozik. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpont teljes VMware-rel lesz virtualizálva. Contoso két ESXi 6.5-ös virtualizációs gazdagépeket, a vCenter Server 6.5-ös által felügyelt rendelkezik.
- Contoso az Active Directory identitáskezelési és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők VMware virtuális gépek futtatását az adatközpontban. A tartományvezérlők helyi ágat, fizikai kiszolgálókon futnak.


## <a name="step-1-buy-and-subscribe-to-azure"></a>1. lépés: Vásárol, és iratkozzon fel az Azure

Contoso cégnek szüksége van, felderíthesse az Azure megvásárlása, hogyan tervezhet, előfizetések és szolgáltatásokat és erőforrásokat módjával.

### <a name="buy-azure"></a>Az Azure megvásárlása

A Contoso történik egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Ez magában foglalja az Azure-ba, hogy jelentős előnnyel jár, többek között rugalmas számlázási lehetőségeket alátámasztó okmányaként Contoso előzetes pénzügyi kötelezettségvállalást, és optimalizálja a díjszabás.

- Contoso mi várható az éves Azure-költések kezelése lesz. A szerződés aláírt, Contoso fizetett teljes mértékben az első évben.
- Contoso cégnek szüksége van, minden kötelezettségvállalás használ, mielőtt az év felett van, vagy elveszíti ezeket dollárban értéke.
- Ha valamilyen okból a Contoso elkötelezettség meghaladja, és a várólistában további, a Microsoft számláz őket a különbséget.
- Minden fenti kötelezettségvállalás járó költségek lesz az azonos díjszabás, valamint azokat, a Contoso szerződésben. Nincsenek nem úgy kapcsolaton át.

### <a name="manage-subscriptions"></a>Előfizetések kezelése

Után kell fizetnie, az Azure-hoz, Contoso kell, hogyan lehet Azure-előfizetések kezelése. Contoso nagyvállalati szerződéssel rendelkezik, és így az Azure-előfizetések száma nincs korlátozva, állíthatja be.

- Az Azure nagyvállalati beléptetés határozza meg, hogyan egy vállalati alakzatokat és az Azure-szolgáltatásokat használ, és határozza meg a core irányítási szerkezete.
- Első lépésként Contoso megállapítása szerint a struktúra (más néven egy enterprise scaffold a nagyvállalati beléptetés. A Contoso használt [Ez a cikk](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-governance) ismertetése, és a egy scaffold kialakítása érdekében.
- Most a Contoso egy funkcionális módszer használatához az előfizetések kezeléséhez határozott meg.
    - A vállalaton belüli fogja használni, amely az Azure-költségvetési szabályozza egy informatikai részleghez. Ez az előfizetés csak csoport lesz.
    - Contoso úgy, hogy más vállalati csoportok csatlakozhat a nagyvállalati beléptetés a részlegek, a jövőben a rendszergazdákéval ebben a modellben.
    - Az informatikai részleg belül Contoso két előfizetések, üzemi és fejlesztési van felépítve.
    - Ha a Contoso van szüksége a jövőben további előfizetéseket, kell hozzáférést, a házirendek és a megfelelőséget ezen előfizetések kezelése. Contoso fogja megtenni bevezetésével [az Azure felügyeleti csoportok](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), mint a fenti előfizetések réteget.

    ![Nagyvállalati struktúra](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Vizsgálja meg a licencelés

Előfizetések konfigurálva, a Contoso tekintse meg a Microsoft. A licencelési stratégia a Contoso Azure, és hogyan Azure virtuális gépek és szolgáltatások kiválasztott és üzembe helyezett áttelepíteni kívánt erőforrások függ. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Az Azure-beli virtuális gépek üzembe helyezésekor, a standard szintű lemezképek tartalmaznak egy licenc, amely a Contoso számára a szoftverrel használt percalapú díja. Azonban a Contoso lett egy hosszú távú Microsoft-ügyfél, és EAs és a nyílt licenceket frissítési garanciával (SA) rendelkező rendelkezik tárolja. 

Az Azure Hybrid Benefit a Contoso az áttelepítés költséghatékony módszer lehetővé teszi azáltal, hogy ez az Azure virtuális gépek és az SQL Server számítási feladatok átalakításával vagy frissítési garanciával rendelkező Windows Server Datacenter és Standard edition-licencek újrafelhasználásával kezelt. Ez lehetővé teszi a számítási díjat fizeti egy alacsonyabb alapú virtuális gépek és az SQL Server Contoso. [További információk](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Licenc Mobility

SA keresztüli biztosít a Microsoft mennyiségi licenc ügyfelek például Contoso, a licenckövetelményeknek megfelelő kiszolgálói alkalmazásokat az Azure-beli aktív SA. Így nem szükséges új licenceket vásárolni. Nincs társított mobilitást díjak a meglévő licenceit is egyszerűen üzembe helyezhetők az Azure-ban. [További információk](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Foglaljon le előre megjósolható számítási feladatokkal példányai

Előre megjósolható számítási feladatokkal által mindig elérhető legyen a futó virtuális gépek igénylő. Például az üzleti alkalmazások, például egy SAP ERP-rendszer.  Másrészről kiszámíthatatlan számítási rendszer, amelynek a változó. Például virtuális gépek, amelyek során nagy kereslet, és nem csúcsidőre időpontokban ki.

![Fenntartott példány](./media/contoso-migration-infrastructure/reserved-instance.png) 

Használatával a fenntartott példányok adott virtuális gép üzemelő példányok fenn kell tartani az idő nagy időtartamok konzolján kérheti le a kedvezményt, mind Priorizált kapacitást. Használatával [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/), valamint az Azure Hybrid Benefittel akár 82 %-a normál használatalapú ki Contoso takaríthat (2018 április) díjszabása.


## <a name="step-2-manage-hybrid-identity"></a>2. lépés: Hibrid identitás kezelése

Így, és az identitás és hozzáférés-kezelés (IAM) Azure-erőforrásokhoz való felhasználói hozzáférés szabályozása egy Azure-infrastruktúra melyekbe fontos lépés.  

- Contoso úgy dönt, hogy a helyszíni Active Directory kiterjesztheti a felhőbe, nem pedig egy új különálló rendszert az Azure-ban.
- Létrehoz egy Azure-alapú Active Directory ehhez.
- Kell kiépíteni az új Azure AD contoso Office 365-höz nem működik a.
- Az Office 365 az Azure AD felhasználói felügyelethez. Ha a Contoso Office 365 használta, Azure AD-bérlő már rendelkeznie, és használhatja azt, mint az elsődleges AD.
- [További](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) az Office 365, Azure AD-ről, és ismerje meg [egy előfizetés hozzáadása](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) egy meglévő Azure AD-bérlőhöz.

### <a name="create-an-azure-ad"></a>Hozzon létre egy Azure ad-ben

Contoso az Azure AD ingyenes kiadásának mobilalkalmazásoknak része egy Azure-előfizetést használ. AD-címtárral az alábbiak szerint állíthatja a Contoso rendszergazdák:

1. A a [az Azure portal](http://portal.azure.com/), akkor navigáljon a **erőforrás létrehozása** > **identitás** > **Azure Active Directory**.
2. A **könyvtár létrehozása**, adja meg a könyvtár nevét, egy kezdeti tartománynevet és a régiót, amelyben az Azure AD-címtárban kell létrehozni.

    ![Hozzon létre az Azure ad-ben](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > A címtár, létrehozott egy kezdeti tartománynevet rendelkezik formájában **domainname.onmicrosoft.com**. A név nem módosítható vagy törölhető. Ehelyett szükségük van a regisztrált tartománynév hozzáadása az Azure ad-ben.

### <a name="add-the-domain-name"></a>A tartománynév hozzáadása

A szokásos tartománynév használatára, a Contoso rendszergazdáknak kell, egy egyéni tartománynév hozzáadása az Azure AD. Ez a beállítás lehetővé teszi számukra ismerős felhasználónevek hozzárendelése. Ha például egy felhasználó bejelentkezhet az e-mail-cím billg@contoso.com, ahelyett, hogy kellene billg@contosomigration.microsoft.com. 

Egyéni tartománynév beállítása, azt hozzá a címtárhoz, egy DNS-bejegyzés hozzáadása, és ellenőrizze a neve az Azure ad-ben.

1. A **egyéni tartománynevek** > **egyéni tartomány hozzáadása**, akkor hozzáadhatja a tartományt.
2. DNS-bejegyzés használhatja az Azure, regisztrálnia kell azt a tartományregisztrálónál. 

    - Az a **egyéni tartománynevek** listán, vegye figyelembe a DNS-információkat a neve. MX bejegyzés használ.
    - Ezek a névkiszolgáló ehhez hozzá kell férniük. Jelentkezzen be a Contoso.com tartományhoz, és hozzon létre egy új MX-rekordot az Azure AD-ben feljegyzett adatokkal által biztosított DNS-bejegyzést.  
1. Miután a DNS-rekordok propagálása a tartományhoz, a részletek nevében kattintanak **győződjön meg arról** az egyéni tartománynév ellenőrzése.

     ![Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Állítsa be a helyszíni és Azure-csoportok és felhasználók

Most, hogy az Azure ad-ben működő, a Contoso rendszergazdák alkalmazottak hozzá kell adnia a helyszíni AD-csoportokat, a rendszer szinkronizálja az Azure AD. Használják inkább a helyi csoport neve megegyezik a tartozó erőforráscsoportok nevét az Azure-ban. Így könnyebben azonosíthatók legyenek az egyezések szinkronizálás céljából.

#### <a name="create-resource-groups-in-azure"></a>Erőforráscsoportok létrehozása az Azure-ban

Azure-erőforráscsoportok összegyűjtésére Azure-erőforrások. Műveletek végrehajtása a csoporton belül az erőforrásokat az Azure-erőforráscsoport azonosítója használata lehetővé teszi.

- Azure-előfizetések több erőforráscsoporthoz is rendelkezhet, de egy erőforráscsoportot csak akkor létezhet egyetlen előfizetésben jön létre.
- Emellett egyetlen rendelkezhet több olyan erőforrást, de egy erőforrás csak egyetlen erőforráscsoportot is tartozhatnak.

Contoso rendszergazdák beállítása Azure-erőforráscsoportot, az alábbi táblázat foglalja össze.

**Erőforráscsoport** | **Részletek**
--- | ---
**ContosoCobRG** | Ez a csoport tartalmazza (COB) üzleti folytonossági kapcsolódó összes erőforrást.  Ez magában foglalja a Contoso az Azure Site Recovery szolgáltatás és az Azure Backup szolgáltatás által használt tárolókat.<br/><br/> Ez is magában foglalja többek között az Azure Migrate és a Database Migration Service migráláshoz használt erőforrásokat.
**ContosoDevRG** | Fejlesztési és tesztelési erőforrásokat tartalmazó csoport.
**ContosoFailoverRG** | Ez a csoport erőforrások sikertelen alkotóelemeit zóna szolgál.
**ContosoNetworkingRG** | Ez a csoport összes hálózati erőforrást tartalmazza.
**ContosoRG** | Ez a csoport tartalmazza az éles alkalmazások és adatbázisok kapcsolódó erőforrásokat.

Ezek erőforráscsoportok a következőképpen hozhat létre:

1. Az Azure Portal > **erőforráscsoportok**, akkor a csoport hozzáadása.
2. Az egyes csoportokhoz, adja meg a nevét, az előfizetés, amely a csoport tartozik, és a régió.
3. Erőforráscsoportok megjelennek a **erőforráscsoportok** listája.

    ![Erőforráscsoportok](./media/contoso-migration-infrastructure/resource-groups.png) 

##### <a name="scaling-resource-groups"></a>Méretezési csoportok

A jövőben a Contoso egyéb erőforráscsoportok igények alapján hozzáadja. Egy erőforráscsoport minden alkalmazás vagy szolgáltatás, például, hogy felügyelhetők, és egymástól függetlenül biztonságos sikerült definiálják.

#### <a name="create-matching-security-groups-on-premises"></a>Hozzon létre megfelelő biztonsági csoportokat a helyszíni

1. Contoso-rendszergazdák a helyszíni Active Directory biztonsági csoportokat, amelyek megfelelnek az Azure-erőforráscsoportok nevei nevű beállítását.
 
    ![A helyszíni AD biztonsági csoportok](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Felügyeleti szempontból során létrehoznak egy további olyan csoport, amely megjelenik az összes többi csoport. Ez a csoport lesz jogosult összes erőforráscsoport az Azure-ban. A globális rendszergazdák csak korlátozott számú hozzáadódik ehhez a csoporthoz.

### <a name="synchronize-ad"></a>Az AD szinkronizálása

A Contoso biztosítani szeretné, hogy közös identitást biztosíthat a helyszíni erőforrások eléréséhez és a felhőben. Ehhez, integrálva lesz az a helyszíni Active Directory Azure ad-ben. Ebben a modellben:

- Felhasználók és a szervezetek kihasználhatja hozzáférni a helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365 vagy más az internetes oldalakkal több ezer olyan egyetlen identitás.
- Rendszergazdák kihasználhatják a csoportok az Active Directory megvalósítása [szerepkör alapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) az Azure-ban.

Integrációs elősegítése érdekében a Contoso használja a [Azure AD Connect eszköz](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Telepítse és konfigurálja az eszközt a tartományvezérlőre, ha a helyi szinkronizálja a helyszíni AD-identitások az Azure ad-hez. 

### <a name="download-the-tool"></a>Az eszköz letöltéséhez


1. Az Azure Portalon, a Contoso rendszergazdák váltson **Azure Active Directory** > **az Azure AD Connect**, és töltse le az eszköz legújabb verzióját a kiszolgálóhoz, akkor használ, a szinkronizálás.

    ![AD Connect letöltése](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Elindítja a **AzureADConnect.msi** telepítés, a **gyorsbeállítások használata**. Ez a leggyakoribb telepítési, és a egy egyerdős topológiával, a Jelszókivonat-szinkronizálást a hitelesítéshez használható.

    ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. A **az Azure AD Connect**, akkor adja meg a hitelesítő adatokat az Azure AD (a képernyő CONTOSO\admin vagy contoso.com\admin) való kapcsolódáshoz.

    ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. A **csatlakozhat az AD DS**, akkor adja meg a hitelesítő adatokat a helyszíni AD.

     ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. A **konfigurálásra kész**, kattintanak **indítsa el a szinkronizálást, amint a konfigurálás befejeződik** azonnal elindítani a szinkronizálást. Ezután telepítse.

Vegye figyelembe:
- Contoso az Azure-bA közvetlen kapcsolattal rendelkezik. Ha a helyszíni AD egy proxyt, olvassa el ezt [cikk](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Az első szinkronizálást követően a helyszíni AD-objektumok az Azure AD-ben látható.

    ![A helyszíni AD az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- A Contoso informatikai csapat jelenik meg az egyes csoportok, a szerepkör alapján.

    ![A helyszíni AD-tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>RBAC beállítását

Azure [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) hozzáféréskezelést az Azure lehetővé teszi. Az RBAC használatával, meg lehet adni csak olyan mértékű hozzáférést a felhasználóknak frissíteniük kell feladatait. A megfelelő RBAC-szerepkörök hozzárendelése felhasználók, csoportok és alkalmazások egy hatókör szintjén. Szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. 

Contoso-rendszergazdák mostantól rendel az AD-csoportokat, akkor szinkronizálja a helyszíni szerepkörök.

1. Az a **ControlCobRG** erőforráscsoportot, kattintson **hozzáférés-vezérlés (IAM)** > **szerepkör-hozzárendelés hozzáadása**.
2. A **szerepkör-hozzárendelés hozzáadása** > **szerepkör**, > **közreműködői**, akkor válassza ki a **ContosoCobRG** AD-csoportot a listából. A csoport megjelenik majd a **kiválasztott tagok** listája. 
3. Ezek ismételje meg ezt az egyéb erőforráscsoportok ugyanazokkal az engedélyekkel (kivéve a **ContosoAzureAdmins**), a közreműködői engedélyekkel ad hozzá az AD-fiókot, amely megegyezik az erőforráscsoport.
4. Az a **ContosoAzureAdmins** AD-csoporthoz hozzárendelni a **tulajdonosa** szerepkör.

    ![A helyszíni AD-tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>3. lépés: Rugalmasság és a vészhelyreállítás megtervezése

### <a name="set-up-regions"></a>Állítsa be a régiók

Azure-erőforrások régiók belül vannak telepítve.

- Régiók régiócsoportokat, és az adatok tárolási helye, szuverenitását, megfelelőségi és rugalmassági követelmények földrajzi határokon belüli összes régió megfelel.
- A régió olyan adatközpontok együttese, amelyek tevődik össze. Ezek az adatközpontok késési értékkel definiált szegélyhálózat-alapú belül vannak üzembe helyezve, és dedikált regionális kis késésű hálózati kapcsolatot.
- Minden egyes Azure-régió párban áll egy másik régióban rugalmasság.
- Olvassa el [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/), és tudomásul [hogyan régióban van párosítva](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso döntött, hogy nyissa meg az USA keleti RÉGIÓJA 2 (Virginia található), az elsődleges régió, USA középső RÉGIÓJA (Iowa található), a másodlagos régióba. Van néhány, ennek oka:

- A Contoso-datacenter New Yorkban található, és a Contoso tekinthető késés a legközelebbi adatközpontba.
- Az USA keleti régiója 2 rendelkezik, a szolgáltatás és a termékek, amely a Contoso kell használnia. Nem minden Azure régiója nem ugyanaz a termékek és szolgáltatások rendelkezésre álló tekintetében. Áttekintheti [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/).
- USA középső RÉGIÓJA az USA keleti RÉGIÓJA 2 Azure párosított régió.

A hibrid környezetről fenyegetésként észlel, mivel a Contoso cégnek szüksége van, érdemes figyelembe venni a rugalmasság és a egy vész-helyreállítási stratégiát beépítése a régió tervezési meg. Széles körben egyetlen régióban üzembe helyezés, a tartalék tartományok és regionális rugalmasság, a párosítás keresztül, egy teljes aktív-aktív minta a cloud services és a database szolgáltatása, telepített és karbantartási az Azure platform stratégiák közötti a felhasználók a két régióban.

Contoso úgy döntött, hogy a középső úton igénybe. Alkalmazások és erőforrások egy elsődleges régióban üzembe lesz, és adatpéldányok teljes infrastruktúra a másodlagos régióban, hogy működjön, egy teljes biztonsági mentés esetén a teljes alkalmazás katasztrófa, vagy a regionális meghibásodással készen áll.

### <a name="set-up-availability-zones"></a>Állítsa be a rendelkezésre állási zónák

A rendelkezésre állási zónák segítségével az alkalmazások és adatok védelme az Adatközpont meghibásodása.

- Egyes rendelkezésre állási zónában az Azure régiókon belül egy egyedi fizikai hely.
- Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze. 
- Nincs legalább három különálló zónákra engedélyezett minden régióban.
- Egy adott régión belül zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat.

Contoso fog üzembe helyezni a rendelkezésre állási zónák alkalmazásként hívja meg a méretezhetőséget, a magas rendelkezésre állás és a rugalmasságot. [További információk](https://docs.microsoft.com/azure/availability-zones/az-overview). 


## <a name="step-4-design-a-network-infrastructure"></a>4. lépés: A hálózati infrastruktúra tervezése

A regionális tervezési helyen, a Contoso egy hálózati stratégiát érdemes készen áll. Úgy gondolja, hogy hogyan a helyszíni adatközpont és az Azure csatlakoztatása és kommunikálnak egymással, és az Azure-ban a hálózati infrastruktúra tervezése kell. Kifejezetten a Contoso kell:

- **Hibrid hálózati kapcsolatok tervezése**: Döntse el, hogyan fogja a helyszíni és az Azure hálózatok összekapcsolásához.
- **Az Azure-beli hálózati infrastruktúra tervezése**: Döntse el, hogyan elvégzi az üzembe helyezést hálózatokon keresztül régióban. Hogyan fog kommunikálni a hálózatok ugyanazon a régión belül és régiók között?
- **Tervezési és az Azure-hálózatok beállítása**: Azure-hálózatok és alhálózatok beállítása, és döntse el, mi helyezkednek el rajtuk.

### <a name="plan-hybrid-network-connectivity"></a>Hibrid hálózati kapcsolatok tervezése

Contoso számít egy [architektúrák száma](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) hibrid hálózatkezelés az Azure és a helyszíni adatközpont között. [További információ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) kapcsolatos lehetőségek összehasonlítása.

Ne feledje a Contoso a helyszíni hálózati infrastruktúrát jelenleg áll az adatközpontban, New York, és az Egyesült Államok keleti része az ágak helyi.  Minden hely egy üzleti osztály internetkapcsolattal rendelkezik.  A fiókok mindegyike kapcsolódik egy IPSec VPN-alagúton keresztül az Adatközpont az interneten keresztül.

![Contoso network](./media/contoso-migration-infrastructure/contoso-networking.png) 

Itt látható, hogyan Contoso úgy döntött, hogy a hibrid kapcsolatot:

1. Állítsa be egy új site-to-site VPN-kapcsolatot a Contoso-datacenter győrben és a két Azure-régió az USA 2. keleti régiója és USA középső RÉGIÓJA között.
2. Azure virtuális hálózatok ág office adatforgalomra a Contoso fő adatközpont keresztül irányítja át. 
3. Mivel a Contoso Azure-beli felskálázással, azt az adatközpont és az Azure-régiók között ExpressRoute kapcsolat hoznak. Ha ez történik, a Contoso megőrzi a VPN-helyek közötti kapcsolat csak feladatátvételi célból.
    - [További](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) kapcsolatos VPN és ExpressRoute hibrid megoldás közötti választáshoz.
    - Győződjön meg arról [ExpressRoute helyek és a támogatási](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Csak VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN és ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Az Azure hálózati infrastruktúra tervezése

Rendkívül fontos, hogy Contoso hálózatokat hozzon létre, amely lehetővé teszi a megfelelő hibrid üzembe helyezést, biztonságos és méretezhető módon. Ehhez a Contoso a hosszú távú megközelítés tart, és tervez, virtuális hálózatok (Vnetek) rugalmas és vállalati használatra. [További](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) virtuális hálózatok tervezésével kapcsolatban.

Csatlakozhat a két régióban, a Contoso egy eseményközpont-hub hálózati modell megvalósításához határozott meg:

- Az egyes régiókban a Contoso Központ-küllő modell fogja használni.
- Szeretne csatlakozni, hálózatok és hubok, Contoso fogja használni az Azure-hálózatok közötti társviszony.

#### <a name="network-peering"></a>Hálózatok közötti társviszony

Az Azure biztosít hálózati társviszony-létesítés virtuális hálózatok és -elosztók csatlakoztatása. Globális társhálózatok létesítésének lehetővé teszi, hogy a különböző régiókban található virtuális hálózatok/hubs közötti kapcsolatok. Az azonos régióban található virtuális hálózatok helyi társviszony-létesítés kapcsolódik. Virtuális hálózatok közötti társviszony biztosítanak számos előnnyel jár:

- Társviszonyban lévő virtuális hálózatok közötti hálózati adatforgalom nem nyilvános.
- A virtuális hálózatok közötti forgalom a Microsoft gerinchálózatán másolatok. A virtuális hálózatok közötti kommunikáció nincs nyilvános interneten, az átjárókat vagy a titkosításhoz szükséges.
- Társviszony-létesítés biztosít egy alapértelmezett, kis késleltetésű, nagy sávszélességű kapcsolat eltérő virtuális hálózatokba tartozó erőforrások között.

[További](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) hálózatok közötti társviszony létesítésével kapcsolatos.

#### <a name="hub-to-hub-across-regions"></a>Eseményközpont-az-hub-régiók között

Contoso fog üzembe helyezni egy központ minden régióban. A központ egy virtuális hálózat (VNet) az Azure-ban, amely a helyszíni hálózathoz való csatlakozási lehetőségek központi helye funkcionál. A hub használata a globális virtuális társhálózatok létesítésének egymáshoz virtuális hálózatok kapcsolódik. Globális virtuális társhálózatok létesítésének kapcsolódik a virtuális hálózatok az Azure-régiók között.

- Az egyes régiókban a hub társviszonyban áll a partner hubra más régióban található.
- A hub társviszonyban áll a régióban található minden hálózathoz és képes csatlakozni az összes hálózati erőforrásokhoz.

    ![Globális társviszony-létesítés](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Központ-küllő egy adott régión belül

Minden egyes régióban Contoso fog üzembe helyezni virtuális hálózatok különböző felhasználási célokra, mint a régió hub topológiájú hálózatokat. Egy régión belüli virtuális hálózatok használatával társviszony-létesítés a hubhoz, és egymást.

#### <a name="design-the-hub-network"></a>A hub hálózatot

A küllős modell, amely a Contoso úgy döntött, belül kell végiggondolni, hogyan lehet a helyszíni adatközpont és az internetről érkező forgalmat, lesznek irányítva. Itt látható, hogyan Contoso dönt, hogy kezelni az USA 2. keleti régiója és USA középső RÉGIÓJA hubs egyaránt útválasztás:

- Contoso van egy "fordított c", más néven hálózati tervez, mivel ez az, hogy a csomagok kimenő bejövő hálózati elérési út.
- A hálózati architektúra két határokat, egy nem megbízható előtér peremhálózati és egy háttér-megbízható zónába tartozik.
- A tűzfal egy hálózati adapter lesz minden egyes zónában megbízható zónákhoz való hozzáférés szabályozása.
- Az internetről:
    - Internetes forgalom letiltottá terheléselosztott nyilvános IP-cím a szegélyhálózaton.
    - Az adatforgalmat a tűzfalon keresztül, és a tűzfal-szabályok érvényesek.
    - Hálózati hozzáférés-vezérléssel vannak bevezetése után a forgalmat továbbítja a megfelelő helyre a megbízható helyek zónájába.
    - A virtuális hálózatok közötti kimenő forgalom az internethez, felhasználó által megadott útvonalak (udr-EK) használatával történik. A forgalom kényszerített a tűzfalon keresztül, és megfelelően Contoso házirendek megvizsgálni.
- A Contoso adatközpontból:
    - VPN-helyek (vagy ExpressRoute) bejövő forgalom eléri az Azure VPN-átjáró nyilvános IP-címét.
    - Adatforgalmat a tűzfalon keresztül, és a tűzfal-szabályok érvényesek.
    - Alkalmazta, hogy a tűzfalszabályok, forgalmat egy belső terheléselosztót (standard szintű Termékváltozat) a belső megbízható zóna alhálózaton lesznek továbbítva.
    - A VPN-kapcsolaton keresztül a helyszíni adatközpontba a megbízható alhálózatról kimenő forgalmat a tűzfal és a szabályok a alkalmazni, mielőtt továbblépne a site-to-site VPN-kapcsolaton keresztül továbbít.



### <a name="design-and-set-up-azure-networks"></a>Tervezési és az Azure-hálózatok beállítása

A hálózati és helyen útválasztási topológiáját Contoso készen áll az Azure-hálózatok és alhálózatok beállítása.

- Contoso az Azure-ban (0.0.0.0 127.255.255.255) osztály egy magánhálózaton hajtja végre. Ez működik, a helyszíni óta vonatkoznak rá a B osztály privát cím terület 172.160.0/16, lehet, hogy a Contoso meg arról, hogy nem kell minden olyan címtartományai között átfedés.
- Az elsődleges és másodlagos régióban található virtuális hálózatok telepíteni fogja.
- Contoso fogja használni, amely tartalmazza az előtag elnevezési **VNET** és a régió rövidítés **EUS2** vagy **CUS**. Használja ezt a szabványt, a hub hálózatok lesznek elnevezve **VNET-HUB-EUS2** (USA keleti RÉGIÓJA 2), és **VNET-HUB-CUS** (USA középső RÉGIÓJA).
- Nem rendelkezik a Contoso egy [IP-Címkezelő megoldás](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), így a hálózati útválasztást, NAT nélkül tervezése


#### <a name="virtual-networks-in-east-us-2"></a>USA 2. keleti régiójában található virtuális hálózatok

USA keleti RÉGIÓJA 2 az elsődleges régióban, amely a Contoso-erőforrások és szolgáltatások telepítéséhez használandó. Itt látható, hogyan tervezhet a Contoso fog hálózatok:

- **Hub**: Az agyi virtuális hálózat, az USA 2. keleti régiójában az a központi pont az elsődleges kapcsolat a helyszíni adatközpontba.
- **Virtuális hálózatok**: Kelet-USA 2. küllő virtuális hálózatok számítási feladatok elkülönítésére, szükség esetén használható. Az agyi virtuális hálózat mellett a Contoso két küllő virtuális hálózatok, az USA keleti RÉGIÓJA 2 fog rendelkezni:
    - **VNET-DEV-EUS2**. A virtuális hálózat biztosítanak a fejlesztési, és a egy teljesen működőképes hálózattal fejlesztési projektek csapat tesztelése. Próbaüzem üzem területként fog működni, és a termelési infrastruktúrával függvényt fogja alkalmazni.
    - **VNET-PROD-EUS2**: Azure IaaS-éles összetevőket ezen a hálózaton található. 
    -  Minden egyes virtuális hálózat lesz a saját egyedi címtér nélkül. Contoso kíván konfigurálja az útválasztást, anélkül, hogy helyezkedik el.
- **Alhálózatok**:
    - Egy alhálózatot az kell minden hálózat minden egyes alkalmazás-szint
    - A termelési hálózat minden alhálózata egy egyező alhálózatot kell fejlesztés a virtuális hálózaton.
    - Emellett az éles hálózati környezetben van tartományvezérlők alhálózatát.

USA 2. keleti régiójában található virtuális hálózatok az alábbi táblázat foglalja össze.

**Virtuális hálózat** | **Címtartomány** | **Társ**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-ÉLES-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-PROD-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-ÉLES-KAPACITÁSEGYSÉGEK

![Hub/küllő elsődleges régióban](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Alhálózatok az agyban East US 2 hálózati (VNET-HUB-EUS2)

**Subnet/zone** | **CIDR** | ** Használható IP-címet
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**OB-UntrustZone** | 10.240.2.0/24 | 251
**OB-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Alhálózatok a East US 2 fejlesztési hálózatban (VNET-DEV-EUS2)

A fejlesztési virtuális hálózat a fejlesztői csapat próbaüzem üzem területként használja. Három alhálózatot tartalmaz.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**DEV-APP-EUS2** | 10.245.20.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Adatbázis-beli virtuális gépek


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Alhálózatok a East US 2 éles hálózaton (VNET-ÉLES-EUS2)

Azure IaaS-összetevőket az éles hálózati környezetben találhatók. Minden alkalmazás csomag esetében a saját alhálózatában. Alhálózatok megegyeznek a fejlesztési hálózat, alhálózat tartományvezérlők igény szerinti hozzáadásával.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**PROD-FE-EUS2** | 10.245.32.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**PROD-APP-EUS2** | 10.245.36.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**PROD-DB-EUS2** | 10.245.40.0/23 | 507 | Adatbázis-beli virtuális gépek
**PROD-DC-EUS2** | 10.245.42.0/24 | 251 | Tartományvezérlő virtuális gépek


![Hub hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>USA középső RÉGIÓJA (másodlagos régió) lévő virtuális hálózatok

USA középső Régiójában a Contoso másodlagos régióba. Itt látható, hogyan tervezhet a Contoso fog hálózatok:

- **Hub**: A hubon, az USA keleti RÉGIÓJA 2 virtuális hálózat az a helyszíni adatközpont és a küllő virtuális hálózatok, az USA keleti RÉGIÓJA 2 is használható, ha szükséges, számítási feladatok elkülönítésére való csatlakozási lehetőségek központi helye külön-külön kezelt más küllőktől elkülönülten kezelhetőek.
- **Virtuális hálózatok**: Contoso két virtuális hálózat, az USA középső Régiójában fog rendelkezni:
    - VNET-PROD-CUS. A virtuális hálózat egy üzemi hálózat, a VNET-PROD_EUS2 hasonló. 
    - VNET-ASR-CUS. A virtuális hálózat olyan hely, ahol virtuális gépek jönnek létre a helyszíni feladatátvétel után, vagy helyként fog működni az Azure virtuális gépek, melyek feladatai át az elsődleges kiszolgálóról a másodlagos régióba. Ez a hálózat hasonlít az éles hálózati környezetben, de nincs rajta a tartományvezérlők.
    -  Minden egyes régióban található virtuális hálózat lesz a saját címtér nélkül. Contoso fog konfigurálja az útválasztást, NAT nélkül
- **Alhálózatok**: Az alhálózatok a rendszer ezeket az USA keleti régiója 2 hasonló módon lett tervezve. A kivétel ez alól, hogy Contoso tartományvezérlők nincs szüksége alhálózatra.

Az alábbi táblázat foglalja össze a virtuális hálózatok, az USA középső Régiójában.

**Virtuális hálózat** | **Címtartomány** | **Társ**
--- | --- | ---
**VNET-HUB-CUS** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK
**VNET-ASR-CUS** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK
**VNET-PROD-CUS** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-PROD-EUS2  


![Hub/küllő párosított régióban](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>A velünk a Kapcsolatot a központi Agyhoz alhálózatok hálózati (VNET-HUB-CUS)

**Alhálózat** | **CIDR** | **Használható IP-címet**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**OB-UntrustZone** | 10.250.2.0/24 | 251
**OB-TrustZone** | 10.250.3.0/24 | 251
**Átjáró-alhálózat** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>A központi USA éles hálózaton (VNET-ÉLES-CUS) alhálózatok

Az éles hálózati környezetben az elsődleges régióban USA keleti RÉGIÓJA 2 párhuzamos nincs éles hálózati környezetben a másodlagos USA középső régiójában. 

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**PROD-FE-CUS** | 10.255.32.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**PROD-APP-CUS** | 10.255.36.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**PROD-DB-CUS** | 10.255.40.0/23 | 507 | Adatbázis-beli virtuális gépek
**PROD-DC-CUS** | 10.255.42.0/24 | 251 | Tartományvezérlő virtuális gépek

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Az USA középső RÉGIÓJA (VNET-ASR-CUS), az USA középső RÉGIÓJA feladatátvételi vagy helyreállítási hálózat alhálózatai

A VNET-ASR-CUS hálózati a régiók közötti feladatátvétel célokat szolgál. A Site Recovery replikálja, és átadja a feladatokat az Azure virtuális gépek régiók közötti használható. Azt is egy Azure-hálózathoz, amely a helyszínen maradnak, de a feladatátvételt az Azure-bA vész-helyreállítási védett munkaterhelésekhez Contoso adatközpontban működik.

VNET-ASR-Kapacitásegységek az üzemi virtuális hálózat, az USA keleti RÉGIÓJA 2, de a domain controller alhálózat nélkül alapszintű ugyanabban az alhálózatban.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**ASR-FE-CUS** | 10.255.16.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**ASR-APP-CUS** | 10.255.20.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**ASR-DB-CUS** | 10.255.24.0/23 | 507 | Adatbázis-beli virtuális gépek

![Hub hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Társviszonyban kapcsolat konfigurálása

A hub, az egyes régiókban lesznek létesíthető társviszony a hub más régióban található, és a hub régión belül minden virtuális hálózatokhoz. Ez lehetővé teszi az hubok való kommunikációra, és a egy adott régión belül minden virtuális hálózatok megtekintéséhez. Vegye figyelembe:

- Társviszony-létesítés kétoldalas kapcsolatot hoz létre. Egy a kezdeményező társ az első virtuális hálózaton, és egy másikat a második virtuális hálózaton.
- Az egy hibrid telepítésben egyenrangú társak közötti forgalom a VPN-kapcsolatot a helyszíni adatközpont és az Azure között látható kell. Ennek engedélyezéséhez nincsenek néhány speciális beállítással, hogy be kell állítani a virtuális társhálózatba tartozó kapcsolatok.

Minden olyan kapcsolatot küllő virtuális hálózatok a hubon keresztül a helyszíni adatközponthoz Contoso kell, hogy a továbbított és keresztirányú forgalomnak a VPN-átjárók.

##### <a name="domain-controller"></a>Tartományvezérlő

A tartományvezérlők a VNET-ÉLES-EUS2 hálózatban a Contoso biztosítani szeretné, mind a EUS2 hub/éles hálózati környezetben között, valamint a helyszíni VPN-kapcsolaton haladjon a forgalom. Ehhez az informatikai rendszergazdák Contoso engedélyeznie kell a következő:

1. **Továbbított forgalom engedélyezése** és **átjáró átviteli konfigurációk engedélyezése** a társviszonyban kapcsolat. Ebben a példában ez a VNET-HUB-EUS2 VNET-ÉLES-EUS2 kapcsolatra lenne.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering1.png)

2. **Továbbított forgalom engedélyezéséhez** és **távoli átjárók használata** többi oldalán a társviszony, a VNET-ÉLES-EUS2 VNET-HUB-EUS2 kapcsolatra.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering2.png)

3. A helyszíni értékre állítjuk be egy statikus útvonalat, amely a helyi forgalom irányítása a virtuális hálózathoz a VPN-alagúton keresztül irányítja. A konfigurációs szeretné befejezni az átjáró, amely a VPN-alagút biztosít a Contoso Azure-bA. Ennek az RRAS használata.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Éles hálózati környezetben 

Egy spoked egyenrangú hálózat nem látható egy spoked egyenrangú hálózat egy központon keresztül egy másik régióban.

A Contoso éles hálózati környezetben megtekintéséhez egymással mindkét régióban Contoso rendszergazdák kell közvetlen társviszonyban kapcsolat létrehozása a VNET-ÉLES-EUS2 és MŰVELE-ÉLES-kapacitásegység használható. 

![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Állítsa be a DNS

Erőforrások a virtuális hálózatok központi telepítése esetén több lehetősége a tartománynevek feloldását rendelkezik. Használja az Azure által biztosított névfeloldást, vagy adja meg a DNS-kiszolgálók a feloldásához. A névfeloldás használata típusa attól függ, hogyan kell az erőforrások kommunikálni egymással. Első [bővebben](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) az Azure DNS szolgáltatással kapcsolatban.

Contoso a rendszergazdák úgy döntött, hogy az Azure DNS szolgáltatásban nem jó választás a hibrid környezetben. Ehelyett használhatja a helyi DNS-kiszolgálók fogjuk azokat.

- Mivel ez egy hibrid hálózat összes a virtuális gépek a helyszínen és az Azure-ban képeseknek kell lenniük feloldani a megfelelő működéséhez. Ez azt jelenti, hogy a virtuális hálózatok egyéni DNS-beállításokat kell alkalmazni.
- Contoso jelenleg rendelkezik a Contoso az adatközpontban és a fiókirodák telepített tartományvezérlő. Az elsődleges DNS-kiszolgálók CONTOSODC1(172.16.0.10) és CONTOSODC2(172.16.0.1)
- Ha a virtuális hálózatok vannak telepítve, a helyi tartományvezérlő a hálózat DNS-kiszolgálóként használandó lesz beállítva. 
- Ennek konfigurálásához, ha egyéni DNS használata a virtuális hálózat az Azure rekurzív feloldók IP-címet (például a 168.63.129.16) a DNS-listához hozzá kell adni.  Ehhez a Contoso DNS-kiszolgáló beállításainak konfigurálja az egyes virtuális hálózatok. Például a VNET-HUB-EUS2 hálózati egyéni DNS-beállításainak a következő lesz:
    
    ![Egyéni DNS](./media/contoso-migration-infrastructure/custom-dns.png)

A helyszíni tartományvezérlők mellett Contoso implementálási négy további-támogatja az Azure-hálózatok, két mindegyik régióhoz. Íme, mi Contoso telepíti az Azure-ban.

**Régió** | **DC** | **Virtuális hálózat** | **Alhálózat** | **IP-cím**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-PROD-EUS2 | PROD-DC-EUS2 | 10.245.42.5
KAPACITÁSEGYSÉG HASZNÁLHATÓ | CONTOSODC5 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4
KAPACITÁSEGYSÉG HASZNÁLHATÓ | CONTOSODC6 | VNET-PROD-CUS | PROD-DC-CUS | 10.255.42.4

A helyszíni tartományvezérlők üzembe helyezése után Contoso cégnek szüksége van, az új tartományvezérlők szerepeljenek a DNS-kiszolgálók listáját hálózatok mindkét régió, a DNS-beállításainak frissítéséhez.



#### <a name="set-up-domain-controllers-in-azure"></a>Állítsa be a tartományvezérlőket az Azure-ban

Miután frissítette a hálózati beállítások, Contoso rendszergazdák építse ki az Azure-ban a tartományvezérlők készen áll.

1. Az Azure Portalon, a megfelelő virtuális hálózatot egy új Windows Server virtuális gép üzembe helyezése.
2. Rendelkezésre állási csoportok az egyes helyeken a virtuális gép létrehozása azokat. A rendelkezésre állási csoportok tegye a következőket:
    - Győződjön meg arról, hogy az Azure-hálót a virtuális gépek elkülöníti azokat különböző infrastruktúrákat az Azure-régióban. 
    -  Lehetővé teszi, hogy a Contoso a 99,95 %-os SLA-t az Azure-beli virtuális gépek jogosult legyen.  [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Rendelkezésre állási csoport](./media/contoso-migration-infrastructure/availability-group.png) 
3. A virtuális gép telepítése után, nyissa meg a hálózati adaptert a virtuális gép. Statikus magánhálózati IP-cím beállítva, és adjon meg egy érvényes címet.

    ![VIRTUÁLIS GÉP HÁLÓZATI ADAPTER](./media/contoso-migration-infrastructure/vm-nic.png)

4. Most, akkor új adatlemez csatolása a virtuális géphez. Ezt a lemezt tartalmaz, az Active Directory-adatbázis és a sysvol-megosztás. 
    - A lemez mérete határozza meg az iops-érték, amely támogatja a száma.
    - Idővel a lemez mérete esetleg növelnie kell a környezet növekedésével.
    - A meghajtó-állomás gyorsítótárazását az olvasási/írási nem állítható be. Az Active Directory-adatbázisok nem támogatják ezt.

     ![Az Active Directory-lemez](./media/contoso-migration-infrastructure/ad-disk.png)

5. A lemez hozzáadása után azok csatlakozzon a virtuális Géphez távoli asztali kapcsolaton keresztül, és nyissa meg a Kiszolgálókezelőt.
6. Ezt a **fájl- és tárolási szolgáltatások**, az új kötet varázsló, amely biztosítja, hogy a meghajtó a megadott F: betűvel vagy újabb futnak a helyi virtuális gépen.

     ![Új kötet varázsló](./media/contoso-migration-infrastructure/volume-wizard.png)

7. A Kiszolgálókezelőben adnak hozzá a **Active Directory Domain Services** szerepkör. Ezután ezek a virtuális gép konfigurálása tartományvezérlőként.

      ![Kiszolgálói szerepkör](./media/contoso-migration-infrastructure/server-role.png)  

9. Miután a virtuális gép egy tartományvezérlő konfigurálva, és újraindul, nyissa meg a DNS-kezelő és konfigurálása már az Azure DNS-feloldási továbbítóként.  Ez lehetővé teszi, hogy a tartományvezérlő nem oldható fel az Azure DNS DNS-lekérdezéseket.

    ![DNS forwarder](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Most, frissítse az egyéni DNS-beállítások az egyes virtuális hálózatok a megfelelő tartományvezérlő számára, a virtuális hálózathoz. A helyszíni tartományvezérlők tartalmazzák a listában.

### <a name="set-up-active-directory"></a>Active Directory beállítása

AD egy kritikus fontosságú szolgáltatás a hálózatkezelés, és megfelelően kell konfigurálni. Contoso-rendszergazdák a Contoso-datacenter, valamint a EUS2 és kapacitásegység használható régiókat AD helyekben fog létrehozni.  

1. Akkor hozzon létre az Adatközpont-hely (ContosoDatacenter) és két új hely (az AZURE-EUS2 és az AZURE-CUS).
2. Miután létrehozta a helyek, a helyek, a virtuális hálózatok és az Adatközpont megfelelő alhálózatok létrehozása azokat.

    ![Tartományvezérlő alhálózatok](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Ezután, hozzon létre két helykapcsolatok mindent csatlakozni. A tartományvezérlők helyükre majd át.

    ![DC-hivatkozások](./media/contoso-migration-infrastructure/dc-links.png)

4. Miután minden be van állítva, az Active Directory replikációs topológiájának van.
    
    ![Tartományvezérlő replikációs](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Minden kész, és a tartományvezérlők és helyek listáját a helyszíni Active Directory felügyeleti központ láthatók.

    ![AD felügyeleti központban](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>5. lépés: Cégirányítási tervezése

Az Azure széles körű cégirányítási vezérlők lehet a szolgáltatások és az Azure platform biztosít. [További információ](https://docs.microsoft.com/azure/security/governance-in-azure) vonatkozó beállítások alapvető ismeretekkel.

Konfigurálása identitás és hozzáférés-vezérlés, a Contoso már megkezdődött helyet a egyes cégirányítási és biztonsági funkcióit helyezi. Széles körben három olyan terület kell figyelembe venni:

- **A házirend**: Az Azure-ban a házirend vonatkozik, és kikényszeríti szabályok és hatások az erőforrások felett, hogy megfelelnek a vállalati követelményeknek és szolgáltatói szerződésekkel.
- **Zárolja**: Az Azure lehetővé teszi zárolási előfizetések, erőforráscsoportok és más erőforrások, így azok csak az ehhez a szolgáltató által módosíthatók.
- **A címkék**: Erőforrások szabályozza, naplózza és címkékkel rendelkező felügyelt. Címkék metaadatok csatlakoztathat erőforrásokat, erőforrások, illetve eszköztulajdonosok vonatkozó információkat.

### <a name="set-up-policies"></a>Házirendek beállítása

Az Azure Policy szolgáltatást kiértékeli az erőforrásokat, és azokat, amelyek nem felelnek meg a már működik a szabályzatdefiníciók vizsgálatát. Előfordulhat például, hogy egy szabályzatot, amely csak lehetővé teszi, hogy bizonyos típusú virtuális gépek, illetve egy adott címkével rendelkeznie erőforrásokra van szükség. 

Azure házirendek határozzák meg a szabályzat-definíció, és a egy szabályzat-hozzárendelés megadja a hatókör, amelyben egy szabályzatot kell alkalmazni. A hatókör között lehet a felügyeleti csoport egy erőforráscsoporthoz. [Ismerje meg,](../governance/policy/tutorials/create-and-manage.md) vonatkozó szabályzatok létrehozása és kezelése.

A Contoso biztosítani szeretné néhány szabályzatok – első lépések:

- Győződjön meg arról, hogy erőforrások kizárólag a EUS2 és CUS régióban telepíthető a szabályzatot szeretné.
- Szeretné korlátozni a virtuális gépek Termékváltozatait csak a jóváhagyott termékváltozatokra. A szándéka, hogy győződjön meg arról, hogy költséges VM Termékváltozatokban nem használja.

#### <a name="limit-resources-to-regions"></a>Korlát erőforrások, régiók

Contoso használja a beépített szabályzatdefiníciót **engedélyezett helyek** erőforrás régiók korlátozása.

1. Az Azure Portalon kattintson a **minden szolgáltatás**, és keressen rá a **házirend**.
2. Válassza ki **hozzárendelések** > **szabályzat hozzárendelése**.
3. A házirend listán válassza ki a **engedélyezett helyek**.
4. Állítsa be **hatókör** az Azure-előfizetést, és válassza a két régióban, az engedélyezett listában szereplő nevével.

    ![A szabályzat engedélyezett régiók](./media/contoso-migration-infrastructure/policy-region.png)

5. Alapértelmezés szerint a szabályzat van beállítva, a **Megtagadás**, ami azt jelenti, hogy ha valaki egy központi telepítési az előfizetést, amely nem a EUS2 vagy CUS elindul, a telepítés meghiúsul. Íme, mi történik, ha a Contoso előfizetésben valaki megpróbálja-telepítés beállítása az USA nyugati adatközpontjába.

    ![Nem sikerült a házirend](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Lehetővé teszi az adott VM-termékváltozatok

Contoso fogja használni a beépített szabályzatdefiníciót **engedélyezéséhez, hogy a virtuális gépek termékváltozatok** az előfizetésben létrehozott virtuális gépek korlátozására. 

![A házirend Termékváltozat](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Szabályzatoknak való megfelelőség ellenőrzése

Szabályzatok azonnal lép érvénybe, és a Contoso erőforrások megfelelőségének ellenőrzéséhez. 

1. Az Azure Portalon kattintson a **megfelelőségi** hivatkozásra.
2. Az eszközmegfelelőségi irányítópult jelenik meg. További részleteket is részletezi.

    ![Szabályzatmegfelelőség](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Zárolások beállítása

Contoso hosszú használja ITIL keretében a felügyeleti rendszerét. Egyik legfontosabb szempontja a keretrendszer a változások, és győződjön meg arról, hogy változáskezelésre megvalósítása az Azure-telepítés, a Contoso biztosítani szeretné.

Contoso zárolások megvalósítása a következő történik:

- Egy erőforráscsoport, amely olyan írásvédett zárolással rendelkezik minden olyan éles vagy feladatátvételi összetevő kell lennie.  Ez azt jelenti, hogy vagy éles elemek törlése, a zárolás el kell távolítani. 
- Nem éles üzemi erőforráscsoportok zárolások védve lesz. Ez azt jelenti, hogy a jogosult felhasználók is olvasni vagy módosítani az erőforrást, de nem lehet törölni.

[További](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) zárolások kapcsolatban.

### <a name="set-up-tagging"></a>Állítsa be a címkézés

Erőforrások nyomon követésére, mivel a rendszer hozzáadja, csak Contoso erőforrások társítása egy megfelelő részleg, a vevő és a környezet számára egyre fontosabb. 

Amellett, hogy az erőforrások és a tulajdonosok kapcsolatos információkat, a címkék lehetővé teszi Contoso összesítő és csoport-erőforrásokat, és az adatokat a jóváírási célból.

Contoso cégnek szüksége van, úgy, hogy megfelel az üzleti az Azure-objektumok megjelenítéséhez. Például a szerepkör vagy a részleg. Vegye figyelembe, hogy erőforrások találhatók ugyanabban az erőforráscsoportban egy címke megosztása nem szükséges. Contoso hoz létre egy egyszerű címkerendszert, így mindenki ugyanazt a címkét használja.

**Címke neve** | **Érték**
--- | ---
Költséghely | 12345: SAP-érvényes költséghely kell lennie.
Részleghez | (Az SAP) üzleti egység nevét. Matches CostCenter.
ApplicationTeam | Az alkalmazás támogatására vonatkozó birtokló csoport e-mail-aliasát.
CatalogName | Neve az alkalmazás vagy ShareServices, a szolgáltatáskatalógus, az erőforrás által támogatott száma.
ServiceManager | E-mail-alias az ITIL Service Manager az erőforráshoz.
COBPriority | A vállalat által az BCDR beállított prioritás. 1 – 5 értékét.
ENV | DEV, STG, ÉLES lehetséges értékei. Jelölő fejlesztésével, átmeneti és éles környezetben.

Példa: 

 ![Az Azure-címkék](./media/contoso-migration-infrastructure/azure-tag.png)

Miután létrehozta a címke, Contoso visszaléphet, és hozzon létre új Azure szabályzat-definíciókat és hozzárendeléseket, a szükséges címkéket használatának kényszerítése a szervezeten belül.


## <a name="step-6-consider-security"></a>6. lépés: Fontos a biztonság

Biztonsági elengedhetetlen a felhőben, és az Azure széles választékának biztonsági eszközöket és képességeket nyújt. Ezek segítségével könnyebben hozhat létre, olyan biztonságos megoldásokat a biztonságos Azure-platformon. Olvasási [bizalom a megbízható felhőben](https://azure.microsoft.com/overview/trusted-cloud/) tudhat meg többet az Azure security.

Néhány szempontot kell figyelembe venni a Contoso esetében:

- **Azure Security Center**: Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. A Security Centerrel biztonsági szabályzatokat alkalmazhat a számítási feladatokra, korlátozhatja a fenyegetéseknek való kitettséget, valamint észlelheti és elháríthatja a támadásokat.  [További információk](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Hálózati biztonsági csoportok (NSG-k)**: Az NSG-t egy szűrőt (tűzfal), amely felsorolja azokat a biztonsági szabályok, amelyek alkalmazásakor, vagy az Azure vnetekhez csatlakoztatni erőforrásokra irányuló hálózati forgalom megtagadásához. [További információk](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Adattitkosítás**: Az Azure Disk Encryption egy olyan funkció, amely segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. [További információk](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Az Azure Security Center használata

Contoso keres egy gyors nézetet az új hibrid felhő és a kifejezetten az Azure számítási feladatok biztonsági rendszeréről.  Ennek eredményeképpen Contoso határozott megvalósítása az Azure Security Center indítása a következő funkciókkal: 

- Központosított szabályzatkezelés
- A folyamatos értékelés
- Végrehajtható javaslatok 

#### <a name="centralize-policy-management"></a>Központosíthatja a Csoportházirend kezelése

Központosított szabályzatkezelés, a Contoso fogja, hogy biztonsági követelményeknek való megfelelés biztonsági szabályzatainak központi kezelésével a teljes környezetben. Ez egyszerűen és gyorsan valósíthat meg egy szabályzatot, amely minden, az Azure-erőforrások vonatkozik.

![Biztonsági házirend](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Mérje fel, és a művelet

Contoso használja a folyamatos biztonsági értékelés, amely figyeli a biztonság, a gépek, hálózatok, tárolás, adatok és alkalmazások; felderítheti a potenciális biztonsági problémákat. 

- A Security Center elemzi a Contoso számítási, az infrastruktúra és erőforrásokat, valamint az Azure-alkalmazások és szolgáltatások biztonsági állapotát.
- A folyamatos értékelés segít felderíteni a potenciális biztonsági problémákat, például a rendszer a hiányzó biztonsági frissítések, a Contoso üzemeltetési csapat, vagy hálózati portok közzétéve. 
- Különösen a Contoso biztosítani szeretné, hogy a virtuális gépek védett. Security Center segítségével, a virtuális gép állapotának ellenőrzése, és a biztonsági rések javítása, mielőtt azok már kihasználták a rangsorolt és végrehajtható javaslatokat.

![Figyelés](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Az NSG-k használata

Contoso korlátozhatja a hálózati biztonsági csoportok használata a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom.

- A hálózati biztonsági csoport egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy a cél IP-címe, illetve portok vagy protokollok alapján.
- Ha egy alhálózathoz, szabályok érvényesek az alhálózaton található összes erőforrást. Hálózati adapterek mellett ez tartalmazza a példányok az Azure-szolgáltatások üzembe helyezheti az alhálózaton.
- Az alkalmazásbiztonsági csoportok (asg-k) lehetővé teszi egy alkalmazás struktúra, amely lehetővé teszi a virtuális gépek természetes bővítményeként konfigurálhatja a hálózati biztonságot, és ezen csoportok alapján a hálózati biztonsági szabályzatok meghatározása.
    - Az alkalmazásbiztonsági csoportok jelenti azt, hogy a Contoso felhasználhatja a biztonsági szabályokat nagy léptékben, konkrét IP-címek manuális karbantartása nélkül. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat.
    - Contoso megadható egy alkalmazásbiztonsági csoportot a forrás- és a biztonsági szabályban. A biztonsági szabály meghatározása után a Contoso is virtuális gépek létrehozása és a virtuális gép hálózati adaptereket hozzárendelése egy csoporthoz. 


Contoso fogja végrehajtani az NSG-k és alkalmazásbiztonsági csoporttal. NSG-kezelésről feladata a contoso. Azt a is aggódik a túlzott az NSG-k és üzemeltetési csapat számára a hozzáadott összetettség. Íme, mi Contoso tegye:

- Minden forgalmat, és kimenő (észak – dél), minden alhálózat egy NSG-szabályt, kivéve a Hub hálózatokban a GatewaySubnets érvényesek lesznek.
- Minden olyan tűzfallal vagy a tartományvezérlő által alhálózati NSG-k és a hálózati adapter NSG-ket is védi.
- Az összes éles üzemi alkalmazások alkalmazásbiztonsági csoporttal a alkalmazni kell.


Contoso felépítette egy modellt, ez megjelenését a rajtuk található alkalmazások számára.

![Biztonság](./media/contoso-migration-infrastructure/asg.png)


Az alkalmazásbiztonsági csoporttal társított NSG-k, győződjön meg arról, hogy csak engedélyezett csomagok is flow, a hálózat egy részét a cél minimális jogosultságokkal rendelkező lesz konfigurálva.

**Művelet** | **Name (Név)** | **Forrás** | **Target** | **Port**
--- | --- | --- | --- | --- 
Engedélyezés | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | AZ APP1-FE 80-AS, 443-AS PORTON
Engedélyezés | AllowWebToApp | APP1-FE | APP1-DB | 1433
Engedélyezés | AllowAppToDB | APP1-APP | Bármelyik | Bármelyik
Megtagadás | DenyAllInbound | Bármelyik | Bármelyik | Bármelyik

### <a name="encrypt-data"></a>Adatok titkosítása

Az Azure Disk Encryption integrálható az Azure Key Vault segítségével szabályozhatja, és a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés kezelésére. Ez biztosítja, hogy minden adat a Virtuálisgép-lemezek vannak titkosítása az Azure storage-ban.  

- Contoso megállapította, hogy az adott virtuális gépek titkosítás megkövetelése.
- Contoso akkor alkalmazza, virtuális gépek ügyféllel, bizalmas, titkosítás vagy a PPI-adatokat.


## <a name="conclusion"></a>Összegzés

Ez a cikk egy Azure-infrastruktúra és az Azure-előfizetéssel, a házirend beállítása Contoso azonosítása hibrid, vészhelyreállítás, hálózatkezelés, cégirányítási és biztonsági. 

Nem minden Contoso itt végrehajtott lépések szükségesek a felhőre való áttelepítést. Ebben az esetben azt szeretne minden típusú migrálások felhasználható a hálózati infrastruktúra tervezése és biztonságos, rugalmas és méretezhető. 

Az infrastruktúra, a Contoso lépés, és próbálja ki az áttelepítési készen áll.

## <a name="next-steps"></a>További lépések

Első áttelepítési forgatókönyvekben, mint Contoso fog [mérje fel a helyszíni SmartHotel360 kétszintű alkalmazás migrálását az Azure](contoso-migration-assessment.md). 
