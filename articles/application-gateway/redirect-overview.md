---
title: Az Azure Application Gateway átirányítási áttekintése
description: További tudnivalók az Azure Application Gateway átirányítási képességét
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
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204377"
---
# <a name="application-gateway-redirect-overview"></a>Átjáró átirányítási – áttekintés

Egy általános forgatókönyv számos webes alkalmazásokhoz, annak érdekében, hogy egy titkosított útvonalat keresztül történik alkalmazás és a felhasználók közötti összes kommunikáció HTTPS átirányítás automatikus HTTP támogatására szolgál. A múltban ügyfelek használt módszerek például létrehozhat egy dedikált háttérkészlet, amelynek egyetlen célja, hogy megkapja a HTTP – HTTPS kérelmek átirányítása a.

Alkalmazás-átjáró mostantól támogatja az átjáró forgalom irányítható. Ez egyszerűbbé teszi az alkalmazás konfigurációját, optimalizálja az erőforrás-felhasználás és többek között a globális és útvonal-alapú átirányítási új átirányítási forgatókönyveket támogat. Átjáró átirányítást alkalmazástámogatási nem korlátozódik a HTTP -> kizárólag HTTPS-átirányítás. Rendelkezik egy általános átirányítási mechanizmus, ami lehetővé teszi a forgalom átirányítása egy figyelőt, hogy egy másik figyelő Alkalmazásátjáró fogadja. Külső webhely átirányítása is támogatott.

Alkalmazás átjáró átirányítást támogatása a következő szolgáltatásokat biztosítja:

-  **Globális átirányítása**

   Egy figyelő átirányítása egy másik figyelő az átjáró számára. Ez lehetővé teszi, hogy HTTP a HTTPS-átirányítás egy helyen.
- **Az átirányítási elérési út-alapú**

   Az ilyen típusú átirányítási lehetővé teszi, hogy HTTP átirányítást HTTPS csak egy adott hely területre példa egy vásárlásra szolgáló bevásárlókocsiból területre jelölik/bevásárlókocsiból / *.
- **Külső webhely átirányítása**

![az átirányítási](./media/redirect-overview/redirect.png)

Ez a módosítás az ügyfelek kell hozzon létre egy új átirányítási konfigurációs objektum, amely azt adja meg a cél figyelő vagy a külső helyen, amelyhez átirányítási van szükség. A konfigurációs elem lehetővé az URI elérési út és a lekérdezési karakterlánc hozzáfűzése az átirányított URL-címet is támogatja. Választhatja azt is, hogy átirányítás egy ideiglenes (HTTP-állapotkód 302) vagy egy Állandó átirányítás (HTTP-állapotkód 301) történik-e. Létrehozása után az új szabály forrás figyelőt a átirányítási konfiguráció van csatolva. Egy alapszintű szabály használata esetén az átirányítási konfigurációs forrás figyelő társított, és globális átirányítás. Elérési út alapú szabály használata esetén az átirányítási definiált az URL-cím elérési út térképen. Ezért csak vonatkozik, a hely egyedi elérési utak területen.

### <a name="next-steps"></a>További lépések

[Alkalmazásátjáró átirányítási URL-cím konfigurálása](tutorial-url-redirect-powershell.md)
