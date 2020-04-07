---
title: Az Azure Blueprints áttekintése
description: Ismerje meg, hogy az Azure Blueprints szolgáltatás hogyan teszi lehetővé összetevők létrehozását, definiálását és üzembe helyezését az Azure-környezetben.
ms.date: 11/21/2019
ms.topic: overview
ms.openlocfilehash: 4787f2d559daffcbf5d4057ac381f0f7e1ae0c57
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677417"
---
# <a name="what-is-azure-blueprints"></a>Mi az az Azure Blueprints?

Hasonlóan ahhoz, ahogyan a tervekkel a mérnökök és az építészek felvázolják egy projekt tervezési paramétereit, az Azure Blueprints segítségével a felhőmérnökök és központi informatikai csoportok meghatározhatnak egy megismételhető Azure-erőforráskészletet, amely megvalósítja és betartja a vállalat szabványait, mintázatait és követelményeit. Az Azure Blueprints segítségével a fejlesztőcsapatok gyorsan építhetnek ki és helyezhetnek üzembe új környezeteket, annak biztos tudatában, hogy megfelelnek a vállalati követelményeknek, és olyan beépített összetevőkkel (például hálózatkezeléssel), amelyek révén az üzembe helyezés és a szolgáltatások nyújtása gyorsabb lehet.

A tervekkel deklaratív módon, összehangoltan helyezhetők üzembe különböző erőforrássablonok és egyéb tervelemek, például a következők:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager-sablonok
- Erőforráscsoportok

Az Azure Blueprints szolgáltatást a globális terjesztésű [Azure Cosmos DB](../../cosmos-db/introduction.md) támogatja.
A tervobjektumok több Azure-régióba vannak replikálva. Ez a replikáció alacsony késést, magas rendelkezésre állást és konzisztens hozzáférést biztosít a tervezet objektumokhoz, függetlenül attól, hogy az Azure Blueprints melyik régióban helyezi üzembe az erőforrásokat.

## <a name="how-its-different-from-resource-manager-templates"></a>Miben különbözik ez a Resource Manager-sablonoktól?

A szolgáltatás úgy van megtervezve, hogy segítsen a _környezet beállításában_. Ez a beállítás gyakran erőforráscsoportok, szabályzatok, szerepkör-hozzárendelések és Resource Manager-sablonok üzembe helyezését is magában foglalja. A tervek olyan csomagok, amelyekben ezen _tervelemek_ mindegyike megtalálható. A csomagok összeállíthatók, és az egyes verzióik kezelhetőek többek között egy CI/CD-folyamaton keresztül. Végül minden csomag egy előfizetéshez van rendelve egyetlen műveletben, amely felügyelhető és nyomon követhető.

Szinte mindent, amit szeretne felvenni az Azure Blueprints üzembe helyezéshez lehet elérni egy Resource Manager sablon. Azonban a Resource Manager-sablonok olyan dokumentumok, amelyek nem léteznek natív módon az Azure-ban – minden sablon tárolása helyileg vagy a verziókövetési rendszerben történik. A sablonok felhasználhatók egy vagy több Azure-erőforrás üzembe helyezéséhez, azonban az erőforrások üzembe helyezése után a sablonnal való aktív kapcsolat elveszik.

Az Azure Blueprints, a blueprint-definíció (mit _kell_ telepíteni) és a blueprint-hozzárendelés (mi _volt_ üzembe helyezett) közötti kapcsolat megmarad. Ez a kapcsolat lehetővé teszi az üzembe helyezett összetevők hatékonyabb nyomon követését és naplózását. Az Azure Blueprints is frissíthet egyszerre több előfizetést, amelyek ugyanaz a terv rajzterv által szabályozott.

Nem kell választani a Resource Manager-sablonok és a tervek között. Minden terv nulla vagy több Resource Manager-sablon _tervelemet_ tartalmazhat. Ez a támogatás azt jelenti, hogy az Erőforrás-kezelő-sablonok könyvtárának fejlesztésére és karbantartására irányuló korábbi erőfeszítések újrafelhasználhatók az Azure Blueprints-ben.

## <a name="how-its-different-from-azure-policy"></a>Miben különbözik ez az Azure Policytól?

A tervek olyan csomagok vagy tárolók, amelyekben az Azure-felhőszolgáltatások, a biztonság és a kialakítás megvalósításával kapcsolatos fókuszspecifikus szabványok, minták és követelmények állíthatók össze, amelyek újrafelhasználhatók a konzisztencia és a megfelelőség fenntartása érdekében.

A [szabályzatok](../policy/overview.md) olyan alapértelmezett engedélyezési / explicit megtagadási rendszerek, amelyek az erőforrások tulajdonságaira összpontosítanak az üzembe helyezés során és a meglévő erőforrások esetében. Segítik a felhőalapú rendszerek irányítását azáltal, hogy ellenőrzik az egy előfizetésen belüli erőforrásoknak a követelményeknek és szabványoknak való megfelelését.

