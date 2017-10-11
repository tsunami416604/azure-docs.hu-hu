---
title: "Az Azure CDN szabályok motor egyezés feltételek |} Microsoft Docs"
description: "Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Az Azure CDN szabálymotor feltételek egyeznek
Ez a témakör részletesen ismerteti az Azure Content Delivery Network (CDN) feltételek egyeznek a [szabálymotor](cdn-rules-engine.md).

A szabály második része a egyezés feltétel. Egy egyezés feltétel azonosítja az adott típusú kérelmet, amely funkciókat érvényesül.

Például akkor lehet, hogy használható szűrheti a kéréseket a tartalom egy adott helyen létrehozott, egy adott IP-cím vagy ország vagy a fejléc-információ kérelmek.

## <a name="always"></a>Mindig

A mindig egyezés feltétel célja, hogy a szolgáltatások alapértelmezett készlete összes kérésre alkalmazni.

## <a name="device"></a>Eszköz

Az eszköz egyezés feltétel kérelmek egy mobileszközről tulajdonságai alapján azonosítja.  Mobil eszköz észlelési sorrendekben [WURFL](http://wurfl.sourceforge.net/).  WURFL képességek és a CDN szabálymotor változók az alábbiak.

> [!NOTE] 
> Az alábbi változók használhatók a **ügyfél kérelem fejléc módosítása** és **ügyfél válaszfejléc módosítása** szolgáltatások.

Képesség | Változó | Leírás | A minta érték(ek)
-----------|----------|-------------|----------------
Márka neve | a(z) % {wurfl_cap_brand_name} | Egy karakterlánc, amely az eszköz márka nevét jelöli. | Samsung
Az eszköz operációs rendszere | a(z) % {wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, az operációs rendszer telepítve az eszközön. | IOS
Eszköz operációs rendszerének verziója | a(z) % {wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, az eszközre telepített operációs rendszer verziószámát. | 1.0.1
Kettős tájolását | a(z) % {wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | Igaz
HTML előnyben részesített DTD | a(z) % {wurfl_cap_html_preferred_dtd} | Egy olyan karakterlánc, amely jelzi a mobil eszköz előnyben részesített dokumentumtípusdefiníció (DTD) a HTML-tartalmakat. | Egyik sem<br/>xhtml_basic<br/>HTML5
A kép Inlining | a(z) % {wurfl_cap_image_inlining} | Olyan logikai érték, amely jelzi, hogy az eszköz támogatja-e a Base64 kódolású képek. | hamis
Az Android | a(z) % {wurfl_vcap_is_android} | Egy logikai érték, amely azt jelzi, hogy az eszköz az Android operációs rendszer használja-e. | Igaz
IOS | a(z) % {wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz iOS használja-e. | hamis
Az intelligens TV | a(z) % {wurfl_cap_is_smarttv} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy intelligens TV. | hamis
Smartphone van | a(z) % {wurfl_vcap_is_smartphone} | Egy logikai érték, amely azt jelzi, hogy az eszköz okostelefont. | Igaz
Tábla van | a(z) % {wurfl_cap_is_tablet} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy tábla. Ez az az operációs rendszer független leírást. | Igaz
Vezeték nélküli eszköz | a(z) % {wurfl_cap_is_wireless_device} | Egy logikai érték, amely azt jelzi, hogy az eszköz vezeték nélküli eszköz nem megfelelőnek. | Igaz
Marketing neve | a(z) % {wurfl_cap_marketing_name} | Egy karakterlánc, amely megadja, hogy az eszköz marketing néven. | BlackBerry 8100 Pearl
Mobil böngésző | a(z) % {wurfl_cap_mobile_browser} | Egy olyan karakterlánc, amely jelzi a böngészőben tartalom kérésére az eszközről. | Chrome
Mobil böngészőverzió | a(z) % {wurfl_cap_mobile_browser_version} | A tartalom kérhet az eszköz használt böngésző verzióját jelző karakterlánc. | 31
Modell neve | a(z) % {wurfl_cap_model_name} | Egy karakterlánc, amely azt jelzi, az eszköz modell neve. | S3
Progresszív letöltés | a(z) % {wurfl_cap_progressive_download} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e a hang-és videófolyamot lejátszását, miközben továbbra is letöltése. | Igaz
Kiadás dátuma | a(z) % {wurfl_cap_release_date} | Egy olyan karakterlánc, amely jelzi a kiválasztott évhez és hónaphoz, amelyen az eszköz WURFL adatbázisba lett hozzáadva.<br/><br/>Formátum:`yyyy_mm` | 2013_december
Megoldási magassága | a(z) % {wurfl_cap_resolution_height} | Az eszköz magassága képpontban jelző egész számot. | 768
Megoldási szélessége | a(z) % {wurfl_cap_resolution_width} | Az eszköz szélességét képpontban jelző egész számot. | 1024


## <a name="location"></a>Hely

Ezen feltételek úgy terveztek, hogy a kérelmező helye alapján kérelmek egyezés.

Név | Cél
-----|--------
SZÁMOT | Egy adott hálózati kérelmekkel azonosítja.
Ország | A megadott országokból kérelmekkel azonosítja.


## <a name="origin"></a>Forrás

Ezen feltételek úgy terveztek, hogy egyeznek kérelmek adott pontra CDN tárolóra vagy egy ügyfél eredeti kiszolgálóra.

Név | Cél
-----|--------
CDN-forrása | A CDN-tárolón tárolt tartalmak iránti kérelmek azonosítja.
Ügyfél forrása | Egy adott ügyfélhez forrás kiszolgálón lévő tartalomhoz azonosítja.


## <a name="request"></a>Kérés

Ezen feltételek úgy terveztek, hogy kérelmet tulajdonságaik alapján egyezés.

Név | Cél
-----|--------
Ügyfél IP-címe | Egy adott IP-címről kérelmekkel azonosítja.
Cookie-k paraméter | Ellenőrzi a megadott értéket minden kérelemhez társított cookie-kat.
Cookie-k paraméter Regex | A cookie-k a megadott reguláris kifejezést minden kérelemhez társított ellenőrzi.
Peremhálózati Cname | Azonosítja a kérelmeket, amelyek egy adott peremhálózati CNAME.
Hivatkozó tartomány | A megadott hostname(s) az említett kérelmek azonosítja.
Kérelem fejléc szövegkonstans | A megadott fejléc értéke a megadott értékeket tartalmazó kérelem azonosítja.
Kérelem fejléc Regex | A megadott fejléc értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
Kérelem fejléc helyettesítő | A megadott fejléc értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
Kérési módszer | A HTTP-metódus azonosítja a kérelmeket.
Kérelem séma | A HTTP protokoll azonosítja a kérelmeket.

## <a name="url"></a>URL-CÍME

Ezen feltételek úgy terveztek, hogy az URL-címek alapján kérelmek egyezés.

Név | Cél
-----|--------
URL-cím elérési út könyvtár | Azonosítja a kéréseket a relatív elérési útja.
URL-cím elérési út bővítmény | A fájlnév-kiterjesztés azonosítja a kérelmeket.
URL-cím elérési út fájlnév | A fájlnév azonosítja a kérelmeket.
URL-cím elérési út szövegkonstans | Összehasonlítja a kérelem relatív elérési út és a megadott értéket.
URL-cím elérési út Regex | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezés.
URL-cím elérési út helyettesítő karakter | Összehasonlítja a kérelmet a megadott minta relatív elérési útja.
Lekérdezés-szövegkonstans URL-címe | Összehasonlítja a lekérdezési karakterlánc és a megadott értéket.
URL-cím lekérdezési paraméter | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
Lekérdezés Regex URL-címe | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
Lekérdezés helyettesítő URL-címe | A megadott értékeket, szemben a lekérdezési karakterlánc hasonlítja össze.


## <a name="next-steps"></a>Következő lépések
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)

