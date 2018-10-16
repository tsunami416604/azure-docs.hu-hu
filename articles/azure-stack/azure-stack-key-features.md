---
title: Funkciók és fogalmak az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az a legfontosabb jellemzők és fogalmak az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: ''
ms.openlocfilehash: 16c908dabd313cd9d64ce5be9b7d0299423a7675
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344685"
---
# <a name="key-features-and-concepts-in-azure-stack"></a>Főbb funkcióiról és koncepciójáról az Azure Stackben
Ha most ismerkedik a Microsoft Azure Stack, ezeket a feltételeket és a szolgáltatások leírása akkor lehet hasznos.

## <a name="personas"></a>Szerepek
Nincsenek a felhő üzemeltetője (szolgáltató) és a bérlő (felhasználói) a Microsoft Azure Stack felhasználói két fajta.

* A **felhőüzemeltető** Azure Stack konfigurálhatja és kezelheti az ajánlatok, tervek, szolgáltatások, kvóták és díjszabás erőforrásokat biztosít a bérlők.  Felhő üzemeltetői is kapacitás kezelése és válaszadás a riasztásokra.  
* A **bérlői** (más néven a felhasználó) használ fel szolgáltatásokat kínál a felhő rendszergazdájához. Bérlők kiépítéséhez, figyeléséhez és azok feliratkozott, például a Web Apps, a Storage és a virtuális gépek szolgáltatások felügyeletéhez.

## <a name="portal"></a>Portál
Az elsődleges használata a Microsoft Azure Stack módszereket a felügyeleti portálon, a felhasználói portál és a PowerShell.

Az Azure Stack portálon minden egyes biztonsági külön példányok az Azure Resource Manager által.  A felhő üzemeltetője használ a felügyeleti portálon, kezelheti az Azure Stack, valamint a van például az ajánlatok bérlő létrehozása.  A felhasználói portál (más néven a bérlői portál) önkiszolgáló élményt nyújt a felhőbeli erőforrások, például a virtuális gépek, tárfiókok és a Web Apps fogyasztását. További információkért lásd: [az Azure Stack rendszergazdai és felhasználói portálok használata](azure-stack-manage-portals.md).

## <a name="identity"></a>Identitás 
Az Azure Stack használ vagy az Azure Active Directory (AAD), vagy az Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltatóként.  

### <a name="azure-active-directory"></a>Azure Active Directory
Az Azure Active Directory a Microsoft felhőalapú, több-bérlős identitásszolgáltató.  A legtöbb hibrid forgatókönyvek az ügyfélidentitás-tárolóval, az Azure Active Directory használja.

### <a name="active-directory-federation-services"></a>Active Directory összevonási szolgáltatások
Előfordulhat, hogy használni kívánja az Active Directory összevonási szolgáltatások (AD FS) az Azure Stack kapcsolat nélküli telepítésekhez.  Az Azure Stack, erőforrás-szolgáltatók és más alkalmazások működnek az AD FS-sel ugyanolyan módon, az Azure Active Directoryval. Az Azure Stack saját AD FS és az Active Directory-példány és a egy Active Directory Graph API-t tartalmaz. Az Azure Stack Development Kit a következő az AD FS-forgatókönyveket teszi lehetővé:

- Jelentkezzen be az üzembe helyezés az AD FS használatával.
- A virtuális gép létrehozása Key Vault titkos
- Hozzon létre egy tárolót a titkos kulcsok tárolása és elérése
- Egyéni RBAC-szerepkörök létrehozása előfizetésben
- Felhasználók hozzárendelése az erőforrások RBAC-szerepkörök
- Azure PowerShell-lel rendszerszintű RBAC-szerepkörök létrehozása
- Jelentkezzen be a felhasználó Azure PowerShell-lel
- Szolgáltatás létrehozása egyszerű ezek segítségével jelentkezzen be az Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Régiók, szolgáltatások, csomagok, ajánlatok és előfizetések
Az Azure Stackben szolgáltatások régiók, előfizetések, ajánlatok és csomagok használatával bérlők érkeznek. Több ajánlattal bérlők előfizethetnek. Ajánlatok egy vagy több csomag is rendelkezhet, és a csomagok egy vagy több szolgáltatást is rendelkezhet.

![](media/azure-stack-key-features/image4.png)

Példa hierarchiája egy bérlő előfizetési ajánlatok, amelyek mindegyike különböző csomagok és -szolgáltatásokat.

