---
title: Útmutató Exchange-társviszony létesítéséhez
titleSuffix: Azure
description: Útmutató Exchange-társviszony létesítéséhez
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ec83778d034cfc512582eddf79995412dad405c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710728"
---
# <a name="exchange-peering-walkthrough"></a>Útmutató Exchange-társviszony létesítéséhez

Ez a szakasz azokat a lépéseket ismerteti, amelyeket követnie kell az Exchange-társak beállításához és kezeléséhez.

## <a name="create-an-exchange-peering"></a>Exchange-társ létrehozása
> [!div class="mx-imgBorder"]
> ![Exchange-partneri munkafolyamatok és kapcsolatok állapota](./media/exchange-peering.png)

Exchange-társítás létesítéséhez a következő lépéseket kell követni:
1. Tekintse át a Microsoft- [partneri házirendet](https://peering.azurewebsites.net/peering) az Exchange-társítás követelményeinek megismeréséhez.
1. A Microsoft peering Location és a peering Facility azonosítójának megkeresése a [PeeringDB](https://www.peeringdb.com/net/694) -ben
1. A további részleteket az Exchange [-társítás létrehozása és módosítása a PowerShell használatával](howto-exchange-powershell.md) című cikk utasításait követve kérheti le az Exchange-társítást egy egyenrangú helyhez.
1. Miután elküldte a kérést, a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően a kapcsolatok állapota jóváhagyva értékre változik
1. A BGP-munkamenet konfigurálása a végén és a Microsoft értesítése
1. BGP-munkamenetet fogunk kiépíteni az összes házirend megtagadásával és a teljes körű ellenőrzéssel.
1. Ha a művelet sikeres, értesítést kap arról, hogy a társ-összekapcsolási állapot aktív.
1. A forgalom ezután az új társításon keresztül engedélyezett lesz.

Vegye figyelembe, hogy a kapcsolati állapotok nem tévesztendő össze a standard [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) munkamenet-állapotokkal.

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társviszony konvertálása Azure-erőforrássá
A következő lépéseket kell követnie egy örökölt Exchange-társítás Azure-erőforrásra való átalakításához:
1. Kövesse az [örökölt Exchange-társítás átalakítása az Azure-erőforrásra](howto-legacy-exchange-powershell.md) című témakör utasításait.
1. Az átalakítási kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően az Exchange-társat aktívként fogja látni a kapcsolatok állapotával.

## <a name="deprovision-exchange-peering"></a>Exchange-társítás megszüntetése
Az Exchange-társítás megszüntetéséhez forduljon a [Microsoft-partnerekhez](mailto:peering@microsoft.com) .

Ha egy Exchange-társítás kiépítésre van beállítva, a kapcsolódási állapotot **PendingRemove** -ként fogja látni.

> [!NOTE]
> Ha a PowerShell-parancsmag futtatásával törli az Exchange-társat a kapcsolódási állapot ProvisioningStarted vagy ProvisioningCompleted, a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a Microsofttal való összevonással kapcsolatos [előfeltételekkel](prerequisites.md).
