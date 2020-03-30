---
title: Útmutató Exchange-társviszony létesítéséhez
titleSuffix: Azure
description: Útmutató Exchange-társviszony létesítéséhez
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: bb761afa6d8953b441b6c9541c43b73031719494
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775172"
---
# <a name="exchange-peering-walkthrough"></a>Útmutató Exchange-társviszony létesítéséhez

Ez a szakasz az Exchange-társviszony-létesítés beállításához és kezeléséhez követendő lépéseket ismerteti.

## <a name="create-an-exchange-peering"></a>Exchange-társviszony-létesítés létrehozása
> [!div class="mx-imgBorder"]
> ![Exchange-társviszony-létesítési munkafolyamat és kapcsolatállapotok](./media/exchange-peering.png)

Az Exchange-társviszony-létesítés kiépítése érdekében a következő lépéseket kell követni:
1. Tekintse át a Microsoft [társviszony-létesítési szabályzatát](https://peering.azurewebsites.net/peering) az Exchange-társviszony-létesítés követelményeinek megértéséhez.
1. A Microsoft társviszony-létesítési helyének és társviszony-létesítési lehetőségének azonosítójának megkeresése a [Társviszony-létesítési formában](https://www.peeringdb.com/net/694)
1. Kérjen Exchange-társviszony-létesítési egy társviszony-létesítési hely utasításával [létrehozása és módosítása exchange-társviszony-létesítés a PowerShell-cikk használatával](howto-exchange-powershell.md) további részletekért.
1. A társtársi létesítési kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően a kapcsolat állapota jóváhagyottra változik
1. BGP-munkamenet konfigurálása a végén, és értesítés a Microsoftról
1. A BGP-munkamenetet DENY ALL házirenddel építjük ki, és végpontok között érvényesítjük.
1. Ha sikeres, értesítést fog kapni arról, hogy a társviszony-létesítési kapcsolat állapota aktív.
1. A forgalom ezután az új társviszony-létesítésen keresztül engedélyezett.

Ne feledje, hogy a kapcsolatállapotokat nem szabad összetéveszteni a szabványos BGP-munkamenet-állapotokkal. [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társviszony konvertálása Azure-erőforrássá
Az örökölt Exchange-társviszony-létesítés Azure-erőforrássá alakításához a következő lépéseket kell követni:
1. Kövesse az [örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá című útmutató utasításait.](howto-legacy-exchange-powershell.md)
1. A konverziós kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően az Exchange-társviszony-létesítési kapcsolatállapottal aktív lesz.

## <a name="deprovision-exchange-peering"></a>Exchange-társviszony-létesítésen való megszüntetés
Lépjen kapcsolatba [a Microsoft társviszony-létesítésével](mailto:peering@microsoft.com) az Exchange társviszony-létesítésnek megszüntetéséhez.

Ha egy Exchange-társviszony-létesítés megszüntetésre van beállítva, a kapcsolat állapota **PendingRemove (Függőeltávolítás) lesz.**

> [!NOTE]
> Ha powershell-parancsmag futtatásával törli az Exchange-társviszony-létesítési állapot kiépítéselindult vagy provisioningCompleted a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

* Ismerje meg [a Microsofttal való társviszony-létesítés beállításának előfeltételeit.](prerequisites.md)
