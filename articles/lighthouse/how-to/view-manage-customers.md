---
title: Ügyfelek és delegált erőforrások megtekintése és kezelése
description: Az Azure delegált erőforrás-kezeléset használó szolgáltatóként megtekintheti az összes delegált ügyfélerőforrást és előfizetést az Azure Portalon az Ügyfeleimmel.
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: 0d4b3187066754e8a549f029623762df539b30b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543426"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ügyfelek és delegált erőforrások megtekintése és kezelése

Az [Azure delegált erőforrás-kezeléset](../concepts/azure-delegated-resource-management.md) használó szolgáltatók az [Azure PortalOn](https://portal.azure.com) az **Ügyfelek** lapon megtekinthetik a delegált ügyfél-erőforrásokat és -előfizetéseket. Míg itt a szolgáltatókra és az ügyfelekre hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a felügyeleti élmény ük konszolidálására.

Az Azure **PortalOn** az Ügyfelek lapjának eléréséhez válassza a **Minden szolgáltatás**lehetőséget, majd keresse meg az Én **ügyfeleimmel,** és jelölje ki. Azt is megtalálhatja, ha beírja a "Saját ügyfeleim" kifejezést az Azure Portal tetején lévő keresőmezőbe.

Ne feledje, hogy a **Saját ügyfelek** lap legnépszerűbb **Ügyfelek** szakasza csak az előfizetéssel vagy erőforráscsoporttal delegált ügyfelekre szóló információkat jeleníti meg. Ha más ügyfelekkel dolgozik (például a [Felhőszolgáltató programon](https://docs.microsoft.com/partner-center/csp-overview)keresztül, akkor nem fogja látni az ügyfelekre vonatkozó információkat az **Ügyfelek** szakaszban, kivéve, ha beszállt az erőforrásokra az Azure delegált erőforrás-kezeléséhez.

A lapon egy külön szakasz, a **Felhőszolgáltató (előzetes verzió)** a [Microsoft ügyfélszerződést (MCA) aláíró](https://docs.microsoft.com/partner-center/confirm-customer-agreement) és [az Azure-csomag alá tartozó](https://docs.microsoft.com/partner-center/azure-plan-get-started)kripta-ügyfelek számlázási adatait és erőforrásait jeleníti meg. További információ: [Ismerkedés a Microsoft Partner-szerződés számlázási fiókjával.](../../billing/mpa-overview.md) Vegye figyelembe, hogy az ilyen kripta-ügyfelek jelennek meg ebben a szakaszban, függetlenül attól, hogy ön is beszállt a delegált azure-beli erőforrás-kezelés. Hasonlóképpen a CSP-ügyfél nem kell megjelennie a **Felhőalapú megoldásszolgáltató (előzetes verzió)** szakaszában **az ügyfeleim** annak érdekében, hogy az Azure delegált erőforrás-kezeléséhez.

> [!NOTE]
> Az ügyfelek megtekinthetik a szolgáltatókkal kapcsolatos információkat, ha az Azure Portalon a **szolgáltatókhoz** navigálnak. További információt a Szolgáltatók megtekintése és kezelése című témakörben [talál.](view-manage-service-providers.md)

## <a name="view-and-manage-customer-details"></a>Vevőrészleteinek megtekintése és kezelése

A vevő részleteinek megtekintéséhez válassza a **Vevők** lehetőséget a **Vevőim** lap bal oldalán.

Minden ügyfélnél láthatja az ügyfél nevét, az ügyfélazonosítóját (bérlői azonosítóját) és az aktivitáshoz társított ajánlatot. A **Delegálások** oszlopban láthatja a delegált előfizetések számát és/vagy a delegált erőforráscsoportok számát.

> [!IMPORTANT]
> A delegálás megtekintéséhez a felhasználóknak meg kell adni az [Olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely tartalmazza az Olvasó-hozzáférést) a bevezetési folyamatban.

Az oldal tetején található szűrők segítségével rendezheti és csoportosíthatja az ügyféladatokat, illetve szűrheti azokat adott ügyfelek, ajánlatok vagy kulcsszavak szerint.

A következő információkat tekintheti meg ezen az oldalon:

- Az ügyfélhez társított összes előfizetés, ajánlat és delegálás megtekintéséhez válassza ki az ügyfél nevét.
- Ha további részleteket szeretne megtudni egy ajánlatról és annak delegálásairól, válassza ki az ajánlat nevét.
- Ha további részleteket szeretne megtudni a delegált előfizetések vagy erőforráscsoportok szerepkör-hozzárendeléseiről, jelölje ki a bejegyzést a **Delegálások** oszlopban.

## <a name="view-and-manage-delegations"></a>Delegálások megtekintése és kezelése

A delegálások a delegált előfizetést/erőforráscsoportot, valamint a hozzá hozzáféréssel rendelkező felhasználókat és engedélyeket jelenítik meg. Az adatok megtekintéséhez válassza a **Delegálások** lehetőséget a **Saját ügyfelek** lap bal oldalán.

A lap tetején található szűrőkkel rendezheti és csoportosíthatja a hozzáférési hozzárendelésadatait, illetve szűrheti adott ügyfelek, ajánlatok vagy kulcsszavak szerint.

### <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

Az egyes delegáláshoz társított felhasználók és engedélyek megjelennek a **Szerepkör-hozzárendelések** oszlopban. Az egyes bejegyzések kiválasztásával megtekintheti az előfizetéshez vagy erőforráscsoporthoz hozzáféréssel rendelkező felhasználók, csoportok és szolgáltatásnévtagok teljes listáját. Itt kiválaszthat egy adott felhasználó, csoport vagy egyszerű szolgáltatás nevet további részletekért.

### <a name="remove-delegations"></a>Delegálások eltávolítása

Ha a felügyelt [szolgáltatások regisztrációs hozzárendelésének törlése szerepkörbe](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) foglalta a felhasználókat, amikor egy ügyfélre regisztrálnak az Azure delegált erőforrás-kezelésére, ezek a felhasználók eltávolíthatják a delegálást, ha kiválasztják a delegálás sorában megjelenő kukák ikont. Ha így tesznek, a szolgáltató bérlőjének egyetlen felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.


## <a name="work-in-the-context-of-a-delegated-subscription"></a>Munka delegált előfizetés sel összefüggésben

Közvetlenül dolgozhat egy delegált előfizetés környezetében az Azure Portalon, anélkül, hogy át váltana a címtárban, amelyben dolgozik. Ehhez tegye a következőket:

1. Válassza a **Címtár + Előfizetés** ikont az Azure Portal tetején.
2. A **Globális előfizetés** szűrőben győződjön meg arról, hogy csak a delegált előfizetés jelölőnégyzet van kiválasztva. Az Aktuális **+ delegált könyvtárak** legördülő lista segítségével csak egy adott könyvtáron belüli előfizetéseket jeleníthet meg. (Ne használja a **Címtár váltása** beállítást, mivel ez megváltoztatja azt a könyvtárat, amelyre be van jelentkezve.)

Ha ezután olyan szolgáltatást érhet el, amely támogatja a [bérlők közötti felügyeleti élményt,](../concepts/cross-tenant-management-experience.md)a szolgáltatás alapértelmezés szerint a kiválasztott delegált előfizetés környezetében lesz. Ezt a fenti lépések követésével módosíthatja, és bejelölheti az **Összes kijelölése** jelölőnégyzetet (vagy választhat egy vagy több előfizetést, amelyben dolgozni szeretne).

> [!NOTE]
> Ha egy vagy több erőforráscsoporthoz kapott hozzáférést, nem pedig egy teljes előfizetéshez, kiválaszthatja azt az előfizetést, amelyhez az adott erőforráscsoport tartozik. Ezután az adott előfizetés környezetében fog dolgozni, de csak a kijelölt erőforráscsoportok elérésére lesz képes.

A delegált előfizetésekhez vagy erőforráscsoportokhoz kapcsolódó funkciókat is elérheti a több-bérlős felügyeleti élményt támogató szolgáltatásokból, ha kiválasztja az előfizetést vagy az erőforráscsoportot az adott szolgáltatásból.

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről.](../concepts/cross-tenant-management-experience.md)
- Ismerje meg, hogyan [tekinthetik meg és kezelhetik](view-manage-service-providers.md) az ügyfelek a szolgáltatókat az Azure **Portalon** található szolgáltatók segítségével.
