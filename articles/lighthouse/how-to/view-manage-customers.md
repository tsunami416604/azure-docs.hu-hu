---
title: Ügyfelek és delegált erőforrások megtekintése és kezelése
description: Az Azure-beli delegált erőforrás-kezelést használó szolgáltatóként megtekintheti az összes delegált ügyfél-erőforrást és-előfizetést, ha a Azure Portalban lévő ügyfelekre kerül.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/23/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: d68ac0a1b6a66482a85f7ab79840099a34c731c2
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615537"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ügyfelek és delegált erőforrások megtekintése és kezelése

Az Azure-beli [delegált erőforrás-kezelést](../concepts/azure-delegated-resource-management.md) használó szolgáltatók használhatják a [Azure Portal](https://portal.azure.com) **saját ügyfelek** lapját a delegált ügyfél-erőforrások és-előfizetések megtekintéséhez. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

Ha a Azure Portal **saját ügyfelek** lapját szeretné elérni, válassza a **minden szolgáltatás**lehetőséget, majd keresse meg az **ügyfeleket** , és válassza ki azt. Azt is megteheti, hogy beírja az "ügyfelek" kifejezést a Azure Portal tetején található keresőmezőbe.

Ne feledje, hogy a **saját ügyfelek** lap felső szakasza csak a delegált előfizetéseket vagy erőforráscsoportokat használó ügyfelek információit jeleníti meg. Ha más ügyfelekkel dolgozik (például a [Cloud Solution Provider programon](https://docs.microsoft.com/partner-center/csp-overview)keresztül), akkor ezekkel az ügyfelekkel kapcsolatos információk nem jelennek meg, hacsak nem készíti elő az erőforrásokat a delegált erőforrás-kezeléshez. A lap alsó részén a **Cloud Solution Provider (előzetes verzió)** szakasz a [Microsoft ügyfél-szerződést (MCA) aláíró](https://docs.microsoft.com/partner-center/confirm-customer-agreement) ügyfeleinek számlázási információit és erőforrásait, valamint az Azure-csomag részét képezi, függetlenül attól, hogy be lett-e telepítve Az Azure-beli delegált erőforrás-kezeléshez. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](https://docs.microsoft.com/azure/billing/mpa-overview).

> [!NOTE]
> Az ügyfelek megtekinthetik a szolgáltatók adatait úgy, hogy a Azure Portalban **lévő szolgáltatókra** navigálnak. További információ: szolgáltatók [megtekintése és kezelése](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Ügyfél adatainak megtekintése és kezelése

Az ügyfél részleteinek megtekintéséhez válassza a **Customers** (ügyfelek) lehetőséget a **Customers (ügyfelek)** oldal bal oldalán.

Minden ügyfél esetében látni fogja az ügyfél nevét, az ügyfél AZONOSÍTÓját (bérlői AZONOSÍTÓját), valamint az engagement szolgáltatáshoz tartozó ajánlatot. A **delegálások** oszlopban látható a delegált előfizetések száma és/vagy a delegált erőforráscsoportok száma.

> [!IMPORTANT]
> A delegálás megtekintéséhez a felhasználóknak meg kell adni az [olvasó](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz) a bevezetési folyamat során.

Az oldal tetején lévő szűrők segítségével rendezheti és csoportosíthatja az ügyfél adatait, vagy szűrheti az ügyfelek, ajánlatok vagy kulcsszavak alapján.

Az alábbi adatokat tekintheti meg erről az oldalról:

- Ha meg szeretné tekinteni az ügyfélhez társított összes előfizetést, ajánlatot és delegálást, válassza ki az ügyfél nevét.
- Ha további részleteket szeretne látni egy ajánlatról és annak delegálásáról, válassza az ajánlat nevét.
- A delegált előfizetések vagy erőforráscsoportok szerepkör-hozzárendeléseivel kapcsolatos további részletek megtekintéséhez válassza ki a bejegyzést a **delegálások** oszlopban.

## <a name="view-delegations"></a>Delegálások megtekintése

A delegálások a delegált előfizetést/erőforráscsoportot, valamint a hozzájuk tartozó felhasználókat és engedélyeket jelenítik meg. Az adatok megtekintéséhez válassza a **saját ügyfelek** lap bal oldalán található **delegálások** lehetőséget.

A lap tetején található szűrők segítségével rendezheti és csoportosíthatja a hozzáférés-hozzárendelési adatokat, illetve szűrheti az egyes ügyfelek, ajánlatok vagy kulcsszavak alapján.

Az egyes delegálásokhoz társított felhasználók és engedélyek a szerepkör- **hozzárendelések** oszlopban jelennek meg. Az egyes bejegyzéseket kiválasztva megtekintheti azoknak a felhasználóknak, csoportoknak és egyszerű szolgáltatásoknak a teljes listáját, amelyek hozzáférést kaptak az előfizetéshez vagy az erőforráscsoporthoz. Az adott felhasználó, csoport vagy egyszerű szolgáltatásnév kiválasztásával további részleteket tudhat meg.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Meghatalmazott előfizetés kontextusában működik

A Azure Portalon belüli delegált előfizetések kontextusában közvetlenül is dolgozhat, és nem kell átváltania a címtárat, amelyen dolgozik. Ehhez tegye a következőket:

1. Válassza ki a **könyvtár + előfizetés** ikont a Azure Portal tetején.
2. A **globális előfizetés** -szűrőben ügyeljen arra, hogy csak az adott delegált előfizetés mezője legyen kiválasztva. A **jelenlegi + delegált címtárak** legördülő lista használatával csak egy adott címtárban lévő előfizetéseket jelenítheti meg. (Ne használja a **switch Directory kapcsolót** , mert ez megváltoztatja azt a könyvtárat, amelybe be van jelentkezve.)

Ha ezt követően olyan szolgáltatáshoz fér hozzá, amely támogatja a [több-bérlős felügyeletet](../concepts/cross-tenant-management-experience.md), a szolgáltatás alapértelmezés szerint a kiválasztott delegált előfizetés kontextusában fog megjelenni. Ezt a fenti lépések követésével módosíthatja, és az **összes kijelölése jelölőnégyzet bejelölésével** (vagy egy vagy több előfizetést is kiválaszthat).

> [!NOTE]
> Ha egy vagy több erőforráscsoporthoz hozzáférést kapott, és nem fér hozzá egy teljes előfizetéshez, akkor kiválaszthatja azt az előfizetést, amelyhez az erőforráscsoport tartozik. Ezután az előfizetés kontextusában fog működni, de csak a kijelölt erőforráscsoportokat fogja tudni elérni.

A delegált előfizetésekhez vagy erőforráscsoportokhöz kapcsolódó funkciókat a több-bérlős felügyeletet támogató szolgáltatásokon belül is elérheti, ha kijelöli az előfizetést vagy az erőforráscsoportot a szolgáltatáson belül.

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- Ismerje meg, hogy az ügyfelek hogyan [tekinthetik meg és kezelhetik a szolgáltatókat](view-manage-service-providers.md) a Azure Portalban **található szolgáltatók** használatával.
