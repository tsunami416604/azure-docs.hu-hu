---
title: Az Azure Blueprints áttekintése
description: Ismerje meg, hogyan a Azure tervezetek szolgáltatás segítségével hozhat létre, adja meg, és üzembe helyezése az Azure-környezetben lévő összetevőkkel.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 960b8145e5f53c6c37820604fd634ccf5fd77c6b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59259422"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Az Azure-tervek szolgáltatás áttekintése

Hasonlóan ahhoz, ahogyan a tervekkel a mérnökök és az építészek felvázolják egy projekt tervezési paramétereit, az Azure Blueprints segítségével a felhőmérnökök és központi informatikai csoportok meghatározhatnak egy megismételhető Azure-erőforráskészletet, amely megvalósítja és betartja a vállalat szabványait, mintázatait és követelményeit. Az Azure Blueprints segítségével a fejlesztőcsapatok gyorsan építhetnek ki és helyezhetnek üzembe új környezeteket, annak biztos tudatában, hogy megfelelnek a vállalati követelményeknek, és olyan beépített összetevőkkel (például hálózatkezeléssel), amelyek révén az üzembe helyezés és a szolgáltatások nyújtása gyorsabb lehet.

A tervekkel deklaratív módon, összehangoltan helyezhetők üzembe különböző erőforrássablonok és egyéb tervelemek, például a következők:

- Szerepkör-hozzárendelések
- Szabályzat-hozzárendelések
- Azure Resource Manager-sablonok
- Erőforráscsoportok

Az Azure Blueprints szolgáltatást a globális terjesztésű [Azure Cosmos DB](../../cosmos-db/introduction.md) támogatja.
A tervobjektumok több Azure-régióba vannak replikálva. A replikáció alacsony késést, magas rendelkezésre állást, valamint konzisztens hozzáférést biztosít a tervobjektumokhoz, függetlenül attól, hogy a Blueprints melyik régiókban helyezi üzembe az erőforrásokat.

## <a name="how-its-different-from-resource-manager-templates"></a>Miben különbözik ez a Resource Manager-sablonoktól?

A szolgáltatás úgy van megtervezve, hogy segítsen a _környezet beállításában_. Ez a beállítás gyakran erőforráscsoportok, szabályzatok, szerepkör-hozzárendelések és Resource Manager-sablonok üzembe helyezését is magában foglalja. A tervek olyan csomagok, amelyekben ezen _tervelemek_ mindegyike megtalálható. A csomagok összeállíthatók, és az egyes verzióik kezelhetőek többek között egy CI/CD-folyamaton keresztül. Végül minden csomag egy előfizetéshez van rendelve egyetlen műveletben, amely felügyelhető és nyomon követhető.

Szinte minden, ami a tervekben szerepel, üzembe helyezhető Resource Manager-sablonokkal is. Azonban a Resource Manager-sablonok olyan dokumentumok, amelyek nem léteznek natív módon az Azure-ban – minden sablon tárolása helyileg vagy a verziókövetési rendszerben történik. A sablonok felhasználhatók egy vagy több Azure-erőforrás üzembe helyezéséhez, azonban az erőforrások üzembe helyezése után a sablonnal való aktív kapcsolat elveszik.

A Blueprints használatakor a tervdefiníciók (a _telepítendő_ összetevők) és a tervhozzárendelések (a _telepített_ összetevők) közötti kapcsolat megmarad. Ez a kapcsolat lehetővé teszi az üzembe helyezett összetevők hatékonyabb nyomon követését és naplózását. A Blueprints használatával ugyanazon terv által irányított több előfizetés egyidejűleg is frissíthető.

Nem kell választani a Resource Manager-sablonok és a tervek között. Minden terv nulla vagy több Resource Manager-sablon _tervelemet_ tartalmazhat. Ez a támogatás azt jelenti, hogy a korábban kifejlesztett és kezelt, Resource Manager-sablonokat tartalmazó kódtár a Blueprintsben is újra felhasználható.

## <a name="how-its-different-from-azure-policy"></a>Miben különbözik ez az Azure Policytól?

A tervek olyan csomagok vagy tárolók, amelyekben az Azure-felhőszolgáltatások, a biztonság és a kialakítás megvalósításával kapcsolatos fókuszspecifikus szabványok, minták és követelmények állíthatók össze, amelyek újrafelhasználhatók a konzisztencia és a megfelelőség fenntartása érdekében.

