---
title: Szolgáltatók megtekintése és kezelése
description: Az ügyfelek a Azure Portal szolgáltatók lapján tekinthetik meg a szolgáltatók, a szolgáltatói ajánlatok és a delegált erőforrások adatait.
ms.date: 11/15/2019
ms.topic: conceptual
ms.openlocfilehash: 453f5d3ba4ed5c49b5e71c7530298bc0a03c73ee
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463811"
---
# <a name="view-and-manage-service-providers"></a>Szolgáltatók megtekintése és kezelése

Az ügyfelek a Azure Portal **szolgáltatók** lapján tekinthetik meg [](https://portal.azure.com) a szolgáltatókkal és a szolgáltatói ajánlatokkal kapcsolatos információkat, az Azure-beli [delegált erőforrás-kezeléssel](../concepts/azure-delegated-resource-management.md), valamint a további szolgáltatói ajánlatok vásárlásával. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

A Azure Portal **szolgáltatók** lapjának eléréséhez az ügyfél kiválaszthatja az **összes szolgáltatást**, majd kereshet **a szolgáltatók között, és kiválaszthatja** azt. Azt is megtalálják, hogy beírja a "szolgáltatók" kifejezést a Azure Portal tetején található keresőmezőbe.

Ne feledje, hogy a **szolgáltatók lapon csak** azok az adatok jelennek meg, amelyekkel az ügyfél előfizetéseit vagy erőforráscsoportait az Azure-beli delegált erőforrás-kezelés segítségével érheti el. Ha az ügyfél olyan további szolgáltatókkal működik együtt, akik nem használják az Azure-beli delegált erőforrás-kezelést az ügyfél erőforrásainak eléréséhez, itt nem jelenik meg a szolgáltatók információi.

> [!NOTE]
> A szolgáltatók megtekinthetik ügyfeleik adatait úgy, hogy a Azure Portalban navigálnak az **ügyfelekhez** . További információ: [ügyfelek és delegált erőforrások megtekintése és kezelése](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Szolgáltató adatainak megtekintése

Ha az ügyfél által használt szolgáltató (k) adatait szeretné megtekinteni **, a szolgáltatók oldal bal** oldalán választhat **szolgáltatói ajánlatokat** .

Az ügyfél minden szolgáltatói ajánlatnál látni fogja a szolgáltató nevét és a hozzá társított ajánlatot, valamint azt a nevet, amelyet az ügyfél a bevezetési folyamat során megadott.

A **delegálások** oszlopban az ügyfél azt látja, hogy hány előfizetés és/vagy erőforráscsoport van delegálva az ajánlat szolgáltatójának. A szolgáltató elérheti és kezelheti ezeket az előfizetéseket és/vagy erőforráscsoportokat az ajánlatban megadott hozzáférési szintnek megfelelően.

## <a name="delegate-resources"></a>Erőforrások delegálása

Ahhoz, hogy a szolgáltató hozzáférhessen egy ügyfél erőforrásaihoz és felügyelje azt, delegálásra van szükség. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a **szolgáltatói ajánlatok** szakaszának tetején egy megjegyzés jelenik meg. Ez lehetővé teszi, hogy az ügyfél tudja, hogy el kell végeznie a beavatkozást, mielőtt a szolgáltató hozzáférhet az ügyfél erőforrásaihoz.

Előfizetések vagy erőforráscsoportok delegálása:

1. Jelölje be a szolgáltatót, az ajánlatot és a nevet tartalmazó sor jelölőnégyzetét. Ezután válassza az **erőforrások delegálása** lehetőséget a képernyő tetején.
1. Tekintse át a szolgáltató és az ajánlat részleteit az **erőforrások delegálása** lap **ajánlat részletei** szakaszában. Az ajánlathoz tartozó szerepkör-hozzárendelések áttekintéséhez válassza a **kattintson ide a kiválasztott ajánlat részleteinek megtekintéséhez**.
1. A **delegált** szakaszban válassza az **előfizetések delegálása** vagy az **erőforráscsoportok delegálása**lehetőséget.
1. Válassza ki az ajánlathoz delegálni kívánt előfizetéseket és/vagy erőforráscsoportokat, majd válassza a **Hozzáadás**lehetőséget.
1. Jelölje be a lap alján található jelölőnégyzetet annak megerősítéséhez, hogy a szolgáltató hozzáférést kíván adni a kiválasztott erőforrásokhoz, majd válassza a **delegálás**lehetőséget.

## <a name="add-or-remove-service-provider-offers"></a>Szolgáltatói ajánlatok hozzáadása vagy eltávolítása

Egy ügyfél hozzáadhat egy új szolgáltatói ajánlatot a **szolgáltatói ajánlatok** oldaláról az **ajánlat hozzáadása**lehetőség kiválasztásával. A szolgáltatónak közzé kell tennie egy ajánlatot ehhez az ügyfélhez. Az ügyfél ezt követően kiválaszthatja az ajánlatot a **privát ajánlatok** képernyőjén, majd a **Létrehozás**lehetőséget is választhatja.

Ha az ügyfél el szeretné távolítani a szolgáltatói ajánlatot, kiválaszthatja az ajánlat sorában látható Kuka ikont. A Törlés megerősítése után a szolgáltató már nem fog tudni hozzáférni az adott ajánlathoz korábban delegált ügyfél-erőforrásokhoz.

## <a name="update-service-provider-offers"></a>A szolgáltatói ajánlatok frissítése

Miután egy ügyfél hozzáadott egy ajánlatot, a szolgáltató közzéteheti az Azure Marketplace-re vonatkozó ajánlat frissített verzióját. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni. Ha közzétette az ajánlat új verzióját, a szolgáltató által kínált **ajánlatok** lapon megjelenik az ajánlat sorában látható "frissítés" ikon. Az ügyfél kiválaszthatja ezt az ikont az ajánlat aktuális verziója és az új verzió közötti különbségek megtekintéséhez.

 ![Ajánlat frissítése ikon](../media/update-offer.jpg)

A módosítások áttekintése után az ügyfél dönthet úgy, hogy frissíti az új verzióra. Ha ezt megtette, az új verzióban megadott engedélyek és egyéb beállítások az adott ajánlathoz delegált előfizetésekre és/vagy erőforráscsoportok érvényesek lesznek.

## <a name="view-delegations"></a>Delegálások megtekintése

A delegálások azokat a szerepkör-hozzárendeléseket jelölik, amelyek engedélyeket biztosítanak a szolgáltatónak az ügyfelek által delegált erőforrások számára. Az információ megtekintéséhez válassza a **szolgáltatók** lap bal oldalán található **delegálások** lehetőséget.

Az oldal tetején lévő szűrők segítségével rendezheti és csoportosíthatja a delegálási adatokat, illetve szűrheti az egyes ügyfelek, ajánlatok vagy kulcsszavak alapján.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Lighthouse](../overview.md)-ról.
- Ismerje meg, hogy a szolgáltatók hogyan [tekinthetik meg és kezelhetik az ügyfeleket](view-manage-customers.md) , ha a Azure Portal **ügyfeleit** szeretnék megtekinteni.