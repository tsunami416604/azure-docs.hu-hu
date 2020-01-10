---
title: Exchange-társítási útmutató
titleSuffix: Azure
description: Exchange-társítási útmutató
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775172"
---
# <a name="exchange-peering-walkthrough"></a>Exchange-társítási útmutató

Ez a szakasz azokat a lépéseket ismerteti, amelyeket követnie kell az Exchange-társak beállításához és kezeléséhez.

## <a name="create-an-exchange-peering"></a>Exchange-társ létrehozása
> [!div class="mx-imgBorder"]
> ![Exchange-társi munkafolyamatok és a kapcsolatok állapota](./media/exchange-peering.png)

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

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társ átalakítása az Azure-erőforrásra
A következő lépéseket kell követnie egy örökölt Exchange-társítás Azure-erőforrásra való átalakításához:
1. Kövesse az [örökölt Exchange-társítás átalakítása az Azure-erőforrásra](howto-legacy-exchange-powershell.md) című témakör utasításait.
1. Az átalakítási kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően az Exchange-társat aktívként fogja látni a kapcsolatok állapotával.

## <a name="deprovision-exchange-peering"></a>Exchange-társítás megszüntetése
Az Exchange-társítás megszüntetéséhez forduljon a [Microsoft-partnerekhez](mailto:peering@microsoft.com) .

Ha egy Exchange-társítás kiépítésre van beállítva, a kapcsolódási állapotot **PendingRemove** -ként fogja látni.

> [!NOTE]
> Ha a PowerShell-parancsmag futtatásával törli az Exchange-társat a kapcsolódási állapot ProvisioningStarted vagy ProvisioningCompleted, a művelet sikertelen lesz.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a Microsofttal való összevonással kapcsolatos [előfeltételekkel](prerequisites.md).
