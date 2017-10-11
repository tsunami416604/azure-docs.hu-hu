---
title: "Áttekintés átirányítja az Azure Application Gateway |} Microsoft Docs"
description: "További tudnivalók az Azure Application Gateway átirányítási képességét"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="application-gateway-redirect-overview"></a>Átjáró átirányítási – áttekintés

Egy általános forgatókönyv számos webes alkalmazásokhoz, annak érdekében, hogy egy titkosított útvonalat keresztül történik alkalmazás és a felhasználók közötti összes kommunikáció HTTPS átirányítás automatikus HTTP támogatására szolgál. A múltban ügyfelek használt módszerek például létrehozhat egy dedikált háttérkészlet, amelynek egyetlen célja, hogy megkapja a HTTP – HTTPS kérelmek átirányítása a.  Alkalmazás-átjáró mostantól támogatja az Alkalmazásátjáró forgalmat irányítható. Ez egyszerűbbé teszi az alkalmazás konfigurációját, optimalizálja az erőforrás-felhasználás és többek között a globális és útvonal-alapú átirányítási új átirányítási forgatókönyveket támogat. Átjáró átirányítást alkalmazástámogatási nem korlátozódik a HTTP -> kizárólag HTTPS-átirányítás. Ez az egy általános átirányítási mechanizmus, lehetővé teszi egy figyelőt, hogy egy másik figyelő Alkalmazásátjáró fogadja forgalom átirányítását. Átirányítás egy külső helyre is támogatja. Alkalmazás átjáró átirányítást támogatása a következő szolgáltatásokat biztosítja:

1. Egy másik figyelő az átjáró egy figyelőt a globális átirányítást. Ez lehetővé teszi, hogy HTTP a HTTPS-átirányítás egy helyen.
2. Elérési út-alapú átirányítási. Az ilyen típusú átirányítási lehetővé teszi, hogy HTTP átirányítást HTTPS csak egy adott hely területre példa egy vásárlásra szolgáló bevásárlókocsiból területre jelölik/bevásárlókocsiból / *.
3. Külső webhely átirányítása.

![az átirányítási](./media/application-gateway-redirect-overview/redirect.png)

Ez a módosítás az ügyfelek kellene hozzon létre egy új átirányítási konfigurációs objektum, amely azt adja meg a cél figyelő vagy a külső helyen, amelyhez átirányítási van szükség. A konfigurációs elem lehetővé az URI elérési út és a lekérdezési karakterlánc hozzáfűzése az átirányított URL-címet is támogatja. Az ügyfelek is sikerült adja átirányítási egyikét (HTTP-állapotkód 302) ideiglenes és állandó átirányítás (HTTP-állapotkód 301). Az átirányítási konfigurációs létrehozása után az új szabály forrás figyelőt van csatolva. Egy alapszintű szabály használata esetén az átirányítási konfigurációs forrás figyelő társított, és globális átirányítás. Elérési út alapú szabály használata esetén az átirányítási konfigurációs az URL-cím elérési út leképezés van definiálva, és ezért csak egy webhely a megadott elérési út területére.

### <a name="next-steps"></a>Következő lépések

[Alkalmazásátjáró átirányítási URL-cím konfigurálása](application-gateway-configure-redirect-powershell.md)
