---
title: "Szolgáltatások és-Azure verem fogalmak |} Microsoft Docs"
description: "A legfontosabb funkcióira és Azure verem fogalmak megismerése."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 09ca32b7-0e81-4a27-a6cc-0ba90441d097
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jeffgilb
ms.reviewer: 
ms.openlocfilehash: b773ddc5da12f92960ef3378decac8569dac9ab9
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="key-features-and-concepts-in-azure-stack"></a>A legfontosabb jellemzők és Azure verem fogalmak

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ha most ismerkedik a Microsoft Azure-vermet, ezeket a feltételeket és a szolgáltatások leírása hasznos lehet.

## <a name="personas"></a>Személyeknek
A felhő üzemeltetője (szolgáltató) és a bérlő (ügyfél) a Microsoft Azure verem, a felhasználók két fajtáinak van.

* A **felhő üzemeltetője** Azure verem konfigurálható és kezelhető a ajánlatokat, tervek, szolgáltatások, kvóták és árképzési erőforrásokat biztosít a bérlők számára.  A felhő üzemeltetői is kapacitás kezelése és válaszadás a riasztásokra.  
* A **bérlői** (más néven a felhasználó) használ fel szolgáltatásokat biztosít a felhő rendszergazdájához. Bérlők kiépítéséhez, figyeléséhez és szolgáltatásokról, amelyek azok előfizetett, például a Web Apps, a tároló és a virtuális gépek kezeléséhez.

## <a name="portal"></a>Portál
Az elsődleges használják a Microsoft Azure verem módszereket a felügyeleti portál, a felhasználói portál és a PowerShell.

Az Azure-verem portálok egyes üzemelnek külön példányokban Azure Resource Manager.  A felhő üzemeltetője a felügyeleti portál Azure verem kezeléséhez, és mint például a bérlő ajánlatok létrehozása használja.  A felhasználói portál (más néven a bérlői portál) önkiszolgáló élményt nyújt a felhőalapú erőforrások, például a virtuális gépeket, a storage-fiókok és a Web Apps felhasználásához. További információkért lásd: [az Azure-verem rendszergazdai és felhasználói portálok](azure-stack-manage-portals.md).

## <a name="identity"></a>Identitás 
Az Azure verem használja vagy az Azure Active Directory (AAD), vagy az Active Directory összevonási szolgáltatások (AD FS) identitás-szolgáltatóként.  

### <a name="azure-active-directory"></a>Azure Active Directory
Az Azure Active Directory a Microsoft felhőalapú, több-bérlős identitásszolgáltató.  A legtöbb hibrid forgatókönyvek az Azure Active Directoryt használva identity.

### <a name="active-directory-federation-services"></a>Active Directory összevonási szolgáltatások
Előfordulhat, hogy szeretne használni az Active Directory összevonási szolgáltatások (AD FS) az Azure-verem leválasztott telepítéséhez.  Az Azure-vermet, erőforrás-szolgáltatók és más alkalmazások működnek hasonlóan az AD FS-sel, az Azure Active Directoryban. Azure verem saját AD FS és az Active Directory példány és egy Active Directory Graph API-t tartalmaz. Az Azure verem szoftverfejlesztői készlet a következő AD FS-forgatókönyveket teszi lehetővé:

- Jelentkezzen be a központi telepítés Active Directory összevonási szolgáltatások használatával.
- A Key Vault a titkos virtuális gép létrehozása
- Hozzon létre egy tárolót a titkos kulcsok tárolásához vagy eléréséhez
- Hozzon létre egyéni RBAC-szerepkörök az előfizetéshez
- Felhasználók hozzárendelése az erőforrás RBAC-szerepkörök
- Hozzon létre rendszerszintű RBAC-szerepkörök Azure PowerShell használatával
- Jelentkezzen be a felhasználó Azure PowerShell használatával
- Szolgáltatás létrehozása rendszerbiztonsági tagok felhasználja jelentkezzen be Azure PowerShell


## <a name="regions-services-plans-offers-and-subscriptions"></a>Régiók, szolgáltatások, tervek, ajánlatok és előfizetések
Azure-készletben szolgáltatások régiók, előfizetések, ajánlatok és tervek bérlők érkeznek. Bérlők több ajánlatok kérhet le. Ajánlatok lehet egy vagy több terveket, és tervek rendelkezhet egy vagy több szolgáltatást.

![](media/azure-stack-key-features/image4.png)

Példa hierarchiája egy bérlői előfizetések ajánlatokat, mindegyiket különböző tervek és szolgáltatásokhoz.