A [szabályzatok](../policy/overview.md) olyan alapértelmezett engedélyezési / explicit megtagadási rendszerek, amelyek az erőforrások tulajdonságaira összpontosítanak az üzembe helyezés során és a meglévő erőforrások esetében. Segítik a felhőalapú rendszerek irányítását azáltal, hogy ellenőrzik az egy előfizetésen belüli erőforrásoknak a követelményeknek és szabványoknak való megfelelését.

Ha a tervekbe belefoglal egy szabályzatot, lehetővé teszi a megfelelő minta vagy kialakítás létrehozását a terv hozzárendelésekor. A szabályzat belefoglalásával azt is biztosíthatja, hogy a környezeten csak jóváhagyott vagy várt módosításokat lehessen végrehajtani, így folyamatosan megfeleljen a terv által kijelölt rendeltetési célnak.

Egy házirend is meg lehet adni egy számos _összetevők_ tervezet-definícióban. A tervek a szabályzatokhoz és kezdeményezésekhez paraméterek használatát is támogatják.

## <a name="blueprint-definition"></a>Tervdefiníció

A tervek _tervelemekből_ állnak. A tervek jelenleg a következő erőforrások használatát támogatják tervelemekként:

|Erőforrás  | Hierarchialehetőségek| Leírás  |
|---------|---------|---------|
|Erőforráscsoportok | Előfizetés | Hozzon létre egy új erőforráscsoportot, amelyet a tervben szereplő többi tervelem fog használni.  Ezekkel a helyőrző erőforráscsoportokkal az erőforrások a kívánt módon rendezhetők el, és kijelölhetők a tervbe foglalt szabályzat- és szerepkör-hozzárendelési tervelemek, valamint az Azure Resource Manager-sablonok hatókörkorlátai. |
|Azure Resource Manager-sablon | Előfizetés, erőforráscsoport | A sablonokkal összetett környezetek állíthatók össze. Ilyen környezetek lehetnek például a SharePoint-farmok, az Azure Automation-állapotkonfigurációk vagy a Log Analytics-munkaterületek. |
|Szabályzat-hozzárendelés | Előfizetés, erőforráscsoport | Lehetővé teszi egy szabályzat vagy kezdeményezés hozzárendelését ahhoz az előfizetéshez, amelyhez a terv hozzá van rendelve. A szabályzatot vagy kezdeményezést a tervrajz-definíció helye hatókörén belül kell lennie. Ha a szabályzat vagy kezdeményezés paraméterekkel rendelkezik, ezeket a paramétereket a terv létrehozásakor vagy hozzárendelésekor lehet hozzárendelni. |
|Szerepkör-hozzárendelés | Előfizetés, erőforráscsoport | Egy meglévő felhasználót vagy csoportot hozzáadhat egy beépített szerepkörhöz, és ezzel biztosíthatja, hogy a megfelelő személyek mindig a megfelelő hozzáféréssel rendelkezzenek az erőforrásokhoz. A szerepkör-hozzárendelések meghatározhatók az egész előfizetésre, vagy leszűkíthetők egy bizonyos erőforráscsoportra, amely a tervben található. |

### <a name="blueprint-definition-locations"></a>Tervrajz-definíció helyek

Tervdefiníció létrehozásakor meg kell határozni a terv mentési helyét. Tervezetek, melyekbe menthetők a [felügyeleti csoport](../management-groups/overview.md) vagy előfizetést, amely rendelkezik **közreműködői** való hozzáférést. Ha a hely egy felügyeleti csoportot, minden gyermek előfizetésre, az adott felügyeleti csoport hozzárendelése a tervezet érhető el.

### <a name="blueprint-parameters"></a>Tervparaméterek

A tervekkel paraméterek rendelhetők a szabályzatokhoz/kezdeményezésekhez, vagy az Azure Resource Manager-sablonokhoz.
Amikor bármilyen _összetevőt_ ad hozzá a tervhez, a szerző eldönti, hogy mindegyik tervhozzárendeléshez megad egy meghatározott értéket, vagy hagyja, hogy az egyes tervhozzárendelések értéke hozzárendeléskor legyen megadva. Ez a fajta rugalmasság biztosítja azt a választási lehetőséget, hogy a terv minden felhasználásakor egy előre meghatározott értéket használunk-e, vagy hagyjuk, hogy az értékről a hozzárendeléskor lehessen dönteni.