Ha a tervekbe belefoglal egy szabályzatot, lehetővé teszi a megfelelő minta vagy kialakítás létrehozását a terv hozzárendelésekor. A szabályzat belefoglalásával azt is biztosíthatja, hogy a környezeten csak jóváhagyott vagy várt módosításokat lehessen végrehajtani, így folyamatosan megfeleljen a terv által kijelölt rendeltetési célnak.

A szabályzat szerepelhet a tervdefiníciószámos _összetevőjének_ egyikeként. A tervek a szabályzatokhoz és kezdeményezésekhez paraméterek használatát is támogatják.

## <a name="blueprint-definition"></a>Tervdefiníció

A tervek _tervelemekből_ állnak. Az Azure Blueprints jelenleg a következő erőforrásokat támogatja műtermékként:

|Erőforrás  | Hierarchialehetőségek| Leírás  |
|---------|---------|---------|
|Erőforráscsoportok | Előfizetés | Hozzon létre egy új erőforráscsoportot, amelyet a tervben szereplő többi tervelem fog használni.  Ezekkel a helyőrző erőforráscsoportokkal az erőforrások a kívánt módon rendezhetők el, és kijelölhetők a tervbe foglalt szabályzat- és szerepkör-hozzárendelési tervelemek, valamint az Azure Resource Manager-sablonok hatókörkorlátai. |
|Azure Resource Manager-sablon | Előfizetés, erőforráscsoport | A sablonok, beleértve a beágyazott és csatolt sablonokat is, összetett környezetek írására szolgálnak. Ilyen környezetek lehetnek például a SharePoint-farmok, az Azure Automation-állapotkonfigurációk vagy a Log Analytics-munkaterületek. |
|Szabályzat-hozzárendelés | Előfizetés, erőforráscsoport | Lehetővé teszi egy szabályzat vagy kezdeményezés hozzárendelését ahhoz az előfizetéshez, amelyhez a terv hozzá van rendelve. A házirendnek vagy kezdeményezésnek a tervezet definíciós helyének hatálya alá kell tartaszta. Ha a szabályzat vagy kezdeményezés paraméterekkel rendelkezik, ezeket a paramétereket a terv létrehozásakor vagy hozzárendelésekor lehet hozzárendelni. |
|Szerepkör-hozzárendelés | Előfizetés, erőforráscsoport | Egy meglévő felhasználót vagy csoportot hozzáadhat egy beépített szerepkörhöz, és ezzel biztosíthatja, hogy a megfelelő személyek mindig a megfelelő hozzáféréssel rendelkezzenek az erőforrásokhoz. A szerepkör-hozzárendelések meghatározhatók az egész előfizetésre, vagy leszűkíthetők egy bizonyos erőforráscsoportra, amely a tervben található. |

### <a name="blueprint-definition-locations"></a>Tervezetdefiníciós helyek

Tervdefiníció létrehozásakor meg kell határozni a terv mentési helyét. A tervrajzok menthetők egy [felügyeleti csoportba](../management-groups/overview.md) vagy előfizetésbe, amelyhez **közreműködői** hozzáféréssel rendelkezik. Ha a hely egy felügyeleti csoport, a tervezet az adott felügyeleti csoport bármely gyermek-előfizetéséhez hozzárendelhető.

### <a name="blueprint-parameters"></a>Tervparaméterek

A tervekkel paraméterek rendelhetők a szabályzatokhoz/kezdeményezésekhez, vagy az Azure Resource Manager-sablonokhoz.
Amikor bármilyen _összetevőt_ ad hozzá a tervhez, a szerző eldönti, hogy mindegyik tervhozzárendeléshez megad egy meghatározott értéket, vagy hagyja, hogy az egyes tervhozzárendelések értéke hozzárendeléskor legyen megadva.
Ez a fajta rugalmasság biztosítja azt a választási lehetőséget, hogy a terv minden felhasználásakor egy előre meghatározott értéket használunk-e, vagy hagyjuk, hogy az értékről a hozzárendeléskor lehessen dönteni.

> [!NOTE]
> A terv rendelkezhet saját paraméterekkel, de ezeket jelenleg csak úgy lehet létrehozni, ha a tervet REST API-ból hozzunk létre, nem a portálról.

További információkért tekintse meg a [tervparamétereket](./concepts/parameters.md) ismertető cikket.

### <a name="blueprint-publishing"></a>Tervek közzététele