### <a name="regions"></a>Régiók
Azure-régiók verem egy alapszintű elem és a felügyeleti rendszer. A szervezetek mindegyik régióhoz több forrásanyag is elérhető régiók lehet. Régiókban elérhető különböző szolgáltatásajánlatok is. Az Azure verem szoftverfejlesztői készlet, csak egy régiót támogatja, és automatikusan nevű *helyi*.

### <a name="services"></a>Szolgáltatások
A Microsoft Azure verem lehetővé teszi, hogy a szolgáltatók számára a szolgáltatások és alkalmazások, például a virtuális gépeket, SQL Server adatbázisok, SharePoint, Exchange és több széles választékát.

### <a name="plans"></a>Tervek
Tervek csoportosítását egy vagy több szolgáltatást is. -Szolgáltatóként hozzon létre tervek biztosítson a bérlők számára. A bérlői előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat.

Minden hozzáadott előfizetésre szolgáltatás konfigurálható, hogyan kezelheti a felhő kapacitása kvótabeállításokat. Kvóták lehetnek például a virtuális gép, memória és CPU-korlátok korlátozások és felhasználói előfizetésenként érvényesek. Kvóták helye alapján lehet meghatározni. Például egy tervet tartalmazó terület A számítási szolgáltatásai eredményezhet. a kvóta két virtuális gép, 4 GB RAM és 10 CPU-magokat.

Az ajánlat létrehozásakor a szolgáltatás-rendszergazda tartalmazhat egy **referenciaterv**. E alap tervek jelennek meg alapértelmezés szerint egy bérlő előfizet, hogy az ajánlat. Amikor a felhasználó (és az előfizetés létrehozása), a felhasználó hozzáfér az e alap terveket (megfelelő kvótával) megadott összes erőforrás-szolgáltató.

A szolgáltatás-rendszergazda is tartalmazhatnak **bővítmény tervek** az ajánlatot. Az előfizetés alapértelmezés szerint nem szerepelnek a bővítmény tervek. Bővítmény tervek tervekben további (kvóták), amely egy előfizetés tulajdonosa adhat hozzá az előfizetések ajánlatot érhető el.

### <a name="offers"></a>Ajánlatok
Ajánlatok olyan beállításcsoportok, egy vagy több terveket tartalmazhatnak, amelyek a bérlők számára megvásárlása telepített szolgáltatók (előfizetés). Alpha kínálnak például tartalmazhat terv A számítási szolgáltatások és a tárolási és hálózati szolgáltatások tartalmazó megtervezése B halmazát tartalmazza.

Ajánlat tartalmaz egy alap tervek, és a szolgáltatás-rendszergazdák hozhat létre, amely a bérlők adhat hozzá az előfizetés bővítmény tervek.

### <a name="subscriptions"></a>Előfizetések
Az előfizetés egy hogyan bérlők vásároljon az ajánlatokat. Az előfizetés egy ajánlatot egy bérlőt kombinációja. A bérlő rendelkezhet több ajánlatokat az előfizetések. Minden előfizetés csak egy ajánlat vonatkozik. A bérlői előfizetések határozza meg, melyik tervek vagy szolgáltatás eléréséhez.

Előfizetések súgó szolgáltatók rendszerezése és felhőalapú erőforrások és szolgáltatások eléréséhez.

A rendszergazda alapértelmezett szolgáltató előfizetés telepítés során jön létre. Ez az előfizetés kezelése az Azure-vermet, további erőforrás-szolgáltatók telepítéséhez és tervek és ajánlatok létrehozása a bérlők számára használható. Nem használandó felhasználói munkaterhelések és alkalmazások futtatásához. 


## <a name="azure-resource-manager"></a>Azure Resource Manager
Azure Resource Manager segítségével egy sablonalapú, deklaratív modell a infrastruktúra erőforrásainak dolgozhat.   Segítségével központi telepítése és kezelése a megoldás-összetevő egyetlen felületet biztosít. Teljes körű információkat és útmutatást, tekintse meg a [Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md).

### <a name="resource-groups"></a>Erőforráscsoportok
Erőforráscsoportok erőforrások, szolgáltatások és alkalmazások gyűjteményei – és az egyes erőforrások típussal, például a virtuális gépek, virtuális hálózatok, nyilvános IP-címek, storage-fiókok és webhelyek rendelkezik. Minden erőforrás egy erőforráscsoportban legyen, és így erőforráscsoportok logikailag rendszerezése erőforrások, például munkaterhelés vagy a hely szerint.  A Microsoft Azure-készletben erőforrások, például a csomagok és ajánlatokat is felügyelete az erőforráscsoportokat.
 
### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok
Az Azure Resource Manager létrehozhat egy sablont (JSON formátumban), amely meghatározza a az alkalmazás telepítését és konfigurálását. Ez a sablon az Azure Resource Manager sablonként is ismert, és a telepítés meghatározásához deklaratív lehetőséget biztosít. A sablonok segítségével az alkalmazást ismételten telepítheti az alkalmazás életciklusa során, és biztos lehet abban, hogy az erőforrások telepítése konzisztens lesz.

## <a name="resource-providers-rps"></a>Erőforrás-szolgáltatók (RPs)
Erőforrás-szolgáltatók a következők: alapját képezik az összes Azure-alapú IaaS-webszolgáltatások és PaaS szolgáltatások. Az Azure Resource Manager szolgáltatások eléréséhez különböző RPs támaszkodik.

Nincsenek négy eligazodást RPs: hálózati, tárolási, számítási és KeyVault. Ezek RPs mindegyikének segítségével konfigurálhatja és szabályozhatja a megfelelő erőforrásokat. Szolgáltatás-rendszergazdákat is hozzáadhat új egyéni erőforrás-szolgáltatók.

### <a name="compute-rp"></a>Függő Entitás számítási
A számítási erőforrás szolgáltató (CRP) lehetővé teszi, hogy az Azure-verem bérlők saját virtuális gépek létrehozásához. A KSZT lehetővé teszi a virtuális gépeket, valamint a virtuálisgép-bővítmények létrehozásához. A virtuálisgép-bővítmény szolgáltatás segítségével IaaS-képességeket biztosít a Windows és Linux virtuális gépekhez.  Tegyük fel használhatja a KSZT rendszerű Linux virtuális gép és a Bash parancsfájlok futtathatók a központi telepítést, hogy a virtuális gép konfigurálása során.

### <a name="network-rp"></a>Hálózati RP
A hálózati erőforrás-szolgáltató (NRP) biztosítja a szoftveralapú hálózatkezelés (SDN) és hálózati Function Virtualization (NFV) funkciókat a magánfelhő.  Az NRP hozhat létre az erőforrásokhoz, mint a szoftverfrissítési terhelés terheléselosztók nyilvános IP-címek, hálózati biztonsági csoportok, virtuális hálózatok.

### <a name="storage-rp"></a>Tárolási RP
A tárolási RP négy Azure-konzisztens tárolószolgáltatások biztosítja: blob, table, várólista és fiókok kezelése. Egy tároló felhőalapú felügyeleti szolgáltatás lehetővé teszi a service provider felügyeleti Azure-konzisztens tárolási szolgáltatások is biztosít. Az Azure Storage tárolásához és lekéréséhez nagy mennyiségű strukturálatlan adatok, például a dokumentumok és médiafájlok a Azure BLOB, a rugalmasságot biztosít, és strukturált NoSQL-alapú Azure táblákkal adatokat. További információ az Azure Storage: [Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md).

#### <a name="blob-storage"></a>Blob Storage
A BLOB storage bármely adatkészlet tárolja. Egy blob állhat bármilyen szövegből vagy bináris adatból, lehet például egy dokumentum, egy médiafájl vagy egy alkalmazástelepítő. A TABLE storage a strukturált adatkészleteket tárolja. A Table Storage a NoSQL-kulcsattribútumok adattára, amely gyors fejlesztési lehetőségeket és nagy adatmennyiségek gyors elérését biztosítja. A Queue storage biztosít a munkafolyamat feldolgozásra, és a felhőszolgáltatások összetevői közötti kommunikáció megbízható üzenetküldés.

Összes blobjának egy tárolóban vannak rendezve. A tárolók nagy előnye, hogy az objektumok csoportjaihoz biztonsági házirendeket lehet rendelni. Egy tárfiókban korlátlan számú tárolót tárolhat, és egy tároló korlátlan számú blobot tartalmazhat a tárfiók 500 TB-os kapacitásán belül. A Blob Storage háromféle blobot biztosít: blokkblobokat, hozzáfűző blobokat és lapblobokat. A blokkblobok a felhőbeli objektumok streamelésére és tárolására vannak optimalizálva, és kiválóan alkalmasak a dokumentumok, médiafájlok, biztonsági másolatok stb. tárolására. A hozzáfűző blobok a blokkblobokhoz hasonló, de a műveletek hozzáfűzésére optimalizált blobok. A naplóíró blobok csak a végükhöz hozzáadott új blokkal frissíthetők. A naplóíró blobok olyan forgatókönyvekhez felelnek meg leginkább, mint például a naplózás, ahol az új adatokat csak a blob végéhez kell hozzáírni. A lapblobok az infrastruktúra-szolgáltatási lemezek optimalizált blobok, amelyek és támogató véletlenszerű írási műveletek, és akár 1 TB lehet. Egy Azure virtuálisgép-hálózathoz csatlakoztatott infrastruktúra-szolgáltatási lemez tulajdonképpen egy lapblobként tárolt VHD.

