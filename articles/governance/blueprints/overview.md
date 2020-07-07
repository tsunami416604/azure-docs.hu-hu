---
title: Az Azure Blueprints áttekintése
description: Ismerje meg, hogyan hozhat létre, határozhat meg és helyezhet üzembe összetevőket az Azure-környezetben az Azure BluePrints szolgáltatásban.
ms.date: 05/06/2020
ms.topic: overview
ms.openlocfilehash: 3a7cece81027bd8ac79250f2f2cd08da637b5f0b
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970926"
---
# <a name="what-is-azure-blueprints"></a>Mi az az Azure Blueprints?

Hasonlóan ahhoz, ahogyan a tervekkel a mérnökök és az építészek felvázolják egy projekt tervezési paramétereit, az Azure Blueprints segítségével a felhőmérnökök és központi informatikai csoportok meghatározhatnak egy megismételhető Azure-erőforráskészletet, amely megvalósítja és betartja a vállalat szabványait, mintázatait és követelményeit. Az Azure-tervezetek lehetővé teszik a fejlesztői csapatok számára, hogy gyorsan építsenek és hozzanak létre olyan új környezeteket, amelyek megbízhatósági kapcsolatban állnak a szervezeti megfelelőségben a beépített összetevők, például a hálózatkezelés, a fejlesztés és a teljesítés felgyorsításához.

A tervekkel deklaratív módon, összehangoltan helyezhetők üzembe különböző erőforrássablonok és egyéb tervelemek, például a következők:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager sablonok (ARM-sablonok)
- Erőforráscsoportok

Az Azure Blueprints szolgáltatást a globális terjesztésű [Azure Cosmos DB](../../cosmos-db/introduction.md) támogatja. A tervobjektumok több Azure-régióba vannak replikálva. Ez a replikálás alacsony késést, magas rendelkezésre állást és konzisztens hozzáférést biztosít a terv objektumaihoz, függetlenül attól, hogy az Azure-tervrajzok mely régiókban helyezik üzembe az erőforrásokat.

## <a name="how-its-different-from-arm-templates"></a>Miben különbözik az ARM-sablonoktól

A szolgáltatás úgy van megtervezve, hogy segítsen a _környezet beállításában_. Ez a beállítás gyakran erőforráscsoport, szabályzatok, szerepkör-hozzárendelések és ARM-sablonok központi telepítéseit tartalmazza. A terv egy olyan csomag, amely egyesíti az egyes _összetevők típusait_ , és lehetővé teszi a csomag összeállítását és verzióját, beleértve a CI/CD-folyamatokat. Végül minden csomag egy előfizetéshez van rendelve egyetlen műveletben, amely felügyelhető és nyomon követhető.

Szinte minden, amit az Azure-tervezetekben szeretne felvenni az üzembe helyezéshez, ARM-sablonnal is elvégezhető. Az ARM-sablon azonban olyan dokumentum, amely nem létezik natív módon az Azure-ban – mindegyiket helyileg vagy a verziókövetés tárolja. A sablonok felhasználhatók egy vagy több Azure-erőforrás üzembe helyezéséhez, azonban az erőforrások üzembe helyezése után a sablonnal való aktív kapcsolat elveszik.

Az Azure-tervezetek esetében a terv meghatározása (mit _kell_ telepíteni) és a terv-hozzárendelés (mi _volt_ az üzembe helyezett) közötti kapcsolat megmarad. Ez a kapcsolat lehetővé teszi az üzembe helyezett összetevők hatékonyabb nyomon követését és naplózását. Az Azure-tervrajzok egyszerre több előfizetést is frissíthetnek, amelyekre ugyanezen terv vonatkozik.

Nem kell választania egy ARM-sablon és egy terv között. Minden tervezet nulla vagy több ARM _-sablonból_állhat. Ez a támogatás azt jelenti, hogy az ARM-sablonok könyvtárainak fejlesztésére és karbantartására tett korábbi erőfeszítések az Azure-tervezetekben újrafelhasználhatók.

## <a name="how-its-different-from-azure-policy"></a>Miben különbözik ez az Azure Policytól?

A tervek olyan csomagok vagy tárolók, amelyekben az Azure-felhőszolgáltatások, a biztonság és a kialakítás megvalósításával kapcsolatos fókuszspecifikus szabványok, minták és követelmények állíthatók össze, amelyek újrafelhasználhatók a konzisztencia és a megfelelőség fenntartása érdekében.

A [szabályzatok](../policy/overview.md) olyan alapértelmezett engedélyezési / explicit megtagadási rendszerek, amelyek az erőforrások tulajdonságaira összpontosítanak az üzembe helyezés során és a meglévő erőforrások esetében. Segítik a felhőalapú rendszerek irányítását azáltal, hogy ellenőrzik az egy előfizetésen belüli erőforrásoknak a követelményeknek és szabványoknak való megfelelését.