### <a name="regions"></a>Régiók
Az Azure Stack-régiók olyan méreteivel és alapvető elemét. Egy szervezet több régióban elérhető erőforrásokhoz való lehet az egyes régiókban. Régióban is elérhető különböző szolgáltatási ajánlatok. Az Azure Stack Development Kit, csak egy adott régióban támogatott, és automatikusan nevű *helyi*.

### <a name="services"></a>Szolgáltatások
A Microsoft Azure Stack lehetővé teszi, hogy a szolgáltatók számára a szolgáltatások és alkalmazások, például a virtuális gépeket, SQL Server adatbázisok, a SharePoint, Exchange és több számos.

### <a name="plans"></a>Tervek
Csomagok egy vagy több szolgáltatás csoportjai. A szolgáltató kiajánlhat a bérlőinek terveket hozhat létre. A bérlői előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat.

Minden hozzáadott díjcsomagra szolgáltatás is konfigurálhatók, amelyek segítségével kezelheti a felhő kapacitása kvótabeállításokat. Kvóták lehetnek korlátozásai – például a virtuális gép, memória és CPU-korlátok, és felhasználói előfizetésenként érvényesek. Hely szerint is kell megkülönböztetni a kvóták. Egy régióban A számítási szolgáltatások tartalmazó csomagot sikerült például két virtuális gépet, 4 GB Memóriára és 10 CPU-magok kvóta tartozik.

Az ajánlat létrehozásakor a szolgáltatás-rendszergazda is tartalmazhat egy **ALAPCSOMAG**. Amikor egy bérlő előfizet az ajánlat ezek alapcsomagok alapértelmezés szerint jelennek meg. Amikor a felhasználó (és az előfizetés létrehozása), a felhasználó hozzáfér az e alap terveket (és a megfelelő kvóták) a megadott összes erőforrás-szolgáltatót.

A szolgáltatás-rendszergazda is tartalmazhatnak **kiegészítő csomagok** egy ajánlatban. Az előfizetés alapértelmezés szerint nem szerepelnek a kiegészítő csomagok. Kiegészítő csomagok elérhető, amely az előfizetés tulajdonosa adhat hozzá az előfizetés egy ajánlatra további terveket (kvóták).

### <a name="offers"></a>Ajánlatok
Ajánlatok csoportjai, egy vagy több olyan csomag, amelyet szolgáltatók nyújtanak a bérlőknek megvásárlásra (előfizetésre). Ajánlat Alpha tartalmazhat például terv A számítási szolgáltatások és a tárolási és hálózati szolgáltatások tartalmazó csomag B tartalmazó.

Az ajánlat a alapcsomagok készletét, és a szolgáltatás-rendszergazdák hozhat létre, amely a bérlők előfizetésüket adhat hozzá kiegészítő csomagok.

### <a name="subscriptions"></a>Előfizetések
Az előfizetés egy hogyan a bérlők számára az ajánlatok vásárolni. Egy előfizetés, egy bérlőt egy ajánlatot kombinációját. A bérlői előfizetések több ajánlattal is rendelkezhet. Az egyes előfizetésekhez érvényes csak egyszer veheti igénybe. A bérlői előfizetések határozza meg, mely csomagok és szolgáltatások eléréséhez.

Előfizetések rendszerezése és a felhőbeli erőforrások és szolgáltatások szolgáltatók segítségével.

A rendszergazdák számára a szolgáltatói előfizetés alapértelmezett üzembe helyezés során jön létre. Ez az előfizetés kezelése az Azure Stack, további erőforrás-szolgáltatókat telepíteni, és hozzon létre csomagokat és ajánlatokat bérlők használható. Nem használandó ügyfél számítási feladatok és alkalmazások futtatásához. Két további előfizetéseket kiegészítik az 1804-verzióval kezdve az alapértelmezett szolgáltatója előfizetés; Mérés előfizetést, és a egy Használatalapú előfizetéssel. Ezen kiegészítéseit lehetővé teszik a megadhat, az alapvető infrastruktúra, a további erőforrás-szolgáltatók és a számítási feladatok kezelését.  

