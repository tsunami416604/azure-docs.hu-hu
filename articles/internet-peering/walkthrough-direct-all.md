---
title: Útmutató a közvetlen társviszony-létesítéshez
titleSuffix: Azure
description: Útmutató a közvetlen társviszony-létesítéshez
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e27fa26514d27d68aecdf9e28b36e2747dc8ffe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84710762"
---
# <a name="direct-peering-walkthrough"></a>Útmutató a közvetlen társviszony-létesítéshez

Ez a szakasz azokat a lépéseket ismerteti, amelyeket követnie kell a közvetlen társítás beállításához és kezeléséhez.

## <a name="create-a-direct-peering"></a>Közvetlen társ létrehozása
> [!div class="mx-imgBorder"]
> ![Közvetlen társ-munkafolyamatok és kapcsolatok állapota](./media/direct-peering.png)

Közvetlen társítás létesítéséhez a következő lépéseket kell követni:
1. A közvetlen társításra vonatkozó követelmények megismeréséhez tekintse át a Microsoft- [partneri házirendet](https://peering.azurewebsites.net/peering) .
1. A társítási kérelem elküldéséhez kövesse a [közvetlen társ létrehozása vagy módosítása](howto-direct-powershell.md) című témakör utasításait.
1. A kérések elküldése után a Microsoft felveszi Önnel a kapcsolatot a regisztrált e-mail-címével, hogy az LOA (engedélyezési engedély) vagy egyéb információkhoz adja meg.
1. Miután a rendszer jóváhagyta a kérést, a kapcsolódási állapot ProvisioningStarted változik.
1. A következőket kell tennie:
    1. teljes huzalozás az LOA szerint
    1. (opcionálisan) a 169.254.0.0/16 használatával végezze el a kapcsolat tesztelését
    1. konfigurálja a BGP-munkamenetet, majd értesítsen minket.
1. A Microsoft az összes szabályzat megtagadásával és teljes körű ellenőrzésével BGP-munkamenetet tesz elérhetővé.
1. Ha a művelet sikeres, értesítést kap arról, hogy a társ-összekapcsolási állapot aktív.
1. A forgalom ezután az új társításon keresztül engedélyezett lesz.

Vegye figyelembe, hogy a kapcsolati állapotok nem tévesztendő össze a standard [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) munkamenet-állapotokkal.

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társviszony konvertálása Azure-erőforrássá
A következő lépéseket kell követnie, hogy egy örökölt közvetlen társat Konvertáljon az Azure-erőforrásra:
1. Kövesse az [örökölt közvetlen társítás átalakítása az Azure-erőforrásra](howto-legacy-direct-powershell.md) című témakör utasításait.
1. Az átalakítási kérelem elküldése után a Microsoft áttekinti a kérést, és szükség esetén felveszi Önnel a kapcsolatot.
1. A jóváhagyást követően a közvetlen kapcsolatot a rendszer aktív állapottal látja el.

## <a name="deprovision-direct-peering"></a>Közvetlen kiépítés megszüntetése
Vegye fel a kapcsolatot a [Microsoft társközi](mailto:peering@microsoft.com) csapatával a közvetlen társítás megszüntetéséhez.

Ha egy közvetlen társítás van beállítva a megszüntetéshez, a **PendingRemove** fogja látni a kapcsolódási állapotot.

> [!NOTE]
> Ha a PowerShell-parancsmagot futtatja a közvetlen társítás törléséhez, amikor a ConnectionState ProvisioningStarted vagy ProvisioningCompleted, a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a Microsofttal való összevonással kapcsolatos [előfeltételekkel](prerequisites.md).