Ha a tervekbe belefoglal egy szabályzatot, lehetővé teszi a megfelelő minta vagy kialakítás létrehozását a terv hozzárendelésekor. A szabályzat belefoglalásával azt is biztosíthatja, hogy a környezeten csak jóváhagyott vagy várt módosításokat lehessen végrehajtani, így folyamatosan megfeleljen a terv által kijelölt rendeltetési célnak.

A szabályzatok a tervrajzok definíciójában szereplő _számos összetevő_ egyike lehet. A tervek a szabályzatokhoz és kezdeményezésekhez paraméterek használatát is támogatják.

## <a name="blueprint-definition"></a>Tervdefiníció

A tervek _tervelemekből_ állnak. Az Azure-tervezetek jelenleg a következő erőforrásokat támogatják összetevőkként:

|Erőforrás  | Hierarchialehetőségek| Leírás  |
|---------|---------|---------|
|Erőforráscsoportok | Előfizetés | Hozzon létre egy új erőforráscsoportot, amelyet a tervben szereplő többi tervelem fog használni.  Ezek a helyőrző erőforráscsoportok lehetővé teszik az erőforrások rendszerezését, és a benne foglalt szabályzatok és szerepkör-hozzárendelési összetevők és ARM-sablonok hatókör-korlátozását. |
|ARM-sablon | Előfizetés, erőforráscsoport | A sablonok, beleértve a beágyazott és a csatolt sablonokat, összetett környezetek létrehozásához használatosak. Ilyen környezetek lehetnek például a SharePoint-farmok, az Azure Automation-állapotkonfigurációk vagy a Log Analytics-munkaterületek. |
|Szabályzat-hozzárendelés | Előfizetés, erőforráscsoport | Lehetővé teszi egy szabályzat vagy kezdeményezés hozzárendelését ahhoz az előfizetéshez, amelyhez a terv hozzá van rendelve. A szabályzatnak vagy kezdeményezésnek a terv meghatározási helyének hatókörén belül kell lennie. Ha a szabályzat vagy kezdeményezés paraméterekkel rendelkezik, ezeket a paramétereket a terv létrehozásakor vagy hozzárendelésekor lehet hozzárendelni. |
|Szerepkör-hozzárendelés | Előfizetés, erőforráscsoport | Egy meglévő felhasználót vagy csoportot hozzáadhat egy beépített szerepkörhöz, és ezzel biztosíthatja, hogy a megfelelő személyek mindig a megfelelő hozzáféréssel rendelkezzenek az erőforrásokhoz. A szerepkör-hozzárendelések meghatározhatók az egész előfizetésre, vagy leszűkíthetők egy bizonyos erőforráscsoportra, amely a tervben található. |

### <a name="blueprint-definition-locations"></a>Terv definíciós helyei

Tervdefiníció létrehozásakor meg kell határozni a terv mentési helyét. A tervrajzok olyan [felügyeleti csoportba](../management-groups/overview.md) vagy előfizetésbe menthetők, amelyhez **közreműködői** hozzáféréssel rendelkezik. Ha a hely egy felügyeleti csoport, a terv a felügyeleti csoport bármely alárendelt előfizetéséhez rendelhető.

### <a name="blueprint-parameters"></a>Tervparaméterek

A tervrajzok egy házirend/kezdeményezés vagy egy ARM-sablon számára is átadhatják a paramétereket. Amikor bármilyen _összetevőt_ ad hozzá a tervhez, a szerző eldönti, hogy mindegyik tervhozzárendeléshez megad egy meghatározott értéket, vagy hagyja, hogy az egyes tervhozzárendelések értéke hozzárendeléskor legyen megadva. Ez a fajta rugalmasság biztosítja azt a választási lehetőséget, hogy a terv minden felhasználásakor egy előre meghatározott értéket használunk-e, vagy hagyjuk, hogy az értékről a hozzárendeléskor lehessen dönteni.

> [!NOTE]
> A terv rendelkezhet saját paraméterekkel, de ezeket jelenleg csak úgy lehet létrehozni, ha a tervet REST API-ból hozzunk létre, nem a portálról.

További információkért tekintse meg a [tervparamétereket](./concepts/parameters.md) ismertető cikket.

### <a name="blueprint-publishing"></a>Tervek közzététele

Amikor létrehozunk egy tervet, az **Vázlat** módban van. Ha a terv készen áll a hozzárendelésre, **közzé kell tenni**. A közzétételhez meg kell adni egy **verzió** sztringet (ez betűket, számokat és kötőjeleket tartalmazhat, és legfeljebb 20 karakter hosszú lehet), valamint **változási megjegyzések** is megadhatók, de ez nem kötelező. A **verzió** megkülönbözteti a tervet annak jövőbeli módosított változataitól, és lehetővé teszi az egyes verziók hozzárendelését. A verziókezelés azt is jelenti, hogy egy adott terv különböző **verziói** is hozzárendelhetők ugyanahhoz az előfizetéshez. Ha a tervben további változások történnek, a **közzétett** 
 **verzió** továbbra is létezik, ahogy a nem **közzétett módosítások**is. Miután elvégeztük a kívánt módosításokat, a frissített tervet **közzétesszük** egy új és egyedi **verzióval**, amely innentől kezdve szintén hozzárendelhető lesz.

