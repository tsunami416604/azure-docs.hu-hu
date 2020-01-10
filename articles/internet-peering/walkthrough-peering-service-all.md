---
title: A peering Service partneri útmutatója
titleSuffix: Azure
description: A peering Service partneri útmutatója
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 28aa9f921de64fcc838935cf65b64a63280ddf16
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774626"
---
# <a name="peering-service-partner-walkthrough"></a>A peering Service partneri útmutatója

Ez a szakasz azokat a lépéseket ismerteti, amelyeket a szolgáltatónak követnie kell, hogy lehetővé tegye a közvetlen társítást a társ-kezelő szolgáltatás számára.

## <a name="create-direct-peering-connection-for-peering-service"></a>Közvetlen társ-létrehozási kapcsolatok létrehozása a partneri szolgáltatáshoz
A szolgáltatók kiterjeszthetik földrajzi hatókörét azáltal, hogy olyan új közvetlen társat hoznak létre, amely támogatja a partneri szolgáltatást. Ennek elvégzéséhez
1. Ha még nem tette meg, legyen egy társ-szolgáltatási partner
1. Az utasításokat követve [hozzon létre vagy módosítson egy közvetlen társ-összevonást a portál használatával](howto-direct-portal.md). Győződjön meg arról, hogy megfelel a magas rendelkezésre állási követelményeknek.
1. Ezután kövesse a következő témakört: a társ-kezelés [engedélyezése a portálon keresztüli közvetlen munkatárson](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-conection-for-peering-service"></a>Örökölt közvetlen társítási conection használata a partneri szolgáltatáshoz
Ha rendelkezik olyan örökölt közvetlen társítással, amelyet a partneri szolgáltatás támogatásához kíván használni,
1. Ha még nem tette meg, legyen egy társ-szolgáltatási partner.
1. Kövesse az utasításokat [egy örökölt közvetlen, az Azure-beli erőforrásra való konvertáláshoz a portál használatával](howto-legacy-direct-portal.md). Ha szükséges, rendeljen további áramköröket a magas rendelkezésre állási követelmények teljesítéséhez.
1. Ezután kövesse a következő témakört: a társ-kezelés [engedélyezése a portálon keresztüli közvetlen munkatárson](howto-peering-service-portal.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [peering Policy](https://peering.azurewebsites.net/peering)-ről.
* Ha szeretne többet megtudni a Microsofttal való közvetlen együttműködés beállításához szükséges lépésekről, kövesse a közvetlen összevonási [útmutató](walkthrough-direct-all.md)utasításait.
* A Microsofttal való Exchange-társítás beállításával kapcsolatos lépésekért kövesse az [Exchange-partneri útmutató](walkthrough-exchange-all.md)című témakört.