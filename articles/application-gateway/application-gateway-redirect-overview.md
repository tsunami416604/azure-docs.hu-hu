---
title: Azure Application Gateway átirányítás áttekintése |} A Microsoft Docs
description: Az átirányítási funkció az Azure Application Gateway ismertetése
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: d05d509b67fd26c958e0e2fa2bbd877db26e6521
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54232209"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway átirányítás áttekintése

Egy általános forgatókönyv számos webes alkalmazásokhoz, hogy támogatja az automatikus HTTP – HTTPS átirányításról, győződjön meg arról, alkalmazás és a felhasználók között minden kommunikáció titkosított elérési útnak keresztül történik. Múltbeli időpont az ügyfél már felhasználta technikák, például létrehozhat egy dedikált, amelynek egyetlen célja, hogy megkapja a HTTP – HTTPS kérelmek átirányítása a háttérkészlethez.  Az Application gateway mostantól támogatja az irányítható át a forgalmat az Application Gateway-en. Ez egyszerűbbé teszi az alkalmazás konfigurációja, optimalizálja az erőforrás-felhasználás, és többek között a globális és -alapú átirányítás új átirányítás forgatókönyveket teszi lehetővé. Application Gateway átirányítás támogatás már nem korlátozódik a HTTP -> csak HTTPS-átirányítás. Ez az egy általános átirányítás mechanizmust, amely lehetővé teszi, hogy egy figyelőt, hogy az Application Gateway egy másik figyelő kézbesítve forgalom átirányítása. A szolgáltatás a külső webhelyre való átirányítást is támogatja. Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

1. Egy másik figyelő az átjáró egy figyelőt a globális átirányítást. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
2. Útvonalalapú átirányítás. Átirányítás az ilyen típusú lehetővé teszi a HTTP – HTTPS átirányításról, csak az egy adott hely területen, például egy kosár vásárlási területen kimaradásával/bevásárlókocsi / *.
3. Átirányítás külső helyre.

![átirányítás](./media/application-gateway-redirect-overview/redirect.png)

Ez a változás az ügyfeleknek kell hozzon létre egy új átirányítási konfigurációs objektumot, amely azt adja meg a cél-figyelő vagy külső helyre, amelyhez átirányítás van szükség. A konfigurációs elem támogatja a beállítások engedélyezéséhez, Hozzáfűzés, az URI elérési út és a lekérdezési karakterláncot az átirányított URL-címet is. Ügyfeleink kiválaszthatják átirányítás-e egy ideiglenes (HTTP-állapotkód: 302) vagy egy Állandó átirányítás (HTTP-állapotkód 301) is. Az átirányítási konfiguráció létrehozása után az új szabály forrás figyelőt csatolva van. Alapszintű szabály használata esetén az átirányítási konfiguráció társítva a forrás-figyelő és globális átirányítást. Útvonalalapú szabály használata esetén az átirányítási konfiguráció van meghatározva, az URL-Címtérkép, és ezért csak érvényes hely a megadott elérési út területéhez.

### <a name="next-steps"></a>További lépések

[Konfigurálja a HTTP – HTTPS átirányításról, az application gateway-en](redirect-http-to-https-portal.md)