## <a name="blueprint-assignment"></a>Tervek hozzárendelése

A tervrajzok minden **közzétett** **verziója** hozzárendelhető (legfeljebb 90 karakter hosszú lehet) egy meglévő előfizetéshez. A portálon alapértelmezés szerint a terv legutóbb **közzétett****verziója** érhető el. Ha a terv tartalmaz összetevő-paramétereket (vagy tervparamétereket), akkor ezeket a hozzárendelés során lehet meghatározni.

## <a name="permissions-in-azure-blueprints"></a>Engedélyek az Azure Blueprintsben

A tervek használatához [szerepköralapú hozzáférés-vezérlésen (RBAC)](../../role-based-access-control/overview.md) kell megadni az engedélyeket. Tervek létrehozásához a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprints/write` – Tervdefiníció létrehozása
- `Microsoft.Blueprint/blueprints/artifacts/write` – Tervelemek létrehozása tervdefiníció alapján
- `Microsoft.Blueprint/blueprints/versions/write` – Terv közzététele

Tervek törléséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> A terv definíciójának engedélyeit a felügyeleti csoport vagy az előfizetés hatókörén kell megadni vagy örökölni.

Tervek hozzárendeléséhez vagy a hozzárendelés megszüntetéséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprintAssignments/write` – Terv hozzárendelése
- `Microsoft.Blueprint/blueprintAssignments/delete` – Terv hozzárendelésének megszüntetése

> [!NOTE]
> Mivel a tervhozzárendelések létrehozása előfizetésenként történik, a terv hozzárendelési és hozzárendelés-megszüntetési engedélyeit az előfizetés hatókörében kell megadni, vagy örökléssel kell biztosítani az előfizetési hatókörben.

A következő beépített szerepkörök érhetők el:

|RBAC szerepkör | Leírás |
|-|-|
|[Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) | A többi engedélyen kívül minden Azure Blueprint kapcsolódó engedélyt tartalmaz. |
|[Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) | Más engedélyek mellett létrehozhat és törölhet tervrajz-definíciókat, de nem rendelkezik terv-hozzárendelési engedélyekkel. |
|[Tervezet közreműködője](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Kezelheti a terv definícióit, de nem rendelheti hozzá őket. |
|[Blueprint operátor](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Meglévő közzétett tervrajzokat is hozzárendelhet, de nem hozhat létre új terv-definíciókat. A terv-hozzárendelés csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással lett végrehajtva. |

Ha ezek a beépített szerepkörök nem felelnek meg a biztonsági követelményeinek, létrehozhat [egyéni szerepköröket](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Ha rendszer által hozzárendelt felügyelt identitást használ, az Azure-tervezetekhez tartozó egyszerű szolgáltatás **tulajdonosi** szerepkört igényel a hozzárendelt előfizetésben az üzembe helyezés engedélyezése érdekében. A portál használata esetén ezt a szerepkört a rendszer automatikusan biztosítja és visszavonja az üzembe helyezéshez. A REST API használata esetén a szerepkört manuálisan kell biztosítani, de az üzembe helyezés befejeződése a rendszer szintén automatikusan visszavonja. Ha felhasználó által hozzárendelt felügyelt identitást használ, csak a terv-hozzárendelést létrehozó felhasználónak van szüksége az `Microsoft.Blueprint/blueprintAssignments/write` engedélyre, amely a **tulajdonos** és a **Blueprint operátor** beépített szerepköreiben is megtalálható.

## <a name="naming-limits"></a>Elnevezési korlátok

Bizonyos mezőkhöz a következő korlátozások vonatkoznak:

|Objektum|Mező|Engedélyezett karakterek|Legfeljebb Hossz|
|-|-|-|-|
|Terv|Name|betűk, számok, kötőjelek és időszakok|48|
|Terv|Verzió|betűk, számok, kötőjelek és időszakok|20|
|Tervek hozzárendelése|Name|betűk, számok, kötőjelek és időszakok|90|
|Tervrajz-összetevő|Name|betűk, számok, kötőjelek és időszakok|48|

## <a name="video-overview"></a>Videó – áttekintés

Az Azure-tervezetek az alábbi áttekintést nyújtanak az Azure Fridays-ről. A videók letöltéséhez látogasson el az Azure Fridays szolgáltatásra, amely [áttekintést nyújt a Channel 9 Azure-tervezetekről](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) .

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>További lépések

- [Terv létrehozása – portál](./create-blueprint-portal.md).
- [Terv létrehozása – PowerShell](./create-blueprint-powershell.md).
- [Hozzon létre egy tervet – REST API](./create-blueprint-rest-api.md).