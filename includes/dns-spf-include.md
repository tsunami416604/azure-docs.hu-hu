---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563337"
---
Küldő házirend-keretrendszer (SPF) rekordok segítségével adja meg, melyik e-mail-kiszolgálók küldhet e-mailt nevében egy tartomány nevét. SPF-rekordokat konfigurációját fontos, hogy megakadályozza, hogy a címzettek megjelölés levélszemétként az e-maileket.

A DNS RFC-ket bevezetett lomtárszolgáltatás SPF új rekordtípus a forgatókönyv támogatása érdekében. Támogatja a régebbi névkiszolgálók, azokat is engedélyezni a txt típusú rekord típusát adja meg az SPF-rekordokat használatát. Ez zavart, ami által feloldva vezetett [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Megállapítja, hogy az SPF-rekordokat TXT típusú rekordok használatával kell létrehozni. Is meghatározza, hogy az SPF-rekordtípus elavult.

**SPF-rekordokat Azure DNS támogatja, és a TXT típusú rekordok használatával kell létrehozni.** Az SPF elavult rekordtípus nem támogatott. Ha Ön [DNS-zónafájl importálása](../articles/dns/dns-import-export.md), bármely SPF-rekordokat, amelyek használják az SPF-rekordtípus TXT típusú rekordok alakítja.
