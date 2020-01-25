---
title: Útmutató a Peering Service szolgáltatáshoz partnerek számára
titleSuffix: Azure
description: Útmutató a Peering Service szolgáltatáshoz partnerek számára
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d626d98613bd5d988b599d0980c885d7f73bb958
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720248"
---
# <a name="peering-service-partner-walkthrough"></a>Útmutató a Peering Service szolgáltatáshoz partnerek számára

Ez a szakasz azokat a lépéseket ismerteti, amelyeket a szolgáltatónak követnie kell, hogy lehetővé tegye a közvetlen társítást a társ-kezelő szolgáltatás számára.

## <a name="create-direct-peering-connection-for-peering-service"></a>Közvetlen társ-létrehozási kapcsolatok létrehozása a partneri szolgáltatáshoz
A szolgáltatók kiterjeszthetik földrajzi hatókörét azáltal, hogy olyan új közvetlen társat hoznak létre, amely támogatja a partneri szolgáltatást. Ennek elvégzéséhez
1. Ha még nem tette meg, legyen egy társ-szolgáltatási partner
1. Az utasításokat követve [hozzon létre vagy módosítson egy közvetlen társ-összevonást a portál használatával](howto-direct-portal.md). Győződjön meg arról, hogy megfelel a magas rendelkezésre állási követelményeknek.
1. Ezután kövesse a következő témakört: a társ-kezelés [engedélyezése a portálon keresztüli közvetlen munkatárson](howto-peering-service-portal.md).

## <a name="use-legacy-direct-peering-connection-for-peering-service"></a>Örökölt közvetlen társas kapcsolatok használata a társítási szolgáltatáshoz
Ha rendelkezik olyan örökölt közvetlen társítással, amelyet a partneri szolgáltatás támogatásához kíván használni,
1. Ha még nem tette meg, legyen egy társ-szolgáltatási partner.
1. Kövesse az utasításokat [egy örökölt közvetlen, az Azure-beli erőforrásra való konvertáláshoz a portál használatával](howto-legacy-direct-portal.md). Ha szükséges, rendeljen további áramköröket a magas rendelkezésre állási követelmények teljesítéséhez.
1. Ezután kövesse a következő témakört: a társ-kezelés [engedélyezése a portálon keresztüli közvetlen munkatárson](howto-peering-service-portal.md).

## <a name="next-steps"></a>Következő lépések

* További információ a [peering Policy](https://peering.azurewebsites.net/peering)-ről.
* Ha szeretne többet megtudni a Microsofttal való közvetlen együttműködés beállításához szükséges lépésekről, kövesse a közvetlen összevonási [útmutató](walkthrough-direct-all.md)utasításait.
* A Microsofttal való Exchange-társítás beállításával kapcsolatos lépésekért kövesse az [Exchange-partneri útmutató](walkthrough-exchange-all.md)című témakört.