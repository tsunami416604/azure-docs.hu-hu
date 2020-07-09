---
title: Szolgáltatók megtekintése és kezelése
description: Az ügyfelek a Azure Portal szolgáltatók lapján tekinthetik meg a szolgáltatók, a szolgáltatói ajánlatok és a delegált erőforrások adatait.
ms.date: 07/07/2020
ms.topic: how-to
ms.openlocfilehash: 56ba5b8c88cd4653c9d41f7f3fcc6219cc376256
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111626"
---
# <a name="view-and-manage-service-providers"></a>Szolgáltatók megtekintése és kezelése

Az ügyfelek a Azure Portal **szolgáltatók** lapján tekinthetik meg [Azure portal](https://portal.azure.com) a szolgáltatók és a szolgáltatói ajánlatok részleteit, adott erőforrások delegálását az [Azure Lighthouse](../overview.md)szolgáltatásba, és az új szolgáltatói ajánlatokat is vásárolhatnak. Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a több bérlőt kezelő vállalatok ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

A Azure Portal **szolgáltatók** lapjának eléréséhez az ügyfél kiválaszthatja az **összes szolgáltatást**, majd kereshet **a szolgáltatók között, és kiválaszthatja** azt. Azt is megtalálják, hogy beírja a "szolgáltatók" vagy az "Azure Lighthouse" kifejezést a Azure Portal tetején található keresőmezőbe.

> [!NOTE]
> **A szolgáltatók lap megtekintéséhez** az ügyfél bérlője számára az [olvasó beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#reader) kell rendelkeznie (vagy egy másik beépített szerepkör, amely olvasói hozzáférést is tartalmaz).
>
> Ajánlatok hozzáadásához, erőforrások delegálásához és az ajánlatok eltávolításához a felhasználónak a [tulajdonos beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#owner) kell rendelkeznie az előfizetéshez.

Ne feledje, hogy a **szolgáltatók lap csak** azokat a szolgáltatói információkat jeleníti meg, amelyek az ügyfél előfizetéseit vagy erőforráscsoportait az Azure Lighthouse használatával érik el. Ha az ügyfél olyan további szolgáltatókkal működik együtt, akik nem használják az Azure Lighthouse-t az ügyfél erőforrásainak eléréséhez, itt nem jelennek meg a szolgáltatók információi.

> [!TIP]
> A szolgáltatók megtekinthetik az ügyfelekkel kapcsolatos információkat, ha a Azure Portalban lévő **ügyfelekre** navigálnak. További információ: [ügyfelek és delegált erőforrások megtekintése és kezelése](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Szolgáltató adatainak megtekintése

A szolgáltatók részleteinek megtekintéséhez az ügyfél kiválaszthatja a szolgáltatói **ajánlatokat** a **szolgáltatók lap bal** oldalán.

Az ügyfél minden szolgáltatói ajánlatnál látni fogja a szolgáltató nevét és a hozzá társított ajánlatot, valamint azt a nevet, amelyet az ügyfél a bevezetési folyamat során megadott.

A **delegálások** oszlopban az ügyfél azt látja, hogy hány előfizetés és/vagy erőforráscsoport van delegálva az ajánlat szolgáltatójának. A szolgáltató elérheti és kezelheti ezeket az előfizetéseket és/vagy erőforráscsoportokat az ajánlatban megadott hozzáférési szintnek megfelelően.

## <a name="add-or-remove-service-provider-offers"></a>Szolgáltatói ajánlatok hozzáadása vagy eltávolítása

Egy ügyfél hozzáadhat egy új szolgáltatói ajánlatot a szolgáltatói **ajánlatok** oldaláról az **ajánlat hozzáadása**lehetőség kiválasztásával. A szolgáltatónak közzé kell tennie egy ajánlatot ehhez az ügyfélhez. Az ügyfél ezt követően kiválaszthatja az ajánlatot a **privát ajánlatok** képernyőjén, majd a **Létrehozás**lehetőséget is választhatja.

Ha az ügyfél el szeretné távolítani a szolgáltatói ajánlatot, kiválaszthatja az ajánlat sorában látható Kuka ikont. A Törlés megerősítése után a szolgáltató már nem fog tudni hozzáférni az adott ajánlathoz korábban delegált ügyfél-erőforrásokhoz.

## <a name="delegate-resources"></a>Erőforrások delegálása

Ahhoz, hogy a szolgáltató hozzáférhessen egy ügyfél erőforrásaihoz és felügyelje azt, delegálásra van szükség. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a **szolgáltató ajánlatok** szakaszának tetején egy megjegyzés jelenik meg. Ez lehetővé teszi, hogy az ügyfél tudja, hogy el kell végeznie a beavatkozást, mielőtt a szolgáltató hozzáférhet az ügyfél erőforrásaihoz.

Előfizetések vagy erőforráscsoportok delegálása:

1. Jelölje be a szolgáltatót, az ajánlatot és a nevet tartalmazó sor jelölőnégyzetét. Ezután válassza az **erőforrások delegálása** lehetőséget a képernyő tetején.
1. Tekintse át a szolgáltató és az ajánlat részleteit az **erőforrások delegálása** lap **ajánlat részletei** szakaszában. Az ajánlathoz tartozó szerepkör-hozzárendelések áttekintéséhez válassza a **kattintson ide a kiválasztott ajánlat részleteinek megtekintéséhez**.
1. A **delegált** szakaszban válassza az **előfizetések delegálása** vagy az **erőforráscsoportok delegálása**lehetőséget.
1. Válassza ki az ajánlathoz delegálni kívánt előfizetéseket és/vagy erőforráscsoportokat, majd válassza a **Hozzáadás**lehetőséget.
1. Jelölje be a lap alján található jelölőnégyzetet annak megerősítéséhez, hogy a szolgáltató hozzáférést kíván adni a kiválasztott erőforrásokhoz, majd válassza a **delegálás**lehetőséget.

## <a name="update-service-provider-offers"></a>A szolgáltatói ajánlatok frissítése

Miután egy ügyfél hozzáadott egy ajánlatot, a szolgáltató közzéteheti az Azure Marketplace-re vonatkozó ajánlat frissített verzióját. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni. Ha közzétette az ajánlat új verzióját, a szolgáltató által biztosított **ajánlatok** lapon megjelenik az ajánlat sorában látható "Update" ikon. Az ügyfél kiválaszthatja ezt az ikont az ajánlat aktuális verziója és az új verzió közötti különbségek megtekintéséhez.

 ![Ajánlat frissítése ikon](../media/update-offer.jpg)

A módosítások áttekintése után az ügyfél dönthet úgy, hogy frissíti az új verzióra. Ha ezt megtette, az új verzióban megadott engedélyek és egyéb beállítások az adott ajánlathoz delegált előfizetésekre és/vagy erőforráscsoportok érvényesek lesznek.

## <a name="view-delegations"></a>Delegálások megtekintése

A delegálások azokat a szerepkör-hozzárendeléseket jelölik, amelyek engedélyeket biztosítanak a szolgáltatónak az ügyfelek által delegált erőforrások számára. Az információ megtekintéséhez válassza a **szolgáltatók** lap bal oldalán található **delegálások** lehetőséget.

A lap tetején lévő szűrők lehetővé teszik a delegálási adatok rendezését és csoportosítását. A szűrést meghatározott ügyfelek, ajánlatok vagy kulcsszavak alapján is elvégezheti.

> [!NOTE]
> Az ügyfelek nem látják ezeket a szerepkör-hozzárendeléseket, illetve azon szolgáltatói bérlőtől származó felhasználókat, akik megkapták ezeket a szerepköröket, amikor a Azure Portal vagy API-kon keresztül [megtekintik a delegált hatókörhöz tartozó szerepkör-hozzárendeléseket](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) .

## <a name="audit-delegations-in-your-environment"></a>Delegálások naplózása a környezetben

Előfordulhat, hogy az ügyfelek megtekinthetik az Azure Lighthouse-ra delegált előfizetéseket és/vagy erőforráscsoportokat. Ez különösen hasznos azoknak az ügyfeleknek, akik nagy számú előfizetéssel rendelkeznek, vagy akiknél sok felhasználó végzi a felügyeleti feladatokat.

Egy [Azure Policy beépített szabályzat-definíciót](../../governance/policy/samples/built-in-policies.md#lighthouse) biztosítunk a hatókörök delegálásának naplózásához egy felügyeleti bérlőhöz. Ezt a házirendet hozzárendelheti egy felügyeleti csoporthoz, amely tartalmazza az összes naplózni kívánt előfizetést. Ha bejelöli a szabályzatnak való megfelelést, minden olyan delegált előfizetés és/vagy erőforráscsoport (a felügyeleti csoporton belül, amelyhez a házirend hozzá van rendelve) nem megfelelő állapotban jelenik meg. Ezután ellenőrizheti az eredményeket, és ellenőrizheti, hogy nincsenek-e váratlan delegálások.

A szabályzatok hozzárendeléséről és a megfelelőségi állapot eredményeinek megjelenítéséről a rövid útmutató [: szabályzat-hozzárendelés létrehozása](../../governance/policy/assign-policy-portal.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Lighthouse](../overview.md)-ról.
- Ismerje meg, hogy a szolgáltatók hogyan [tekinthetik meg és kezelhetik az ügyfeleket](view-manage-customers.md) a Azure Portal **saját ügyfelek** lapján.
