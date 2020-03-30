---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179750"
---
A feladói házirendkeret-rekordok (SPF) rekordjai határozzák meg, hogy mely e-mail kiszolgálók küldhetnek e-mailt a tartománynév nevében. Az SPF-rekordok helyes konfigurálása fontos annak megakadályozása érdekében, hogy a címzettek levélszemétként jelöljék meg az e-maileket.

A DNS RFC-k eredetileg egy új SPF-rekordtípust vezettek be, amely támogatja ezt a forgatókönyvet. A régebbi névkiszolgálók támogatásához lehetővé tették a TXT rekordtípus használatát is az SPF rekordok megadásához. Ez a kétértelműség zavart okozott, amelyet [az RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1)oldott meg. Azt írja, hogy az SPF-rekordokat a TXT rekordtípus használatával kell létrehozni. Azt is kimondja, hogy az SPF rekordtípus elavult.

**Az SPF-rekordokat az Azure DNS támogatja, és a TXT rekordtípus használatával kell létrehozni.** Az elavult SPF rekordtípus nem támogatott. [DNS-zónafájl importálásakor](../articles/dns/dns-import-export.md)az SPF-rekordtípust használó SPF-rekordok AtTXT rekordtípussá alakulnak át.
