---
title: Az Azure CDN használatával a CORS |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Content Delivery Network (CDN) számára az eltérő eredetű erőforrások megosztása (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7070397f6e69b21add75bad8220f0b8ebe36d266
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="using-azure-cdn-with-cors"></a>Az Azure CDN a CORS használatával
## <a name="what-is-cors"></a>Mi az a CORS?
A CORS (Cross eredetű erőforrások megosztása) az HTTP szolgáltatása: lehetővé teszi, hogy a webalkalmazás fut egy tartomány egy másik tartományban lévő erőforrások eléréséhez. Ahhoz, hogy az esélye, többhelyes parancsfájlok futtatására, minden modern webböngésző néven ismert biztonsági korlátozások megvalósítása [azonos eredetű házirend](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Ez megakadályozza, hogy a hívó API-k egy másik tartományban származó weblap.  A CORS engedélyezése egy eredeti adatforrással (a forrástartomány) API-k hívására az eredeti biztonságos lehetőséget biztosít.

## <a name="how-it-works"></a>Működés
A CORS kérelmek két különböző *egyszerű kérelmek* és *összetett kérelmeket.*

### <a name="for-simple-requests"></a>Egyszerű kérelmeknél:

1. A böngésző további a CORS kérelmet küld **származási** HTTP-kérés fejlécének. A fejléc értéke van definiálva, kombinációja szülőlap szolgáltatott eredeti *protokoll* *tartomány,* és *port.*  Ha egy lap https://www.contoso.com fabrikam.com küldheti megpróbál hozzáférni a felhasználó adatai, fabrikam.com származási, a következő kérelemfejlécet:

   `Origin: https://www.contoso.com`

2. A kiszolgáló a következő jelenhetnek meg:

   * Egy **hozzáférés-vezérlési-engedélyezése-forrás** jelző a származási hely engedélyezett válaszában fejléc. Példa:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Például ha a kiszolgálón nincs engedélyezve az eltérő eredetű kérelem a származási fejléc ellenőrzése után 403-as HTTP-hibakódot

   * Egy **hozzáférés-vezérlési-engedélyezése-forrás** helyettesítő karakter, amely lehetővé teszi minden eredet fejléc:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Összetett kérelmeknél:

Egy összetett vonatkozó kérés, mert a CORS kérelem ahol a böngésző küldéséhez szükséges egy *ellenőrzési kérés* (azaz egy előzetes mintavételt) a tényleges CORS kérelem elküldése előtt. Az ellenőrzési kérés a kiszolgáló engedélyt kér Ha az eredeti CORS lépne, és igényeljen egy `OPTIONS` kérelem azonos URL-címét.

> [!TIP]
> A CORS-adatfolyamok és közös nehézségek további részleteket a [való REST API CORS](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Helyettesítő karakteres vagy egyetlen forrás forgatókönyvek
Az Azure CDN CORS automatikusan együttműködve további konfigurációra amikor a **hozzáférés-vezérlési-engedélyezése-forrás** fejléc értéke helyettesítő karakter (*) vagy egy egyetlen forrása.  A CDN gyorsítótárazhatják az első válasz, és később fogja használni a azonos fejléc.

Ha a kérések már végzett és a CDN CORS a beállítása előtt a a forrás, szüksége lesz a végpont tartalom töltse be újra a tartalmat a tartalom kiürítése a **hozzáférés-vezérlési-engedélyezése-forrás** fejléc.

## <a name="multiple-origin-scenarios"></a>Több forrás forgatókönyv
Ha szeretné engedélyezni a források számára engedélyezett a CORS adott listáját, részek lesznek egy kicsit bonyolultabb. A probléma akkor fordul elő, amikor gyorsítótárba helyezi azt a CDN a **hozzáférés-vezérlési-engedélyezése-forrás** fejléc az első CORS-forrás.  Ha egy másik CORS származási későbbi kérést, a gyorsítótárazott teljesíti-e a a CDN **hozzáférés-vezérlési-engedélyezése-forrás** fejléc, amelyek nem egyeznek.  Ennek számos módja van.

### <a name="azure-cdn-premium-from-verizon"></a>Prémium szintű Azure CDN a Verizontól
Ennek legjobb módja **verizon Azure CDN Premium**, amely azt mutatja, néhány speciális funkcióinak. 

Kell [hozzon létre egy szabályt](cdn-rules-engine.md) ellenőrizni a **származási** a kérelem fejléce.  Ha egy érvényes forrás, a szabály állítja be a **hozzáférés-vezérlési-engedélyezése-forrás** fejléc a következő a forrás, a kérelemben.  Ha a forrás megadott a **származási** fejléc nem engedélyezett, a szabály el kell hagynia a **hozzáférés-vezérlési-engedélyezése-forrás** fejlécet, amely hatására a böngésző elutasítja a kérelmet. 

Ehhez a szabályok motorral két módja van.  Mindkét esetben a **hozzáférés-vezérlési-engedélyezése-forrás** a fájl forráskiszolgálóról fejléc teljesen figyelmen kívül hagyja, a CDN szabálymotor teljes körű kezelése a CORS engedélyezett eredetet.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Minden érvényes eredet egy reguláris kifejezések
Ebben az esetben létre fog hozni egy reguláris kifejezés, amely tartalmazza az összes is engedélyezni szeretné a tartalmazza: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Verizon Azure CDN** használ [Perl kompatibilis reguláris kifejezések](http://pcre.org/) , a reguláris kifejezések motor.  Egy eszköz, például használhatja [reguláris kifejezések 101](https://regex101.com/) a reguláris kifejezés érvényesítése.  Vegye figyelembe, hogy a "/" karakter érvényes reguláris kifejezések, és nem kell megjelölni, azonban ez a karakter escape ajánlott eljárás, és néhány regex érvényesség-ellenőrzők által várt.
> 
> 

Ha megfelel a reguláris kifejezéssel, a szabály felülírja a **hozzáférés-vezérlési-engedélyezése-forrás** fejléc (ha van ilyen) és a kérelmet küldött az eredeti forrásból.  Azt is megteheti további CORS fejlécek, például a **hozzáférés-vezérlési-engedélyezése-metódusok**.

![A reguláris kifejezéssel szabályok – példa](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Az egyes származási kérelmek fejléc szabályt.
Ahelyett, hogy a reguláris kifejezések, akkor inkább szabályt hozhat létre külön minden forrás használatával engedélyezni szeretné a **kérelem fejléc helyettesítő** [feltételének](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). A reguláris kifejezés használata esetén a önmagában szabálymotor beállítja a CORS-fejléceket. 

![Szabályok reguláris kifejezés nélkül – példa](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> A helyettesítő karakter használatát a fenti példában a * közli a szabálymotor a HTTP és HTTPS kereséséhez.
> 
> 

### <a name="azure-cdn-standard"></a>Az Azure CDN Standard
Az Azure CDN Standard profilok, a csak mechanizmust, amely lehetővé teszi több források helyettesítő eredeti használata nélkül, hogy használja [lekérdezési karakterláncok gyorsítótárazása](cdn-query-string.md).  Engedélyezze a CDN-végpont a lekérdezési karakterlánc beállítást, majd egy egyedi lekérdezési karakterlánc minden olyan engedélyezett tartományhoz érkező kérésekhez szükség. Ez azt eredményezi, hogy a CDN-t egy külön objektum minden egyes egyedi lekérdezési karakterlánc gyorsítótárazását. Ez a megközelítés ideális nem, azonban módon gyorsítótárazza a CDN ugyanazon fájl több másolatát okoz.  

