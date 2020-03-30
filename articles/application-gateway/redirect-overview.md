---
title: Átirányítás – áttekintés az Azure Application Gateway alkalmazásátjárójához
description: Ismerje meg az átirányítási képesség az Azure Application Gateway átirányítási szolgáltatás átirányítása az egyik figyelő egy másik figyelő, vagy egy külső webhelyre kapott forgalmat.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 5943d8aad4d5dd0d981fae9b2325dd3fc75b31e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129889"
---
# <a name="application-gateway-redirect-overview"></a>Alkalmazásátjáró átirányítás – áttekintés

Az alkalmazásátjáró segítségével átirányíthatja a forgalmat.  Általános átirányítási mechanizmusa lehetővé teszi, hogy egy figyelő által fogadott forgalmat egy másik figyelőre vagy egy külső webhelyre lehessen átirányítani. Ez leegyszerűsíti az alkalmazáskonfigurációt, optimalizálja az erőforrás-használatot, és támogatja az új átirányítási forgatókönyveket, beleértve a globális és az útvonal-alapú átirányítást.

Számos webalkalmazás gyakori átirányítási forgatókönyve az automatikus HTTP-HTTPS átirányítás támogatása annak biztosítása érdekében, hogy az alkalmazás és a felhasználók közötti kommunikáció titkosított útvonalon történjen. A múltban az ügyfelek olyan technikákat alkalmaztak, mint például egy dedikált háttérkészlet létrehozása, amelynek egyetlen célja a HTTP-n kapott kérelmek átirányítása HTTPS-re. Az Alkalmazásátjáró átirányítási támogatásával ezt egyszerűen úgy érheti el, hogy új átirányítási konfigurációt ad hozzá egy útválasztási szabályhoz, és egy másik figyelőt ad meg HTTPS protokollal a célfigyelőként.

A következő átirányítási típusok támogatottak:

- 301 Állandó átirányítás
- 302 talált
- 303 Lásd: Egyéb
- 307 Ideiglenes átirányítás

Az Application Gateway átirányítási támogatása a következő funkciókat nyújtja:

-  **Globális átirányítás**

   Átirányítások egyik figyelőtől egy másik figyelő az átjárón. Ez lehetővé teszi a HTTP–HTTPS átirányítást egy webhelyen.
- **Görbealapú átirányítás**

   Ez a fajta átirányítás csak egy adott helyterületen engedélyezi a HTTP-t HTTPS-átirányításra, például a /cart/* által jelölt bevásárlókosár-területen.
- **Átirányítás külső webhelyre**

![Átirányítás](./media/redirect-overview/redirect.png)

Ezzel a módosítással az ügyfeleknek létre kell hozniuk egy új átirányítási konfigurációs objektumot, amely megadja azt a célfigyelőt vagy külső helyet, amelyre az átirányítás szükséges. A konfigurációs elem azt is támogatja, hogy engedélyezze az URI-elérési út és a lekérdezési karakterlánc hozzáfűzését az átirányított URL-címhez. Az átirányítás típusát is megadhatja. Létrehozása után ez az átirányítási konfiguráció egy új szabály on keresztül csatlakozik a forrásfigyelőhöz. Alapszabály használataesetén az átirányítási konfiguráció egy forrásfigyelőhöz van társítva, és globális átirányítás. Elérési út alapú szabály használata esetén az átirányítási konfiguráció az URL-elérési út térképén lesz definiálva. Így csak a webhely adott elérési útterületén érvényes.

### <a name="next-steps"></a>További lépések

[URL-átirányítás konfigurálása alkalmazásátjárón](tutorial-url-redirect-powershell.md)
