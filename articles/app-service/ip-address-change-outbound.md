---
title: Felkészülés a kimenő IP-címváltozásra
description: Ha a kimenő IP-cím meg fog változni, olvassa el, hogy mi a teendő, hogy az alkalmazás a módosítás után is működjön.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671670"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Felkészülés a kimenő IP-címváltozásra?

Ha értesítést kapott arról, hogy az Azure App Service-alkalmazás kimenő IP-címei változnak, kövesse a jelen cikkben található utasításokat.

## <a name="determine-if-you-have-to-do-anything"></a>Határozza meg, hogy tennie kell-e valamit

* 1. lehetőség: Ha az App Service-alkalmazás nem használ IP-szűrést, explicit integrációs listát vagy a kimenő forgalom speciális kezelését, például az útválasztást vagy a tűzfalat, nincs szükség műveletre.

* 2. lehetőség: Ha az alkalmazás speciális kezeléssel rendelkezik a kimenő IP-címekhez (lásd az alábbi példákat), adja hozzá az új kimenő IP-címeket, bárhol is jelennek meg a meglévők. Ne cserélje le a meglévő IP-címeket. Az új kimenő IP-címeket a következő szakaszutasításait követve találhatja meg.

  Előfordulhat például, hogy egy kimenő IP-cím kifejezetten szerepel az alkalmazáson kívüli tűzfalon, vagy egy külső fizetési szolgáltatás rendelkezik egy engedélyezett listával, amely tartalmazza az alkalmazás kimenő IP-címét. Ha a kimenő cím az alkalmazáson kívül bárhol van beállítva egy listában, azt módosítani kell.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>A kimenő IP-címek megkeresése az Azure Portalon

Az új kimenő IP-címek azok érvénybe lépése előtt megjelennek a portálon. Amikor az Azure elkezdi használni az újakat, a régiek már nem lesznek használva. A program egyszerre csak egy készletet használ, így a felvétellisták bejegyzéseinek régi és új IP-címekkel is rendelkezniük kell, hogy megakadályozzák a kimaradást a kapcsoló bekövetkeztekekor. 

1.  Nyissa meg az [Azure Portalt](https://portal.azure.com).

2.  A bal oldali navigációs menüben válassza az **App Services**lehetőséget.

3.  Válassza ki az App Service-alkalmazást a listából.

1.  Ha az alkalmazás függvényalkalmazás, olvassa el [a Függvényalkalmazás kimenő IP-címeit.](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)

4.  A **Beállítások** fejléc alatt kattintson a bal oldali navigációs sáv **Tulajdonságok gombjára,** és keresse meg a **Kimenő IP-címek**feliratú szakaszt.

5. Másolja az IP-címeket, és adja hozzá őket a kimenő forgalom speciális kezeléséhez, például egy szűrőhöz vagy az engedélyezett listához. Ne törölje a meglévő IP-címeket a listában.

## <a name="next-steps"></a>További lépések

Ez a cikk ismerteti, hogyan készülhet fel az Azure által kezdeményezett IP-címmódosításra. Az Azure App Service IP-címeiről további információt a [Bejövő és kimenő IP-címek az Azure App Service-ben című témakörben talál.](overview-inbound-outbound-ips.md)