> [!NOTE]
> A terv rendelkezhet saját paraméterekkel, de ezeket jelenleg csak úgy lehet létrehozni, ha a tervet REST API-ból hozzunk létre, nem a portálról.

További információkért tekintse meg a [tervparamétereket](./concepts/parameters.md) ismertető cikket.

### <a name="blueprint-publishing"></a>Tervek közzététele

Amikor létrehozunk egy tervet, az **Vázlat** módban van. Ha a terv készen áll a hozzárendelésre, **közzé kell tenni**. A közzétételhez meg kell adni egy **verzió** sztringet (ez betűket, számokat és kötőjeleket tartalmazhat, és legfeljebb 20 karakter hosszú lehet), valamint **változási megjegyzések** is megadhatók, de ez nem kötelező. A **verzió** megkülönbözteti a tervet annak jövőbeli módosított változataitól, és lehetővé teszi az egyes verziók hozzárendelését. A verziókezelés azt is jelenti, hogy egy adott terv különböző **verziói** is hozzárendelhetők ugyanahhoz az előfizetéshez. Ha további módosításokat végez a terven, a **közzétett** **verzió** továbbra is létezni fog, ahogyan a **közzé nem tett módosítások** is. Miután elvégeztük a kívánt módosításokat, a frissített tervet **közzétesszük** egy új és egyedi **verzióval**, amely innentől kezdve szintén hozzárendelhető lesz.

## <a name="blueprint-assignment"></a>Tervek hozzárendelése

A tervek minden **közzétett** **verziója** hozzárendelhető a meglévő előfizetésekhez. A portálon alapértelmezés szerint a terv legutóbb **közzétett** **verziója** érhető el. Ha a terv tartalmaz összetevő-paramétereket (vagy tervparamétereket), akkor ezeket a hozzárendelés során lehet meghatározni.

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
> A tervezet definition engedélyekkel kell kapnak, vagy a felügyeleti csoportba vagy előfizetésbe hatókör szeretné menteni az örökölt.

Tervek hozzárendeléséhez vagy a hozzárendelés megszüntetéséhez a fióknak a következő engedélyekkel kell rendelkeznie:

- `Microsoft.Blueprint/blueprintAssignments/write` – Terv hozzárendelése
- `Microsoft.Blueprint/blueprintAssignments/delete` – Terv hozzárendelésének megszüntetése

> [!NOTE]
> Mivel a tervhozzárendelések létrehozása előfizetésenként történik, a terv hozzárendelési és hozzárendelés-megszüntetési engedélyeit az előfizetés hatókörében kell megadni, vagy örökléssel kell biztosítani az előfizetési hatókörben.

A fenti engedélyek mindegyikének birtokában szerepelnek a **tulajdonosa** szerepkör. A **közreműködői** szerepkör rendelkezik tervezet és törlési tervezet engedélyek létrehozása, de nem rendelkezik a tervezet-hozzárendelési engedélyt. Ha ezek a beépített szerepkörök nem felelnek meg a biztonsági követelményeinek, létrehozhat [egyéni szerepköröket](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Az Azure Blueprints-tervek szolgáltatásnevének **Tulajdonos** szerepkörrel kell rendelkeznie a hozzárendelt előfizetésen ahhoz, hogy az üzembe helyezés engedélyezett legyen. A portál használata esetén ezt a szerepkört a rendszer automatikusan biztosítja és visszavonja az üzembe helyezéshez. A REST API használata esetén a szerepkört manuálisan kell biztosítani, de az üzembe helyezés befejeződése a rendszer szintén automatikusan visszavonja.

## <a name="video-overview"></a>Áttekintő videó

A következő Azure tervek áttekintése az Azure fridays-re. Videó letöltése, látogasson el a [Azure fridays-re – Azure tervek áttekintése](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) Channel 9-on.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>További lépések

- [Terv létrehozása – Portál](create-blueprint-portal.md)
- [Terv létrehozása – REST API](create-blueprint-rest-api.md)