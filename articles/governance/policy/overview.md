---
title: Az Azure szabályzatának áttekintése
description: Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatdefiníciókat hozhat létre, rendelhet hozzá és kezelhet az Azure-környezetben.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384430"
---
# <a name="what-is-azure-policy"></a>Mi az Azure Policy?

A szabályozás azt ellenőrzi, hogy a szervezet hatékony és hatékony használattal tudja-e elérni a céljait. Üzleti céljait és informatikai projektek között egyértelműség létrehozásával megfelel ennek az igénynek.

A cégben jelentős mennyiségű, láthatóan megoldhatatlan informatikai probléma jelentkezik? A hatékony informatikai szabályozás stratégiai szinten kezeli a kezdeményezések tervezését és a prioritások meghatározását a problémák kezelése és megelőzése érdekében. A stratégiai szükség, ahol az Azure Policy pedig kifejezetten.

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. A szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon, hogy azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseknek. Az Azure Policy megfelel ennek az igénynek az erőforrások megfelel-e a hozzárendelt szabályzatok kiértékelik. A Azure Policy által tárolt összes adatok titkosítva maradnak.

Például rendelkezhet egy olyan szabályzattal, amely csak bizonyos méretű virtuálisgép-termékváltozatot engedélyez a környezetben. Ez a házirend megvalósítása, miután új és meglévő erőforrások megfelelőségi értékeli ki. A megfelelő szabályzat típusát, a meglévő erőforrások megfelelőségének tehető meg. Később a jelen dokumentációban azt átjövünk további részleteket a létrehozása és az Azure Policyvel házirendek megvalósítása.

> [!IMPORTANT]
> Az Azure Policy megfelelőségértékelése mostantól tarifacsomagtól függetlenül minden hozzárendeléshez elérhető. Ha a hozzárendeléseknél nem láthatók a megfelelőségi adatok, győződjön meg róla, hogy az előfizetés regisztrálva van a Microsoft.PolicyInsights erőforrás-szolgáltatón.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Mennyiben különbözik ez az RBAC-től?

A Azure Policy és a szerepköralapú hozzáférés-vezérlés (RBAC) között van néhány lényeges különbség. Az RBAC a különféle hatókörű felhasználói műveletekre összpontosít. Előfordulhat, hogy lehet hozzáadni egy erőforráscsoport, a közreműködő szerepkört lehetővé teszi, hogy hajtsa végre a módosításokat az adott erőforráscsoporton. A Azure Policy az erőforrás tulajdonságaira koncentrál az üzembe helyezés során és a már meglévő erőforrások esetében. Azure Policy a vezérlők tulajdonságait, például az erőforrások típusait vagy helyét. A RBAC-től eltérően a Azure Policy egy alapértelmezett engedélyezési és explicit megtagadás rendszer.

### <a name="rbac-permissions-in-azure-policy"></a>RBAC-engedélyek az Azure Policyban

Az Azure Policy több engedéllyel (más néven művelettel) rendelkezik két erőforrás-szolgáltatóban:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Számos beépített szerepkör oszt ki engedélyeket Azure Policy-erőforrásoknak. Az **erőforrás-házirend közreműködői** szerepkör a legtöbb Azure Policy műveletet tartalmazza. A **tulajdonos** teljes körű jogosultságokkal rendelkezik. Mind a **közreműködő** , mind az **olvasó** használhatja az összes olvasási Azure Policy műveletet, de a **közreműködő** is aktiválhatja a szervizelést.

