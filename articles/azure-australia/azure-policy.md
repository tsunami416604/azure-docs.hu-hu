---
title: A Azure Policy és az Azure-tervezetek biztonsági megfelelősége
description: A megfelelőség biztosítása és a biztonság betartatása a Azure Policy és az Azure-beli kormányzati szervek számára készült tervezetekkel, az ASD ISM-hez és az Essential 8-hoz kapcsolódóan.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571743"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>A Azure Policy és az Azure-tervezetek biztonsági megfelelősége

Az IT-környezetben való irányítás kényszerítésének kihívása, függetlenül attól, hogy az összes szervezet számára létezik-e helyszíni, Felhőbeli natív vagy hibrid környezet. Hatékony technikai irányítási keretrendszert kell biztosítani ahhoz, hogy a Microsoft Azure környezet megfeleljen a tervezési, szabályozási és biztonsági követelményeknek.

Az ausztrál kormányzati szervek számára fontos, hogy a kockázatok felmérése során megfontolandó szempontok az [ausztrál Cyber Security Center (ASCs) Information Security Manual](https://acsc.gov.au/infosec/ism/index.htm) (ISM) kézikönyvben találhatók. Az ISM-en belül részletezett vezérlők többsége megköveteli, hogy a technikai irányítás alkalmazása hatékonyan legyen kezelve és érvényesítve. Fontos, hogy a környezetek konfigurációjának kiértékeléséhez és érvényesítéséhez megfelelő eszközökkel rendelkezzen.

A Microsoft Azure két ingyenes szolgáltatást biztosít, amelyek segítik a kihívásokat, az Azure Policy és az Azure tervrajzait.

## <a name="azure-policy"></a>Azure Policy

Azure Policy lehetővé teszi egy szervezet informatikai irányításának technikai elemeinek alkalmazását. Azure Policy a beépített szabályzatok folyamatosan bővülő könyvtárát tartalmazza. Minden szabályzat kikényszeríti a szabályokat és a hatásokat a célként megadott Azure-erőforrásokra vonatkozóan.

Ha a szabályzatot erőforrásokhoz rendeli hozzá, akkor a szabályzat általános megfelelőségét kiértékelheti, és szükség esetén szervizelheti.

A beépített Azure-szabályzatok ezen könyvtára lehetővé teszi, hogy egy szervezet gyorsan érvényesítse a ASCS ISM-ben talált vezérlők típusait. Ilyenek például a következők:

* Virtuális gépek figyelése hiányzó rendszerfrissítésekhez
* Fiókok naplózása emelt szintű engedélyekkel a többtényezős hitelesítéshez
* Titkosítatlan SQL-adatbázisok azonosítása
* Egyéni Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatának monitorozása
* Azon Azure-régiók korlátozása, amelyekben az erőforrások létrehozhatók

Ha az irányítási vagy szabályozási szabályozások nem teljesülnek a beépített Azure Policy definíciója, egyéni definíció hozható létre és rendelhető hozzá. Minden Azure Policy definíció a JSON-ban van definiálva, [](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)és a szabványos definíciós struktúrát követi. A meglévő Azure Policy-definíciók is duplikálható és használhatók egy egyéni házirend-definíció alapján.

Az egyes Azure-szabályzatok erőforrásokhoz, különösen összetett környezetekhez vagy szigorú szabályozási követelményekkel rendelkező környezetekhez való hozzárendelésével nagy terhelést hozhat létre a rendszergazdák számára. Ezeknek a kihívásoknak az elősegítése érdekében az Azure-szabályzatok egy csoportba csoportosíthatók, Azure Policy kezdeményezést hozhatnak létre. A szabályzattal kapcsolatos kezdeményezések a kapcsolódó Azure-szabályzatok összevonására szolgálnak, amelyek csoportként való alkalmazása esetén egy adott biztonsági vagy megfelelőségi célkitűzés alapját képezik. A Microsoft beépített Azure Policy kezdeményezési definíciókat ad hozzá, beleértve a konkrét szabályozási követelmények teljesítésére tervezett definíciókat is:

![Szabályozási megfelelőségi szabályzattal kapcsolatos kezdeményezések](media/regulatory-initiatives.png)

Az összes Azure-házirend és-kezdeményezés hozzá van rendelve egy hozzárendelési hatókörhöz. Ez a hatókör az Azure-előfizetés, az Azure felügyeleti csoport vagy az Azure-erőforráscsoport szintjén van definiálva. Miután hozzárendelte a szükséges Azure-házirendeket vagy házirend-kezdeményezéseket, a szervezet az újonnan létrehozott Azure-erőforrások konfigurációs követelményeit is kikényszerítheti.

Új Azure Policy vagy kezdeményezés kiosztása nem érinti a meglévő Azure-erőforrásokat. Azure Policy tud; azonban lehetővé teheti, hogy egy szervezet megtekintse a meglévő Azure-erőforrások megfelelőségét. A szervezet saját belátása szerint bármely, nem megfelelőként azonosított erőforrás szervizelhető

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy és kezdeményezések működés közben

Az elérhető beépített Azure Policy és kezdeményezési definíciók a Azure Portal házirend szakaszának definíció csomópontjában találhatók:

![Beépített Azure Policy-definíciók](media/policy-definitions.png)

A beépített definíciók könyvtárának használatával gyorsan megkeresheti azokat a házirendeket, amelyek megfelelnek a szervezeti követelményeknek, áttekintheti a házirend-definíciót, és hozzárendelheti a szabályzatot a megfelelő erőforrásokhoz. Az ISM esetében például a többtényezős hitelesítés (MFA) szükséges minden Kiemelt felhasználó számára, és minden felhasználó számára fontos adattárakhoz férhet hozzá. Azure Policy a Azure Policy definíciók között a "MFA" kifejezésre kereshet:

![Azure MFA-szabályzatok](media/mfa-policies.png)

Ha azonosította a megfelelő szabályzatot, a szabályzatot a kívánt hatókörhöz rendeli. Ha nincs olyan beépített szabályzat, amely megfelel a követelményeinek, duplikálhatja a meglévő szabályzatot, és elvégezheti a kívánt módosításokat:

![Meglévő Azure Policy duplikálása](media/duplicate-policy.png)

A Microsoft az egyéni Azure-szabályzatok létrehozásához a [githubon](https://github.com/Azure/azure-policy) Azure Policy minták gyűjteményét is tartalmazza. Ezek a házirend-minták közvetlenül a Azure Policy-szerkesztőbe másolhatók a Azure Portalon belül.

Azure Policy kezdeményezések létrehozásakor rendezheti a rendelkezésre álló házirend-definíciók listáját, valamint a beépített és az egyéni is, a szükséges definíciók hozzáadásával.

Előfordulhat például, hogy a Windows rendszerű virtuális gépekhez kapcsolódó összes házirend számára elérhető Azure Policy definíciók listájában keres. Ezután ezeket a definíciókat egy olyan kezdeményezésre tervezték, amely az ajánlott virtuálisgép-megerősítési eljárások kikényszeríti a következőket:

![Azure-szabályzatok listája](media/initiative-definitions.png)

Egy Azure Policy vagy házirend-kezdeményezés hozzárendelési hatókörhöz való hozzárendelésekor lehetőség van arra, hogy kizárjon Azure-erőforrásokat a házirendek hatásaiból az Azure Management Groups vagy az Azure-erőforráscsoportok kizárásával.

### <a name="real-time-enforcement-and-compliance-assessment"></a>A valós idejű kényszerítés és megfelelőség értékelése

Azure Policy a hatókörön belüli Azure-erőforrások megfelelőségi vizsgálatát az alábbi feltételek teljesülése esetén:

* Azure Policy vagy Azure Policy kezdeményezés hozzárendelésekor
* Meglévő Azure Policy vagy kezdeményezés hatókörének módosításakor
* Igény szerint az API-n keresztül legfeljebb 10 vizsgálat/óra
* 24 óránként egyszer – az alapértelmezett viselkedés

Egy Azure-erőforrásra vonatkozó házirend-megfelelőségi vizsgálat 15 perccel az erőforrás módosítása után történik.

Az erőforrások Azure Policy megfelelőségének áttekintését a Azure Portal a szabályzatok megfelelőségi irányítópultján keresztül tekintheti át:

![Azure Policy megfelelőségi pontszám](media/simple-compliance.png)

A teljes erőforrás-megfelelőség százalékos értéke az összes hatókörön belüli telepített erőforrás megfelelőségének összesítése az összes hozzárendelt Azure-szabályzattal szemben. Ez lehetővé teszi, hogy azonosítsa a nem megfelelő környezetben lévő erőforrásokat, és tervezze meg a tervet az erőforrások legjobb szervizeléséhez.

A szabályzat megfelelőségi irányítópultja az egyes erőforrások változási előzményeit is tartalmazza. Ha egy erőforrás úgy van azonosítva, hogy már nem felel meg a hozzárendelt házirendnek, és az automatikus szervizelés nincs engedélyezve, megtekintheti, hogy ki hajtotta végre a változást, mi változott, és az adott erőforráson történt módosítások után.

## <a name="azure-blueprints"></a>Azure Blueprints

Az Azure-tervezetek kiterjesztik a Azure Policy képességeit azáltal, hogy a következőket ötvözik:

* Azure RBAC
* Azure-erőforráscsoportok
* [Azure Resource Manager sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

A tervrajzok lehetővé teszik az Azure-erőforrásokat a Resource Manager-sablonokból üzembe helyező környezeti tervek létrehozását, a RBAC konfigurálását, valamint a konfiguráció érvényesítését és naplózását Azure Policy hozzárendelésével. A tervrajzok szerkeszthető és újratelepíthető környezeti sablont alkotnak. A terv létrehozása után hozzá lehet rendelni egy Azure-előfizetéshez. A hozzárendelés után létrejön a tervben meghatározott összes Azure-erőforrás, és az Azure-szabályzatok is érvényesek lesznek. Egy Azure Blueprintban definiált erőforrások központi telepítése és konfigurálása a Azure Portal Azure Blueprint konzolján figyelhető meg.

A szerkesztett Azure-tervezeteket újra közzé kell tenni a Azure Portalban. A terv minden újraközzétételekor a terv verziószáma növekszik. A verziószám lehetővé teszi annak meghatározását, hogy egy adott terv melyik verzióját telepítette egy szervezet Azure-előfizetésére. Ha szükséges, a terv aktuálisan hozzárendelt verziója frissíthető a legújabb verzióra.

A Azure Blueprint használatával üzembe helyezett erőforrások az üzembe helyezés időpontjában konfigurálhatók az [Azure-erőforrások zárolásával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) . Az erőforrás-zárolások megakadályozzák, hogy az erőforrások véletlenül módosítva vagy törölve legyenek.

A Microsoft számos iparági és szabályozási követelménynek Azure Blueprint sablonokat fejleszt. A rendelkezésre álló Azure Blueprint-definíciók aktuális könyvtára a Azure Portalban vagy a [Azure Security and Compliance Blueprint](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) oldalon tekinthető meg a szolgáltatás-megbízhatósági portálon.

### <a name="azure-blueprint-artifacts"></a>Azure Blueprint összetevők

Azure Blueprint létrehozásához kezdjen el egy üres tervrajzi sablonnal, vagy használja a meglévő minta tervrajzok egyikét kiindulási pontként. A tervbe az üzembe helyezés részeként konfigurált összetevőket adhat hozzá:

![Azure Blueprint összetevők](media/blueprint-artifacts.png)

Ezek az összetevők magukban foglalhatják az Azure-erőforráscsoportot és-erőforrásokat, valamint a hozzájuk kapcsolódó Azure Policy és házirend-kezdeményezéseket, amelyek a környezet megfelelőségének biztosításához szükséges konfigurációt érvényesítik, például az ISM-vezérlők a rendszer megerősítéséhez.

Ezeket az összetevőket paraméterekkel is lehet konfigurálni. Ezek az értékek akkor érhetők el, ha a terv hozzá lett rendelve egy Azure-előfizetéshez, és üzembe lett helyezve. A paraméterek lehetővé teszik egyetlen terv létrehozását és az erőforrások különböző környezetekben történő üzembe helyezését anélkül, hogy az alapul szolgáló tervet kellene szerkeszteni.

A Microsoft Azure PowerShell-és CLI-parancsmagokat fejleszt az Azure-tervezetek létrehozásához és kezeléséhez azzal a szándékkal, hogy a tervet a CI/CD-folyamaton keresztül egy szervezet tudja fenntartani és üzembe helyezni.

## <a name="next-steps"></a>További lépések

Ez a cikk azt ismerteti, hogyan lehet érvényesíteni a szabályozást és a biztonságot a Azure Policy és az Azure-tervezetekkel. Most, hogy magas szinten tettük közzé, megismerheti, hogyan használhatja az egyes szolgáltatásokat részletesebben:

* [Azure Policy áttekintése](https://docs.microsoft.com/azure/governance/policy/overview)
* [Az Azure tervrajzai – áttekintés](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy-minták](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Azure Policy minták tárháza](https://github.com/Azure/azure-policy)
* [Azure Policy definíciós struktúra](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure Policy effektusok](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
