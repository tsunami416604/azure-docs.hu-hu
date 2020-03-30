---
title: Útmutató a közvetlen társviszony-létesítéshez
titleSuffix: Azure
description: Útmutató a közvetlen társviszony-létesítéshez
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d88fcfc4d3e073bf544f2ca0f4d01dbe305b45da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775510"
---
# <a name="direct-peering-walkthrough"></a>Útmutató a közvetlen társviszony-létesítéshez

Ez a szakasz ismerteti a közvetlen társviszony-létesítés beállításához és kezeléséhez követendő lépéseket.

## <a name="create-a-direct-peering"></a>Közvetlen társviszony-létesítés létrehozása
> [!div class="mx-imgBorder"]
> ![Közvetlen társviszony-létesítési munkafolyamat és kapcsolatállapotok](./media/direct-peering.png)

A közvetlen társviszony-létesítés kiépítése érdekében a következő lépéseket kell követni:
1. Tekintse át a Microsoft [társviszony-létesítési szabályzatát](https://peering.azurewebsites.net/peering) a közvetlen társviszony-létesítés követelményeinek megértéséhez.
1. A társviszony-létesítési kérelem elküldéséhez kövesse a [Közvetlen társviszony-létesítés létrehozása vagy módosítása című, közvetlen társviszony-létesítési](howto-direct-powershell.md) útmutató utasításait.
1. A társtársi kapcsolatfelvételi kérelem elküldése után a Microsoft a regisztrált e-mail-cím használatával felveszi a kapcsolatot a LOA (Letter of Authorization) megadásával vagy egyéb információkért.
1. A társviszony-létesítési kérelem jóváhagyása után a kapcsolat állapota a Kiépítés started lesz.
1. A következőket kell:
    1. teljes kábelezés szerint a LOA
    1. (opcionálisan) hajtson végre kapcsolatvizsgálatot a 169.254.0.0/16
    1. konfigurálja a BGP munkamenetet, majd értesítsen minket.
1. A Microsoft a DENY ALL házirenddel rendelkező BGP-munkamenetet úgy, hogy a végpontok között érvényesítse a bgp-munkamenetet.
1. Ha sikeres, értesítést fog kapni arról, hogy a társviszony-létesítési kapcsolat állapota aktív.
1. A forgalom ezután az új társviszony-létesítésen keresztül engedélyezett.

Ne feledje, hogy a kapcsolatállapotokat nem szabad összetéveszteni a szabványos BGP-munkamenet-állapotokkal. [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társviszony konvertálása Azure-erőforrássá
Az örökölt közvetlen társviszony-létesítés Azure-erőforrássá alakításához a következő lépéseket kell követni:
1. Kövesse az [örökölt közvetlen társviszony-létesítés azure-ba](howto-legacy-direct-powershell.md) című útmutató utasításait.
1. A konverziós kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően a közvetlen társviszony-létesítés aktív kapcsolatállapottal fog látni.

## <a name="deprovision-direct-peering"></a>Közvetlen társviszony-létesítés megszüntetése
Lépjen kapcsolatba [a Microsoft társviszony-létesítési](mailto:peering@microsoft.com) csapatával a közvetlen társviszony-létesítés megszüntetéséhez.

Ha egy közvetlen társviszony-létesítés megszüntetésre van beállítva, a kapcsolat állapota **PendingRemove lesz.**

> [!NOTE]
> Ha a PowerShell-parancsmag futtatásával törli a közvetlen társviszony-létesítés, amikor a ConnectionState kiépítéselindult vagy provisioningCompleted a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

* Ismerje meg [a Microsofttal való társviszony-létesítés beállításának előfeltételeit.](prerequisites.md)
