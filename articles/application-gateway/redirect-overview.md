---
title: Az Azure Application Gateway átirányítás áttekintése
description: Az átirányítási funkció az Azure Application Gateway ismertetése
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 8e88e0e11b3ccab7cc2c68b2617df2d588680780
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60715800"
---
# <a name="application-gateway-redirect-overview"></a>Application Gateway átirányítás áttekintése

Az application gateway segítségével forgalom átirányítása.  Általános átirányítási mechanizmusa lehetővé teszi, hogy egy figyelő által fogadott forgalmat egy másik figyelőre vagy egy külső webhelyre lehessen átirányítani. Ez egyszerűbbé teszi az alkalmazás konfigurációja, optimalizálja az erőforrás-felhasználás, és többek között a globális és -alapú átirányítás új átirányítás forgatókönyveket teszi lehetővé.

Számos webes alkalmazás átirányítási gyakran előfordul, hogy támogatja az automatikus HTTP – HTTPS átirányításról, győződjön meg arról, alkalmazás és a felhasználók között minden kommunikáció titkosított elérési útnak keresztül történik. Múltbeli időpont az ügyfél már felhasználta technikák, például létrehozhat egy dedikált, amelynek egyetlen célja, hogy megkapja a HTTP – HTTPS kérelmek átirányítása a háttérkészlethez. Átirányítás támogatása az Application Gatewayben, és úgy lehet megvalósítani, ez egyszerűen egy útválasztási szabályt ad hozzá egy új átirányítási konfiguráció, és a egy másik figyelő megadása a célfigyelőt, HTTPS protokollal.

Átirányítás a következő típusú támogatottak:

- 301 Permanent Redirect
- 302 Found
- 303 zobrazit Jiné
- 307 Temporary Redirect

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

-  **Globális átirányítás**

   Átirányítások egy figyelő az átjáró egy másik figyelővel. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- **Az átirányítási elérési út alapján**

   Átirányítás az ilyen típusú lehetővé teszi a HTTP – HTTPS átirányításról, csak az egy adott hely területen, például egy kosár vásárlási területen kimaradásával/bevásárlókocsi / *.
- **Átirányítás külső helyre**

![átirányítás](./media/redirect-overview/redirect.png)

Ez a változás az ügyfeleknek kell hozzon létre egy új átirányítási konfigurációs objektumot, amely azt adja meg a cél-figyelő vagy külső helyre, amelyhez átirányítás van szükség. A konfigurációs elem támogatja a beállítások engedélyezéséhez, Hozzáfűzés, az URI elérési út és a lekérdezési karakterláncot az átirányított URL-címet is. Azt is beállíthatja az átirányítás típusát. Létrehozása után az átirányítási konfiguráció csatolva van egy új szabályt a forrás figyelőt. Alapszintű szabály használata esetén az átirányítási konfiguráció társítva a forrás-figyelő és globális átirányítást. Útvonalalapú szabály használata esetén az átirányítási konfiguráció meg az URL-Címtérkép van meghatározva. Ezért csak érvényes egy helyet a megadott elérési út területéhez.

### <a name="next-steps"></a>További lépések

[Az application gateway URL-átirányítás konfigurálása](tutorial-url-redirect-powershell.md)
