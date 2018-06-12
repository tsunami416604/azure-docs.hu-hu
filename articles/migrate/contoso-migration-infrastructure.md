---
title: Contoso-áttelepítési-infrastruktúra beállítása |} Microsoft Docs
description: Ismerje meg, hogyan a Contoso beállítja az Azure-infrastruktúrák áttelepítése az Azure-bA.
services: azure-migrate
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 8b7f0675c1bbf378d02eb52843caf27a1dce2fb8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301201"
---
# <a name="contoso---deploy-a-migration-infrastructure"></a>Contoso - telepítsen egy áttelepítés

Ez a cikk megvizsgálja, hogy a Contoso hogyan állít be egy helyszíni és az Azure infrastruktúra áttelepítése az Azure-ba, és a hibrid környezetben futó üzleti előkészítésekor.

- Egy adott Contoso – mintaarchitektúra.
- Az áttelepítési stratégiáját függ, hogy kell-e a cikkben leírt összes elemet. Például ha csak felhőalapú natív alkalmazások az Azure-ban most felépítése, szükség lehet egy kevésbé összetett hálózati struktúra.

Ez a dokumentum a második dokumentum hogyan Contoso áttelepíti a fiktív vállalat a helyszíni erőforrások a Microsoft Azure felhőbe cikkek sorozat. Az adatsorozat háttér-információkat tartalmaz, és telepítési forgatókönyv olyan készlete, amely bemutatja, hogyan állíthat be egy áttelepítési infrastruktúra felmérheti a helyszíni erőforrások az áttelepítéshez megfelelőségét, és futtassa az áttelepítések különböző típusú. Forgatókönyvek nő összetettségét, és a következőkben hozzáadott további cikkek adott idő alatt.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
[A következő cikket: 1: áttekintés](contoso-migration-overview.md) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használjuk áttekintést nyújt. | Elérhető
2. cikk: Az Azure-infrastruktúrák (Ez a cikk) központi telepítése | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md) | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Áthelyezési Azure virtuális gépek és a felügyelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
[Cikk 5: Az Azure virtuális gépek áthelyezési](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso át SmartHotel alkalmazásuk csak a Site Recovery segítségével virtuális gépeket.
[Cikk 6: Azure virtuális gépek és az SQL Server rendelkezésre állási csoportok áthelyezési](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázis egy SQL Server rendelkezésre állási csoporthoz. | Elérhető
[7. cikk: Áthelyezési egy Linux-alkalmazást az Azure virtuális gépen](contoso-migration-rehost-linux-vm.md) | Bemutatja, hogyan Contoso áttelepíti a Linux osTicket app Azure virtuális gépeken. | Elérhető
[8. cikk: Áthelyezési egy Linux alkalmazást az Azure virtuális gépek és az Azure-beli MySQL kiszolgáló](contoso-migration-rehost-linux-vm-mysql.md) | Bemutatja, hogyan Contoso áttelepíti a Linux osTicket alkalmazásba Site Recovery és MySQL munkaterület áttelepítése (biztonsági mentése és visszaállítása) Azure-beli MySQL Server-példány. | Elérhető

A Contoso beállítása az infrastruktúra-elemek cikkben szükségük az szituációkban az áttelepítés befejezéséhez. 


## <a name="overview"></a>Áttekintés

Ahhoz, azok áttelepítheti az Azure-ba, rendkívül fontos, hogy a Contoso az infrastruktúra előkészítése.  Általában öt területek kell gondolniuk van:

1. **Azure-előfizetések**: hogyan fogja azokat vásárláshoz, és működjön együtt az Azure platformon és a szolgáltatások?
2. **Hibrid identitás**: hogyan azok kezeli és a helyszíni és az Azure-erőforrások való hozzáférés szabályozása áttelepítése után? Hogyan azok kiterjesztése, vagy helyezze át az Identitáskezelés a felhőben?
3. **Vész-helyreállítási és rugalmasság**: hogyan fogja azok ellenőrizze, hogy az alkalmazások és infrastruktúrájukat rugalmas Ha valamilyen okból kimaradás és katasztrófa történik?
4. **Hálózati**: hogyan kell azokat a hálózati infrastruktúra megtervezésére, és a helyszíni adatközpontját és az Azure közötti kapcsolatot létrehozni?
5. **Biztonsági és irányítási**: hogyan fogja azokat a hibrid vagy az Azure üzembe, és legyen összhangban a biztonsági és a cégirányítási követelmények?

## <a name="before-you-start"></a>Előkészületek

Mielőtt először az infrastruktúra megnézi, érdemes néhány háttér-információkat az Azure-képességek arról lesz szó ebben a cikkben olvasható:

- Nincsenek elérhető Azure eléréséhez, beleértve a használatalapú fizetés, nagyvállalati szerződés (EA) megvásárlása számos beállítási lehetőséggel, vagy az úgynevezett nyílt licencelési Microsoft viszonteladók számára, vagy a Microsoft Partners felhő megoldás szolgáltatókat (CSP). További tudnivalók [beszerzési lehetőségek](https://azure.microsoft.com/pricing/purchase-options/), és megtudhatja, hogyan [Azure-előfizetések vannak rendszerezve](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/).
- Azure áttekintést kaphat [identitások és hozzáférések felügyeletéhez](https://www.microsoft.com/en-us/trustcenter/security/identity). Különösen megismerése [az Azure AD és kiterjesztése a helyszíni AD a felhőbe](https://docs.microsoft.com/azure/active-directory/identity-fundamentals). Nincs a hasznos letölthető e-könyv kapcsolatos [identitás és hozzáférés-kezelés (IAM) hibrid környezetben](https://azure.microsoft.com/resources/hybrid-cloud-identity/).
- Azure beállításokkal való hibrid kapcsolathoz hálózati robusztus infrastruktúrát kínál. Áttekintheti a [hálózat és a hálózati hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/security-network-overview).
- Bevezetés az [Azure biztonsági](https://docs.microsoft.com/azure/security/azure-security), és olvassa el a terv létrehozása [cégirányítási](https://docs.microsoft.com/azure/security/governance-in-azure).


## <a name="on-premises-architecture"></a>A helyszíni architektúrája

Itt található ábra: az aktuális Contoso helyszíni infrastruktúrát.

 ![Contoso-architektúra](./media/contoso-migration-infrastructure/contoso-architecture.png)  

- Contoso található az a New York Város keleti Amerikai Egyesült Államokban lévő egyik fő datacenter rendelkezik.
- Három további helyi ágak keresztül az Amerikai Egyesült Államokban rendelkeznek.
- A fő adatközpontok csatlakozik az internethez a részszálas metro ethernet-kapcsolat (500 MB/s).
- Minden fiókiroda helyi csatlakozik az internethez, üzleti osztály kapcsolatok használata IPSec VPN-alagutat a fő adatközpontok vissza a. Ez lehetővé teszi, hogy a teljes hálózat véglegesen csatlakoztatja, és optimalizálja a internetkapcsolat.
- A fő adatközpontok teljesen virtualizált a VMware. Két ESXi 6.5 virtualizációs gazdagépeket, kezeli a vCenter Server 6.5 rendelkeznek.
- Contoso az Active Directory, az Identitáskezelés és a DNS-kiszolgálók a belső hálózaton.
- A tartományvezérlők, az adatközpontban, a VMware virtuális gépek futtatásához. A helyi ágak: tartományvezérlő fizikai kiszolgálókon.


## <a name="step-1-buy-and-subscribe-to-azure"></a>1. lépés: Vásárolhat és Azure előfizetés

Contoso mérje fel, Azure megvásárlása, előfizetések tervezővel, és a szolgáltatások és erőforrások licenc szükséges.

### <a name="buy-azure"></a>Azure megvásárlása

A Contoso fog egy [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Ez magában foglalja az Azure-ba, amely feljogosítja arra, hogy vett szolgáltatás érvényessége alatt nagy előnye, ideértve a rugalmas számlázási lehetőségek és optimalizált árképzési társaságuk kész előre.

- Contoso becsült Mi az Azure fordítják az éves ütemezéshez lesz. A szerződés aláírt azok fizetett teljes az első évre.
- Contoso kell használnia a kötelezettségvállalások mindegyikét előtt az év, illetve azok elveszíti ezeket dollár értékét.
- Ha valamilyen okból haladhatja meg az előre és töltött több, a Microsoft fog számla azokat a különbséget.
- A kötelezettségvállalás fent költségeit lesz az azonos sebességét, és azokat a szerződésben. Nincsenek nem távelérésből eredő hátrányok csökkentésére a címen.

### <a name="manage-subscriptions"></a>Előfizetések kezelése

Után az Azure-fizet, Contoso ki kell deríteni az előfizetések kezelése. EGY rendelkeznek, és így nem vonatkozik korlát az Azure-előfizetések számát tudja állítani.

- Egy Azure nagyvállalati beléptetés határozza meg, hogyan vállalati alakzat és Azure-szolgáltatásokat használ, és határozza meg a core irányítási szerkezete.
- Első lépésként a Contoso megbizonyosodott egy struktúra (a nagyvállalati beléptetés a egy vállalati scaffold néven ismert. Segítségükkel [Ez a cikk](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-governance) felhasználóinál megértéséhez, valamint egy scaffold tervezése.
- Most a Contoso határozott, hogy a működési módszert használja a-előfizetések kezeléséhez.
    - A vállalati belül egyetlen IT-részleg az Azure keret szabályozza lesz rendelkeznek. Ez az előfizetések csak csoport lesz.
    - Ezek majd tovább ezt a modellt a jövőben, hogy más vállalati csoportok csatlakozhat, a nagyvállalati beléptetés a részlegek.
    - Az informatikai részleg belül a Contoso két előfizetések, éles üzemi pontjának és fejlesztési van strukturált.
    - Contoso további előfizetések a jövőben van szükség, ha azok kell kezelni a hozzáférést, a házirendek és a megfelelőségi előfizetésekben. Fog tudni ehhez bevezetésével [Azure felügyeleti csoportok](https://docs.microsoft.com/azure/azure-resource-manager/management-groups-overview), mint a fenti előfizetések további réteget.

    ![Vállalati struktúra](./media/contoso-migration-infrastructure/enterprise-structure.png) 

### <a name="examine-licensing"></a>Vizsgálja meg a licencelés

Az előfizetések konfigurálva a Contoso is megtekinthetik a Microsoft szoftverlicencelési. A licencelési stratégia az erőforrásokat, amelyeket be szeretne áttelepíteni az Azure, és hogyan Azure virtuális gépek és szolgáltatások kijelölt és telepített függ. 

#### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Virtuális gépek Azure-ban való telepítésekor, a standard rendszerkép által használt szoftver perc Contoso felszámított licenc magában foglalja. Contoso azonban lett egy hosszú távú Microsoft-ügyfél és EAs maradt, és nyissa meg a licencek és frissítési garancia (SA). 

Azure hibrid juttatás lehetővé teheti, menti az Azure virtuális gépek és az SQL Server munkaterhelések konvertálása vagy újból felhasználja a Windows Server Datacenter és Standard edition licencek, a frissítési garancia kezelt Contoso áttelepítéshez, költséghatékony módszert biztosít. Ezzel a lépéssel engedélyezi a Contoso alacsonyabb alapján számítási sebességet fizetni a virtuális gépek és az SQL Server. [További információk](https://azure.microsoft.com/pricing/hybrid-benefit/).


#### <a name="license-mobility"></a>Licenchordozhatósági

SA keresztüli Licenchordozhatósági biztosít a Microsoft mennyiségi licenc ügyfelek számára például a Contoso jogosult kiszolgáló alkalmazások telepítése a Azure aktív SA rugalmasan. Ez nem új licencek vásárlása céljából kell. A nem kapcsolódó mobilitási díjak meglévő licenceket is könnyen telepíthető az Azure-ban. [További információk](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="reserve-instances-for-predictable-workloads"></a>Tartalék példányok előre jelezhető munkaterhelések

Kiszámítható munkaterhelések azok, amelyek folyamatosan elérhető legyen a futó virtuális gépeket. Például az üzletági alkalmazások, például egy SAP ERP rendszer.  Másrészről előre nem látható munkaterhelések azok változó. Például magas a képező virtuális gépek igény, és nem csúcsidőre időpontokban ki.

![Fenntartott példány](./media/contoso-migration-infrastructure/reserved-instance.png) 

Cserébe fenntartott példányok adott, akkor tudja, hogy a nagy időtartamok idő fenntartásához szükséges Virtuálisgép-példány használja, konzol egy kedvezményes, mind rangsorolt kapacitás kérheti le. Használatával [Azure fenntartott példányok](https://azure.microsoft.com/pricing/reserved-vm-instances/)együtt Azure hibrid juttatásra, a Contoso takaríthat 82 %-kal ki rendszeres használatalapú fizetés, árképzési (április 2018).


## <a name="step-2-manage-hybrid-identity"></a>2. lépés: A hibrid identitás kezelése

Azure-erőforrások identitások és hozzáférések felügyeletéhez (IAM) a felhasználói hozzáférés szabályozása és ad húzza együtt az Azure-infrastruktúra fontos lépés.  

- Contoso döntse el, a helyszíni Active Directoryban kiterjeszti a felhőbe, mint egy új külön rendszert az Azure-ban.
- Akkor hozzon létre egy Azure-alapú Active Directory ehhez.
- Contoso nem rendelkezik Office 365 helyen, így egy új Azure AD létre kell hozni azokat.
- Office 365 az Azure AD használ a felhasználókezeléshez. Contoso használta az Office 365, ha azok volna már rendelkezik egy Azure AD alapelve, és ez az elsődleges AD.
- [További](https://support.office.com/article/understanding-office-365-identity-and-azure-active-directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9) Office 365, Azure ad-val kapcsolatos és további [előfizetés hozzáadása](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) egy meglévő Azure ad.

### <a name="create-an-azure-ad"></a>Hozzon létre egy Azure AD

A Contoso Azure-előfizetés használ az Azure AD ingyenes kiadás része. Adja hozzá egy új Active directory az alábbiak szerint:

1. Az a [Azure-portálon](http://portal.azure.com/), navigáljon a Contoso **hozzon létre egy erőforrást** > **identitás** > **Azure Active Directory**.
2. A **könyvtár létrehozása**, akkor adja meg a könyvtár nevét, egy kezdeti tartománynevet, és a régióban, amelyben az Azure AD-címtár kell létrehozni.

    ![Az Azure AD létrehozása](./media/contoso-migration-infrastructure/azure-ad-create.png) 

    > [!NOTE]
    > Ha ezek a következő könyvtár létrehozásakor az űrlap domainname.onmicrosoft.com rendelkezik egy kezdeti tartománynevet. A név nem módosítható vagy törölhető. Ehelyett van szükségük a regisztrált tartománynév hozzáadása az Azure ad Szolgáltatásba.

### <a name="add-the-domain-name"></a>A tartománynév hozzáadása

A szabványos tartománynevét használja, a Contoso meg kell adni egy egyéni nevet, az Azure ad Szolgáltatásba. Ez a beállítás lehetővé teszi a rendszergazdáknak az ismerős felhasználónevek hozzárendelése. Például egy felhasználó e-mail címét is bejelentkezés billg@contoso.com, ahelyett, hogy az igénylő billg@contosomigration.onmicrosoft.com. 

Egyéni név beállítása azok hozzáadása a címtárhoz, DNS-bejegyzés, és ellenőrizze a neve az Azure AD.

1. A **egyéni tartománynevek** > **egyéni tartomány hozzáadása**, megnövelik a tartományhoz.
2. A DNS-bejegyzés használata az Azure-ban van szükségük a tartományregisztráló regisztrálja őket. 

    - Az a **egyéni tartománynevek** listában, akkor vegye figyelembe a DNS-információkat a neve. Contoso MX bejegyzése használ.
    - Ehhez a névkiszolgáló való hozzáférésre van szükségük. Contoso akkor jelentkezett be a Contoso.com tartományhoz, és az Azure Active Directory, az áttelepítés előtt feljegyzett adatokkal által biztosított DNS-bejegyzés új MX rekord létrehozása.  
1. Miután a DNS-rekordok terjesztése, a tartományhoz tartozó részletek nevében kattintva **győződjön meg arról** ellenőrizze az egyéni nevet.

     ![Az Azure AD DNS](./media/contoso-migration-infrastructure/azure-ad-dns.png) 

### <a name="set-up-on-premises-and-azure-groups-and-users"></a>A helyszíni és az Azure csoportok és felhasználók

Most, hogy az Azure AD fut, adja hozzá a dolgozóknak a Contoso kell a helyszíni Active Directory-csoportokat, amely szinkronizálja az Azure AD. Azt javasoljuk, hogy azok az erőforráscsoportok az Azure-ban nevei megegyeznek a helyszíni-neveket használja. Így könnyebben való szinkronizálás céljából egyezések azonosítására.

#### <a name="create-resource-groups-in-azure"></a>Az Azure erőforráscsoport-sablonok létrehozása

Azure erőforráscsoport-sablonok összegyűjtésére Azure-erőforrások. Egy erőforráscsoport azonosítója használatával lehetővé teszi, hogy a csoportban lévő erőforrások a műveletek végrehajtásához Azure.

- Azure-előfizetés tartozhat több erőforrás-csoportok, de az erőforráscsoport csak egyetlen előfizetéssel belül létezhet.
- Emellett egyetlen erőforráscsoportként működnek rendelkezhet több erőforrást, de egy erőforrás csak egyetlen csoport is tartozhatnak.

A Contoso Azure erőforráscsoport-sablonok beállítása, az alábbi táblázat foglalja össze.

**Erőforráscsoport** | **Részletek**
--- | ---
**ContosoCobRG** | A csoport tartalmazza az összes erőforrásaihoz (COB) üzleti folytonosság érdekében.  Ez magában foglalja a tárolók Contoso hoz létre, amikor az Azure Site Recovery szolgáltatásban, és az Azure Backup szolgáltatás használatával.<br/><br/> Ez is magában foglalja az áttelepítéshez, a Azure áttelepítése és az adatbázis áttelepítési szolgáltatásokkal együtt használt erőforrások.
**ContosoDevRG** | Ez a csoport tartalmazza a fejlesztési és tesztelési erőforrásokat.
**ContosoFailoverRG** | Ez a csoport az erőforrásokat a feladatátvételt követően zóna funkcionál.
**ContosoNetworkingRG** | Ez a csoport tartalmazza az összes hálózati erőforrásokhoz.
**ContosoRG** | Ez a csoport tartalmazza az üzemi alkalmazások és az adatbázisok erőforrásaihoz.

Akkor hozzon létre erőforráscsoportok az alábbiak szerint:

1. Az Azure portálon > **erőforráscsoportok**, adnak hozzá egy csoporthoz.
2. Az egyes csoportok akkor adja meg a nevét, az előfizetés, amely a csoport tartozik, és a régióban.
3. Erőforráscsoportok megjelennek a **erőforráscsoportok** listája.

    ![Erőforráscsoportok](./media/contoso-migration-infrastructure/resource-groups.png) 


#### <a name="create-matching-security-groups-on-premises"></a>Hozzon létre megfelelő biztonsági csoportok a helyszíni

1. A helyszíni Active Directoryban a Contoso kiderül, hogy az Azure erőforráscsoport-sablonok nevének egyeznie biztonsági csoportok beállítása.
 
    ![A helyszíni Active Directory biztonsági csoportokat](./media/contoso-migration-infrastructure/on-prem-ad.png) 

2. Felügyeleti szempontból azokat, amelyek nem kerülnek be az összes többi csoport további csoport létrehozása. Ez a csoport összes erőforráscsoport jogokkal rendelkezik az Azure-ban. Globális rendszergazdák korlátozott számú a rendszer ehhez a csoporthoz adja hozzá.

### <a name="synchronize-ad"></a>Az AD szinkronizálása

Contoso szeretne közös identitás biztosítása a helyszíni erőforrások eléréséhez, és a felhőben. Ehhez az Azure AD azok integrálása a helyszíni Active Directoryban. Ebben a modellben:

- Felhasználók és a szervezetek egy egyetlen identitást hozzáférni a helyszíni alkalmazásokhoz és felhőszolgáltatásokhoz, mint például az Office 365 vagy az interneten lévő többi hely ezer előnyeinek életbe.
- Rendszergazdák kihasználhatják a csoportok megvalósításához Active Directory [szerepköralapú hozzáférés vezérlés (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) az Azure-ban.

Lehetővé teszi az integrációt, a Contoso használja a [Azure AD Connect eszköz](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Miután telepítette, és konfigurálja az eszközt a tartományvezérlőn, a helyi szinkronizálják a helyszíni AD-identitások az Azure ad. 

### <a name="download-the-tool"></a>Töltse le az eszközt

1. Az Azure-portálon az Azure Ugrás **Azure Active Directory** > **az Azure AD Connect**, és letölti az eszköz legújabb verzióját a kiszolgálóhoz, a szinkronizálás használatát.

    ![Letöltési AD Connect](./media/contoso-migration-infrastructure/download-ad-connect.png) 

2. Elindítja a **AzureADConnect.msi** telepítés használatával **gyorsbeállítások használata**. Ez a leggyakrabban használt telepítését, és egy egyerdős topológiával, a Jelszókivonat-szinkronizálást a hitelesítéshez használható.

    ![AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz1.png) 

3. A **az Azure AD Connect**, akkor adja meg a hitelesítő adatokat az Azure AD (a képernyő CONTOSO\admin vagy contoso.com\admin) való kapcsolódáshoz.

    ![AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz2.png) 

4. A **csatlakozás az Active Directory tartományi szolgáltatások**, akkor adja meg a helyszíni hitelesítő adatait az AD.

     ![AD Connect varázsló](./media/contoso-migration-infrastructure/ad-connect-wiz3.png) 

5. A **beállíthatja az**, kattintanak **a szinkronizálási folyamat indítása, ha a konfiguráció befejezése** a szinkronizálás azonnali indításához. Majd telepíteniük.


- A Contoso Azure közvetlen kapcsolattal rendelkezik. Ha a helyszíni AD, a rendszer proxy mögött olvasható ez [cikk](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-connectivity).
- Az első szinkronizálást követően a helyszíni Active Directory-objektumok láthatók az Azure AD-ben.

    ![A helyszíni AD az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 

- A Contoso informatikai csapat minden csoportban betöltött szerepük alapján jelennek meg.

    ![A helyszíni AD tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-group-members.png) 

### <a name="set-up-rbac"></a>Az RBAC beállítása

Azure [szerepköralapú hozzáférés-vezérlést (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) lehetővé teszi a részletes hozzáféréskezelést az Azure-bA. Az RBAC használata lehetővé teszi, hogy csak olyan mértékű hozzáférést biztosítson, ami a felhasználóknak a feladataik elvégzéséhez szükséges. A megfelelő RBAC szerepkör hozzárendelése felhasználók, csoportok és alkalmazások egy hatókör szintjén. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást. 

Contoso most szerepkörök hozzárendelése az AD-csoportok a helyszíni szinkronizált.

1. Az a **ControlCobRG** erőforráscsoport, kattintson **hozzáférés-vezérlés (IAM)** > **Hozzáadás**.
2. A **engedélyek hozzáadása** > **szerepkör**, és melyik **közreműködő**, és válassza ki a **ContosoCobRG** AD-csoport a listából. A csoport megjelenik majd **kijelölt tagok** listája. 
3. Ezek ismételje meg ezt az egyéb erőforráscsoportok ugyanazokkal az engedélyekkel (kivéve a **ContosoAzureAdmins**), a közreműködői engedélyekkel ad hozzá az AD-fiókot, amely megfelel az erőforráscsoportot.
4. Az a **ContosoAzureAdmins** AD-csoport hozzárendelése a **tulajdonos** szerepkör.

    ![A helyszíni AD tagok az Azure-ban](./media/contoso-migration-infrastructure/on-prem-ad-groups.png) 


## <a name="step-3-design-for-resilience-and-disaster"></a>3. lépés: A rugalmasság és a vészhelyreállítás tervezése

Azure-erőforrások régiók belül vannak telepítve.
- Régiók földrajzi vannak szervezve, és rezidens, a közös joghatóság alá, a megfelelőség és a rugalmasság követelményeit is figyelembe véve földrajzi határain belül.
- A régió adatközpontok készlete áll. Ezek adatközpontok késleltetés által definiált szegélyhálózati belül van telepítve, és egy dedikált regionális alacsony késleltetésű hálózaton keresztül kapcsolódik.
- Minden Azure-régió, egy másik régióban, a rugalmasságot van párosítva.
- További információ a [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/), és megismerheti [hogyan van párosítva régiók](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).


Nyissa meg az USA keleti régiója 2 (Virginia található), az elsődleges régióban, mint a másodlagos régióba USA középső RÉGIÓJA contoso mellett döntött. Többféle ennek oka:

- A Contoso datacenter győrben található, és azokat figyelembe venni a legközelebbi adatközpont késése.
- 2 USA keleti régiójában rendelkezik, a szolgáltatás és a termékek kell használni. Nem minden Azure-régiók ugyanazok a termék és az elérhető szolgáltatások. Tekintse át [régiónként Azure termékek](https://azure.microsoft.com/global-infrastructure/services/).
- USA középső RÉGIÓJA az Azure-párosított régió, az USA keleti régiója 2.

Mivel ezek a hibrid környezet gondolja, Contoso kell kell figyelembe venni, hogyan hozhat létre a rugalmasság és a vész-helyreállítási stratégiát régió rendelkeznek. Széles körű stratégiák közé a egyetlen területi telepítést, a szolgáltatások, például a tartalék tartományok és regionális hibatűrőnek, párosítás keresztül történő teljes aktív-aktív modell felhőszolgáltatások és adatbázis központilag telepítve és karbantartási Azure platformon két régió felhasználóit.

A Contoso egy középső közúti érvénybe választotta. Azokat fogja telepíteni az alkalmazásokat és az elsődleges régióban erőforrásokat, és ne a teljes infrastruktúra a másodlagos régióba, hogy működjön, és teljes biztonsági mentés esetén a teljes alkalmazás katasztrófa, vagy ha régió nem készen áll.


## <a name="step-4-design-a-network-infrastructure"></a>4. lépés: A hálózati infrastruktúra kialakítása

Helyen régió rendelkeznek a Contoso figyelembe kell venni egy hálózati stratégia készen áll. Gondolja, hogy a helyszíni adatközpontját és Azure csatlakozás kommunikálnak egymással, illetve az Azure-ban a hálózati infrastruktúra megtervezésére van szükségük. Kifejezetten van szükségük:

**Hibrid hálózati kapcsolatok tervezése**: rájönni arra, hogyan azok fog hálózatok csatlakoztatása a helyszíni és az Azure között.
**Egy Azure hálózati infrastruktúra megtervezésére**: döntse el, hogyan azokat elérhetővé hálózatok a régiók keresztül. Hogyan kell tájékoztatnia a hálózatokon belül ugyanabban a régióban, és régiók között.
**És beállítása az Azure-hálózatok**: állítsa be az Azure-hálózatok és alhálózatok, és döntse el, mi legyen elhelyezve a bennük foglalt.

### <a name="plan-hybrid-network-connectivity"></a>Hibrid hálózati kapcsolatok tervezése

Contoso figyelembe veendő egy [architektúrák száma](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) a hibrid hálózatkezelés Azure és a helyszíni adatközpont között. [További](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) kapcsolatos lehetőségek összehasonlítása.

Ne feledje a Contoso a helyi hálózati infrastruktúra jelenleg áll Győri az adatközpontjában, és helyi elágazik az Amerikai keleti részén.  Minden helyen van egy üzleti osztály internetkapcsolat.  Összes majd csatlakozik-e az Adatközpont IPSec VPN-alagúton keresztül az interneten keresztül.

![Contoso hálózati](./media/contoso-migration-infrastructure/contoso-networking.png) 

Ez hogyan Contoso úgy döntött, a hibrid kapcsolat megvalósításához:

1. A Contoso datacenter Győri és a két Azure-régiók USA keleti régiója 2 és USA középső RÉGIÓJA között új pont-pont VPN-kapcsolat beállítása.
2. A fő Contoso datacenter keresztül átirányítja az fiókirodai office adatforgalomra egy Azure virtuális hálózatot. 
3. Akkor növelheti az Azure-telepítés, mivel azok az adatközpontjában, és az Azure-régiók között ExpressRoute kapcsolat fog létesíteni. Ha ez történik, a VPN-webhelyek kapcsolat csak feladatátvételi célokra lesz megőrzését.
    - [További](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/considerations) közötti VPN- és ExpressRoute hibrid megoldás kiválasztása.
    - Győződjön meg arról [ExpressRoute helyek és a támogatás](https://docs.microsoft.com/azure/expressroute/expressroute-locations-providers).


**Csak VPN**

![Contoso VPN](./media/contoso-migration-infrastructure/hybrid-vpn.png) 


**VPN- és ExpressRoute**

![Contoso VPN/ExpressRoute](./media/contoso-migration-infrastructure/hybrid-vpn-expressroute.png) 


### <a name="design-the-azure-network-infrastructure"></a>Az Azure hálózati infrastruktúra tervezése

Nagyon fontos, hogy a Contoso hálózatok hozzon létre úgy, hogy a hibrid telepítés segítségével biztonságos és skálázható módon telepíthetők. Ehhez az szükséges, Contoso végzése a hosszú távú megközelítést, és virtuális hálózatokról (Vnetekről) kell lennie a rugalmasság és készen áll a vállalati tervez. [További](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) Vnetek tervezésével kapcsolatos.

Szeretne csatlakozni a két régió, a Contoso központ hub hálózati modell megvalósításához határozott meg:

- Minden régióban a Contoso központ küllős modell fogja használni.
- Szeretne csatlakozni, hálózatok és hubok, Contoso fogja használni, Azure hálózati társviszony-létesítés.

#### <a name="network-peering"></a>Hálózati társviszony-létesítés

Azure biztosít a hálózati társviszony-létesítést úgy, hogy csatlakozzon a virtuális hálózatokat és hubok. Globális társviszony-létesítés lehetővé teszi, hogy a különböző régiókban Vnetek/hubok kapcsolatainak. Helyi társviszony-létesítés csatlakozik a Vnetek ugyanabban a régióban. VNet-társviszony létesítése – számos előnyt nyújtanak:

- Nincsenek társviszonyban Vnetek közötti hálózati forgalmat a sajátja.
- A Vnetek közötti forgalom Microsoft hálózat másolatok. A Vnetek közötti kommunikáció nincs nyilvános interneten, az átjárók vagy a titkosításhoz kötelező.
- Társviszony-létesítés biztosít egy alapértelmezett, a különböző Vnetek az erőforrások alacsony késésű és nagy sávszélességű kapcsolatot.

[További](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) kapcsolatos hálózati társviszony-létesítés.

#### <a name="hub-to-hub-across-regions"></a>Hub-az-hub régiók között

Contoso telepíti minden egyes régió egy csomópontjában. Egy virtuális hálózatot (VNet) is, amely különbséglemezként funkcionál középpontja a helyszíni hálózatot az Azure-ban. A központ Vnetek fog csatlakozni egymáshoz globális Vnetben társviszony-létesítés használatával. Globális Vnetben társviszony-létesítés a Vnetek Azure-régiók közötti kapcsolatot.

- A központ minden régióban nincsenek társviszonyban, a partner hubhoz más régióban.
- A központ nincsenek társviszonyban, minden hálózathoz a régióban, és csatlakozni tud-e az összes hálózati erőforrásokhoz.

    ![Globális társviszony-létesítés](./media/contoso-migration-infrastructure/global-peering.png) 

#### <a name="hub-and-spoke-within-a-region"></a>Hub-és-küllős régión belül

Minden régióban Contoso telepíti Vnetekhez különböző célokra küllős hálózatokként a régió központi. Régión belül Vnetek csatlakozhatnak-társviszony létesítése – szeretne az elosztóhoz, és egymást.

#### <a name="design-the-hub-network"></a>A központi hálózat tervezése

Belül a küllős modellt, amely a Contoso választotta, hogyan foglalkoznia kell a helyszíni adatközpontját és az internetről érkező forgalmat, továbbítja. Ez hogyan Contoso kezelésére, az USA keleti régiója 2 és a központi USA hubok útválasztási döntött:

- A "visszirányú c","néven ismert hálózati azok tervez, ez ugyanis, hogy az elérési utat, a bejövő, a kimenő hálózati kövesse a csomagok.
- A hálózati architektúra tartalmaz két határokat, egy nem megbízható előtér-szegélyhálózati zóna és a háttér-megbízható helyek zónájába.
- A tűzfal van egy hálózati adapter minden egyes zónában megbízható zónák hozzáférés szabályozása.
- Az internetről:
    - Internetes forgalom elért egy elosztott terhelésű nyilvános IP-címet a szegélyhálózaton.
    - Ilyen típusú adatforgalom a tűzfalon keresztül, és tűzfalszabályok továbbítódik.
    - Hálózati hozzáférés-vezérlést valósíthatók meg, miután forgalmat továbbítja a megfelelő helyre a megbízható helyek zónájába.
    - A vnet kimenő forgalom az internethez, felhasználó által definiált útvonalak (udr-EK) használata történik. A forgalom kényszeríti a tűzfalon keresztül, és megfelelően Contoso házirendek megvizsgálni.
- A Contoso datacenter:
    - Bejövő forgalom a VPN-helyek (vagy ExpressRoute) a nyilvános IP-címét az Azure VPN gateway találatok.
    - Tűzfal-szabályokat és a tűzfalon keresztül továbbítódik.
    - Szabályok alkalmazása után forgalmat továbbíthatja a rendszer a belső megbízható helyek zónájába alhálózaton belső terheléselosztót (Standard Termékváltozat).
    - VPN-kapcsolaton keresztül a helyszíni adatközpontját a megbízható alhálózati kimenő forgalmat a tűzfal és a szabályokat alkalmazza, mielőtt a felhasználó a VPN-helyek kapcsolaton keresztül történik.



### <a name="design-and-set-up-azure-networks"></a>És Azure-hálózatok beállítása

A hálózati és útválasztási topológiájának helyen a Contoso készen áll az Azure-hálózatok és alhálózatok beállítása.

- A Contoso Azure (0.0.0.0 127.255.255.255) egy osztály A magánhálózaton valósítja meg. Ez működik, a helyszíni óta aktuális rendelkeznek a B osztály privát cím terület 172.160.0/16, hogy a meg arról, hogy bármely átfedés címtartományai között nem lehet.
- Ezek fog telepíteni az elsődleges és másodlagos régióban Vnetek.
- Ezek fogja használni, amely tartalmazza az előtag elnevezési **VNET** és a régió rövidítés **EUS2** vagy **logikai csoport EGYÉ**. Ezt a szabványt használ, a központ hálózatok lesznek elnevezve **VNET-HUB-EUS2** (USA keleti régiója 2), és **VNET-HUB-logikai csoport EGYÉ** (központi USA).
- Nem rendelkezik a Contoso egy [IP-Címkezelő megoldás](https://docs.microsoft.com/windows-server/networking/technologies/ipam/ipam-top), így azok kell terveznie az hálózati útválasztási nélkül hálózati címfordítást.


#### <a name="virtual-networks-in-east-us-2"></a>Virtuális hálózatok, az USA keleti régiója 2. régiója

USA keleti régiója 2 az elsődleges régióban, amely a Contoso erőforrások és szolgáltatások telepítéséhez használandó. Itt látható, hogyan fog azok felelős mérnök hálózatok:

- **Hub**: A virtuális hálózat, az USA keleti régiója 2 is középpontja a helyszíni adatközpontját elsődleges kapcsolata.
- **Vnetek**: küllő Vnetek az USA keleti régiója 2 segítségével szükség esetén a feladatok elkülönítése céljából. A központ VNet mellett a Contoso két küllős Vnetek az USA keleti régiója 2 fog rendelkezni:
    - **VNET-FEJLESZTŐI-EUS2**. Ez a virtuális hálózat a fejlesztési és tesztelési munkacsoport fog egy teljesen működőképes hálózati fejlesztői projektek ad meg. Egy éles kísérleti területre fog működni, és a termelési infrastruktúrával függvény hagyatkoznak.
    - **VNET-termék-EUS2**: Azure IaaS éles összetevőket is található, a hálózathoz. 
    -  Minden egyes virtuális hálózat lesz a saját egyedi címterület-átlapolás nélkül. Kívánnak konfigurálja az útválasztást anélkül, hogy a hálózati címfordítást.
- **Alhálózatok**:
    - Nem lesznek alhálózat minden hálózat minden egyes alkalmazás szinten
    - A termelési hálózat minden alhálózat egy egyező alhálózatot rendelkezik fejlesztői virtuális.
    - Emellett az éles hálózati környezetben van tartományvezérlők alhálózatot.

Az alábbi táblázat foglalja össze Vnetek az USA keleti régiója 2.

**Virtuális hálózat** | **tartomány** | **társ**
--- | --- | ---
**VNET-HUB-EUS2** | 10.240.0.0/20 | VNET-HUB-CUS2, A VNET-FEJLESZTŐI-EUS2, A VNET-TERMÉK-EUS2
**VNET-FEJLESZTŐI-EUS2** | 10.245.16.0/20 | VNET-HUB-EUS2
**VNET-TERMÉK-EUS2** | 10.245.32.0/20 | VNET-HUB-EUS2, VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ

![Hub/küllős elsődleges régióban](./media/contoso-migration-infrastructure/primary-hub-peer.png) 


#### <a name="subnets-in-the-east-us-2-hub-network-vnet-hub-eus2"></a>A keleti Velünk 2 Hub (VNET-HUB-EUS2) hálózat alhálózatai

**Alhálózat/zóna** | **CIDR** | ** Használható IP-címet
--- | --- | ---
**IB-UntrustZone** | 10.240.0.0/24 | 251
**IB-TrustZone** | 10.240.1.0/24 | 251
**Objekt-UntrustZone** | 10.240.2.0/24 | 251
**Objekt-TrustZone** | 10.240.3.0/24 | 251
**GatewaySubnets** | 10.240.10.0/24 | 251


#### <a name="subnets-in-the-east-us-2-dev-network-vnet-dev-eus2"></a>A keleti Velünk 2 fejlesztői (VNET-fejlesztői-EUS2) hálózat alhálózatai

A fejlesztői VNet egy éles kísérleti terület lesz a fejlesztő csapat által. Három alhálózatot tartalmaz.

**Alhálózat** | **CIDR** | **címek** | **Az alhálózaton**
--- | --- | --- | ---
**FEJLESZTŐI-FE-EUS2** | 10.245.16.0/22 | 1019 | Virtuális gépek Frontends vagy webes réteg
**FEJLESZTŐI-ALKALMAZÁS-EUS2** | 10.245.20.0/22 | 1019 | Alkalmazás rétegbeli virtuális gépek
**FEJLESZTŐI-DB-EUS2** | 10.245.24.0/23 | 507 | Adatbázis virtuális gépek


#### <a name="subnets-in-the-east-us-2-production-network-vnet-prod-eus2"></a>Keleti Velünk 2 éles hálózati környezetben működő (VNET-termék-EUS2) alhálózatai

Az Azure IaaS-összetevők az éles hálózati környezetben található. Minden alkalmazás szinthez saját alhálózatba tartozik. Alhálózatok egyeznek, a fejlesztői hálózat tartományvezérlők alhálózatot hozzáadásával.

**Alhálózat** | **CIDR** | **címek** | **Az alhálózaton**
--- | --- | --- | ---
**TERMÉK-FE-EUS2** | 10.245.32.0/22 | 1019 | Virtuális gépek Frontends vagy webes réteg
**TERMÉK-ALKALMAZÁS-EUS2** | 10.245.36.0/22 | 1019 | Alkalmazás rétegbeli virtuális gépek
**TERMÉK-DB-EUS2** | 10.245.40.0/23 | 507 | Adatbázis virtuális gépek
**TERMÉK-DC-EUS2** | 10.245.42.0/23 | 251 | Tartományvezérlő virtuális gépek


![Központi hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-eus2.png)


#### <a name="virtual-networks-in-central-us-secondary-region"></a>USA középső RÉGIÓJA (másodlagos régióba) lévő virtuális hálózatok

USA középső RÉGIÓJA Contoso másodlagos régióban. Itt látható, hogyan fog azok felelős mérnök hálózatok:

- **Hub**: A virtuális hálózat, az USA keleti régiója 2 kapcsolat a helyszíni adatközpontját és a Vnetek az USA keleti régiója 2 segítségével szükség esetén feladatok elkülönítése céljából küllős középpontja hub külön más küllők felügyelt.
- **Vnetek**: két Vnetek Államok középső rendelkeznek:
    - VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ. Ez a virtuális hálózat egy üzemi hálózat, a VNET-PROD_EUS2 hasonló. 
    - VNET-ASR-LOGIKAI CSOPORT EGYÉ. Ez a virtuális hálózat fog működni, olyan hely, ahol a feladatátvétel a helyszíni után a virtuális gépek jönnek létre, vagy olyan hely, az Azure virtuális gépeken, amelyek feladatátvétel történt az elsődleges a másodlagos régióba. Ez a hálózat hasonlít az éles hálózati környezetben, de nincs meg minden olyan tartományvezérlőn.
    -  Minden régióban VNet lesz a saját címterület-átlapolás nélkül. Kívánnak konfigurálja az útválasztást anélkül, hogy a hálózati címfordítást.
- **Alhálózatok**: az alhálózatok fog tervezett, a hasonló módon ezeket az USA keleti régiója 2. A kivétel ez alól, hogy a tartományvezérlők nem kell egy alhálózatot.

A Vnetek Államok középső az alábbi táblázat foglalja össze.

**Virtuális hálózat** | **tartomány** | **társ**
--- | --- | ---
**VNET-HUB-LOGIKAI CSOPORT EGYÉ** | 10.250.0.0/20 | VNET-HUB-EUS2, A VNET-ASR-LOGIKAI CSOPORT EGYÉ, A VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ
**VNET-ASR-LOGIKAI CSOPORT EGYÉ** | 10.255.16.0/20 | VNET-HUB-LOGIKAI CSOPORT EGYÉ, VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ
**VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ** | 10.255.32.0/20 | VNET-HUB-LOGIKAI CSOPORT EGYÉ, A VNET-ASR-LOGIKAI CSOPORT EGYÉ, A VNET-TERMÉK-EUS2  


![Hub/küllős párosított régióban](./media/contoso-migration-infrastructure/paired-hub-peer.png)


#### <a name="subnets-in-the--central-us-hub-network-vnet-hub-cus"></a>A központi Velünk Hub (VNET-HUB-logikai csoport EGYÉ) hálózat alhálózatai

**Alhálózat** | **CIDR** | **Használható IP-címet**
--- | --- | ---
**IB-UntrustZone** | 10.250.0.0/24 | 251
**IB-TrustZone** | 10.250.1.0/24 | 251
**Objekt-UntrustZone** | 10.250.2.0/24 | 251
**Objekt-TrustZone** | 10.250.3.0/24 | 251
**A GatewaySubnet** | 10.250.2.0/24 | 251


#### <a name="subnets-in-the-central-us-production-network-vnet-prod-cus"></a>A központi, éles hálózati környezetben (VNET-termék-logikai csoport EGYÉ) alhálózatai

USA keleti régiója 2 elsődleges régióban éles hálózati környezetben működő párhuzamosan nincs éles hálózati környezetben a másodlagos régióban USA középső RÉGIÓJA. 

**Alhálózat** | **CIDR** | **címek** | **Az alhálózaton**
--- | --- | --- | ---
**TERMÉK-FE-LOGIKAI CSOPORT EGYÉ** | 10.255.32.0/22 | 1019 | Virtuális gépek Frontends vagy webes réteg
**TERMÉK-ALKALMAZÁS-LOGIKAI CSOPORT EGYÉ** | 10.255.36.0/22 | 1019 | Alkalmazás rétegbeli virtuális gépek
**TERMÉK-DB-LOGIKAI CSOPORT EGYÉ** | 10.255.40.0/23 | 507 | Adatbázis virtuális gépek
**TERMÉK-DC-LOGIKAI CSOPORT EGYÉ** | 10.255.42.0/24 | 251 | Tartományvezérlő virtuális gépek

#### <a name="subnets-in-the-central-us-failoverrecovery-network-in-central-us-vnet-asr-cus"></a>USA középső RÉGIÓJA (VNET-ASR-logikai csoport EGYÉ) a az USA középső RÉGIÓJA feladatátvételi vagy helyreállítási hálózat alhálózatai

A VNET-ASR-logikai csoport EGYÉ hálózati régiók közötti feladatátvétel célokat szolgál. A Site Recovery replikálásához és feladatátadásához Azure virtuális gépeken, régiók közötti használandó. Azt a helyszíni maradnak, de a feladatátadást vész-helyreállítási védett munkaterhelések Azure-hálózatot a Contoso adatközpontot is funkcionál.

VNET-ASR-logikai csoport EGYÉ az üzemi virtuális hálózat, az USA keleti régiója 2. régiója, de a tartomány a tartományvezérlő alhálózat nélkül alapvető ugyanabban az alhálózatban.

**Alhálózat** | **CIDR** | **címek** | **Az alhálózaton**
--- | --- | --- | ---
**AZ ASR-FE-LOGIKAI CSOPORT EGYÉ** | 10.255.16.0/22 | 1019 | Virtuális gépek Frontends vagy webes réteg
**AZ ASR-ALKALMAZÁS-LOGIKAI CSOPORT EGYÉ** | 10.255.20.0/22 | 1019 | Alkalmazás rétegbeli virtuális gépek
**AZ ASR-DB-LOGIKAI CSOPORT EGYÉ** | 10.255.24.0/23 | 507 | Adatbázis virtuális gépek

![Központi hálózati architektúra](./media/contoso-migration-infrastructure/azure-networks-cus.png)

#### <a name="configure-peered-connections"></a>Nincsenek társviszonyban kapcsolatok konfigurálása

A központ minden régióban fog társítottak, a központ más régióban, illetve a központ régión belül minden Vnetek. Ez lehetővé teszi hubok való kommunikációhoz, és megtekintheti az összes Vnetek régión belül. Vegye figyelembe:

- Társviszony-létesítés kapcsolatot hoz létre duplex. Az első VNet a kezdeményező levő helyreállítani, és egy másik, a második virtuális hálózaton.
- Az egy hibrid telepítésben forgalomhoz, amely egyenrangú társak közötti kell a VPN-kapcsolat a helyszíni adatközpontját és az Azure közötti láthatók. Ennek engedélyezéséhez van néhány speciális beállítással társviszonyban kapcsolatok használata esetén be kell állítania.

Bármely érkező kapcsolatok küllős Vnetek keresztül a központ a helyszíni adatközponthoz Contoso kell továbbított és keresztirányú-kezelési forgalom engedélyezése a VPN-átjárók.

##### <a name="domain-controller"></a>Tartományvezérlő

A tartományvezérlők a VNET-termék-EUS2 hálózati a Contoso biztosítani a forgalmat a EUS2 hub/éles hálózati környezetben között, mind a helyszíni VPN-kapcsolaton keresztül. Ehhez szükséges lehetővé teszi az alábbiak:

1. **Továbbított forgalom engedélyezése** és **átjáró átvitel konfigurációk engedélyezése** a peered kapcsolatban. Ebben a példában a VNET-HUB-EUS2 VNET-termék-EUS2 kapcsolatra lenne.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering1.png)

2. **Engedélyezi a továbbított forgalmat** és **távoli átjárókat használ** más oldalán a társviszony, a VNET-termék-EUS2 VNET-HUB-EUS2 kapcsolatra.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering2.png)

3. A helyszíni fog beállítani a biztonsági statikus útvonal, amely a helyi forgalmát átirányítja a virtuális hálózaton a VPN-alagúton keresztül irányíthatja. Az átjárón, amely a VPN-alagút biztosít a Contoso Azure szeretné elvégezni a konfigurálását. A Contoso a Windows útválasztási és távelérési használ.

    ![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering3.png)

##### <a name="production-networks"></a>Éles hálózati környezetben 

Egy spoked társ hálózati spoked társ hálózaton keresztül a központ egy másik régióban nem látható.

A Contoso-éles hálózati környezetben megtekintéséhez egymás mindkét régiókban van szükségük a VNET-termék-EUS2 és BBI-termék-logikai csoport EGYÉ közvetlen peered VPN-kapcsolat létrehozásához. 

![Társviszony-létesítés](./media/contoso-migration-infrastructure/peering4.png)

### <a name="set-up-dns"></a>DNS-t

Erőforrások virtuális hálózatok központi telepítésekor több tartománynév feloldásával kapcsolatos lehetősége van. Azure által biztosított névfeloldás használata, vagy adja meg a DNS-kiszolgálók a feloldásához. A névfeloldás használata típusa attól függ, hogyan kell az erőforrások kommunikálnak egymással. Első [további információt](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#azure-provided-name-resolution) az Azure DNS szolgáltatással kapcsolatos.

Contoso mellett döntött, hogy az Azure DNS-szolgáltatás nincs-e a hibrid környezetben érdemes választani. Ehelyett azok fog használni a helyi DNS-kiszolgálóra.

- Mivel ez egy hibrid minden a virtuális gépek helyszíni hálózat és az Azure-ban kell tudják feloldani a megfelelő működéséhez. Ez azt jelenti, hogy a Vnetek egyéni DNS-beállításokat kell alkalmazni.
- Contoso már van telepítve a Contoso-adatközpontban és a fiókirodákban található tartományvezérlők. Az elsődleges DNS-kiszolgálók CONTOSODC1(172.16.0.10) és CONTOSODC2(172.16.0.1)
- Ha a Vnetek vannak telepítve, a helyszíni tartományvezérlők állítja be a hálózatok DNS-kiszolgálóként használni. 
- Ennek konfigurálásához, amikor a virtuális hálózaton egyéni DNS-sel Azure rekurzív feloldókat IP-cím (például 168.63.129.16) a DNS-listához hozzá kell adni.  Ehhez az szükséges, Contoso minden egyes virtuális hálózaton konfigurálja a DNS-kiszolgáló beállításai. Például a VNET-HUB-EUS2 hálózati egyéni DNS-beállításainak következőképpen nézne ki:
    
    ![Egyéni DNS](./media/contoso-migration-infrastructure/custom-dns.png)

A helyi tartományvezérlő mellett Contoso vannak valósít meg négy további-támogatja az Azure-hálózatok, két mindegyik régióhoz. Ez mit azok fogja központilag telepíteni az Azure-ban.

**Régió** | **TARTOMÁNYVEZÉRLŐ** | **Virtuális hálózat** | **Alhálózat** | **IP-cím**
--- | --- | --- | --- | ---
EUS2 | CONTOSODC3 | VNET-TERMÉK-EUS2 | TERMÉK-DC-EUS2 | 10.245.42.4
EUS2 | CONTOSODC4 | VNET-TERMÉK-EUS2 | TERMÉK-DC-EUS2 | 10.245.42.5
LOGIKAI CSOPORT EGYÉ | CONTOSODC5 | VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ | TERMÉK-DC-LOGIKAI CSOPORT EGYÉ | 10.255.42.4
LOGIKAI CSOPORT EGYÉ | CONTOSODC6 | VNET-TERMÉK-LOGIKAI CSOPORT EGYÉ | TERMÉK-DC-LOGIKAI CSOPORT EGYÉ | 10.255.42.4

A helyszíni tartományvezérlők üzembe helyezésekor, után Contoso kell a DNS-beállítások vagy régió, hálózatok az új tartományvezérlők szerepeljenek a DNS-kiszolgálók listája.



#### <a name="set-up-domain-controllers-in-azure"></a>Az Azure-ban tartományvezérlők beállítása

Hálózati beállítások frissítése után Contoso készen áll az Azure-ban a tartományvezérlők felé.

1. Az Azure portálon ezek a megfelelő virtuális hálózatba egy új Windows Server virtuális gép központi telepítése.
2. Azok minden helyen a virtuális gép rendelkezésre állási készletek hozza létre. Rendelkezésre állási készletek tegye a következőket:
    - Győződjön meg arról, hogy az Azure-hálót a virtuális gépek elválasztja a különböző infrastruktúra az Azure-régióban. 
    -  Lehetővé teszi, hogy jogosult legyen a virtuális gépek az Azure 99,95 % SLA Contoso.  [További információk](https://docs.microsoftcom/azure/virtual-machines/windows/regions-and-availability#availability-sets).

    ![Rendelkezésre állási csoport](./media/contoso-migration-infrastructure/availability-group.png) 
3. A virtuális gép telepítése után azok toll, a hálózati illesztő a virtuális gép számára. Itt, akkor állítsa be a magánhálózati IP-címe statikus cím, és adjon meg érvényes címet.

    ![VIRTUÁLIS GÉP HÁLÓZATI ADAPTER](./media/contoso-migration-infrastructure/vm-nic.png)

4. Most hogy adatlemezzel új a virtuális géphez. Ezt a lemezt az Active Directory-adatbázis és a sysvol-megosztás tartalmaz. 
    - A lemez mérete határozza meg, hogy az támogatja-e IOPS száma.
    - Adott idő alatt a környezet növekedésével növelje a lemez mérete módosítania kell.
    - A meghajtó-állomás gyorsítótárazását írható/olvashatóvá nem állítható be. Active Directory-adatbázis nem támogatja ezt.

     ![Active Directory-lemez](./media/contoso-migration-infrastructure/ad-disk.png)

5. A lemez hozzáadása után azokat a virtuális gép kapcsolódni a távoli asztali kapcsolaton keresztül, és nyissa meg a Kiszolgálókezelőt.
6. Ezt a **fájl- és tárolási szolgáltatások**, az új kötet varázsló, amely biztosítja, hogy a meghajtó a megadott F: betű vagy újabb futnak a helyi virtuális Gépen.

     ![Új kötet varázsló](./media/contoso-migration-infrastructure/volume-wizard.png)

7. A Kiszolgálókezelő megnövelik a **Active Directory tartományi szolgáltatások** szerepkör. Ezt követően azok a virtuális gép konfigurálása tartományvezérlőként működik.

      ![Kiszolgálói szerepkör](./media/contoso-migration-infrastructure/server-role.png)  

9. Miután a virtuális gép van konfigurálva, a tartományvezérlő és újraindítása után, nyissa meg a DNS-kezelőben, és konfigurálása az Azure DNS-feloldási továbbítóként.  Ez lehetővé teszi, hogy a tartományvezérlő nem oldható fel az Azure DNS DNS-lekérdezéseket.

    ![DNS-továbbító](./media/contoso-migration-infrastructure/dns-forwarder.png)

10. Most majd frissítse az egyéni DNS-beállítások minden egyes vnet és a megfelelő tartományvezérlő, a virtuális hálózat régió. A helyszíni tartományvezérlők tartalmazzák a listában.

### <a name="set-up-active-directory"></a>Az Active Directory beállítása

AD a hálózatokban fontos szolgáltatás, és megfelelően kell konfigurálni. Contoso AD helyekben a Contoso datacenter, valamint a EUS2 és a logikai csoport EGYÉ régiók fog létrehozni.  

1. Akkor hozzon létre a datacenter hely (ContosoDatacenter) és két új helyeket (AZURE-EUS2 és AZURE-logikai csoport EGYÉ).
2. Miután létrehozta a helyek, azok a helyek, a virtuális hálózatokat és a datacenter egyező alhálózatok hozzon létre.

    ![DC alhálózatok](./media/contoso-migration-infrastructure/dc-subnets.png)

3. Ezután ezek hozzon létre két helykapcsolatok mindent csatlakozni. A tartományvezérlők majd helyezni az helyükre.

    ![DC hivatkozások](./media/contoso-migration-infrastructure/dc-links.png)

4. Miután minden be van állítva, az Active Directory replikációs topológiájának rendelkezésre áll.
    
    ![DC-replikáció](./media/contoso-migration-infrastructure/ad-resolution.png)

5. Minden befejeződött, és a tartományvezérlők és helyek listáját a helyszíni Active Directory felügyeleti központ láthatók.

    ![AD felügyeleti központban](./media/contoso-migration-infrastructure/ad-center.png)

## <a name="step-5-plan-for-governance"></a>5. lépés: A cégirányítási megtervezése

Azure services és az Azure platform lehetővé teszi az irányítás vezérlők számos. [További](https://docs.microsoft.com/azure/security/governance-in-azure) vonatkozó beállítások alapvető ismeretekkel.

Az identitás és hozzáférés-vezérlést, Contoso bizonyos irányítási és a biztonsági szempontok bevezetni már megkezdődött. Széles körű nincsenek három olyan terület, akkor figyelembe kell vennie:

- **Házirend**: az Azure-ban házirend vonatkozik, és érvényesíti a szabályok és hatások keresztül az erőforrások, hogy megfelel a vállalati követelményeknek és a szolgáltatásiszint-szerződések maradnak erőforrások.
- **Zárolja**: Azure lehetővé teszi a zárolási előfizetések, erőforrások csoportokat és más erőforrások, így azok csak módosíthatják azokat ehhez hitelesítésszolgáltatóval.
- **Címkék**: erőforrások hozható szabályozott, naplózva, és kezelhető címkékkel. Címkék metaadatok csatolása erőforrásokat, és erőforrásokat, illetve eszköztulajdonosok információt nyújt.

### <a name="set-up-policies"></a>Házirendek beállítása

Az Azure szolgáltatás kiértékeli az erőforrások, azok nem felelnek meg a már működik a házirend-definíciók keresése. Például előfordulhat, hogy rendelkezik olyan házirendet, amely csak lehetővé teszi, hogy a virtuális gépek bizonyos típusú, vagy egy adott címkének erőforrásokat igényel. 

Azure házirendek adja meg a házirend-definíció, és a házirend-hozzárendelést, adja meg, amelyben a házirendet alkalmazni hatókörét. A hatókör felügyeleti csoportból az erőforráscsoporthoz között lehet. [Ismerje meg,](https://docs.microsoft.com/azure/azure-policy/create-manage-policy) létrehozása és házirendek kezelése.

Ismerkedés a házirendek néhány contoso szeretne:

- Annak érdekében, hogy erőforrások csak telepíthetők a EUS2 és a logikai csoport EGYÉ régiókban házirend kívánják.
- Csak a jóváhagyott termékváltozatok a virtuális gép termékváltozatok korlátozni kívánja azokat. Nem biztosítására, hogy költséges VM SKU nem kívánja.

#### <a name="limit-resources-to-regions"></a>Korlát erőforrásokat régiók

Contoso használja a beépített házirend-definíció **engedélyezett helyek** erőforrás régiók korlátozása.

1. Az Azure portálon kattintson **minden szolgáltatás**, keresse meg a **házirend**.
2. Válassza ki **hozzárendelések** > **házirendet**.
3. A szabályzatok listájában válassza ki a **engedélyezett helyek**.
4. Állítsa be **hatókör** az Azure-előfizetés, és válassza ki az engedélyezett listában szereplő két régió nevét.

    ![Engedélyezett régiók házirend](./media/contoso-migration-infrastructure/policy-region.png)

5. Alapértelmezés szerint a házirend beállítása a **Megtagadás**, ami azt jelenti, hogy ha valaki elindítja a központi telepítés, amely nem a EUS2 vagy logikai csoport EGYÉ az előfizetést, a telepítés meghiúsul. Mi történik, ha valaki van a Contoso előfizetés megpróbál egy üzembe helyezést beállítani az USA nyugati régiója itt található.

    ![Nem sikerült házirend](./media/contoso-migration-infrastructure/policy-failed.png)

#### <a name="allow-specific-vm-skus"></a>Adott virtuális gép termékváltozatok engedélyezése

Contoso fogja használni a beépített házirend-definíció **lehetővé teszi a virtuális gépek termékváltozatok** korlátozására virtuális gépeket, az előfizetés hozhatók létre. 

![Házirend Termékváltozat](./media/contoso-migration-infrastructure/policy-sku.png)



#### <a name="check-policy-compliance"></a>Szabályzatoknak való megfelelés ellenőrzése

Házirendek életbe azonnal, és a Contoso ellenőrizheti a megfelelőségét erőforrásokhoz. 

1. Az Azure portálon kattintson a **megfelelőségi** hivatkozásra.
2. A megfelelőség-irányítópultot jelenik meg. Részletezve további tájékoztatást talál.

    ![Szabályzatmegfelelőség](./media/contoso-migration-infrastructure/policy-compliance.png)


### <a name="set-up-locks"></a>Zárolások beállítása

Contoso hosszú használja az ITIL keretrendszer rendszereik kezelését. Egyik legfontosabb szempontja a keretrendszer vezérlő váltása, és a Contoso biztosítani szeretné, győződjön meg arról, hogy az Azure-telepítés meg van valósítva a változáskezelés.

Contoso fog zárolás végrehajtása az alábbiak szerint:

- Minden éles vagy a feladatátvételi összetevő, amely rendelkezik a csak olvasható zárolási erőforráscsoportban kell lennie.  Ez azt jelenti, hogy vagy éles elemek törlése, a zárolás el kell távolítani. 
- Nem éles erőforráscsoportok CanNotDelete zárolások fog rendelkezni. Ez azt jelenti, hogy a hitelesített felhasználók olvasni vagy módosítani az erőforrást, de nem lehet törölni.

[További](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) vonatkozó zárolásokat.

### <a name="set-up-tagging"></a>Címkézés beállítása

Erőforrások követheti nyomon, mivel azok van-e adva, szüksége lesz egyre a Contoso egy megfelelő részleg, az ügyfél és a környezet erőforrások hozzárendelni. 

Erőforrások információit és tulajdonosok lépések, mellett címkék lehetővé teszi Contoso összesítő és csoportosítási erőforrásokat, és az adatokat a jóváírási célokra használható.

Contoso kell úgy, hogy az üzleti érdemes az Azure eszközök megjelenítéséhez. Például, de a szerepkör vagy a részleg. Vegye figyelembe, hogy az erőforrások azonos címkével lássa ugyanabban az erőforráscsoportban lenniük, hogy nem kell. Ehhez az szükséges, a Contoso létrehoz egy egyszerű címkerendszert, hogy bárki használja ugyanazt a címkét.

**A címke neve** | **Érték**
--- | ---
Költséghely | 12345: egy érvényes erőforrás elérhető kell legyen.
Részleghez | (Az SAP) részleg nevét. Egyezések CostCenter.
ApplicationTeam | Az alkalmazás támogatására vonatkozó birtokló csoport e-mail aliasát.
Katalógusnév | Az alkalmazás vagy ShareServices, nevenként a szolgáltatási katalógus, amely támogatja az erőforrás.
ServiceManager | E-mailek alias az ITIL a Service Manager az erőforráshoz.
COBPriority | A prioritás által az üzleti célú beállítva. Az 1-5 értékeket.
ENV | Fejlesztői, STG, termék a lehetséges értékek. Képviselő fejleszt, átmeneti és üzemi.

Példa: 

 ![Az Azure címkék](./media/contoso-migration-infrastructure/azure-tag.png)

Miután létrehozta a címke, a Contoso lépjen vissza, és hozzon létre új Azure házirend-definíciók és hozzárendeléseket, a szükséges címkéket használatának kényszerítése a teljes szervezetben.


## <a name="step-6-consider-security"></a>6. lépés:, Fontolja meg biztonsági

Biztonsági elengedhetetlen a felhőben, és Azure széles köréről biztonsági eszközöket és képességeket nyújt. Ezek segítségével hozható létre biztonságos megoldás, a biztonságos Azure platformon. Olvasási [abban, hogy a megbízható felhőben](https://azure.microsoft.com/overview/trusted-cloud/) tudhat meg többet az Azure biztonsági.

Van néhány fő szempontokat kell figyelembe venni a contoso

- **Az Azure Security Center**: az Azure Security Center biztosít egységes biztonsági kezelése és az advanced threat protection hibrid felhő-munkaterhelések között. A Security Centerrel biztonsági szabályzatokat alkalmazhat a számítási feladatokra, korlátozhatja a fenyegetéseknek való kitettséget, valamint észlelheti és elháríthatja a támadásokat.  [További információk](https://docs.microsoft.com/azure/security-center/security-center-intro).
- **Hálózati biztonsági csoportokkal (NSG-k)**: egy NSG egy szűrő (tűzfal) felsorolja azokat a biztonsági szabályok, amelyek alkalmazása esetén adatforgalom engedélyezéséhez vagy letiltásához hálózati erőforrások az Azure Vnetekhez csatlakoztatni. [További információk](https://docs.microsoft.com/azure/virtual-network/security-overview).
- **Adattitkosítás**: Azure Disk Encryption egy olyan képességet, amely segít a Windows és Linux IaaS virtuális gépek lemezeit titkosításához. [További információk](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest).

### <a name="work-with-the-azure-security-center"></a>Az Azure Security Center használata

A Contoso egy gyors képet kaphat a biztonsági állapotát az új hibrid felhős a és kifejezetten az Azure munkaterhelések keres.  Ennek eredményeképpen Contoso határozott végrehajtásához az Azure Security Center kezdődő, és a következő szolgáltatásokat: 

- Központosított szabályzatkezelés
- Folyamatos ellenőrzése
- Végrehajtható javaslatok 

#### <a name="centralize-policy-management"></a>Központosíthatja a Csoportházirend kezelése

Központi Csoportházirend kezelése, a Contoso által központilag teljes környezetüket között a biztonsági házirendek kezelése biztosítja a biztonsági követelményeknek való megfelelés majd. Akkor is gyorsan és egyszerűen létrehozható egy házirendet, amely minden, az Azure-erőforrások vonatkozik.

![Biztonsági házirend](./media/contoso-migration-infrastructure/security-policy.png)

#### <a name="assess-and-action"></a>Értékeléséhez és művelet

Contoso fogja használni, a folyamatos biztonsági értékelést, amely figyeli a biztonsági gépek, hálózatok, tárolási, adat és alkalmazások; potenciális biztonsági problémákat felderítéséhez. 

- A Security Center elemzi a Contoso-számítási, infrastruktúra és erőforrásokat, illetve az Azure-alkalmazások és szolgáltatások biztonsági állapotát.
- Folyamatos assessment segít a Contoso műveleti csapata felderítéséhez a potenciális biztonsági problémákat, például a rendszer a hiányzó biztonsági frissítéseket, vagy hálózati portok kitett. 
- A Contoso különösen biztosítania kell a virtuális gépek mindegyikének védelmét biztosítani. Security Center segítségével, a virtuális gép állapotának ellenőrzése és biztonsági rések kijavítani, ahhoz, azok még kihasznált rangsorolt és végrehajthatóként ajánlások.

![Figyelés](./media/contoso-migration-infrastructure/monitoring.png)

### <a name="work-with-nsgs"></a>Az NSG-k használata

Contoso korlátozhatja a hálózati forgalmat hálózati biztonsági csoportok használata a virtuális hálózatán lévő erőforrásokat.

- A hálózati biztonsági csoport egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy a cél IP-címe, illetve portok vagy protokollok alapján.
- Ha egy alhálózat alkalmazza, szabályok érvényesek az alhálózat összes erőforrást. Hálózati illesztők mellett ez az alhálózat üzembe helyezett Azure services példányát magában foglalja.
- Biztonsági csoportok (ASGs) lehetővé teszik a hálózati biztonság beállítása egy alkalmazás struktúra, amely lehetővé teszi a csoport virtuális gépek természetes bővítménye, és határozza meg a hálózati biztonsági házirendek alapján ezeket a csoportokat.
    - Alkalmazás biztonsági csoportok, az azt jelenti, hogy a biztonsági házirend léptékű manuális karbantartást explicit IP-címek nélkül is újrahasznosíthatja. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat.
    - Az alkalmazásbiztonsági csoportokat megadhatja forrásként és célként is a biztonsági szabályokban. Miután a biztonsági házirend lett meghatározva, virtuális gépek létrehozása, és rendelje hozzá a virtuális gép hálózati adapterek csoportba. 


Contoso NSG-ket és ASGs fogja végrehajtani. Érintett NSG-kezeléssel kapcsolatos. Kapcsolatos túlhasználat NSG-ket, és azt jelentheti, az a műveleti személyzet összetettsége miatt aggódó fontosságúak.  Ennek tudatában általuk elfogadott két fő rendszerbiztonsági tagok, hogy általános szabály használata:

- Az összes forgalom esetében bejövő és kimenő minden alhálózat (Észak-déli irányú), egy NSG szabály, kivéve a központ hálózatok GatewaySubnets érvényesek lesznek.
- A tűzfalak és a tartományvezérlő által alhálózati NSG-ket és a hálózati NSG-ket is védi.
- Az összes üzemi környezetben működő alkalmazásokat ASGs alkalmazva lesz.


Contoso létrehozta ezt megjelenését az alkalmazásuk kezelésére mintáját.

![Biztonság](./media/contoso-migration-infrastructure/asg.png)


Az NSG-ket a ASGs társított legalacsonyabb jogosultsági szint annak biztosítására, hogy csak engedélyezett csomagok is haladjanak a hálózat egy részét a rendeltetési van konfigurálva.

**Művelet** | **Name (Név)** | **Forrás** | **Target** | **Port**
--- | --- | --- | --- | --- 
Engedélyezés | AllowiInternetToFE | VNET-HUB-EUS1/IB-TrustZone | AZ APP1-FE 80-AS ÉS 443-AS
Engedélyezés | AllowWebToApp | AZ APP1-FE | AZ APP1-ADATBÁZIS | 1433
Engedélyezés | AllowAppToDB | AZ APP1-ALKALMAZÁS | Bármelyik | Bármelyik
Megtagadás | DenyAllInbound | Bármelyik | Bármelyik | Bármelyik

### <a name="encrypt-data"></a>Adatok titkosítása

Az Azure Disk Encryption integrálja az Azure Key Vault szabályozni, és a lemez-titkosítási kulcsok és titkos kulcsainak kulcstároló előfizetés kezeléséhez. Ez biztosítja, hogy a virtuális gépek lemezei lévő összes adat titkosítva legyenek az Azure tárolás közben.  

- Contoso megállapította, hogy adott virtuális gépek titkosítás használata.
- Ezek fog alkalmazni a virtuális gépek ügyféllel, bizalmas, titkosítás vagy PPI adatok.


## <a name="conclusion"></a>Összegzés

Ebben a cikkben Contoso beállítása az Azure-infrastruktúra és állítsa be, vagy tervezett infrastruktúra házirend Azure-előfizetés, hibrid kikereséséhez vész-helyreállítási, a hálózat, az irányítás és a biztonsági. 

A lépéseket, amelyek a Contoso befejeződött itt közül nem mindegyik szükségesek a felhőre való áttérést. Abban az esetben azok kívánta tervezze meg a hálózati infrastruktúra áttelepítések minden alkalmazástípus esetében használható, és biztonságos, rugalmas és méretezhető. 

Az infrastruktúrával, és azok készen lépés, és próbálja ki az áttelepítés.

## <a name="next-steps"></a>További lépések

Egy első áttelepítési forgatókönyv szerint Contoso is szeretné áttelepíteni a helyszíni SmartHotel kétféle alkalmazásuk Azure VMware virtuális gépeken futó. Az alkalmazás virtuális gépeket az Azure virtuális gépen, és az alkalmazás adatbázis egy Azure SQL-felügyelt példányon fogja át.
