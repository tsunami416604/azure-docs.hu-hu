---
title: Ügyfelek és delegált erőforrások megtekintése és kezelése
description: Az Azure Lighthouse szolgáltatást használó szolgáltatóként megtekintheti az összes delegált ügyfél-erőforrást és-előfizetést, ha a Azure Portalban lévő ügyfelekre kerül.
ms.date: 07/06/2020
ms.topic: how-to
ms.openlocfilehash: a850dc8cfe7667a75ced64ef0bd0afc352b20daa
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026270"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ügyfelek és delegált erőforrások megtekintése és kezelése

Az Azure Lighthouse szolgáltatást használó szolgáltatók használhatják a [Azure Portal](https://portal.azure.com) **saját ügyfelek** lapját a delegált ügyfelek erőforrásainak és előfizetésének megtekintéséhez. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

Ha a Azure Portal **saját ügyfelek** lapját szeretné elérni, válassza a **minden szolgáltatás**lehetőséget, majd keresse meg az **ügyfeleket** , és válassza ki azt. Azt is megteheti, hogy beírja az "ügyfelek" kifejezést a Azure Portal tetején található keresőmezőbe.

Ne feledje, hogy a **saját ügyfelek** lap legfontosabb **ügyfelek** szakasza csak a delegált előfizetéseket vagy erőforráscsoportokat használó ügyfelek adatait jeleníti meg. Ha más ügyfelekkel dolgozik (például a [Cloud Solution Provider programon](/partner-center/csp-overview)keresztül), akkor nem fogja látni az ügyfelekkel kapcsolatos információkat a **Customers (ügyfelek** ) szakaszban, hacsak nem telepítette az erőforrásokat az Azure-beli [delegált erőforrás-kezeléshez](../concepts/azure-delegated-resource-management.md).

A lap alsó részén, a **Cloud Solution Provider (előzetes verzió)** nevű külön szakasz a számlázási adatokat és erőforrásokat jeleníti meg a CSP azon ügyfelei számára, akik [aláírták a Microsoft Customer szerződést (MCA)](/partner-center/confirm-customer-agreement) , és [Az Azure-csomag alatt](/partner-center/azure-plan-get-started)találhatók. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](../../billing/mpa-overview.md). Vegye figyelembe, hogy az ilyen CSP-ügyfelek ebben a szakaszban jelennek meg, függetlenül attól, hogy az Azure-beli delegált erőforrás-kezeléshez is felvette őket. Hasonlóképpen, a CSP-ügyfélnek nem kell megjelennie az **ügyfelek** **felhőalapú megoldás-szolgáltató (előzetes verzió)** szakaszában, hogy az Azure-beli delegált erőforrás-kezeléshez be lehessen őket készíteni.

> [!NOTE]
> Az ügyfelek megtekinthetik a szolgáltatók adatait úgy, hogy a Azure Portalban **lévő szolgáltatókra** navigálnak. További információ: szolgáltatók [megtekintése és kezelése](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Ügyfél adatainak megtekintése és kezelése

Az ügyfél részleteinek megtekintéséhez válassza a **Customers** (ügyfelek) lehetőséget a **Customers (ügyfelek)** oldal bal oldalán.

Minden ügyfél esetében látni fogja az ügyfél nevét, az ügyfél AZONOSÍTÓját (bérlői AZONOSÍTÓját), valamint az engagement szolgáltatáshoz tartozó ajánlatot. A **delegálások** oszlopban látható a delegált előfizetések száma és/vagy a delegált erőforráscsoportok száma.

> [!IMPORTANT]
> A delegálás megtekintéséhez a felhasználóknak meg kell adni az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz) a bevezetési folyamat során.

Az oldal tetején lévő szűrők segítségével rendezheti és csoportosíthatja az ügyfél adatait, vagy szűrheti az ügyfelek, ajánlatok vagy kulcsszavak alapján.

Az alábbi adatokat tekintheti meg erről az oldalról:

- Ha meg szeretné tekinteni az ügyfélhez társított összes előfizetést, ajánlatot és delegálást, válassza ki az ügyfél nevét.
- Ha további részleteket szeretne látni egy ajánlatról és annak delegálásáról, válassza az ajánlat nevét.
- A delegált előfizetések vagy erőforráscsoportok szerepkör-hozzárendeléseivel kapcsolatos további részletek megtekintéséhez válassza ki a bejegyzést a **delegálások** oszlopban.

## <a name="view-and-manage-delegations"></a>Delegálások megtekintése és kezelése

A delegálások a delegált előfizetést/erőforráscsoportot, valamint a hozzájuk tartozó felhasználókat és engedélyeket jelenítik meg. Az adatok megtekintéséhez válassza a **saját ügyfelek** lap bal oldalán található **delegálások** lehetőséget.

A lap tetején található szűrők segítségével rendezheti és csoportosíthatja a hozzáférés-hozzárendelési adatokat, illetve szűrheti az egyes ügyfelek, ajánlatok vagy kulcsszavak alapján.

### <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

Az egyes delegálásokhoz társított felhasználók és engedélyek a szerepkör- **hozzárendelések** oszlopban jelennek meg. Az egyes bejegyzéseket kiválasztva megtekintheti azoknak a felhasználóknak, csoportoknak és egyszerű szolgáltatásoknak a teljes listáját, amelyek hozzáférést kaptak az előfizetéshez vagy az erőforráscsoporthoz. Az adott felhasználó, csoport vagy egyszerű szolgáltatásnév kiválasztásával további részleteket tudhat meg.

### <a name="remove-delegations"></a>Delegálások eltávolítása

Ha a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörrel](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) rendelkező felhasználók számára felvette az ügyfelet az Azure-beli delegált erőforrás-kezeléshez, a felhasználók eltávolíthatnak egy delegálást úgy, hogy kiválasztja az adott delegálás sorában megjelenő Kuka ikont. Ha igen, a szolgáltató bérlője egyik felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.

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
