---
title: Ügyfelek és az Azure Portalon delegált erőforrások megtekintése és kezelése
description: Az Azure-szolgáltató erőforrás-kezelés meghatalmazott, nyissa meg az Azure Portalon ügyfeleim megtekintheti összes delegált ügyfélerőforrásait és előfizetések.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809854"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ügyfelek és a delegált erőforrások megtekintése és kezelése

Szolgáltatók használatával [Azure erőforrás-kezelés delegált](../concepts/azure-delegated-resource-management.md) használhatja a **ügyfeleim** lapját a [az Azure portal](https://portal.azure.com) delegált ügyfélerőforrásait megtekintéséhez és az előfizetések. Azt a szolgáltatók és az ügyfelek itt fog hivatkozni, miközben kezelése több bérlőnél vállalatok is ugyanazzal az eljárással egyesíthetők a saját felügyeleti kezelőfelület.

Hozzáférés a **ügyfeleim** az Azure Portalon, válassza a lap **minden szolgáltatás**, majd keresse meg az **ügyfeleim** , és jelölje ki. Is megtalálhatja, írja be a "Ügyfeleim" kifejezést a keresőmezőbe az Azure portal tetején.

Vegye figyelembe, hogy a **ügyfeleim** lap csak ügyfelek, akik van delegált előfizetések vagy erőforráscsoportok kapcsolatos információ jelenik meg. Ha Ön működik együtt más ügyfelek számára (például keresztül a [Cloud Solution Provider program](https://docs.microsoft.com/partner-center/csp-overview), kapcsolatos információ itt ezek az ügyfelek nem jelenik meg, kivéve, ha, készítse elő az erőforrásaikat delegált erőforrás-kezelés.

> [!NOTE]
> Az ügyfelek tekintheti meg az szolgáltatók kapcsolatos **szolgáltatók** az Azure Portalon. További információ: [megtekintése és kezelése a szolgáltatók](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Megtekintheti és kezelheti az ügyfelek részletei

Ügyfél részleteinek megtekintéséhez válassza ki a **ügyfelek** bal oldalán a **ügyfeleim** lapot.

Minden egyes ügyfél láthatja az ügyfél nevét, ügyfél-azonosító (bérlő azonosító) és az engagement társított ajánlat. Az a **delegálásokat** oszlopban láthatja az előfizetéseinek száma a delegált és/vagy delegált erőforráscsoportok száma.

Szűrők az oldal tetején lévő lehetővé teszik a rendezés és csoportosítás a customer info vagy szűrő az adott ügyfélnek, ajánlatok vagy kulcsszavak alapján.

Ezen a lapon a következő adatokat tekintheti meg:

- Az összes az előfizetések kínál, és egy ügyfél hozzárendelt delegálásokat válassza ki az ügyfél nevét.
- Az ajánlat és annak delegálásokat kapcsolatos további információk megtekintéséhez válassza ki a ajánlat nevét.
- Acrolecess hozzárendelések delegált előfizetések vagy erőforráscsoportok részleteinek megtekintéséhez jelölje ki a bejegyzést a **delegálásokat** oszlop.

## <a name="view-delegations"></a>Nézet delegálásokat

Delegálásokat megjelenítése az előfizetés/erőforráscsoport delegálva lett, a felhasználók és az engedélyeket, amelyek hozzáféréssel együtt. Ezek az adatok megtekintéséhez jelölje ki **delegálásokat** bal oldalán a **ügyfeleim** lapot.

Szűrők az oldal tetején lévő lehetővé teszik a rendezés és csoportosítás a hozzáférési hozzárendelés információk vagy az adott ügyfélnek, az ajánlatok vagy a kulcsszavak szűrés.

A felhasználók és a társított minden egyes delegálás engedélyek megjelennek a **szerepkör-hozzárendelések** oszlop. Kiválaszthatja, hogy minden bejegyzés a felhasználók, csoportok és kapott hozzáférést az előfizetés vagy az erőforráscsoport egyszerű szolgáltatások teljes listájának megtekintéséhez. Itt kiválaszthatja egy adott felhasználó, csoport vagy az egyszerű szolgáltatásnév további információért.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Delegált előfizetés környezetében működik

Dolgozunk a címtár közötti váltás nélkül használhassa közvetlenül az Azure Portalon, a meghatalmazott előfizetés környezetében dolgozhat. Ehhez tegye a következőket:

1. Válassza ki a **címtár és előfizetés** ikonra az Azure portal tetején.
2. Az a **globális előfizetés** szűréséhez, győződjön meg arról, hogy csak a box, a delegált előfizetés van kiválasztva. Használhatja a **jelenlegi + delegált könyvtárak** csak egy adott címtárhoz belül előfizetések megjelenítése a legördülő listából. (Ne használja a **címtár váltása** lehetőség, mivel, amely módosítja a könyvtárat, amelyhez be van jelentkezve.)

Ha egy szolgáltatás, amely támogatja a majd elérni [több-bérlős felhasználói élmény](../concepts/cross-tenant-management-experience.md), a szolgáltatás alapértelmezés szerint a kiválasztott delegált előfizetés keretében. Ezt módosíthatja, és ellenőrzi a fenti lépések elvégzésével a **válassza ki az összes** be (vagy csapatmunkában vesz részt inkább egy vagy több előfizetés kiválasztásával).

> [!NOTE]
> Ha Ön egy vagy több erőforráscsoport a hozzáférést, nem pedig egy teljes előfizetés hozzáférést kapott, kiválaszthatja az előfizetést, amelyhez az adott erőforráscsoportba tartozó tartozik. Majd használni az adott előfizetés keretében, de csak akkor érhessék el a kijelölt erőforráscsoportot.

Delegált előfizetések vagy erőforráscsoportok a szolgáltatásokban, amelyek támogatják a több-bérlős felhasználói élmény a vagy erőforráscsoportonként csoportban az adott szolgáltatás kiválasztásával kapcsolatos funkciókat is elérheti.

## <a name="next-steps"></a>További lépések

- Ismerje meg [több-bérlős felhasználói élmény](../concepts/cross-tenant-management-experience.md).
- Ismerje meg, hogy az ügyfelek lehet [megtekintése és kezelése a szolgáltatók](view-manage-service-providers.md) a **szolgáltatók** az Azure Portalon.