Ha egyik beépített szerepkör sem tartalmazza a szükséges engedélyeket, hozzon létre egy [egyéni szerepkört](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Szabályzatdefiníció

Az Azure Policyban a szabályzatok létrehozásának és bevezetésének folyamata egy szabályzatdefiníció létrehozásával kezdődik. Mindegyik szabályzatdefiníció feltételekkel, kényszerítve van rendelkezik. És a egy meghatározott hatása, ha a feltételek teljesülnek.

Az Azure Policy biztosítunk számos beépített szabályzatok alapértelmezés szerint elérhető. Például:

- **Engedélyezett Storage-fiók SKU**: meghatározza, hogy az üzembe helyezett Storage-fiók SKU-méreteken belül van-e. Annak hatását, hogy megtagadja az összes storage-fiókok, amelyek nem a Termékváltozat-méretek meghatározott készletét.
- **Engedélyezett erőforrástípus**: meghatározza az üzembe helyezhető erőforrástípusok típusát. Hatás az összes olyan erőforrást, amely nem része a definiált listában elutasítása.
- **Engedélyezett helyszínek**: korlátozza az új erőforrások elérhető helyeinek korlátozását. Biztosítja a földrajzi megfelelőségi követelmények betartását.
- **Engedélyezett virtuálisgép-SKU**: az üzembe helyezhető virtuálisgép-SKU-készletet határozza meg.
- **Címke hozzáadása az erőforrásokhoz**: egy kötelező címkét és annak alapértelmezett értékét alkalmazza, ha azt nem az üzembe helyezési kérelem adja meg.
- A **címke és a hozzá tartozó érték betartatása**: kötelező címke és annak értéke egy erőforráshoz.
- **Nem engedélyezett erőforrástípusok**: megakadályozza, hogy a rendszer ne telepítse az erőforrástípusok listáját.

Megvalósítani a ezeket a szabályzatdefiníciók (mind a beépített és egyéni-definíciók), kell rendelni őket. Ezen szabályzatok bármelyike hozzárendelhető az Azure Portalon, a PowerShellben vagy az Azure CLI-n.

Számos különböző műveletek, például a szabályzat-hozzárendelést vagy a házirend-frissítési szabályzat-kiértékelés történik. A teljes listát lásd: [szabályzat-kiértékelési eseményindítók](./how-to/get-compliance-data.md#evaluation-triggers).

További, a szabályzatdefiníciók szerkezetéről szóló információkért lásd a [szabályzatdefiníciók szerkezetével](./concepts/definition-structure.md) foglalkozó témakört.

## <a name="policy-assignment"></a>Szabályzat-hozzárendelés

A szabályzat-hozzárendelés egy olyan szabályzatdefiníció, amely egy adott hatókörön belül érvényes. Ez a hatókör egy [felügyeleti csoportból](../management-groups/overview.md) egy adott erőforrásra terjedhet. A *hatókör* kifejezés az összes olyan erőforrásra, erőforrás-csoportra, előfizetésre vagy felügyeleti csoportra vonatkozik, amelyhez a házirend-definíció hozzá van rendelve. A szabályzat-hozzárendeléseket az összes alárendelt erőforrás örökli. Ez a kialakítás azt jelenti, hogy egy erőforráscsoport alkalmazott házirend is alkalmazza a rendszer az adott erőforráscsoportba tartozó erőforrások. Azt is meg lehet azonban adni, hogy a szabályzat-hozzárendelés egy adott alhatókörre ne vonatkozzon.

Egy előfizetésre vonatkozóan megadhat például egy olyan szabályzatot, amely megakadályozza a hálózati erőforrások létrehozását, Egy erőforráscsoport, az adott előfizetésben hálózati infrastruktúra szánt sikerült kizárni. Ezután a hálózati erőforrások létrehozásával megbízott felhasználóknak hálózati erőforráscsoporthoz hozzáférést adhat.

Egy másik példában előfordulhat, hogy a felügyeleti csoport szintjén hozzá kell rendelnie egy erőforrástípus engedélyezési lista házirendjét. Ezután hozzárendelne egy megengedőbb (több erőforrástípust engedélyező) szabályzatot egy alárendelt felügyeleti csoporthoz vagy akár közvetlenül az előfizetésekhez. Ez azonban nem működne, mivel a szabályzat egy explicit tiltási rendszer. A megoldás az, hogy kizárja az alárendelt felügyeleti csoportot vagy az előfizetést a felügyeleti csoport szintjén hozzárendelt szabályzatból, majd hozzárendeli a megengedőbb szabályzatot az alárendelt felügyeleti csoportok vagy az előfizetések szintjén. Ha egy szabályzat eredménye egy erőforrás, majd az erőforrás engedélyezésének egyetlen módja, hogy a tiltó szabályzat módosítása.

További, a szabályzatdefiníciók és -hozzárendelések a portálon keresztül történő beállítására vonatkozó tudnivalókért lásd a [szabályzat-hozzárendelés nem megfelelő erőforrások azonosításának céljából, az Azure környezetben történő létrehozásával](assign-policy-portal.md) foglalkozó cikket. A [PowerShellhez](assign-policy-powershell.md) és az [Azure CLI-hez](assign-policy-azurecli.md) is elérhetők lépések.

## <a name="policy-parameters"></a>Szabályzatparaméterek

A szabályzatparaméterek leegyszerűsítik a szabályzatok kezelését, mert csökkentik a létrehozandó szabályzatdefiníciók számát. A szabályzatdefiníciók létrehozásakor megadhat paramétereket, amelyek általánosabb érvényűvé teszik a definíciót. Ezután az adott szabályzatdefiníciót különböző helyzetekben újra felhasználhatja, ha a szabályzatdefiníció hozzárendelésekor más és más értékeket ad meg. Megadhat például egy adott helykészletet egy előfizetéshez.

Paraméterek vannak meghatározva, a szabályzat-definíció létrehozása során. Ha egy paraméter meg van határozva, nevet és opcionálisan adott érték. Például megadhat egy paramétert egy *hely* nevű szabályzathoz. Ezután a szabályzat hozzárendelésekor különböző értékeket adhat meg a paraméterhez, például *EastUS* vagy *WestUS*.

A házirend-paraméterekkel kapcsolatos további információkért lásd: [definíciós struktúra – paraméterek](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Kezdeményezési definíció

A kezdeményezési definíciók olyan szabályzatdefiníció-gyűjtemények, amelyek egy átfogó cél teljesülését szolgálják. A kezdeményezési definíciók egyszerűbbé teszik a szabályzatdefiníciók kezelését és hozzárendelését. Ehhez azzal járulnak hozzá, hogy több szabályzatot egyetlen elembe csoportosítanak. Létrehozhat például egy kezdeményezést **Monitorozás engedélyezése az Azure Security Centerben** néven, amelynek az a célja, hogy monitorozza az Azure Security Centerben elérhető összes biztonsági javaslatot.

> [!NOTE]
> Az SDK, például az Azure CLI és a Azure PowerShell, a **PolicySet** nevű tulajdonságok és paraméterek használatával hivatkozhat a kezdeményezésekre.

Egy ilyen kezdeményezés a következő szabályzatdefiníciókat tartalmazhatja:

- **Titkosítatlan SQL Database-adatbázisok monitorozása a Security Centerben** – A titkosítatlan SQL Database-adatbázisok és -kiszolgálók monitorozásához.
- Az **operációs rendszer biztonsági réseinak figyelése Security Centerban** – olyan kiszolgálók figyelésére, amelyek nem elégítik ki a beállított alapkonfigurációt.
- **Végpontok hiányzó védelmének monitorozása a Security Centerben** – Az olyan kiszolgálók monitorozásához, amelyek nem rendelkeznek telepített végpontvédelmi ügynökkel.

## <a name="initiative-assignment"></a>Kezdeményezési hozzárendelés

A szabályzat-hozzárendelésekhez hasonlóan a kezdeményezési hozzárendelések olyan kezdeményezési definíciók, amelyek egy adott hatókörhöz vannak hozzárendelve. A kezdeményezési hozzárendeléseknek köszönhetően nem kell olyan sok kezdeményezési definíciót létrehozni az egyes hatókörökhöz. Ez a hatókör egy felügyeleti csoporttól egy adott erőforrásra is terjedhet.

Minden egyes kezdeményezés több hatókörhöz is hozzárendelhető. Az egyik kezdeményezés az **előfizetéshez** és a **subscriptionB**is hozzárendelhető.

## <a name="initiative-parameters"></a>Kezdeményezési paraméterek

A szabályzatparaméterekhez hasonlóan a kezdeményezési paraméterek is a redundancia csökkentésével egyszerűsítik a kezdeményezések kezelését. Kezdeményezési paraméterek a következők paramétereket a szabályzatdefiníciók a kezdeményezésen belül használja.

Vegyünk példának egy olyan helyzetet, ahol egy kezdeményezési definícióhoz (**C kezdeményezés**) az **A szabályzat** és a **B szabályzat** szabályzatdefiníció tartozik, amelyek mindketten különböző típusú paramétert várnak:

| Szabályzat | Paraméter neve |Paraméter típusa  |Megjegyzés |
|---|---|---|---|
| A szabályzat | allowedLocations | tömb  |Ez a paraméter sztringek listáját várja értékként, mivel a paraméter típusa tömbként lett megadva |
| B szabályzat | allowedSingleLocation |sztring |Ez a paraméter egy szót vár értékként, mivel a paraméter típusa sztringként lett megadva |

Ebben a forgatókönyvben három lehetőség van a **C kezdeményezés** kezdeményezési paramétereinek megadására:

- A kezdeményezésen belüli szabályzatdefiníciók paramétereinek használata. Ebben az esetben az *allowedLocations* és az *allowedSingleLocation* lesz a **C kezdeményezés** kezdeményezési paramétere.
- Értékek megadása a kezdeményezési definíción belüli szabályzatdefiníciók paramétereihez. Ebben a példában megadhat egy listát a **policya paraméterhez – allowedLocations** és **B szabályzat paraméter – allowedSingleLocation**. Az értékeket az adott kezdeményezés hozzárendelésekor is megadhatja.
- Adjon meg egy listát mindazon lehetséges *értékekről*, amelyeket használhat a kezdeményezés hozzárendelésekor. A kezdeményezés hozzárendelésekor a kezdeményezésen belüli szabályzatdefiníciók örökölt paraméterei csak olyan értékekkel rendelkezhetnek, amelyek ebben a listában szerepelnek.

Amikor érték beállítások egy kezdeményezési definíciót hoz létre, Ön nem adhat egy másik értéket a kezdeményezés hozzárendelésekor, mert nem szerepel a listában.

## <a name="maximum-count-of-azure-policy-objects"></a>Azure Policy objektumok maximális száma

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Javaslatok a szabályzatok kezeléséhez

Íme néhány mutatók és tippek figyelembe kell venni:

- Indítsa el egy naplózási kezdve a helyett, nyomon követni a szabályzatdefiníció hatását a környezet erőforrásaira egy megtagadási érvénybe. Ha vannak olyan parancsprogramjai már az automatikus méretezés, hogy az alkalmazások egy megtagadási effektus beállítása akadályozhatják ilyen automatizálási feladatok már érvényben.

- Fontolja meg a szervezeti hierarchia definíciókat és -hozzárendelések létrehozásakor. Javasolt létrehozni a definíciókat magasabb szintű, például a felügyeleti csoport vagy az előfizetés szintjén. Ezután hozzon létre a hozzárendelést a következő alárendelt szinten. Ha egy definíciót, egy felügyeleti csoportot hoz létre, a hozzárendelés leszűkítheti a egy előfizetést, vagy az erőforráscsoport, a felügyeleti csoporton belül.

- Javasoljuk, hogy hoz létre, és még egy szabályzat-definíció a kezdeményezési definíciók.
  Például megadhatja a szabályzat-definíció *policyDefA* , és létrehozhatja azt a Initiative definition *initiativeDefC*alatt. Ha később új szabályzat-definíciót hoz létre a *definícióéhoz* -hoz a *policyDefA*hasonló célokkal, akkor hozzáadhatja azt a *initiativeDefC* alatt, és nyomon követheti azokat.

- Miután létrehozott egy kezdeményezési hozzárendelést, az hozzáadódik a kezdeményezés szabályzatdefiníciók is a kezdeményezések hozzárendelések részévé válik.

- Kezdeményezési hozzárendelések abban az esetben, ha a kezdeményezésen belül minden szabályzat is értékeli ki.
  Egy házirend kiértékelése külön-külön kell, ha fontos foglalja bele-kezdeményezéshez.

## <a name="video-overview"></a>Videó – áttekintés

Az Azure Policy alábbi áttekintése a 2018-as buildből származik. A diaképek és videók letöltéséhez látogasson el [Az Azure-környezetre a Channel 9 Azure Policyon keresztül](https://channel9.msdn.com/events/Build/2018/THR2030) .

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az Azure Policy tudnivalóit és néhány fontosabb fogalmat, folytatásként a következő témaköröket javasoljuk:

- [Rendeljen hozzá egy szabályzat-definíciót a portál használatával](./assign-policy-portal.md).
- [Rendeljen hozzá egy szabályzat-definíciót az Azure CLI használatával](./assign-policy-azurecli.md).
- [Házirend-definíció társítása a PowerShell használatával](./assign-policy-powershell.md).
