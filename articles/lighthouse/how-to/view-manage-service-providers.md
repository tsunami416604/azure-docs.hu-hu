---
title: Megtekintheti és kezelheti a szolgáltatók az Azure Portalon
description: Ügyfelek használhatják a szolgáltatás szolgáltatók oldal az Azure Portalon kapcsolatos információ szolgáltatók, a service provider ajánlatok és a delegált erőforrások megtekintéséhez.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a45458e7417bba058522fdc0dbc34fee04ad9af8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809840"
---
# <a name="view-and-manage-service-providers"></a>Megtekintheti és kezelheti a szolgáltatók

Az ügyfelek használhatják a **szolgáltatók** lapját a [az Azure portal](https://portal.azure.com) szolgáltatók és a service provider ajánlatok információ megtekintéséhez delegálása erőforrásoknál keresztül [Azure az erőforrás-kezelés delegált](../concepts/azure-delegated-resource-management.md), és a kiegészítő service provider, amelyek üzemi. Azt a szolgáltatók és az ügyfelek itt fog hivatkozni, miközben kezelése több bérlőnél vállalatok is ugyanazzal az eljárással egyesíthetők a saját felügyeleti kezelőfelület.

Hozzáférés a **szolgáltatók** oldal az Azure Portalon, az ügyfél kiválaszthatja **minden szolgáltatás**, majd keresse meg az **szolgáltatók** , és jelölje ki. Akkor is megkeresheti azt írja be a "Szolgáltatók" kifejezést a keresőmezőbe, az Azure portal tetején.

Vegye figyelembe, hogy a **szolgáltatók** oldal csak a szolgáltató, amely rendelkezik hozzáféréssel az ügyfél előfizetések vagy erőforráscsoportok Azure-erőforrás delegált felügyelettel kapcsolatos jeleníti meg. Ha egy ügyfél meghatalmazott Azure erőforrás-kezelés nem használják az ügyfél erőforrások eléréséhez további szolgáltatók együttműködik, kapcsolatos információ szolgáltatók itt nem látható.

> [!NOTE]
> Szolgáltatók kapcsolatos információ az ügyfelek tekintheti meg az **ügyfeleim** az Azure Portalon. További információ: [megtekintése és kezelése az ügyfelek és a delegált erőforrások](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Szolgáltatás-szolgáltató részleteinek megtekintése

Az ügyfél működő szolgáltató(k) információ megtekintéséhez, és kiválaszthatja **szolgáltatótól** bal oldalán a **szolgáltatók** lapot.

Az egyes service provider-ajánlat az ügyfél jelenik meg a szolgáltató nevét és az ajánlat nevét, az ügyfél a bevezetési folyamat során megadott rendelhető hozzá.

Az a **delegálásokat** oszlopban, az ügyfél látja, hogy az ajánlat szolgáltatója átadott hány előfizetések és/vagy erőforráscsoportok. A szolgáltató elérheti és kezelheti ezeket az előfizetések és/vagy a hozzáférési szintek az ajánlatban meghatározott erőforráscsoportokra lesz.

## <a name="delegate-resources"></a>Erőforrások delegálása

A szolgáltató eléréséhez és a egy ügyfél-erőforrások kezelése, mielőtt azok kell delegálható. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált olyan erőforrások, egy megjegyzés, amely tetején megjelenik a **szolgáltatótól** szakaszban. Ez lehetővé teszi, hogy intézkedjen a szolgáltató férjenek hozzá az ügyfél-erőforrásokat kell ismernie az ügyfél.

Delegált előfizetések vagy erőforráscsoportok:

1. Jelölje be a nevét, az ajánlat és a szolgáltató tartalmazó sor. Válassza ki **erőforrások delegálása** a képernyő tetején.
1. Az a **ajánlat részletei** szakaszában a **erőforrások delegálása** lapon ellenőrizze a szolgáltató adatait, és kínálnak. Az ajánlat szerepkör-hozzárendelések felülvizsgálatához válassza **Ide kattintva megtekintheti a kiválasztott ajánlat adatait**.
1. Az a **delegált** szakaszban jelölje be **előfizetések delegálása** vagy **erőforráscsoportok delegálása**.
1. Válassza ki, majd válassza az előfizetések és/vagy szeretne delegálni, az ajánlathoz tartozó erőforráscsoportok **Hozzáadás**.
1. Jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy szeretné-e a service provider hozzáférést választotta ki, majd válassza ki az erőforrásokat az oldal alján **delegált**.

## <a name="add-or-remove-service-provider-offers"></a>Adja hozzá, vagy távolítsa el a service provider-ajánlatok

Egy ügyfél adhat hozzá egy új service provider-ajánlat, a **szolgáltatótól** lap választásával **Hozzáadás ajánlat**. A szolgáltató közzé kell tennie egy ajánlatot az ügyfélhez. Az ügyfél kiválaszthatja, hogy ajánlata a **szóló ajánlatok** képernyőn, majd válassza ki **létrehozás**.

Ha az ügyfél szeretné eltávolítani a service provider-ajánlat, és kiválaszthatja a Kuka ikonra a ajánlatra sorában. Miután ellenőrizte a törlés, a szolgáltató többé nem lesz az adott ajánlat, amely korábban meghatalmazott is ügyfél-erőforrásokhoz való hozzáférés.

## <a name="view-delegations"></a>Nézet delegálásokat

Delegálásokat képviseli, amely a hozzáférési jogot a szolgáltató egy ügyfél meghatalmazott rendelkezik az erőforrások a szerepkör-hozzárendeléseket. Ezek az adatok megtekintéséhez jelölje ki **delegálásokat** bal oldalán a **szolgáltatók** lapot.

Szűrők az oldal tetején lévő lehetővé teszik a rendezés és csoportosítás a delegálás info vagy szűrő az adott ügyfélnek, ajánlatok vagy kulcsszavak alapján.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure világítótoronyig](../overview.md).
- Ismerje meg, hogyan lehet a szolgáltatók [megtekintése és kezelése az ügyfelek](view-manage-customers.md) a **ügyfeleim** az Azure Portalon.