#### <a name="table-storage"></a>Table Storage
A TABLE storage a Microsoft NoSQL kulcs-/ attribútumtár – nélkül sémákat, így a hagyományos relációs adatbázisoktól különböző kialakítást rendelkezik. Mivel adatok hiánya sémák tárolja, célszerű könnyen adatait az alkalmazás igényeinek változásával igényeinek. A Table Storage könnyen használható, így a fejlesztők gyorsan létrehozhatják benne alkalmazásaikat. A Table Storage egy kulcs-/attribútumtár, ami azt jelenti, hogy a táblázatok minden értékét egy típusos tulajdonságnévvel tárolja. A tulajdonságnév felhasználható szűréshez vagy a kiválasztási feltételek megadásához is. A tulajdonságok és értékeik gyűjteménye egy entitást alkot. Tárolási hiánya táblasémákat, mert ugyanaz a tábla két entitása a Tulajdonságok különböző gyűjteményeit tartalmazhatja, és ezek a Tulajdonságok különböző típusúak lehetnek. A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. Egy táblán korlátlan számú entitást tárolhat, és egy tárfiók a kapacitásán belül korlátlan számú táblát tartalmazhat.

#### <a name="queue-storage"></a>Queue Storage
Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás összetevői gyakran le vannak választva, hogy egymástól függetlenül lehessen őket méretezni. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

### <a name="keyvault"></a>KeyVault
A KeyVault RP biztosít a felügyeleti és titkos kulcsokat, például a jelszavak és a tanúsítványok naplózását. Tegyük fel a bérlő segítségével a KeyVault RP rendszergazdai jelszóval vagy kulcsok adja meg a virtuális gép üzembe helyezése során.

## <a name="high-availability-for-azure-stack"></a>Magas rendelkezésre állású Azure verem
*A következőkre vonatkozik: Azure verem 1802 vagy újabb verzió*

Egy virtuális Gépre kiterjedő éles rendszerek esetén az Azure-ban a magas rendelkezésre állásának eléréséhez, virtuális gépek rendelkezésre állási csoportok, amelyek között osztja el őket több tartalék tartományok és frissítési tartományok kerülnek. Ezzel a módszerrel [a rendelkezésre állási csoportokban telepített virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) fizikailag elkülönített, egymástól a különálló kiszolgáló például rackszekrények, a hiba rugalmasságot engedélyezéséhez az alábbi ábrán látható módon lesz:

  ![Az Azure verem magas rendelkezésre állás](media/azure-stack-key-features/high-availability.png)

### <a name="availablity-sets-in-azure-stack"></a>Azure-készletben beállítja elérhetőségét
Amíg Azure verem infrastruktúrája már rugalmas sikertelen, az alapul szolgáló technológiát (feladatátvételi fürtszolgáltatás) továbbra is bizonyos időre leállítást kapcsolatban felmerült virtuális gépek érintett fizikai kiszolgálón hardver meghibásodása. Azure verem támogatja, hogy a rendelkezésre állási készlet legfeljebb három tartalék tartományok, hogy azok konzisztensek legyenek az Azure.

- **Tartományok fault**. Virtuális gépek rendelkezésre állási csoportba helyez elosztásával őket lehetőség szerint egyenletes több tartalék tartományok (Azure verem csomópontok) keresztül lesz fizikailag különítve egymástól. A hardver meghibásodása esetén a sikertelen tartalék tartomány a virtuális gépek fog más tartalék tartományok újraindul, de, ha lehetséges, őrizni, külön tartalék tartományok az azonos rendelkezésre állási készlet más virtuális gépek. A hardver ismét online elérhető lesz, ha a rebalanced fenntartásához magas rendelkezésre állású virtuális gépek kell. 
 
- **Tartományok frissítése**. Frissítési tartományok egy másik Azure fogalom, amely a rendelkezésre állási csoportokban magas rendelkezésre állást biztosít. Egy frissítési tartomány az a logikai csoport az alapul szolgáló hardverben, amely egy időben karbantartási is változni. Az azonos frissítési tartományban található virtuális gépek együtt újraindul tervezett karbantartás során. Bérlők virtuális gépek rendelkezésre állási csoportok belül hozzon létre, az Azure platformon automatikusan osztja el a virtuális gépek között ezek tartományok frissítése. Azure-készletben virtuális gépek élő a fürt többi online gazdagép keresztül telepítik át, a mögöttes állomás frissítése előtt. Mivel a gazdagép frissítése közben. bérlő állásidő nélkül, a frissítési szolgáltatás Azure veremben csak az Azure-ral tanúsítványsablon kompatibilitása létezik. 