Amikor létrehozunk egy tervet, az **Vázlat** módban van. Ha a terv készen áll a hozzárendelésre, **közzé kell tenni**. A közzétételhez meg kell adni egy **verzió** sztringet (ez betűket, számokat és kötőjeleket tartalmazhat, és legfeljebb 20 karakter hosszú lehet), valamint **változási megjegyzések** is megadhatók, de ez nem kötelező. A **verzió** megkülönbözteti a tervet annak jövőbeli módosított változataitól, és lehetővé teszi az egyes verziók hozzárendelését. A verziókezelés azt is jelenti, hogy egy adott terv különböző **verziói** is hozzárendelhetők ugyanahhoz az előfizetéshez. Ha további módosításokat hajt végre a tervezeten, a **közzétett**
**verzió** továbbra is létezik, csakúgy, mint a közzé nem **tett módosítások**. Miután elvégeztük a kívánt módosításokat, a frissített tervet **közzétesszük** egy új és egyedi **verzióval**, amely innentől kezdve szintén hozzárendelhető lesz.

## <a name="blueprint-assignment"></a>Tervek hozzárendelése

A tervezet **minden közzétett** **verziója** hozzárendelhető (legfeljebb 90 karakter hosszúságú névvel) egy meglévő előfizetéshez. A portálon alapértelmezés szerint a terv legutóbb **közzétett****verziója** érhető el. Ha a terv tartalmaz összetevő-paramétereket (vagy tervparamétereket), akkor ezeket a hozzárendelés során lehet meghatározni.

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
> A tervezet definíciós engedélyeket kell adni, vagy örökölt a felügyeleti csoport vagy előfizetés hatóköre, ahol menti.

Tervek hozzárendeléséhez vagy a hozzárendelés megszüntetéséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprintAssignments/write` – Terv hozzárendelése
- `Microsoft.Blueprint/blueprintAssignments/delete` – Terv hozzárendelésének megszüntetése

> [!NOTE]
> Mivel a tervhozzárendelések létrehozása előfizetésenként történik, a terv hozzárendelési és hozzárendelés-megszüntetési engedélyeit az előfizetés hatókörében kell megadni, vagy örökléssel kell biztosítani az előfizetési hatókörben.

A következő beépített szerepkörök érhetők el:

|RBAC szerepkör | Leírás |
|-|-|
|[Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) | Az egyéb engedélyek mellett tartalmazza az összes Azure Blueprint kapcsolódó engedélyeket. |
|[Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) | Más engedélyek mellett létrehozhat és törölhet tervezetdefiníciókat, de nem rendelkezik tervezet-hozzárendelési engedélyekkel. |
|[Blueprint közreműködő](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Kezelheti a tervezetdefiníciókat, de nem rendelheti hozzá őket. |
|[Terv operátor](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Meglévő közzétett tervrajzok hozzárendelése, de nem hozhat létre új tervrajzokat. A blueprint-hozzárendelés csak akkor működik, ha a hozzárendelés felhasználó által hozzárendelt felügyelt identitással történik. |

Ha ezek a beépített szerepkörök nem felelnek meg a biztonsági követelményeinek, létrehozhat [egyéni szerepköröket](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Ha egy rendszer által hozzárendelt felügyelt identitás használata, az Azure Blueprints szolgáltatásnévi szerepkörhöz a hozzárendelt előfizetés **tulajdonosi** szerepköre van szükség az üzembe helyezés engedélyezéséhez. A portál használata esetén ezt a szerepkört a rendszer automatikusan biztosítja és visszavonja az üzembe helyezéshez. A REST API használata esetén a szerepkört manuálisan kell biztosítani, de az üzembe helyezés befejeződése a rendszer szintén automatikusan visszavonja. Ha egy felhasználó által hozzárendelt felügyelt identitás, csak a `Microsoft.Blueprint/blueprintAssignments/write` rendszerrajz-hozzárendelést létrehozó felhasználónak van szüksége az engedélyre, amely a **Tulajdonos** és a **Blueprint Operator** beépített szerepkörökben is szerepel.

## <a name="naming-limits"></a>Elnevezési korlátok

Bizonyos mezőkre a következő korlátozások vonatkoznak:

|Objektum|Mező|Engedélyezett karakterek|Legfeljebb Hossz|
|-|-|-|-|
|Blueprint|Név|betűk, számok, kötőjelek és pont|48|
|Blueprint|Verzió|betűk, számok, kötőjelek és pont|20|
|Tervek hozzárendelése|Név|betűk, számok, kötőjelek és pont|90|
|Tervrajz-műtárgy|Név|betűk, számok, kötőjelek és pont|48|

## <a name="video-overview"></a>Videó – áttekintés

Az Azure Blueprints alábbi áttekintése az Azure Fridays.The following overview of Azure Blueprints is from Azure Fridays. Videóletöltésért látogasson el az [Azure Fridays – Az Azure Blueprints áttekintése](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) a 9-es csatornán.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>További lépések

- [Hozzon létre egy tervrajzot - Portál](./create-blueprint-portal.md).
- [Hozzon létre egy tervrajzot - PowerShell](./create-blueprint-powershell.md).
- [Hozzon létre egy tervrajzot - REST API](./create-blueprint-rest-api.md).