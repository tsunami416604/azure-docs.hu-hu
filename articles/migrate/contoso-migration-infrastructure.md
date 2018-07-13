---
title: Contoso – egy áttelepítési infrastruktúra beállítása |} A Microsoft Docs
description: Ismerje meg, hogy beállítja a Contoso egy Azure-infrastruktúra az Azure-ba való migrálásra.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: bf1406c8e361e0a1433b0e26c477c3c34e987fcf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38562758"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - áttelepítési infrastruktúra üzembe helyezése

Ez a cikk megvizsgálja, hogy beállítja a Contoso egy helyszíni és az Azure infrastruktúra előkészítése az Azure-ba történő migrálásra, valamint a hibrid környezetben futó üzleti.

- Egy adott Contoso mintaarchitektúra.
- Az áttelepítési stratégiáját függ, hogy kell-e a cikkben leírt összes elemet. Például ha az Azure-ban csak felhőbeli natív alkalmazásokat fejleszt, szüksége lehet egy kevésbé összetett hálózati struktúrát.

Ez a dokumentum a második cikksorozatot, amely dokumentálja hogyan a fiktív Contoso áttelepíti a helyszíni erőforrások a Microsoft Azure felhőbe. A sorozat háttér-információkat is tartalmaz, és a központi telepítési forgatókönyv egy készletét, amely bemutatja, hogyan állítható be a migrálási infrastruktúra felmérheti a a helyszíni erőforrások migrálásra való alkalmasságát, és futtassa a különböző típusú áttelepítéseket. Forgatókönyvek egyre összetettebbé válnak, és adunk hozzá további cikkek idővel.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[1. cikk: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk sorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
2. cikk: Üzembe helyezése egy Azure-infrastruktúra (a jelen cikkben) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Az összes Contoso áttelepítési forgatókönyvek ugyanazon az infrastruktúrán használható. | Elérhető
[3. cikk: A helyszíni erőforrások értékelése](contoso-migration-assessment.md) | Bemutatja, hogyan Contoso fut a VMware-en futó helyszíni kétrétegű SmartHotel alkalmazás értékelése. Mérje fel az alkalmazás virtuális gépek a [Azure Migrate](migrate-overview.md) szolgáltatás és az alkalmazás SQL Server-adatbázisnak a [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a egy felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Az alkalmazás előtérbeli virtuális gépet át [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az adatbázis használatával a [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás migrálása a felügyelt SQL-példányt. | Elérhető
[Cikk 5: Az Azure virtuális gépeken áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan a Contoso át SmartHotel alkalmazásuk virtuális gépek csak a Site Recovery használatával.
[A cikk 6: Újratárolás az Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan telepíti át a Contoso a SmartHotel alkalmazást. A Site Recovery számára, hogy az alkalmazás virtuális gépeit és a egy SQL Server rendelkezésre állási csoportot az alkalmazás-adatbázis áttelepítése a Database Migration service használnak. | Elérhető
[7. cikk: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso telepíti át a Linux-osTicket alkalmazás Azure virtuális gépekre. | Elérhető
[A cikk 8: Egy Linux alkalmazás Újratárolása az Azure virtuális gépek és az Azure MySQL-kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti az Azure MySQL Server-példány a Linux osTicket alkalmazás migrálása a Site Recovery és a MySQL Workbench segítségével (biztonsági mentés és visszaállítás). | Elérhető

Ez a cikk az infrastruktúra-elemek beállítása Contoso szükségük az áttelepítési forgatókönyvek végrehajtásához. 


## <a name="overview"></a>Áttekintés

Mielőtt nekik is át az Azure-ba, rendkívül fontos, hogy a Contoso az infrastruktúra előkészítése.  Általában akkor kell kidolgoznia öt széles körű területből áll:

**1. lépés: Az Azure-előfizetések**: hogyan lesz, az Azure megvásárlása, és dolgozhat az Azure-platform és szolgáltatások?  
**2. lépés: A hibrid identitás**: hogyan fog, kezelése és az áttelepítés után a helyszíni és Azure-erőforrásokhoz való hozzáférésének? Hogyan azok bővítése vagy Identitáskezelés áthelyezése a felhőbe?  
**3. lépés: Vész-helyreállítási és rugalmasság**: hogyan fog, ellenőrizze, hogy az alkalmazások és infrastruktúra rugalmas leállások és katasztrófák kezelése fordulhat elő, ha?  
**4. lépés: Hálózatkezelés**: hogyan kell azokat a hálózati infrastruktúra tervezése, és a helyszíni adatközpont és az Azure közötti kapcsolatot?  
**5. lépés: Biztonsági**: hogyan fogja azokat biztonságos hibrid/Azure-beli üzembe helyezésükben?  
**6. lépés: Cégirányítási**: hogyan azok biztosítják az üzembe helyezés, biztonsági és cégirányítási követelmények összhangban?

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdjük áttekinteni az infrastruktúra, érdemes olvassa el ebben a cikkben bemutatjuk az Azure-képességek néhány háttér-információkat:

- Nincsenek elérhető az Azure a hozzáférés, beleértve a használatalapú fizetés, a nagyvállalati szerződés (EA) megvásárlása számos lehetőséget, vagy a Felhőszolgáltatók (CSP) néven ismert Microsoft-viszonteladók, vagy a Microsoft Partners Open licencprogram. Ismerje meg [vásárlási lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), és megtudhatja, hogyan lehet [Azure-előfizetések vannak rendszerezve](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Az Azure áttekintése [identitás és hozzáférés-kezelés](https://www.microsoft.com/en-us/trustcenter/security/identity). Különösen ismertetése [Azure ad-ben, és kiterjeszti a helyszíni AD a felhőbe](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Nincs információ hasznos letölthető e-könyv [identitás és hozzáférés-kezelés (IAM) hibrid környezetben](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Az Azure robusztus hálózati infrastruktúrát hibrid kapcsolódási lehetőségeket biztosít. Áttekintheti a [hálózatkezelés és a hálózati hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/security-network-overview).
- Ismerkedjen meg [Azure biztonsági](https://docs.microsoft.com/azure/security/azure-security), és olvassa el a terv létrehozása [cégirányítási](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>A helyszíni architektúra

Íme az aktuális Contoso a helyszíni infrastruktúra bemutató ábra.

 ![Contoso-architektúra](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso a az városa New York a kelet-Egyesült Államok található egy fő adatközpont rendelkezik.
- Három további helyszíni ágakat Egyesült államokbeli városba rendelkeznek.
- A fő adatközpont fiber metro ethernet-kapcsolattal (500 MB/s) az internethez csatlakozik.
- Minden egyes fiókiroda helyi üzleti osztály kapcsolatok, térjen vissza a fő adatközpont IPSec VPN-alagutat az internetes csatlakozik. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpont teljes VMware-rel lesz virtualizálva. Két ESXi 6.5-ös virtualizációs gazdagépeket, a vCenter Server 6.5-ös által felügyelt rendelkeznek.
- Contoso az Active Directory identitáskezelési és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők VMware virtuális gépek futtatását az adatközpontban. A tartományvezérlők helyi ágat, fizikai kiszolgálókon futnak.


## <a name="step-1-buy-and-subscribe-to-azure"></a>1. lépés: Vásárol, és iratkozzon fel az Azure

Contoso cégnek szüksége van, felderíthesse az Azure megvásárlása, hogyan tervezhet, előfizetések és szolgáltatásokat és erőforrásokat módjával.

### <a name="buy-azure"></a>Az Azure megvásárlása

A Contoso fog egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Ez magában foglalja az Azure-ban, amely feljogosítja arra őket, hogy jelentős előnnyel jár, többek között rugalmas számlázási lehetőségeket és optimalizált díjszabása előzetes pénzügyi kötelezettségvállalást.

- Contoso mi várható az éves Azure-költések kezelése lesz. Megállapodást, akkor, amikor azok fizetős teljes mértékben az első évben.
- Contoso kell használnia minden kötelezettségeiket, vagy az év, akkor elveszíti ezeket dollárban értékét.
- Ha valamilyen okból haladhatja meg a kötelezettségvállalásos és köszönhetően több, a Microsoft számláz őket a különbséget.
- Minden fenti kötelezettségvállalás járó költségek a azonos díjszabás, valamint azokat, a szerződésben lesz. Nincsenek nem úgy kapcsolaton át.

### <a name="manage-subscriptions"></a>Előfizetések kezelése

Után kell fizetnie, az Azure-hoz, Contoso kell csomagváltással kapcsolatos tájékoztatásért előfizetéseiket. Nagyvállalati szerződéssel rendelkeznek, és így az Azure-előfizetések száma nincs korlátozva, állíthatja be.

- Az Azure nagyvállalati beléptetés határozza meg, hogyan vállalati alakzat és Azure-szolgáltatásokat használ, és egy alapvető cégirányítási struktúrát határozza meg.
- Első lépésként Contoso eldöntöttük, egy struktúra (más néven egy enterprise scaffold a nagyvállalati beléptetés a. Használja azokat [Ez a cikk](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) könnyebben megérthetik, és a egy scaffold tervezéséhez.
- Most a Contoso egy funkcionális módszer használatával előfizetéseiket határozott meg.
    - A vállalati belső rendelkeznek, amely az Azure-költségvetési szabályozza egy informatikai részleghez. Ez az előfizetés csak csoport lesz.
    - Majd tovább ebben a modellben a jövőben, hogy más céges csoportok a nagyvállalati beléptetés a részlegek, csatlakozhat.
    - Az informatikai részleg belül Contoso két előfizetések, üzemi és fejlesztési van felépítve.
    - Ha a Contoso van szüksége a jövőben további előfizetéseket, azok kell hozzáférést, a házirendek és a megfelelőséget ezen előfizetések kezelése. Ők is látni fogják megtenni bevezetésével [az Azure felügyeleti csoportok](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), mint a fenti előfizetések réteget.

    ![Nagyvállalati struktúra](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Vizsgálja meg a licencelés

Előfizetések konfigurálva, a Contoso tekintse meg a Microsoft-licencelési. Licencelési stratégiájuk az erőforrásokat, amelyeket be szeretne migrálni az Azure, és hogyan Azure virtuális gépek és szolgáltatások kiválasztott és üzembe helyezett függ. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Az Azure-beli virtuális gépek üzembe helyezésekor, a standard szintű lemezképek tartalmaznak egy licenc, amely a Contoso számára a szoftverrel használt percalapú díja. Contoso azonban már egy hosszú távú a Microsoft ügyfél és EAs maradt, és nyissa meg a licenceket frissítési garancia (SA). 

Az Azure Hybrid Benefit értékelem lehetővé mentése az Azure virtuális gépek és az SQL Server számítási feladatok átalakítása vagy újbóli használata a Windows Server Datacenter és Standard edition-licenceket frissítési garanciával rendelkező Contoso az áttelepítéshez, költséghatékony módszert biztosít. Ez lehetővé teszi a számítási díjat fizeti egy alacsonyabb alapú virtuális gépek és az SQL Server Contoso. [További információk](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Licenc Mobility

SA keresztüli biztosít a Microsoft mennyiségi licenc ügyfelek például Contoso, a licenckövetelményeknek megfelelő kiszolgálói alkalmazásokat az Azure-beli aktív SA. Így nem szükséges új licenceket vásárolni. Nincs társított mobilitást díjak a meglévő licenceit is egyszerűen üzembe helyezhetők az Azure-ban. [További információk](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Foglaljon le előre megjósolható számítási feladatokkal példányai

Előre megjósolható számítási feladatokkal által mindig elérhető legyen a futó virtuális gépek igénylő. Például az üzleti alkalmazások, például egy SAP ERP-rendszer.  Másrészről kiszámíthatatlan számítási rendszer, amelynek a változó. Például virtuális gépek, amelyek során nagy kereslet, és nem csúcsidőre időpontokban ki.

![Fenntartott példány](./media/contoso-migration-infrastructure/reserved-instance.png) 

Használja a fenntartott példányok adott Virtuálisgép-példányok, akkor tudja, hogy az idő nagy időtartamok karban kell tartani, engedélyezések konzol kérheti le egy kedvezmény, és Priorizált kapacitást. Használatával [Azure Reserved Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/), valamint az Azure Hybrid Benefittel akár 82 %-a normál használatalapú ki Contoso takaríthat (2018 április) díjszabása.


## <a name="step-2-manage-hybrid-identity"></a>2. lépés: A hibrid Identitáskezelés

Így, és az identitás és hozzáférés-kezelés (IAM) Azure-erőforrásokhoz való felhasználói hozzáférés szabályozása az Azure-infrastruktúra melyekbe fontos lépés.  

- Contoso úgy dönt, hogy azok a helyszíni Active Directory kiterjesztheti a felhőbe, nem pedig egy új különálló rendszert az Azure-ban.
- Akkor hozzon létre egy Azure-alapú Active Directory ehhez.
- Contoso nem rendelkezik Office 365-höz a helyen, így szükségük van egy új Azure AD kiépítése.
- Az Office 365 az Azure AD felhasználói felügyelethez. Contoso Office 365 használja, ha azok lenne már rendelkezik egy Azure ad-ben kulcsfontosságú alapelve, és használja, amely az elsődleges AD.
- [További](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) az Office 365, Azure AD-ről, és ismerje meg [egy előfizetés hozzáadása](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) egy meglévő Azure ad-hez.

### <a name="create-an-azure-ad"></a>Hozzon létre egy Azure ad-ben

Contoso az Azure AD ingyenes kiadásának mobilalkalmazásoknak része egy Azure-előfizetést használ. Egy új AD-címtár adja hozzá a következőképpen:

1. Az a [az Azure portal](http://portal.azure.com/), Ugrás a Contoso **erőforrás létrehozása** > **identitás** > **Azure Active Directory**.
2. A **könyvtár létrehozása**, adja meg a könyvtár nevét, egy kezdeti tartománynevet és a régiót, amelyben az Azure AD-címtárban kell létrehozni.

    ![Hozzon létre az Azure ad-ben](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > A könyvtár létrehozásához az űrlap domainname.onmicrosoft.com rendelkezik egy kezdeti tartománynevet. A név nem módosítható vagy törölhető. Ehelyett szükségük van a regisztrált tartománynév hozzáadása az Azure ad-ben.

### <a name="add-the-domain-name"></a>A tartománynév hozzáadása

A standard szintű tartománynevét használja, Contoso kell hozzáadása egy egyéni nevet az Azure ad-hez. Ez a beállítás lehetővé teszi, hogy a rendszergazdák jól ismert felhasználóneveket rendeljen. Például egy felhasználó e-mail-címmel bejelentkezhet billg@contoso.com, ahelyett, hogy kellene billg@contosomigration.onmicrosoft.com. 

Állíthat be egyéni nevet, adja hozzá a könyvtárat, DNS-bejegyzés, és ellenőrizze a neve az Azure ad-ben.

1. A **egyéni tartománynevek** > **egyéni tartomány hozzáadása**, akkor hozzáadhatja a tartományt.
2. DNS-bejegyzés használhatja az Azure, regisztrálnia kell azt a tartományregisztrálónál. 

    - Az a **egyéni tartománynevek** listán, vegye figyelembe a DNS-információkat a neve. Contoso MX bejegyzés használ.
    - Ezek a névkiszolgáló ehhez hozzá kell férniük. Contoso esetén azok jelentkezett be a Contoso.com tartományhoz, és létrehozott egy új MX-rekordot az Azure AD-ben feljegyzett adatokkal által biztosított DNS-bejegyzést.  
1. Miután a DNS-rekordok propagálása a tartományhoz, a részletek nevében kattintanak **győződjön meg arról** a egyéni név ellenőrzése.

     ![Az Azure AD-DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>Állítsa be a helyszíni és Azure-csoportok és felhasználók

Most, hogy az Azure ad-ben működő, Contoso kell hozzáadni az alkalmazottak számára a helyszíni AD-csoportokat, amely szinkronizálja az Azure ad-hez. Azt javasoljuk, hogy a helyi csoport neve megegyezik az Azure-ban az erőforráscsoportok nevei használnak. Így könnyebben azonosíthatók legyenek az egyezések szinkronizálás céljából.

#### <a name="create-resource-groups-in-azure"></a>Erőforráscsoportok létrehozása az Azure-ban

Azure-erőforráscsoportok összegyűjtésére Azure-erőforrások. Műveletek végrehajtása a csoporton belül az erőforrásokat az Azure-erőforráscsoport azonosítója használata lehetővé teszi.

- Azure-előfizetések több erőforráscsoporthoz is rendelkezhet, de egy erőforráscsoportot csak akkor létezhet egyetlen előfizetésben jön létre.
- Emellett egyetlen rendelkezhet több olyan erőforrást, de egy erőforrás csak egyetlen csoporthoz is tartozhatnak.

Contoso beállítása Azure-erőforráscsoportot, az alábbi táblázat foglalja össze.

**Erőforráscsoport** | **Részletek**
--- | ---
**ContosoCobRG** | Ez a csoport tartalmazza (COB) üzleti folytonossági kapcsolódó összes erőforrást.  Ez magában foglalja a tárolók, amely a Contoso hoz létre az Azure Site Recovery szolgáltatás és az Azure Backup szolgáltatás használata során.<br/><br/> Ez is magában foglalja többek között az Azure Migrate és a Database Migration Service migráláshoz használt erőforrásokat.
**ContosoDevRG** | Fejlesztési és tesztelési erőforrásokat tartalmazó csoport.
**ContosoFailoverRG** | Ez a csoport erőforrások sikertelen alkotóelemeit zóna szolgál.
**ContosoNetworkingRG** | Ez a csoport összes hálózati erőforrást tartalmazza.
**ContosoRG** | Ez a csoport tartalmazza az éles alkalmazások és adatbázisok kapcsolódó erőforrásokat.

Ezek erőforráscsoportok a következőképpen hozhat létre:

1. Az Azure Portal > **erőforráscsoportok**, akkor a csoport hozzáadása.
2. Az egyes csoportokhoz, adja meg a nevét, az előfizetés, amely a csoport tartozik, és a régió.
3. Erőforráscsoportok megjelennek a **erőforráscsoportok** listája.

    ![Erőforráscsoportok](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Hozzon létre megfelelő biztonsági csoportokat a helyszíni

1. Contoso azok a helyszíni Active Directory biztonsági csoportokat, amelyek megfelelnek az Azure-erőforráscsoportok nevei nevű beállítását.
 
    ![A helyszíni AD biztonsági csoportok](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Felügyeleti szempontból során létrehoznak egy további olyan csoport, amely megjelenik az összes többi csoport. Ez a csoport lesz jogosult összes erőforráscsoport az Azure-ban. A globális rendszergazdák csak korlátozott számú hozzáadódik ehhez a csoporthoz.

### <a name="synchronize-ad"></a>Az AD szinkronizálása

Contoso szeretne közös identitást biztosíthat a helyszíni erőforrások eléréséhez és a felhőben. Ehhez az Azure ad-vel, integrálhatja a helyszíni Active Directory. Ebben a modellben:

- Felhasználók és a szervezetek kihasználhatja hozzáférni a helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365 vagy más az internetes oldalakkal több ezer olyan egyetlen identitás.
- Rendszergazdák kihasználhatják a csoportok az Active Directory megvalósítása [szerepkör alapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) az Azure-ban.

Integráció, a Contoso használat megkönnyítése érdekében a [Azure AD Connect eszköz](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Telepítse és konfigurálja az eszközt a tartományvezérlőre, ha a helyi szinkronizálja a helyszíni AD-identitások az Azure ad-hez. 

### <a name="download-the-tool"></a>Az eszköz letöltéséhez

1. Az Azure Portalon az Azure Ugrás **Azure Active Directory** > **az Azure AD Connect**, és letölti a legfrissebb verziót, az eszköz akkor használ, a szinkronizálás a kiszolgálóhoz.

    ![AD Connect letöltése](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Elindítja a **AzureADConnect.msi** telepítés használatával **gyorsbeállítások használata**. Ez a leggyakoribb telepítési, és a egy egyerdős topológiával, a Jelszókivonat-szinkronizálást a hitelesítéshez használható.

    ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. A **az Azure AD Connect**, akkor adja meg a hitelesítő adatokat az Azure AD (a képernyő CONTOSO\admin vagy contoso.com\admin) való kapcsolódáshoz.

    ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. A **csatlakozhat az AD DS**, akkor adja meg a hitelesítő adatait a helyszíni AD.

     ![Az AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. A **konfigurálásra kész**, kattintanak **indítsa el a szinkronizálást, amint a konfigurálás befejeződik** azonnal elindítani a szinkronizálást. Ezután telepítse.


- Contoso az Azure-bA közvetlen kapcsolattal rendelkezik. Ha a helyszíni AD egy proxyt, olvassa el ezt [cikk](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Az első szinkronizálást követően a helyszíni AD-objektumok az Azure AD-ben látható.

    ![A helyszíni AD az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- Contoso IT-részlegének jelennek meg az egyes csoportokban szerepkörük alapján.

    ![A helyszíni AD-tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>RBAC beállítását

Azure [szerepköralapú hozzáférés-vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) hozzáféréskezelést az Azure lehetővé teszi. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges. A megfelelő RBAC-szerepkörök hozzárendelése felhasználók, csoportok és alkalmazások egy hatókör szintjén. Szerepkör-hozzárendelés hatóköre egy előfizetés, erőforráscsoport vagy egyetlen erőforrás lehet. 

Contoso most szerepköröket rendelhet az azok szinkronizálja a helyszíni AD-csoportokat.

1. Az a **ControlCobRG** erőforráscsoportot, kattintson **hozzáférés-vezérlés (IAM)** > **Hozzáadás**.
2. A **engedélyek hozzáadása** > **szerepkör**, kiválasztják **közreműködői**, és válassza ki a **ContosoCobRG** AD-csoportot a listából. A csoport megjelenik majd **kiválasztott tagok** listája. 
3. Ezek ismételje meg ezt az egyéb erőforráscsoportok ugyanazokkal az engedélyekkel (kivéve a **ContosoAzureAdmins**), a közreműködői engedélyekkel ad hozzá az AD-fiókot, amely megegyezik az erőforráscsoport.
4. Az a **ContosoAzureAdmins** AD-csoporthoz hozzárendelni a **tulajdonosa** szerepkör.

    ![A helyszíni AD-tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>3. lépés: A rugalmasság és a vészhelyreállítás megtervezése

Azure-erőforrások régiók belül vannak telepítve.
- Régiók régiócsoportokat, és az adatok tárolási helye, szuverenitását, megfelelőségi és rugalmassági követelmények földrajzi határokon belüli összes régió megfelel.
- A régió olyan adatközpontok együttese, amelyek tevődik össze. Ezek az adatközpontok késési értékkel definiált szegélyhálózat-alapú belül vannak üzembe helyezve, és dedikált regionális kis késésű hálózati kapcsolatot.
- Minden egyes Azure-régió párban áll egy másik régióban rugalmasság.
- Olvassa el [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/), és tudomásul [hogyan régióban van párosítva](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Contoso úgy döntött, hogy nyissa meg a másodlagos régió, USA középső RÉGIÓJA és USA keleti RÉGIÓJA 2 (Virginia található), az elsődleges régióba. Van néhány, ennek oka:

- A Contoso-datacenter New Yorkban található, és a legközelebbi adatközpontba késés venni azokat.
- Az USA keleti régiója 2 rendelkezik, a szolgáltatás és a termékek kell használniuk. Nem minden Azure régiója nem ugyanaz a termékek és szolgáltatások rendelkezésre álló tekintetében. Áttekintheti [az Azure-termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/).
- USA középső RÉGIÓJA az USA keleti RÉGIÓJA 2 Azure párosított régió.

A hibrid környezetről úgy gondolja, Contoso kell figyelembe venni a rugalmasság és a egy vész-helyreállítási stratégiát beépítése saját régió tervezési kell. Széles körben egyetlen régióban üzembe helyezés, a tartalék tartományok és regionális rugalmasság, a párosítás keresztül, egy teljes aktív-aktív minta a cloud services és a database szolgáltatása, telepített és karbantartási az Azure platform stratégiák közötti a felhasználók a két régióban.

Contoso úgy döntött, hogy a középső úton igénybe. VNET-ÉLES-KAPACITÁSEGYSÉG HASZNÁLHATÓ.


## <a name="step-4-design-a-network-infrastructure"></a>A virtuális hálózat egy üzemi hálózat, a VNET-PROD_EUS2 hasonló.

VNET-AUTOMATIKUS RENDSZER-HELYREÁLLÍTÁSI-KAPACITÁSEGYSÉG HASZNÁLHATÓ. A virtuális hálózat olyan hely, ahol virtuális gépek jönnek létre a helyszíni feladatátvétel után, vagy helyként fog működni az Azure virtuális gépek, melyek feladatai át az elsődleges kiszolgálóról a másodlagos régióba. Ez a hálózat hasonlít az éles hálózati környezetben, de nincs rajta a tartományvezérlők.

**Minden egyes régióban található virtuális hálózat lesz a saját címtér nélkül.
**Alhálózatok**: az alhálózatok a rendszer ezeket az USA keleti régiója 2 hasonló módon lett tervezve. A kivétel ez alól, akkor nincs szüksége alhálózatra tartományvezérlők.
**Az alábbi táblázat foglalja össze a virtuális hálózatok, az USA középső Régiójában.

### <a name="plan-hybrid-network-connectivity"></a>VNET-HUB-KAPACITÁSEGYSÉGEK

10.250.0.0/20 [VNET-HUB-EUS2, VNET-ASR-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK

VNET-ASR-KAPACITÁSEGYSÉGEK  10.255.16.0/20  VNET-HUB-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK

![VNET-ÉLES-KAPACITÁSEGYSÉGEK](./media/contoso-migration-infrastructure/contoso-networking.png) 

10.255.32.0/20

1. VNET-HUB-CUS, VNET-ASR-CUS, VNET-ÉLES-EUS2
2. Hub/küllő párosított régióban 
3. A velünk a Kapcsolatot a központi Agyhoz alhálózatok hálózati (VNET-HUB-CUS) Használható IP-címet
    - [10.250.0.0/24
    - 10.250.1.0/24


**10.250.2.0/24**

![10.250.3.0/24](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**Átjáró-alhálózat**

![A központi USA éles hálózaton (VNET-ÉLES-CUS) alhálózatok](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Az éles hálózati környezetben az elsődleges régióban USA keleti RÉGIÓJA 2 párhuzamos nincs éles hálózati környezetben a másodlagos USA középső régiójában.

TERMÉKKATALÓGUS-FE-KAPACITÁSEGYSÉGEK 10.255.32.0/22 [ÉLES – ALKALMAZÁS-KAPACITÁSEGYSÉGEK

10.255.36.0/22

- ÉLES – ADATBÁZIS-KAPACITÁSEGYSÉGEK
- 10.255.40.0/23

#### <a name="network-peering"></a>Hálózatok közötti társviszony

ÉLES – DC-KAPACITÁSEGYSÉGEK 10.255.42.0/24 Az USA középső RÉGIÓJA (VNET-ASR-CUS), az USA középső RÉGIÓJA feladatátvételi vagy helyreállítási hálózat alhálózatai A VNET-ASR-CUS hálózati a régiók közötti feladatátvétel célokat szolgál.

- A Site Recovery replikálja, és átadja a feladatokat az Azure virtuális gépek régiók közötti használható.
- Azt is egy Azure-hálózathoz, amely a helyszínen maradnak, de a feladatátvételt az Azure-bA vész-helyreállítási védett munkaterhelésekhez Contoso adatközpontban működik. VNET-ASR-Kapacitásegységek az üzemi virtuális hálózat, az USA keleti RÉGIÓJA 2, de a domain controller alhálózat nélkül alapszintű ugyanabban az alhálózatban.
- ASR-FE-KAPACITÁSEGYSÉGEK

[10.255.16.0/22

#### <a name="hub-to-hub-across-regions"></a>ASR-ALKALMAZÁS-KAPACITÁSEGYSÉGEK

10.255.20.0/22 ASR-ADATBÁZIS-KAPACITÁSEGYSÉGEK A hub használata a globális virtuális társhálózatok létesítésének egymáshoz virtuális hálózatok kapcsolódik. Globális virtuális társhálózatok létesítésének kapcsolódik a virtuális hálózatok az Azure-régiók között.

- Az egyes régiókban a hub társviszonyban áll a partner hubra más régióban található.
- A hub társviszonyban áll a régióban található minden hálózathoz és képes csatlakozni az összes hálózati erőforrásokhoz.

    ![Globális társviszony-létesítés](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Központ-küllő egy adott régión belül

Minden egyes régióban Contoso fog üzembe helyezni virtuális hálózatok különböző felhasználási célokra, mint a régió hub topológiájú hálózatokat. Egy régión belüli virtuális hálózatok használatával társviszony-létesítés a hubhoz, és egymást.

#### <a name="design-the-hub-network"></a>A hub hálózatot

A küllős modell, amely a Contoso választotta, végiggondolni, hogyan lehet szükséges a helyszíni adatközpont és az internetről érkező forgalmat, lesznek irányítva. Itt látható, hogyan Contoso kezeli az USA 2. keleti régiója és USA középső RÉGIÓJA hubs egyaránt útválasztás választotta:

- Egy "fordított c", más néven hálózati azok tervez, mivel ez az, hogy a csomagok kimenő bejövő hálózati elérési út.
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
    - Szabályok alkalmazása után forgalmat egy belső terheléselosztót (standard szintű Termékváltozat) a belső megbízható zóna alhálózaton lesznek továbbítva.
    - A VPN-kapcsolaton keresztül a helyszíni adatközpontba a megbízható alhálózatról kimenő forgalmat a tűzfal és a szabályok a alkalmazni, mielőtt továbblépne a site-to-site VPN-kapcsolaton keresztül továbbít.



### <a name="design-and-set-up-azure-networks"></a>Tervezési és az Azure-hálózatok beállítása

A hálózat és a helyen útválasztási topológiáját, a Contoso beállítása az Azure-hálózatok és alhálózatok készen áll.

- Contoso az Azure-ban (0.0.0.0 127.255.255.255) osztály egy magánhálózaton hajtja végre. Ez akkor működik, óta a helyszínen aktuális rendelkeznek a B osztály privát cím terület 172.160.0/16 növelhető meg arról, hogy nem kell minden olyan címtartományai között átfedés.
- Ezek fogjuk telepíteni az elsődleges és másodlagos régióban található virtuális hálózatok.
- Az előtagot tartalmazó elnevezési használatának mikéntjét **VNET** és a régió rövidítés **EUS2** vagy **CUS**. Használja ezt a szabványt, a hub hálózatok lesznek elnevezve **VNET-HUB-EUS2** (USA keleti RÉGIÓJA 2), és **VNET-HUB-CUS** (USA középső RÉGIÓJA).
- Nem rendelkezik a Contoso egy [IP-Címkezelő megoldás](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), így a szükséges hálózati útválasztást, NAT nélkül tervezése


#### <a name="virtual-networks-in-east-us-2"></a>USA 2. keleti régiójában található virtuális hálózatok

USA keleti RÉGIÓJA 2 az elsődleges régióban, amely a Contoso-erőforrások és szolgáltatások telepítéséhez használandó. Itt látható, hogy azok mérnök hálózatok fogjuk:

- **Hub**: az agyi virtuális hálózat, az USA 2. keleti régiója elsődleges kapcsolatot a helyszíni adatközpontját a központi helye.
- **Virtuális hálózatok**: Kelet-USA 2. küllő virtuális hálózatok segítségével számítási feladatok elkülönítésére, ha szükséges. Az agyi virtuális hálózat mellett a Contoso két küllő virtuális hálózatok, az USA keleti RÉGIÓJA 2 fog rendelkezni:
    - **VNET-DEV-EUS2**. A virtuális hálózat biztosít a fejlesztési és tesztelési csapata egy teljes körűen használható hálózati fejlesztési projektek lesz. Próbaüzem üzem területként fog működni, és a termelési infrastruktúrával függvényt fogja alkalmazni.
    - **VNET-ÉLES-EUS2**: az Azure IaaS éles összetevőket ezen a hálózaton található. 
    -  Minden egyes virtuális hálózat lesz a saját egyedi címtér nélkül. Konfigurálja az útválasztást, NAT nélkül kívánnak
- **Alhálózatok**:
    - Egy alhálózatot az kell minden hálózat minden egyes alkalmazás-szint
    - A termelési hálózat minden alhálózata egy egyező alhálózatot kell fejlesztési a virtuális hálózaton.
    - Emellett az éles hálózati környezetben van tartományvezérlők alhálózatát.

USA 2. keleti régiójában található virtuális hálózatok az alábbi táblázat foglalja össze.

**Virtuális hálózat** | **Címtartomány** | **Társ**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, VNET-DEV-EUS2, VNET-ÉLES-EUS2
**VNET-DEV-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-ÉLES-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-ÉLES-KAPACITÁSEGYSÉGEK

![Hub/küllő elsődleges régióban](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>Alhálózatok az agyban East US 2 hálózati (VNET-HUB-EUS2)

**Alhálózat/zóna** | **CIDR** | ** Használható IP-címet
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**Objekt-UntrustZone** | 10.240.2.0/24 | 251
**Objekt-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>Alhálózatok a East US 2 fejlesztési hálózatban (VNET-DEV-EUS2)

A fejlesztési virtuális hálózat a fejlesztői csapat próbaüzem üzem területként használja. Három alhálózatot tartalmaz.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**DEV-FE-EUS2** | 10.245.16.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**DEV-ALKALMAZÁS-EUS2** | 10.245.20.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**DEV-DB-EUS2** | 10.245.24.0/23 | 507 | Adatbázis-beli virtuális gépek


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Alhálózatok a East US 2 éles hálózaton (VNET-ÉLES-EUS2)

Azure IaaS-összetevőket az éles hálózati környezetben találhatók. Minden alkalmazás csomag esetében a saját alhálózatában. Alhálózatok megegyeznek a fejlesztési hálózat, alhálózat tartományvezérlők igény szerinti hozzáadásával.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**TERMÉKKATALÓGUS-FE-EUS2** | 10.245.32.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**ÉLES – ALKALMAZÁS-EUS2** | 10.245.36.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**TERMÉKKATALÓGUS-DB-EUS2** | 10.245.40.0/23 | 507 | Adatbázis-beli virtuális gépek
**ÉLES – DC-EUS2** | 10.245.42.0/23 | 251 | Tartományvezérlő virtuális gépek


![Hub hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>USA középső RÉGIÓJA (másodlagos régió) lévő virtuális hálózatok

USA középső Régiójában a Contoso másodlagos régióba. Itt látható, hogy azok mérnök hálózatok fogjuk:

- **Hub**: A hubon, az USA keleti RÉGIÓJA 2 virtuális hálózat a helyszíni adatközpontját, és a küllő virtuális hálózatok, az USA keleti RÉGIÓJA 2 is használható, ha szükséges, számítási feladatok elkülönítésére csatlakozási lehetőségek központi helye külön-külön kezelt más küllőktől elkülönülten kezelhetőek.
- **Virtuális hálózatok**: USA középső régiójában két virtuális hálózat rendelkeznek:
    - VNET-ÉLES-KAPACITÁSEGYSÉG HASZNÁLHATÓ. A virtuális hálózat egy üzemi hálózat, a VNET-PROD_EUS2 hasonló. 
    - VNET-AUTOMATIKUS RENDSZER-HELYREÁLLÍTÁSI-KAPACITÁSEGYSÉG HASZNÁLHATÓ. A virtuális hálózat olyan hely, ahol virtuális gépek jönnek létre a helyszíni feladatátvétel után, vagy helyként fog működni az Azure virtuális gépek, melyek feladatai át az elsődleges kiszolgálóról a másodlagos régióba. Ez a hálózat hasonlít az éles hálózati környezetben, de nincs rajta a tartományvezérlők.
    -  Minden egyes régióban található virtuális hálózat lesz a saját címtér nélkül. Konfigurálja az útválasztást, NAT nélkül kívánnak
- **Alhálózatok**: az alhálózatok a rendszer ezeket az USA keleti régiója 2 hasonló módon lett tervezve. A kivétel ez alól, akkor nincs szüksége alhálózatra tartományvezérlők.

Az alábbi táblázat foglalja össze a virtuális hálózatok, az USA középső Régiójában.

**Virtuális hálózat** | **Címtartomány** | **Társ**
--- | --- | ---
**VNET-HUB-KAPACITÁSEGYSÉGEK** | 10.250.0.0/20 | VNET-HUB-EUS2, VNET-ASR-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK
**VNET-ASR-KAPACITÁSEGYSÉGEK** | 10.255.16.0/20 | VNET-HUB-CUS, VNET-ÉLES-KAPACITÁSEGYSÉGEK
**VNET-ÉLES-KAPACITÁSEGYSÉGEK** | 10.255.32.0/20 | VNET-HUB-CUS, VNET-ASR-CUS, VNET-ÉLES-EUS2  


![Hub/küllő párosított régióban](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>A velünk a Kapcsolatot a központi Agyhoz alhálózatok hálózati (VNET-HUB-CUS)

**Alhálózat** | **CIDR** | **Használható IP-címet**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**Objekt-UntrustZone** | 10.250.2.0/24 | 251
**Objekt-TrustZone** | 10.250.3.0/24 | 251
**Átjáró-alhálózat** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>A központi USA éles hálózaton (VNET-ÉLES-CUS) alhálózatok

Az éles hálózati környezetben az elsődleges régióban USA keleti RÉGIÓJA 2 párhuzamos nincs éles hálózati környezetben a másodlagos USA középső régiójában. 

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**TERMÉKKATALÓGUS-FE-KAPACITÁSEGYSÉGEK** | 10.255.32.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**ÉLES – ALKALMAZÁS-KAPACITÁSEGYSÉGEK** | 10.255.36.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**ÉLES – ADATBÁZIS-KAPACITÁSEGYSÉGEK** | 10.255.40.0/23 | 507 | Adatbázis-beli virtuális gépek
**ÉLES – DC-KAPACITÁSEGYSÉGEK** | 10.255.42.0/24 | 251 | Tartományvezérlő virtuális gépek

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>Az USA középső RÉGIÓJA (VNET-ASR-CUS), az USA középső RÉGIÓJA feladatátvételi vagy helyreállítási hálózat alhálózatai

A VNET-ASR-CUS hálózati a régiók közötti feladatátvétel célokat szolgál. A Site Recovery replikálja, és átadja a feladatokat az Azure virtuális gépek régiók közötti használható. Azt is egy Azure-hálózathoz, amely a helyszínen maradnak, de a feladatátvételt az Azure-bA vész-helyreállítási védett munkaterhelésekhez Contoso adatközpontban működik.

VNET-ASR-Kapacitásegységek az üzemi virtuális hálózat, az USA keleti RÉGIÓJA 2, de a domain controller alhálózat nélkül alapszintű ugyanabban az alhálózatban.

**Alhálózat** | **CIDR** | **Címek** | **Az alhálózat**
--- | --- | --- | ---
**ASR-FE-KAPACITÁSEGYSÉGEK** | 10.255.16.0/22 | 1019 | Előtérrendszer/webes szintű virtuális gépek
**ASR-ALKALMAZÁS-KAPACITÁSEGYSÉGEK** | 10.255.20.0/22 | 1019 | Alkalmazás szintű virtuális gépek
**ASR-ADATBÁZIS-KAPACITÁSEGYSÉGEK** | 10.255.24.0/23 | 507 | Adatbázis-beli virtuális gépek

![Hub hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Társviszonyban kapcsolat konfigurálása

A hub, az egyes régiókban lesznek létesíthető társviszony a hub más régióban található, és a hub régión belül minden virtuális hálózatokhoz. Ez lehetővé teszi az hubok való kommunikációra, és a egy adott régión belül minden virtuális hálózatok megtekintéséhez. Vegye figyelembe:

- Társviszony-létesítés kétoldalas kapcsolatot hoz létre. Egy a kezdeményező társ az első virtuális hálózaton, és egy másikat a második virtuális hálózaton.
- Az egy hibrid telepítésben egyenrangú társak közötti forgalom a VPN-kapcsolatot a helyszíni adatközpont és az Azure között látható kell. Ennek engedélyezéséhez nincsenek néhány speciális beállítással, hogy be kell állítani a virtuális társhálózatba tartozó kapcsolatok.

Minden olyan kapcsolatot küllő virtuális hálózatok a hubon keresztül a helyszíni adatközponthoz Contoso kell, hogy a továbbított és keresztirányú forgalomnak a VPN-átjárók.

##### <a name="domain-controller"></a>Tartományvezérlő

A tartományvezérlők a VNET-ÉLES-EUS2 hálózatban a Contoso biztosítani szeretné, mind a EUS2 hub/éles hálózati környezetben között, valamint a helyszíni VPN-kapcsolaton haladjon a forgalom. Ehhez szükséges, hogy a következő:

1. **Továbbított forgalom engedélyezése** és **átjáró átviteli konfigurációk engedélyezése** a társviszonyban kapcsolat. Ebben a példában ez a VNET-HUB-EUS2 VNET-ÉLES-EUS2 kapcsolatra lenne.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering1.png)

2. **Továbbított forgalom engedélyezéséhez** és **távoli átjárók használata** többi oldalán a társviszony, a VNET-ÉLES-EUS2 VNET-HUB-EUS2 kapcsolatra.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering2.png)

3. A helyszíni azok fog beállítani egy statikus útvonalat, amely a helyi forgalom irányítása a virtuális hálózathoz a VPN-alagúton keresztül irányítja. A konfigurációs szeretné befejezni az átjáró, amely a VPN-alagút biztosít a Contoso Azure-bA. Contoso a Windows útválasztási és távelérési használ.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Éles hálózati környezetben 

Egy spoked egyenrangú hálózat nem látható egy spoked egyenrangú hálózat egy központon keresztül egy másik régióban.

A Contoso éles hálózati környezetben megtekintéséhez egymással mindkét régióban létre kell hozniuk közvetlen társviszonyban kapcsolat a VNET-ÉLES-EUS2 és MŰVELE-ÉLES-CUS. 

![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>Állítsa be a DNS

Erőforrások a virtuális hálózatok központi telepítése esetén több lehetősége a tartománynevek feloldását rendelkezik. Használja az Azure által biztosított névfeloldást, vagy adja meg a DNS-kiszolgálók a feloldásához. A névfeloldás használata típusa attól függ, hogyan kell az erőforrások kommunikálni egymással. Első [bővebben](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) az Azure DNS szolgáltatással kapcsolatban.

Contoso úgy döntött, hogy az Azure DNS szolgáltatásban nem jó választás a hibrid környezetben. Ehelyett használhatja a helyi DNS-kiszolgálók fogjuk azokat.

- Mivel ez egy hibrid hálózat összes a virtuális gépek a helyszínen és az Azure-ban képeseknek kell lenniük feloldani a megfelelő működéséhez. Ez azt jelenti, hogy a virtuális hálózatok egyéni DNS-beállításokat kell alkalmazni.
- Contoso jelenleg rendelkezik a Contoso az adatközpontban és a fiókirodák telepített tartományvezérlő. Az elsődleges DNS-kiszolgálók CONTOSODC1(172.16.0.10) és CONTOSODC2(172.16.0.1)
- Ha a virtuális hálózatok vannak telepítve, a helyszíni tartományvezérlők a hálózatokat a DNS-kiszolgálóként használandó lesz beállítva. 
- Ennek konfigurálásához, ha egyéni DNS használata a virtuális hálózat az Azure rekurzív feloldók IP-címet (például a 168.63.129.16) a DNS-listához hozzá kell adni.  Ehhez a Contoso DNS-kiszolgáló beállításainak konfigurálja az egyes virtuális hálózatok. Például a VNET-HUB-EUS2 hálózati egyéni DNS-beállításainak a következő lesz:
    
    ![Egyéni DNS](./media/contoso-migration-infrastructure/custom-dns.png)

A helyszíni tartományvezérlők mellett Contoso implementálási négy több-az Azure-hálózatok, két minden olyan régió esetében támogatja. Íme, mi azok helyezünk üzembe az Azure-ban.

**Régió** | **TARTOMÁNYVEZÉRLŐ** | **Virtuális hálózat** | **Alhálózat** | **IP-cím**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-ÉLES-EUS2 | ÉLES – DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-ÉLES-EUS2 | ÉLES – DC-EUS2 | 10.245.42.5
KAPACITÁSEGYSÉG HASZNÁLHATÓ | CONTOSODC5 | VNET-ÉLES-KAPACITÁSEGYSÉGEK | ÉLES – DC-KAPACITÁSEGYSÉGEK | 10.255.42.4
KAPACITÁSEGYSÉG HASZNÁLHATÓ | CONTOSODC6 | VNET-ÉLES-KAPACITÁSEGYSÉGEK | ÉLES – DC-KAPACITÁSEGYSÉGEK | 10.255.42.4

A helyszíni tartományvezérlők üzembe helyezése után Contoso frissítenie kell a DNS-beállítások hálózatokon a mindkét régióban tartalmazza az új tartományvezérlők a saját DNS-kiszolgálók listáját.



#### <a name="set-up-domain-controllers-in-azure"></a>Állítsa be a tartományvezérlőket az Azure-ban

Miután frissítette a hálózati beállítások, Contoso építse ki az Azure-ban a tartományvezérlők készen áll.

1. Az Azure Portalon, a megfelelő virtuális hálózatot egy új Windows Server virtuális gép üzembe helyezése.
2. Rendelkezésre állási csoportok az egyes helyeken a virtuális gép létrehozása azokat. A rendelkezésre állási csoportok tegye a következőket:
    - Győződjön meg arról, hogy az Azure-hálót a virtuális gépek elkülöníti azokat különböző infrastruktúrákat az Azure-régióban. 
    -  Lehetővé teszi, hogy a Contoso a 99,95 %-os SLA-t az Azure-beli virtuális gépek jogosult legyen.  [További információk](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).

    ![Rendelkezésre állási csoport](./media/contoso-migration-infrastructure/availability-group.png) 
3. A virtuális gép telepítése után a hálózati adaptert a virtuális gép tollas azokat. Itt, meg azokat a magánhálózati IP-cím statikus, és adjon meg egy érvényes címet.

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

    ![DNS-továbbító](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Most majd frissítse az egyéni DNS-beállítások az egyes virtuális hálózatok a megfelelő tartományvezérlő számára, a virtuális hálózathoz. A helyszíni tartományvezérlők tartalmazzák a listában.

### <a name="set-up-active-directory"></a>Active Directory beállítása

AD egy kritikus fontosságú szolgáltatás a hálózatkezelés, és megfelelően kell konfigurálni. Contoso felépítheti az AD helyekben a Contoso-datacenter, valamint a EUS2 és kapacitásegység használható régiókat.  

1. Akkor hozzon létre az Adatközpont-hely (ContosoDatacenter) és két új hely (az AZURE-EUS2 és az AZURE-CUS).
2. Miután létrehozta a helyek, a helyek, a virtuális hálózatok és az Adatközpont megfelelő alhálózatok létrehozása azokat.

    ![Tartományvezérlő alhálózatok](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Ezután, hozzon létre két helykapcsolatok mindent csatlakozni. A tartományvezérlők helyükre majd át.

    ![DC-hivatkozások](./media/contoso-migration-infrastructure/dc-links.png)

4. Miután minden be van állítva, az Active Directory replikációs topológiájának van.
    
    ![Tartományvezérlő replikációs](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Minden kész, és a tartományvezérlők és helyek listáját a helyszíni Active Directory felügyeleti központ láthatók.

    ![AD felügyeleti központban](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>5. lépés: A cégirányítási tervezése

Az Azure széles körű cégirányítási vezérlők lehet a szolgáltatások és az Azure platform biztosít. [További információ](https://docs.microsoft.com/azure/security/governance-in-azure) vonatkozó beállítások alapvető ismeretekkel.

Konfigurálása identitás és hozzáférés-vezérlés, Contoso már megkezdődött, szabályozási és biztonsági bizonyos aspektusainak vezetnek be. Széles körben három olyan terület, figyelembe kell vennie:

- **A házirend**: szabályzat az Azure-ban vonatkozik, és kikényszeríti szabályok és hatások az erőforrások felett, hogy megfelelnek a vállalati követelményeknek és szolgáltatói szerződésekkel.
- **Zárolja**: az Azure lehetővé teszi, hogy zárolási előfizetések, erőforráscsoportok és más erőforrások, így azok csak az ehhez a szolgáltató által módosíthatók.
- **A címkék**: erőforrások is lehet szabályozza, naplózza és címkékkel rendelkező felügyelt. Címkék metaadatok csatlakoztathat erőforrásokat, erőforrások, illetve eszköztulajdonosok vonatkozó információkat.

### <a name="set-up-policies"></a>Házirendek beállítása

Az Azure Policy szolgáltatást kiértékeli az erőforrásokat, és azokat, amelyek nem felelnek meg a már működik a szabályzatdefiníciók vizsgálatát. Például előfordulhat, hogy rendelkezik egy szabályzatot, amely csak lehetővé teszi, hogy bizonyos típusú virtuális gépek, illetve egy adott címkével rendelkeznie erőforrásokra van szükség. 

Azure házirendek határozzák meg a szabályzat-definíció, és a szabályzat-hozzárendelés megadása a hatókör, amelyben egy szabályzatot kell alkalmazni. A hatókör között lehet a felügyeleti csoport egy erőforráscsoporthoz. [Ismerje meg,](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) vonatkozó szabályzatok létrehozása és kezelése.

Contoso szeretne néhány szabályzatok – első lépések:

- Győződjön meg arról, hogy erőforrások kizárólag a EUS2 és CUS régióban telepíthető szabályzat szeretnének.
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

Contoso hosszú használja ITIL keretében a felügyeleti rendszerek. Egyik legfontosabb szempontja a keretrendszer a változások, és győződjön meg arról, hogy változáskezelésre megvalósítása az Azure-telepítés, a Contoso biztosítani szeretné.

Contoso implementálási zárolás a következő:

- Egy erőforráscsoport, amely olyan írásvédett zárolással rendelkezik minden olyan éles vagy feladatátvételi összetevő kell lennie.  Ez azt jelenti, hogy vagy éles elemek törlése, a zárolás el kell távolítani. 
- Nem éles üzemi erőforráscsoportok zárolások védve lesz. Ez azt jelenti, hogy a jogosult felhasználók is olvasni vagy módosítani az erőforrást, de nem lehet törölni.

[További](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) zárolások kapcsolatban.

### <a name="set-up-tagging"></a>Állítsa be a címkézés

Erőforrások nyomon követésére, mivel a rendszer hozzáadja, csak Contoso erőforrások társítása egy megfelelő részleg, a vevő és a környezet számára egyre fontosabb. 

Amellett, hogy az erőforrások és a tulajdonosok kapcsolatos információkat, a címkék lehetővé teszi Contoso összesítő és csoport-erőforrásokat, és az adatokat a jóváírási célból.

Contoso szeretné vizuálisan megjeleníteni az Azure eszközeiket úgy, hogy üzleti van értelme. Például, de a szerepkör vagy a részleg. Vegye figyelembe, hogy erőforrások találhatók ugyanabban az erőforráscsoportban egy címke megosztása nem szükséges. Ehhez Contoso hoz létre egy egyszerű címkerendszert, így mindenki ugyanazt a címkét használja.

**Címke neve** | **Érték**
--- | ---
Költséghely | 12345: egy érvényes költséghely SAP-kell lennie.
Részleghez | (Az SAP) üzleti egység nevét. Egyezések CostCenter.
ApplicationTeam | Az alkalmazás támogatására vonatkozó birtokló csoport e-mail-aliasát.
Katalógusnév | Neve az alkalmazás vagy ShareServices, a szolgáltatáskatalógus, az erőforrás által támogatott száma.
ServiceManager | E-mail-alias az ITIL Service Manager az erőforráshoz.
COBPriority | A vállalat által az BCDR beállított prioritás. 1 – 5 értékét.
ENV | DEV, STG, ÉLES lehetséges értékei. Jelölő fejlesztésével, átmeneti és éles környezetben.

Példa: 

 ![Az Azure-címkék](./media/contoso-migration-infrastructure/azure-tag.png)

Miután létrehozta a címke, Contoso visszaléphet, és hozzon létre új Azure szabályzat-definíciókat és hozzárendeléseket, a szükséges címkéket használatának kényszerítése a szervezeten belül.


## <a name="step-6-consider-security"></a>6. lépés: Fontos a biztonság

Biztonsági elengedhetetlen a felhőben, és az Azure széles választékának biztonsági eszközöket és képességeket nyújt. Ezek segítségével könnyebben hozhat létre, olyan biztonságos megoldásokat a biztonságos Azure-platformon. Olvasási [bizalom a megbízható felhőben](https://azure.microsoft.com/overview/trusted-cloud/) tudhat meg többet az Azure security.

Van néhány fő szempontokat kell figyelembe venni a contoso

- **Az Azure Security Center**: az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. A Security Centerrel biztonsági szabályzatokat alkalmazhat a számítási feladatokra, korlátozhatja a fenyegetéseknek való kitettséget, valamint észlelheti és elháríthatja a támadásokat.  [További információk](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Hálózati biztonsági csoportok (NSG-k)**: egy NSG-t egy szűrőt (tűzfal), amely felsorolja azokat a biztonsági szabályok, amelyek alkalmazásakor, vagy az Azure vnetekhez csatlakoztatni erőforrásokra irányuló hálózati forgalom megtagadásához. [További információk](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Adattitkosítás**: az Azure Disk Encryption egy olyan funkció, amely segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. [További információk](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Az Azure Security Center használata

Contoso keres egy gyors nézetet az új hibrid felhő és a kifejezetten az Azure számítási feladatok biztonsági rendszeréről.  Ennek eredményeképpen Contoso határozott megvalósítása az Azure Security Center indítása a következő funkciókkal: 

- Központosított szabályzatkezelés
- A folyamatos értékelés
- Végrehajtható javaslatok 

#### <a name="centralize-policy-management"></a>Központosíthatja a Csoportházirend kezelése

Központosított szabályzatkezelés, a Contoso fogja, hogy biztonsági követelményeknek való megfelelés biztonsági szabályzatainak központi kezelésével a teljes környezetre. Ezek egyszerűen és gyorsan valósíthat meg egy szabályzatot, amely minden, az Azure-erőforrások vonatkozik.

![Biztonsági házirend](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Mérje fel, és a művelet

Contoso használja a folyamatos biztonsági értékelés, amely figyeli a biztonság, a gépek, hálózatok, tárolás, adatok és alkalmazások; felderítheti a potenciális biztonsági problémákat. 

- A Security Center elemzi a Contoso számítási, az infrastruktúra és erőforrásokat, valamint az Azure-alkalmazások és szolgáltatások biztonsági állapotát.
- A folyamatos értékelés segít felderíteni a potenciális biztonsági problémákat, például a rendszer a hiányzó biztonsági frissítések, a Contoso üzemeltetési csapat, vagy hálózati portok közzétéve. 
- Különösen a Contoso biztosítani szeretné, hogy az összes virtuális gépeiken védettek. Security Center segítségével, a virtuális gép állapotának ellenőrzése, és a biztonsági rések javítása, mielőtt azok már kihasználták a rangsorolt és végrehajtható javaslatokat.

![Figyelés](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Az NSG-k használata

Contoso korlátozhatja a hálózati biztonsági csoportok használata a virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalom.

- A hálózati biztonsági csoport egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy a cél IP-címe, illetve portok vagy protokollok alapján.
- Ha egy alhálózathoz, szabályok érvényesek az alhálózaton található összes erőforrást. Hálózati adapterek mellett ez tartalmazza a példányok az Azure-szolgáltatások üzembe helyezheti az alhálózaton.
- Az alkalmazásbiztonsági csoportok (asg-k) lehetővé teszi egy alkalmazás struktúra, amely lehetővé teszi a virtuális gépek természetes bővítményeként konfigurálhatja a hálózati biztonságot, és ezen csoportok alapján a hálózati biztonsági szabályzatok meghatározása.
    - Alkalmazás biztonsági csoportok jelenti azt, hogy a biztonsági szabályokat nagy léptékben, konkrét IP-címek manuális karbantartása nélkül is újrahasznosíthatja. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat.
    - Az alkalmazásbiztonsági csoportokat megadhatja forrásként és célként is a biztonsági szabályokban. A biztonsági szabály meghatározása után létrehozhat virtuális gépeket, és a virtuális gép hálózati adaptereket hozzárendelése egy csoporthoz. 


Contoso fogja végrehajtani az NSG-k és alkalmazásbiztonsági csoporttal. NSG-kezelésről érintett legyenek. Azok a miatt aggódó kapcsolatos túlhasználat NSG-k és az összetettséget, ez jelezheti azt a munkatársak számára.  Ezt szem általuk elfogadott két fő résztvevők általános szabály használják:

- Minden forgalmat, és kimenő (észak – dél), minden alhálózat egy NSG-szabályt, kivéve a Hub hálózatokban a GatewaySubnets érvényesek lesznek.
- Minden olyan tűzfallal vagy a tartományvezérlő által alhálózati NSG-k és a hálózati adapter NSG-ket is védi.
- Az összes éles üzemi alkalmazások alkalmazásbiztonsági csoporttal a alkalmazni kell.


Contoso felépítette egy modell arról, hogyan ez fog megjelenni az alkalmazásaikban.

![Biztonság](./media/contoso-migration-infrastructure/asg.png)


Az alkalmazásbiztonsági csoporttal társított NSG-k, győződjön meg arról, hogy csak engedélyezett csomagok is flow, a hálózat egy részét a cél minimális jogosultságokkal rendelkező lesz konfigurálva.

**Művelet** | **Name (Név)** | **Forrás** | **Target** | **Port**
--- | --- | --- | --- | --- 
Engedélyezés | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | AZ APP1-FE 80-AS, 443-AS PORTON
Engedélyezés | AllowWebToApp | AZ APP1-FE | AZ APP1-DB | 1433
Engedélyezés | AllowAppToDB | AZ APP1-ALKALMAZÁS | Bármelyik | Bármelyik
Megtagadás | DenyAllInbound | Bármelyik | Bármelyik | Bármelyik

### <a name="encrypt-data"></a>Adatok titkosítása

Az Azure Disk Encryption integrálható az Azure Key Vault segítségével szabályozhatja, és a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés kezelésére. Ez biztosítja, hogy minden adat a Virtuálisgép-lemezek vannak titkosítása az Azure storage-ban.  

- Contoso megállapította, hogy az adott virtuális gépek titkosítás megkövetelése.
- Azok a alkalmazni ügyféllel, bizalmas, virtuális gépek titkosítási vagy PPI adatokat fogjuk.


## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso beállítása az Azure-infrastruktúra, és állítsa be, vagy tervezett, infrastruktúra-szabályzat Azure-előfizetés, akkor a hibrid azonosítsa, vészhelyreállítás, hálózatkezelés, cégirányítási és biztonsági. 

Nem minden Contoso itt végrehajtott lépések szükségesek a felhőre való áttelepítést. Abban az esetben azok minden típusú migrálások felhasználható a hálózati infrastruktúra tervezése szeretne, és biztonságos, rugalmas és méretezhető. 

Az e-infrastruktúrára azok készen áll át, és próbálja ki a migrálás.

## <a name="next-steps"></a>További lépések

Első áttelepítési forgatókönyvekben, mint a Contoso tervezi, hogy [mérje fel helyszíni SmartHotel kétszintű alkalmazás az Azure-ba való migráláshoz](contoso-migration-assessment.md). 