### <a name="upgrade-scenarios"></a>Frissítési forgatókönyvek 
Virtuális gépek a rendelkezésre állási csoportokban létrehozni, mielőtt Azure verem verzió 1802 kap egy alapértelmezett hiba és a frissítési tartományok száma (1 és 1 rendre). A virtuális gépek magas elérhetőségét elérni a már meglévő rendelkezésre állási csoportok, meg kell először törölje a meglévő virtuális gépek, majd telepítse újra őket az új a rendelkezésre állási csoportban és a megfelelő hiba és a frissítési tartomány darabszámukból, lásd: [módosítása a a Windows virtuális gépek rendelkezésre állási készlet](https://docs.microsoft.com/azure/virtual-machines/windows/change-availability-set). 

Virtuálisgép-méretezési készlet, a rendelkezésre állási csoport jön létre belsőleg egy alapértelmezett tartalék tartomány és a frissítési tartományok számának (3. és 5 rendre). A virtuális gép skálázása előbb 1802 frissítés helyez egy rendelkezésre állási csoportban, az alapértelmezett hiba és a frissítési tartomány számát a készlet (1 és 1 rendre). A Virtuálisgép-méretezési készlet újabb terjedésének eléréséhez példány frissítéséhez terjessze ki a Virtuálisgép-méretezési készlet 1802 frissítés előtt voltak, és törölje a Virtuálisgép-méretezési készlet régebbi példányai-példányok száma szerint. 

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlést (RBAC)
Az RBAC segítségével rendszer hozzáférési jogot engedéllyel rendelkező felhasználók, csoportok és a szolgáltatások egy előfizetés, erőforráscsoporthoz vagy egyéni erőforrás szintjén szerepkörök hozzárendelésével. Egyes szerepkörök egy felhasználó, csoport vagy a szolgáltatás rendelkezik-e a Microsoft Azure verem erőforrások keresztül hozzáférési szintjét adja meg.

Az Azure RBAC rendelkezik, amelyek érvényesek az összes erőforrástípus három alapvető szerepkörök: tulajdonos, közreműködő, és ahhoz való olvasóra. Tulajdonos minden erőforrást, beleértve a jogot, hogy mások számára delegálása teljes hozzáféréssel rendelkezik. A közreműködői hozhat létre és kezelése az Azure-erőforrások minden típusú, de nem tud hozzáférést biztosítani, mások számára. Olvasó csak a meglévő Azure-erőforrások tekintheti meg. A többi Azure RBAC szerepkört az adott Azure-erőforrások felügyelet lehetővé tételéhez. A virtuális gép közreműködő szerepkört például lehetővé teszi, hogy létrehozását és kezelését a virtuális gépek, de nem engedélyezi a virtuális hálózat vagy az alhálózatot, amely a virtuális gép csatlakozik felügyeleti.

## <a name="usage-data"></a>Használati adatok
A Microsoft Azure verem gyűjti és használati adatokat von összes erőforrás-szolgáltató, és továbbítja azt az Azure-bA feldolgozásra Azure kereskedelmi. Az Azure-veremben összegyűjtött használati adatok egy REST API-n keresztül tekintheti meg. Egy Azure-konzisztens bérlői API, valamint szolgáltató és meghatalmazott szolgáltató API-k használata az adatok lekérése az összes bérlői előfizetések között van. Ezen adatok segítségével egy külső eszköz, vagy a számlázással vagy a jóváírási szolgáltatást integrálja. Miután használati feldolgozása az Azure kereskedelmi is megtekinthető a számlázási Azure-portálon.

## <a name="in-development-build-of-azure-stack-development-kit"></a>A fejlesztési buildverzióját Azure verem szoftverfejlesztői készlet
A fejlesztési buildek korai támogatókat kiértékelheti az Azure verem szoftverfejlesztői készlet legújabb verziójának segítségével. A legutóbbi jelentős kiadása alapján növekményes buildek fontosságúak. Főverziók továbbra is oldhatók fel néhány havonta, amíg a fejlesztési buildek közötti a kiadások időnként ad ki.

A fejlesztési buildek a következő előnyöket nyújtják:
- Hibajavítások
- Új funkciók
- Egyéb fejlesztések

## <a name="next-steps"></a>További lépések
[Az Azure verem telepítésének előfeltételei](azure-stack-deploy.md)

