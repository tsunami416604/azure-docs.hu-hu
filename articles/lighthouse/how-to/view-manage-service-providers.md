---
title: Szolgáltatók megtekintése és kezelése
description: Az ügyfelek az Azure Portal On-Szolgáltatók lapján megtekinthetik a szolgáltatókkal, a szolgáltatóajánlatokkal és a delegált erőforrásokkal kapcsolatos információkat.
ms.date: 02/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94103c293ffa7ccfb9d7da0a237dc1b1c6540b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270627"
---
# <a name="view-and-manage-service-providers"></a>Szolgáltatók megtekintése és kezelése

Az ügyfelek az [Azure Portalon](https://portal.azure.com) található **Szolgáltatók** lapon megtekinthetik a szolgáltatókkal és a szolgáltatói ajánlatokkal kapcsolatos információkat, delegálhatnak bizonyos erőforrásokat az [Azure delegált erőforrás-kezelésén](../concepts/azure-delegated-resource-management.md)keresztül, és vásárolhatnak új szolgáltatói ajánlatokat. Míg itt a szolgáltatókra és az ügyfelekre hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a felügyeleti élmény ük konszolidálására.

Az Azure **Portalon** a Szolgáltatók lap eléréséhez az ügyfél kiválaszthatja a **Minden szolgáltatást**, majd kereshet a szolgáltatók **at,** és kiválaszthatja azt. Azt is megtalálhatja, ha beírja a "Szolgáltatók" a keresőmezőbe az Azure Portal tetején található.

Ne feledje, hogy a **Szolgáltatók** lap csak azokat a szolgáltatókat jeleníti meg, amelyek az Azure delegált erőforrás-kezelésén keresztül hozzáférnek az ügyfél előfizetéseihez vagy erőforráscsoportjaihoz. Ha egy ügyfél olyan további szolgáltatókkal működik együtt, akik nem használják az Azure delegált erőforrás-kezelését az ügyfél erőforrásainak eléréséhez, az ilyen szolgáltatókra vonatkozó információk itt nem jelennek meg.

> [!NOTE]
> A szolgáltatók az Azure Portalon az **Ügyfeleimmel** való navigálással tekinthetik meg az ügyfeleik adatait. További információt az [Ügyfelek és a delegált erőforrások megtekintése és kezelése](view-manage-customers.md)témakörben talál.

## <a name="view-service-provider-details"></a>A szolgáltató részleteinek megtekintése

A szolgáltatókkal kapcsolatos információk megtekintéséhez az ügyfél kiválaszthatja a **Szolgáltatóajánlatok lehetőséget** a **Szolgáltatók** lap bal oldalán.

Az ügyfél minden egyes szolgáltatóajánlatesetében látni fogja a szolgáltató nevét és a hozzá társított ajánlatot, valamint azt a nevet, amelyet az ügyfél a bevezetési folyamat során megadott.

A **Delegálások** oszlopban az ügyfél azt látja, hogy hány előfizetést és/vagy erőforráscsoportot delegáltak a szolgáltatónak az adott ajánlathoz. A szolgáltató az ajánlatban megadott hozzáférési szinteknek megfelelően elérheti és kezelheti ezeket az előfizetéseket és/vagy erőforráscsoportokat.

## <a name="delegate-resources"></a>Erőforrások delegálása

Ahhoz, hogy a szolgáltató elérhesse és kezelhesse az ügyfél erőforrásait, delegálni kell őket. Ha egy ügyfél elfogadott egy ajánlatot, de még nem delegált erőforrásokat, akkor megjelenik egy megjegyzés a **Szolgáltató ajánlatok** szakasz tetején. Ez tudatja az ügyféllel, hogy lépéseket kell tennie ahhoz, hogy a szolgáltató hozzáférhessen az ügyfél erőforrásaihoz.

Előfizetések vagy erőforráscsoportok delegálása:

1. Jelölje be a szolgáltatót, az ajánlatot és a nevet tartalmazó sor jelölőnégyzetét. Ezután válassza **az Erőforrások delegálása** lehetőséget a képernyő tetején.
1. A **Delegált erőforrások** lap **Ajánlat részletei** szakaszában tekintse át a szolgáltató és az ajánlat részleteit. Az ajánlat szerepkör-hozzárendeléseinek megtekintéséhez **kattintson ide a kiválasztott ajánlat részleteinek megtekintéséhez.**
1. A **Meghatalmazott** csoportban válassza az **Előfizetések delegálása** vagy **az Erőforráscsoportok delegálása**lehetőséget.
1. Válassza ki azajánlathoz delegálni kívánt előfizetéseket és/vagy erőforráscsoportokat, majd válassza **a Hozzáadás lehetőséget.**
1. Jelölje be a lap alján található jelölőnégyzetet, és ellenőrizze, hogy hozzáférést kíván adni a szolgáltatónak a kijelölt erőforrásokhoz, majd válassza a **Meghatalmazott**lehetőséget.

## <a name="add-or-remove-service-provider-offers"></a>Szolgáltatói ajánlatok hozzáadása és eltávolítása

Az ügyfél új szolgáltatói ajánlatot adhat hozzá a **Szolgáltató ajánlatai** oldalról az **Ajánlat hozzáadása**lehetőséget választva. A szolgáltatónak közzé kell tennie egy ajánlatot az ügyfél számára. Az ügyfél ezután kiválaszthatja az ajánlatot a **Privát ajánlatok** képernyőn, majd a **Létrehozás**lehetőséget.

Ha az ügyfél el szeretne távolítani egy szolgáltatói ajánlatot, kiválaszthatja a kuka ikont az adott ajánlat sorában. A törlés megerősítése után a szolgáltató a továbbiakban nem fér hozzá az ajánlathoz korábban delegált ügyfél-erőforrásokhoz.

## <a name="update-service-provider-offers"></a>Szolgáltatói ajánlatok frissítése

Miután egy ügyfél hozzáadott egy ajánlatot, a szolgáltató közzéteheti ugyanannak az ajánlatnak egy frissített verzióját az Azure Marketplace-en. Előfordulhat például, hogy új szerepkör-definíciót szeretnének hozzáadni. Ha az ajánlat új verzióját közzétették, a **Szolgáltató által kínált** oldal egy "update" ikont jelenít meg az ajánlat sorában. Az ügyfél ezt az ikont az ajánlat aktuális verziója és az új verzió közötti különbségek megtekintéséhez választhatja ki.

 ![Ajánlat frissítése ikon](../media/update-offer.jpg)

A módosítások áttekintése után az ügyfél dönthet úgy, hogy az új verzióra frissít. Ezt követően az új verzióban megadott engedélyek és egyéb beállítások az adott ajánlathoz delegált előfizetésekre és/vagy erőforráscsoportokra vonatkoznak.

## <a name="view-delegations"></a>Delegálások megtekintése

A delegálások azt a szerepkör-hozzárendelést jelölik, amely engedélyeket ad a szolgáltatónak az ügyfél által delegált erőforrásokra vonatkozóan. Az adatok megtekintéséhez válassza a Szolgáltatók **lap** bal oldalán található **Delegálások** lehetőséget.

A lap tetején található szűrőkkel rendezheti és csoportosíthatja a delegálási adatokat. Szűrhet adott ügyfelek, ajánlatok vagy kulcsszavak szerint is.

> [!NOTE]
> Az ügyfelek nem fogják látni ezeket a szerepkör-hozzárendeléseket, vagy a szolgáltató bérlői nek bármely felhasználóját, akik megkapta ezeket a szerepköröket, amikor az Azure Portalon vagy API-kon keresztül [tekinti meg a delegált hatókör szerepkör-hozzárendelési adatait.](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope)

## <a name="audit-delegations-in-your-environment"></a>Delegálások naplózása a környezetben

Előfordulhat, hogy az ügyfelek betekintést szeretnének nyerni az [Azure delegált erőforrás-kezeléséhez](../concepts/azure-delegated-resource-management.md)a szolgáltatóknak delegált előfizetésekés/vagy erőforráscsoportokba. Ez különösen hasznos azoknak az ügyfeleknek, akik nagy számú előfizetéssel rendelkeznek, vagy akiknek sok olyan felhasználója van, akik felügyeleti feladatokat látnak el.

Az [Azure Policy beépített szabályzat-definícióját](../../governance/policy/samples/built-in-policies.md#lighthouse) biztosítjuk a hatókörök egy kezelő bérlőnek való delegálásának naplózásához. Ezt a házirendet hozzárendelheti egy felügyeleti csoporthoz, amely tartalmazza a naplózni kívánt összes előfizetést. Ha ellenőrzi, hogy megfelel-e ennek a szabályzatnak, a delegált előfizetések és/vagy erőforráscsoportok (azon a felügyeleti csoporton belül, amelyhez a szabályzat hozzá van rendelve) nem megfelelő állapotban jelennek meg. Ezután áttekintheti az eredményeket, és meggyőződhet arról, hogy nincsenek váratlan delegálások.

A házirendek hozzárendeléséről és a megfelelőségi állapoteredményeinek megtekintéséről a [Rövid útmutató: Házirend-hozzárendelés létrehozása](../../governance/policy/assign-policy-portal.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
 
- További információ az [Azure Világítótoronyról.](../overview.md)
- Megtudhatja, hogy a szolgáltatók hogyan [tekinthetik meg és kezelhetik az ügyfeleket](view-manage-customers.md) az Azure Portalon az **Ügyfeleimmel.**