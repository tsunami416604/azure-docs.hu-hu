---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770587"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

Az Azure Policy segít a szervezeti szabványok érvényesítésében és a megfelelőség im.- szintű értékelésében. A megfelelőségi irányítópulton keresztül összesített nézetet biztosít a környezet általános állapotának kiértékeléséhez, lehetővé téve az erőforrásonkénti, házirendenkénti részletességleásozást. Emellett a meglévő erőforrások tömeges szervizelése és az új erőforrások automatikus szervizelése révén segít az erőforrások megfelelőségének megfelelővé tételében.

Az Azure-szabályzat gyakori használati esetei közé tartozik az erőforrás-konzisztencia, a szabályozási megfelelőség, a biztonság, a költségek és a felügyelet szabályozásának megvalósítása. Ezekhez a gyakori használati esetekhez szabályzatdefiníciók már elérhetők az Azure-környezetben, mint beépített beépített, hogy könnyebben elévülhet.

## <a name="overview"></a>Áttekintés

Az Azure Policy úgy értékeli ki az Azure-beli erőforrásokat, hogy összehasonlítja ezeknek az erőforrásoknak a tulajdonságait az üzleti szabályokkal. Ezeket a [JSON formátumban](./concepts/definition-structure.md)leírt üzleti [szabályokat házirend-definícióknak nevezzük.](#policy-definition) A felügyelet egyszerűsítése érdekében számos üzleti szabály csoportosítható [egy szakpolitikai kezdeményezés](#initiative-definition) (más néven _policySet_) létrehozásához. Az üzleti szabályok létrejötte után a szabályzat definíciója vagy kezdeményezése hozzá van [rendelve](#assignments) az Azure által támogatott erőforrások bármely hatóköréhez, például [felügyeleti csoportokhoz,](../management-groups/overview.md)előfizetésekhez, [erőforráscsoportokhoz](../../azure-resource-manager/management/overview.md#resource-groups)vagy egyes erőforrásokhoz. A hozzárendelés az adott hozzárendelés [hatókörén](../../azure-resource-manager/management/overview.md#understand-scope) belüli összes erőforrásra vonatkozik.
Az alhatótávok szükség esetén kizárhatók.

Az Azure Policy [egy JSON formátumot](./concepts/definition-structure.md) használ, hogy a kiértékelés által használt logikát alakítsa ki annak megállapítására, hogy egy erőforrás megfelelő-e vagy sem. A definíciók közé tartoznak a metaadatok és a házirendszabály. A definiált szabály függvényeket, paramétereket, logikai operátorokat, feltételeket és [tulajdonságaliasokat](./concepts/definition-structure.md#aliases) használhat a kívánt forgatókönyvhöz. A házirendszabály határozza meg, hogy a hozzárendelés hatókörében mely erőforrások kerülése kiértékelésre.

### <a name="understand-evaluation-outcomes"></a>Az értékelési eredmények ismertetése

Az erőforrások kiértékelése az erőforrás életciklusa, a házirend-hozzárendelés életciklusa és a rendszeres folyamatos megfelelőségi értékelés során meghatározott időpontokban történik. Az erőforrások kiértékelésének időpontjai vagy eseményei a következők:

- Az erőforrás létrehozása, frissítése vagy törlése egy házirend-hozzárendeléssel rendelkező hatókörben történik.
- Egy szabályzat vagy kezdeményezés újonnan van hozzárendelve egy hatókörhöz.
- A hatókörhöz már hozzárendelt házirend vagy kezdeményezés frissül.
- A standard megfelelőségi értékelési ciklus során, amely 24 óránként egyszer fordul elő.

A házirendek értékelésének időpontjáról és módjáról az [Értékelési eseményindítók](./how-to/get-compliance-data.md#evaluation-triggers)című témakörben talál részletes információt.

### <a name="control-the-response-to-an-evaluation"></a>Az értékelésre adott válasz ellenőrzése

A nem megfelelő erőforrások kezelésére vonatkozó üzleti szabályok szervezetenként igen eltérőek lehetnek. Példák arra, hogy egy szervezet hogyan szeretné, hogy a platform válaszoljon egy nem reklamációs erőforrásra:

- Az erőforrás-módosítás megtagadása
- Az erőforrás módosításának naplózása
- Az erőforrás módosítása a módosítás előtt
- Az erőforrás módosítása a módosítás után
- Kapcsolódó kompatibilis erőforrások üzembe helyezése

Az Azure Policy lehetővé teszi az egyes üzleti válaszok at a [hatások](./concepts/effects.md)alkalmazásával. A hatások a [házirend-definíció](./concepts/definition-structure.md) **házirendszabály-részében** vannak beállítva.

### <a name="remediate-non-compliant-resources"></a>Nem megfelelő erőforrások szervizelése

Bár ezek a hatások elsősorban az erőforrás létrehozásakor vagy frissítésekor érintik az erőforrást, az Azure Policy is támogatja a meglévő nem megfelelő erőforrások kezelését anélkül, hogy módosítania kellene az erőforrást. A meglévő erőforrások megfelelőségéről az [erőforrások javítása](./how-to/remediate-resources.md)című témakörben talál további információt.

### <a name="video-overview"></a>Videó – áttekintés

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. Diák vagy videó letöltés, látogasson [el az Azure-környezet szabályozása az Azure-szabályzat](https://channel9.msdn.com/events/Build/2018/THR2030) a Channel 9-en.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Első lépések

### <a name="azure-policy-and-rbac"></a>Azure-szabályzat és RBAC

Van néhány fő különbség az Azure-szabályzat és a szerepköralapú hozzáférés-vezérlés (RBAC) között. Az Azure Policy kiértékeli az állapotot az Erőforrás-kezelőben és egyes erőforrás-szolgáltatók tulajdonságaiban képviselt erőforrások tulajdonságainak vizsgálatával. Az Azure Policy nem korlátozza a műveleteket (más néven _műveleteket)._ Az Azure Policy biztosítja, hogy az erőforrás állapota megfelel az üzleti szabályoknak, anélkül, hogy aggódnia attól, hogy ki hajtotta végre a módosítást, vagy kinek van engedélye a módosításra.

Az RBAC a felhasználói [műveletek](../../role-based-access-control/resource-provider-operations.md) különböző hatókörökön való kezelésére összpontosít. Ha egy művelet vezérlésére van szükség, akkor az RBAC a megfelelő eszköz a használatra. Még akkor is, ha egy személy rendelkezik hozzáféréssel egy művelet végrehajtásához, ha az eredmény egy nem megfelelő erőforrás, az Azure-szabályzat továbbra is blokkolja a létrehozás vagy frissítés.

Az RBAC és az Azure-szabályzat kombinációja teljes hatókör-vezérlést biztosít az Azure-ban.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. Az **erőforrás-házirend közreműködői** szerepkör tartalmazza a legtöbb Azure-szabályzati műveletet. **A tulajdonosnak** teljes jogai vannak. A **Közreműködő** és a **Reader** egyaránt hozzáférhet az összes _olvasási_ Azure-szabályzat-művelethez. **A közreműködő** aktiválhatja az erőforrás-szervizelést, de nem _hozhat létre_ definíciókat vagy hozzárendeléseket.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A **deployIfNotExists** házirend-hozzárendelés felügyelt identitásának elegendő engedélyre van szüksége a sablonban található erőforrások létrehozásához vagy frissítéséhez. További információt a [Házirend-definíciók konfigurálása a szervizeléshez](./how-to/remediate-resources.md#configure-policy-definition)című témakörben talál.

### <a name="resources-covered-by-azure-policy"></a>Az Azure-szabályzat hatálya alá tartozó erőforrások

Az Azure Policy kiértékeli az Azure összes erőforrását. Bizonyos erőforrás-szolgáltatók, például [a vendégkonfiguráció](./concepts/guest-configuration.md), [az Azure Kubernetes-szolgáltatás](../../aks/intro-kubernetes.md)és az [Azure Key Vault](../../key-vault/key-vault-overview.md)esetében a beállítások és objektumok kezelésének mélyebb integrációja van. További információ: [Resource Provider modes](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Íme néhány tipp és tipp, amelyeket szem előtt kell tartania:

- Kezdje egy naplózási hatás helyett a megtagadási hatás nyomon követésére hatása a házirend-definíció a környezetben lévő erőforrások. Ha már rendelkezik az alkalmazások automatikus skálázására már meglévő parancsfájlokkal, a megtagadási hatás beállítása akadályozhatja a már meglévő automatizálási feladatokat.

- A definíciók és hozzárendelések létrehozásakor vegye figyelembe a szervezeti hierarchiákat. Azt javasoljuk, hogy definíciók létrehozása magasabb szinteken, például a felügyeleti csoport vagy az előfizetési szinten. Ezután hozza létre a hozzárendelést a következő gyermekszinten. Ha egy felügyeleti csoportban hoz létre definíciót, a hozzárendelés hatóköre az adott felügyeleti csoporton belüli előfizetésre vagy erőforráscsoportra is levihető.

- Azt javasoljuk, hogy még egyetlen házirend-definícióesetén is hozzon létre és osszon hozzá kezdeményezésdefiníciókat.
  Például, van _politikadefiníciós politikaDefA,_ és hozza létre a kezdeményezés _definíciós kezdeményezésDefC_. Ha később egy másik házirend-definíciót hoz létre a _policyDefB_ számára a _policyDefA-hoz_hasonló célokkal, hozzáadhatja azt _az initiativeDefC_ csoportban, és együtt nyomon követheti őket.

- Miután létrehozott egy kezdeményezés-hozzárendelést, a kezdeményezéshez hozzáadott házirend-definíciók is a kezdeményezések hozzárendelésének részévé válnak.

- A kezdeményezési hozzárendelés kiértékelésekor a program a kezdeményezésen belüli összes szabályzatot is értékeli.
  Ha egyénileg kell értékelnie egy házirendet, jobb, ha nem tartalmazza azt egy kezdeményezésben.

## <a name="azure-policy-objects"></a>Az Azure-szabályzat objektumai

### <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Minden házirend-definíciónak vannak olyan feltételei, amelyek mellett a végrehajtás feltételei vannak. És van egy meghatározott hatása, hogy történik, ha a feltételek teljesülnek.

Az Azure Policy számos beépített szabályzatot kínálunk, amelyek alapértelmezés szerint elérhetők. Például:

- **Engedélyezett tárfiók termékváltozatok** (megtagadása): Azt határozza meg, ha egy tárfiók üzembe helyezett egy készlet termékváltozat méretek. Hatása az, hogy megtagadja az összes olyan tárfiókok, amelyek nem tartják be a megadott termékváltozat-méretek készletét.
- **Engedélyezett erőforrástípus** (megtagadás): A telepíthető erőforrástípusokat határozza meg. Hatása, hogy megtagadja az összes erőforrást, amely nem része ennek a definiált listának.
- **Engedélyezett helyek** (Megtagadás): Korlátozza az új erőforrások rendelkezésre álló helyeit. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-skus (deny):** A virtuálisgép-skus-ok üzembe helyezhető készletét adja meg.
- **Címke hozzáadása az erőforrásokhoz** (Módosítás): A szükséges címkét és annak alapértelmezett értékét alkalmazza, ha a központi telepítési kérelem nem adja meg.
- **Hozzáfűzési címke és az alapértelmezett érték** (Hozzáfűzés): A szükséges címke és annak értékét egy erőforráshoz kényszeríti.
- **Nem engedélyezett erőforrástípusok** (Megtagadás): Megakadályozza az erőforrástípusok listájának üzembe helyezését.

Ezek a házirend-definíciók (beépített és egyéni definíciók) megvalósításához hozzá kell rendelnie őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

A házirendek kiértékelése számos különböző művelettel történik, például házirend-hozzárendeléssel vagy házirend-frissítéssel. A teljes listát a [Házirend-értékelési eseményindítók című témakörben található.](./how-to/get-compliance-data.md#evaluation-triggers)

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

A paraméterek a házirend-definíció létrehozásakor kerülnek definiálásra. A paraméterek a definiálásukkor kapnak egy nevet és esetleg egy értéket is. Például megadhat egy paramétert egy _hely_ nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például _EastUS_ vagy _WestUS_.

A házirend-paraméterekről további információt a [Definícióstruktúra - Paraméterek](./concepts/definition-structure.md#parameters)című témakörben talál.

### <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

> [!NOTE]
> Az SDK, például az Azure CLI és az Azure PowerShell, **a policyset** nevű tulajdonságok at és paramétereket használ a kezdeményezésekhez.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- **Az operációs rendszer biztonsági réseinek figyelése a Security Centerben** – Olyan kiszolgálók figyelése, amelyek nem felelnek meg a konfigurált alapkonfigurációnak.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. A kezdeményezési paraméterek olyan paraméterek, amelyeket a kezdeményezésen belüli házirend-definíciók használnak.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az _allowedLocations_ és az _allowedSingleLocation_ lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben a példában megadhat egy helylistát a **policyA**'s paraméternek – **az allowedLocations** és **a policyB**'s paraméternek – **az allowedSingleLocation**. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges _értékekről_, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Amikor értékbeállításokat hoz létre egy kezdeményezésdefinícióban, nem tud más értéket megadni a kezdeményezés-hozzárendelés során, mert az nem része a listának.

### <a name="assignments"></a>Hozzárendelések

A hozzárendelés olyan házirend-definíció vagy kezdeményezés, amelyet egy adott hatókörön belül kell leadni. Ez a hatókör egy [felügyeleti csoporttól](../management-groups/overview.md) az egyes erőforrásokig terjedhet. A _hatókör_ kifejezés az összes olyan erőforrásra, erőforráscsoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyhez a definíció hozzá van rendelve. A hozzárendeléseket az összes gyermekerőforrás örökli. Ez a kialakítás azt jelenti, hogy az erőforráscsoportra alkalmazott definíció az adott erőforráscsoport erőforrásaira is vonatkozik. A hozzárendelésből azonban kizárhat egy alhatókört.

Az előfizetéshatókörben például rendelhet olyan definíciót, amely megakadályozza a hálózati erőforrások létrehozását. Kizárhat egy erőforráscsoportot az előfizetésben, amely hálózati infrastruktúrára szolgál. Ezután hozzáférést biztosít ehhez a hálózati erőforráscsoporthoz azoknak a felhasználóknak, akikben megbízik a hálózati erőforrások létrehozásában.

Egy másik példában előfordulhat, hogy a felügyeleti csoport szintjén szeretne hozzárendelni egy erőforrástípus engedélyezési listadefinícióját. Ezután hozzárendelhet egy megengedőbb szabályzatot (amely további erőforrástípusokat engedélyez) egy gyermekfelügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez a példa azonban nem működik, mert az Azure Policy explicit megtagadási rendszer. Ehelyett ki kell zárnia a gyermekfelügyeleti csoportot vagy előfizetést a felügyeleti csoportszintű hozzárendelésből. Ezután rendelje hozzá az engedékenyebb definíciót a gyermekkezelési csoporthoz vagy előfizetési szinthez. Ha bármely hozzárendelés azt eredményezi, hogy egy erőforrás tanusítása megtörténik, akkor az erőforrás engedélyezésének egyetlen módja a megtagadási hozzárendelés módosítása.

A hozzárendelések portálon keresztültörténő beállításáról további információt a [Házirend-hozzárendelés létrehozása a nem megfelelő erőforrások Azure-környezetben történő azonosításához](assign-policy-portal.md)című témakörben talál. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="maximum-count-of-azure-policy-objects"></a>Az Azure Policy-objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Tekintse át a házirend-definíciós struktúrát](./concepts/definition-structure.md).
- [Házirend-definíció hozzárendelése a portál használatával](./assign-policy-portal.md).