## <a name="azure-resource-manager"></a>Azure Resource Manager
Az Azure Resource Managerrel, az infrastruktúra-erőforrások, sablon-alapú, deklaratív modellben dolgozhat.   Üzembe helyezés és kezelés a megoldás-összetevőket segítségével egyetlen felületet biztosít. Teljes körű információkat és útmutatást lásd: a [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Erőforráscsoportok
Erőforráscsoportok erőforrások, szolgáltatások és alkalmazások gyűjteményei, és minden erőforrás rendelkezik egy típus, például a virtuális gépek, virtuális hálózatok, nyilvános IP-címek, tárfiókok és webhelyeket. Minden erőforrás egy erőforráscsoportba tartozó legyen, és így az erőforráscsoportok segítségével logikailag erőforrások rendszerezése, például a számítási feladat vagy a hely szerint.  A Microsoft Azure Stack erőforrások, például a csomagokat és ajánlatokat is felügyelt erőforráscsoport.

Ellentétben [Azure](../azure-resource-manager/resource-group-move-resources.md), hogy erőforrások nem helyezhetők át erőforráscsoportok között. Az Azure Stack felügyeleti portálon egy erőforrás vagy erőforráscsoport tulajdonságainak megtekintésekor a *áthelyezése* gomb akkor érhető el, és nem érhető el. 
 
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
Az Azure Resource Managerrel, létrehozhat olyan sablonokat (JSON formátumban), amely meghatározza, üzembe helyezését és konfigurálását az alkalmazás. Ez a sablon egy Azure Resource Manager-sablon nevezik, és lehetővé teszi a deklaratív üzembe helyezési meghatározása. A sablonok segítségével az alkalmazást ismételten telepítheti az alkalmazás életciklusa során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

## <a name="resource-providers-rps"></a>Erőforrás-szolgáltatók (RPs)
Erőforrás-szolgáltatók olyan webes szolgáltatásokat, amelyek alapját alkotják, az összes Azure-alapú IaaS és PaaS-szolgáltatások. Az Azure Resource Manager-szolgáltatásokhoz való hozzáférés biztosításához a különböző RPs támaszkodik.

Nincsenek négy alapvető RPs: hálózati, tárolási, számítási és KeyVault. Ezek RPs mindegyike segítségével konfigurálhatja, és szabályozhatja a megfelelő erőforrásokkal. Szolgáltatás-rendszergazdákat is hozzáadhat új egyéni erőforrás-szolgáltatók.

### <a name="compute-rp"></a>Helyreállítási pont Védettként COMPUTE
A Compute Resource Provider (CRP) lehetővé teszi, hogy az Azure Stack-bérlők számára saját virtuális gépek létrehozása. A CRP képes létrehozni virtuális gépeket, valamint a virtuális gépi bővítmények tartalmazza. A virtuálisgép-bővítmény szolgáltatás segít az IaaS-képességeket nyújt a Windows és Linux rendszerű virtuális gépek.  Tegyük fel a KSZT használatával egy Linux rendszerű virtuális gép üzembe helyezése és futtatása a Bash-szkriptek konfigurálása a virtuális gép üzembe helyezése során.

### <a name="network-rp"></a>Hálózati RP
A hálózati erőforrás-szolgáltató (NRP) a magánfelhő hálózati függvény virtualizációs (NFV) és a szoftveralapú hálózatkezelés (SDN) funkciókat kínál.  Használhatja az NRP erőforrások, például szoftver load balancers, nyilvános IP-címek, hálózati biztonsági csoportok, virtuális hálózatok létrehozásához.

### <a name="storage-rp"></a>Tárolási RP
A Storage RP négy egységes Azure-storage-szolgáltatások nyújtásának: blob, table, queue és fiókok kezelése. Nyújt egy felhőalapú felügyeleti társzolgáltatás megkönnyítése érdekében a service provider felügyeleti egységes Azure-Storage-szolgáltatás is. Az Azure Storage tárolja, és hívhat le nagy mennyiségű strukturálatlan adat, például dokumentumokat és médiafájlokat az Azure-Blobok, rugalmasan és strukturált NoSQL-alapú adatok az Azure Tables segítségével. További információ az Azure Storage: [a Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
A BLOB storage bármilyen adatkészlet tárolja. Egy blob állhat bármilyen szövegből vagy bináris adatból, lehet például egy dokumentum, egy médiafájl vagy egy alkalmazástelepítő. A TABLE storage strukturált adatkészleteket tárolja. A Table Storage a NoSQL-kulcsattribútumok adattára, amely gyors fejlesztési lehetőségeket és nagy adatmennyiségek gyors elérését biztosítja. A Queue storage megbízható üzenetküldést, a munkafolyamat-feldolgozáshoz és a felhőszolgáltatás összetevői közötti kommunikációhoz biztosít.

Összes blobjának egy tárolóban vannak rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. Egy tárfiók tetszőleges számú tárolót tartalmazhat, és a egy tároló korlátlan számú blobot a tárfiók 500 TB-os kapacitásán belül is tartalmazhat. A Blob Storage háromféle blobot biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat. A blokkblobok a felhőbeli objektumok streamelésére és tárolására vannak optimalizálva, és kiválóan alkalmasak a dokumentumok, médiafájlok, biztonsági másolatok stb. tárolására. A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni. Lapblobok infrastruktúra-szolgáltatási lemezek megjelenítésére vannak optimalizálva, és a támogató véletlenszerű ír, és legfeljebb 1 TB lehet. Egy Azure virtuálisgép-hálózathoz csatlakoztatott infrastruktúra-szolgáltatási lemez tulajdonképpen egy lapblobként tárolt VHD.

#### <a name="table-storage"></a>Table Storage
A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár – rendelkezik egy tervezés nélkül sémák, kivitelezésében különbözik a hagyományos relációs adatbázisoktól. Mivel hiánya sémák adattárak, könnyebbé vált a igazíthatja az adatok az alkalmazás változásával igényeinek. A Table Storage könnyen használható, így a fejlesztők gyorsan létrehozhatják benne alkalmazásaikat. A Table Storage egy kulcs-/attribútumtár, ami azt jelenti, hogy a táblázatok minden értékét egy típusos tulajdonságnévvel tárolja. A tulajdonságnév felhasználható szűréshez vagy a kiválasztási feltételek megadásához is. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Tárolási hiánya táblasémákat, mert ugyanaz a tábla két entitása a Tulajdonságok különböző gyűjteményeit is tartalmazhat, és azokat a tulajdonságokat, különböző típusú is lehet. A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

#### <a name="queue-storage"></a>Queue Storage
Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

### <a name="keyvault"></a>KeyVault
A KeyVault RP biztosít a felügyeleti és a titkos kulcsokat, például a jelszavak és tanúsítványok naplózását. Például egy bérlő segítségével a KeyVault RP rendszergazdai jelszavak vagy -kulcsok adja meg a virtuális gép üzembe helyezése során.

## <a name="high-availability-for-azure-stack"></a>Magas rendelkezésre állás az Azure Stackben
*A következőkre vonatkozik: Azure Stack 1802-es vagy újabb verziók*

Magas rendelkezésre állás az Azure-beli virtuális gépre kiterjedő éles rendszer, a virtuális gépek kerülnek egy rendelkezésre állási csoportban, a azokat több tartalék tartomány és frissítési tartományok. Ezzel a módszerrel [a rendelkezésre állási csoportokban üzembe helyezett virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) fizikailag elkülönített egymással a különálló kiszolgálóra állványokon, hogy hiba rugalmasság, az alábbi ábrán látható módon:

  ![Az Azure Stack magas rendelkezésre állás](media/azure-stack-key-features/high-availability.png)

### <a name="availability-sets-in-azure-stack"></a>Rendelkezésre állási csoportok az Azure Stackben
Azure Stack infrastruktúrája már hatással a meghibásodások, míg az alapul szolgáló technológiát (feladatátvételi fürtszolgáltatás) továbbra is leállást bizonyos virtuális gépek érintett fizikai kiszolgálón hardverhiba esetén. Az Azure Stack támogatja a rendelkezésre állási csoport, amely legfeljebb három tartalék tartományt az Azure-ral konzisztens kellene.

- **Tartalék tartományok**. Virtuális gépeket egy rendelkezésre állási csoportot helyezett osztja szét őket lehető legegyenletesebben több tartalék tartomány (az Azure Stack-csomópontok) keresztül lesz fizikailag különítve egymástól. Hardverhiba esetén a sikertelen tartalék tartomány virtuális gépeket fogja indítani a többi tartalék tartományban, de, külön tartalék tartományokban, ha lehetséges, a más virtuális gépek ugyanazon rendelkezésre állási csoportban tartani. A hardver visszatér online állapotba, ha virtuális gépek fog rebalanced magas rendelkezésre állás fenntartása érdekében. 
 
- **Frissítési tartományok**. Frissítési tartományok egy másik Azure demonstráció létrehozásában, amely a rendelkezésre állási csoportokat magas rendelkezésre állást biztosít. Frissítési tartomány, amelyek karbantartása egy időben is mennek keresztül alapul szolgáló hardver logikai csoportjai. Az azonos frissítési tartományban található virtuális gépek együtt tervezett karbantartás során újraindul. Bérlő virtuális gépek létrehozása rendelkezésre állási csoportban, mint az Azure platform automatikusan elosztja a virtuális gépek között ezek frissítési tartományok. Az Azure Stackben, virtuális gépek élő áttelepítése a fürt többi online gazdagép között, a mögöttes állomás frissítése előtt. A gazdagép frissítése közben nem bérlői jár, mivel a frissítési tartomány funkció az Azure Stacken csak létezik sablon kompatibilitás érdekében az Azure-ral. 

### <a name="upgrade-scenarios"></a>Frissítési forgatókönyvek esetében 
Virtuális gépek az Azure Stack 1802-es verzió kapnak egy alapértelmezett tartalék és frissítési tartományok száma előtt létrehozott rendelkezésre állási csoportok (1 és 1 jelölik). Magas rendelkezésre állás virtuális gépekhez a már meglévő rendelkezésre állási csoportokban, először törölnie kell a meglévő virtuális gépek, majd ezt követően telepítse újra azokat egy új rendelkezésre állási számokkal a megfelelő tartalék és frissítési tartomány leírtak szerint, [módosítása a Windows virtuális gép rendelkezésre állási](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Virtuálisgép-méretezési csoportokhoz tartozó rendelkezésre állási csoport jön létre belső egy alapértelmezett tartalék tartomány és frissítési tartományok száma (3. és 5 jelölik). Az 1802-es frissítés kerülnek egy rendelkezésre állási csoport előtt létrehozott bármely virtuális gép méretezési az alapértelmezett tartalék és frissítési tartomány száma (1 és 1 jelölik). Szeretné frissíteni a virtuális gép méretezési csoport példányaihoz újabb megállítását eléréséhez, horizontális felskálázás a virtuálisgép-méretezési csoportok, amelyek a jelen az 1802-es frissítés előtt voltak példányok száma alapján, és törölje a régebbi a virtuálisgép-méretezési csoportok példányai. 

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Az RBAC használatával hozzáférést rendszer engedéllyel rendelkező felhasználók, csoportok és szolgáltatások egy előfizetés, erőforráscsoport vagy egyéni erőforrás szintjén szerepkörök hozzárendelésével. Minden egyes szerepkör a felhasználó, csoport vagy szolgáltatás rendelkezik a Microsoft Azure Stack-erőforrások hozzáférési szint határozza meg.

Az Azure RBAC rendelkezik három alapvető szerepkörök, amelyek érvényesek az összes erőforrástípus: tulajdonos, közreműködő és olvasó. Tulajdonos az összes olyan erőforrásokhoz, beleértve a jogot arra, hogy mások való hozzáférés delegálására teljes hozzáféréssel rendelkezik. Közreműködői hozhat létre és az Azure-erőforrások összes típusa felügyelhető, de nem adhat hozzáférést másoknak. Olvasó csak megtekintheti a meglévő Azure-erőforrások. A többi Azure-beli RBAC-szerepkörök lehetővé teszik az adott Azure-erőforrások kezelése. A virtuális gépek Közreműködője szerepkör például-virtuális gépek létrehozása és kezelése lehetővé teszi, de nem teszi lehetővé a virtuális hálózat és az alhálózathoz, amelyhez csatlakozik a virtuális gép felügyeletét.

## <a name="usage-data"></a>Használati adatok
A Microsoft Azure Stack gyűjti és összesíti az összes erőforrás-szolgáltató különböző használati adatait, és továbbítja azt az Azure-bA a feldolgozáshoz az Azure kereskedelmi. Az Azure Stacken az összegyűjtött használati adatok a REST API-n keresztül is megtekinthetők. Van egy egységes Azure-bérlői API-t, valamint a szolgáltató és delegált szolgáltató API-k az összes bérlő előfizetés használati adatainak beolvasásához. Ezeket az adatokat egy külső eszközzel vagy a szolgáltatás számlázási vagy költséghelyi elszámolás integrálásához használható. Használat az Azure kereskedelmi által feldolgozott, amint azt az Azure számlázási portálján lehet megtekinteni.

## <a name="in-development-build-of-azure-stack-development-kit"></a>A fejlesztési hozhat létre az Azure Stack fejlesztői készlete
A fejlesztési környezetek lehetővé teszik a korai támogatók kiértékelése az Azure Stack Development Kit legújabb verzióját. A legutóbbi jelentős kiadása alapján növekményes buildek zajlik. Főbb verziók továbbra is feloldják néhány havonta, míg a fejlesztés a buildek közzéteszik időnként a kiadások között.

A fejlesztési környezetek lesz a következő előnyöket nyújtják:
- Hibajavítások
- Új funkciók
- Egyéb fejlesztések

## <a name="next-steps"></a>További lépések
[Az Adminisztráció alapjai](azure-stack-manage-basics.md)

