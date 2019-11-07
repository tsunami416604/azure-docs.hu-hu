---
title: Azure CDN a Microsoft Standard Rules Engine műveleteiből | Microsoft Docs
description: Dokumentáció a Microsoft Standard Rules Engine műveleteinek Azure CDN.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615987"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azure CDN a Microsoft Standard Rules Engine műveleteiből

Ez a cikk felsorolja az Azure Content Delivery Network (CDN) számára elérhető műveletek részletes leírásait a Microsoft [Standard Rules Engine](cdn-standard-rules-engine.md)-től.

A szabály második része egy művelet. Egy művelet határozza meg, hogy milyen viselkedést alkalmaz a rendszer az egyeztetési feltételek alapján azonosított kérelem típusára.

## <a name="actions"></a>Műveletek

A következő műveletek használhatók. 

## <a name="cache-expiration"></a>Gyorsítótár lejárata

Ez a művelet lehetővé teszi, hogy felülírja a végpont ÉLETTARTAMát a szabályoknak megfelelő feltételekkel megadott kérelmek esetében.

**Kötelező mezők**

Gyorsítótár viselkedése |                
---------------|----------------
Gyorsítótár megkerülése | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer nem gyorsítótárazza a tartalmat.
Felülbírálás | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer felülírja a forrásból visszaadott TTL-értéket a műveletben megadott értékkel.
Ha hiányzik, állítsa be | Ha ez a beállítás be van jelölve, és a szabály megfelel a forrástól, akkor a szabály az élettartam értéket a műveletben megadott értékre állítja be.

**További mezők**

Nap | Óra | perc | másodperc
-----|-------|---------|--------
int | int | int | int 

## <a name="cache-key-query-string"></a>Gyorsítótár-kulcs lekérdezési karakterlánca

Ez a művelet lehetővé teszi a gyorsítótár-kulcs módosítását a lekérdezési karakterláncok alapján.

**Kötelező mezők**

Viselkedés | Leírás
---------|------------
tartalmazza | Ha ez a beállítás be van jelölve, és a szabály egyezik, a paraméterekben megadott lekérdezési karakterláncok szerepelni fognak a gyorsítótár kulcsának létrehozásakor. 
Minden egyedi URL gyorsítótárazása | Ha ez a beállítás be van jelölve, és a szabály megfelel, minden egyedi URL-cím saját gyorsítótár-kulccsal fog rendelkezni. 
Exclude | Ha ez a beállítás be van jelölve, és a szabály egyezik, a paraméterekben megadott lekérdezési karakterláncok ki lesznek zárva a gyorsítótár-kulcs létrehozásakor.
Lekérdezési sztringek figyelmen kívül hagyása | Ha ez a beállítás be van jelölve, és a szabály egyezik, a rendszer nem veszi figyelembe a lekérdezési karakterláncokat a gyorsítótár kulcsának létrehozásakor. 

## <a name="modify-request-header"></a>Kérelem fejlécének módosítása

Ez a művelet lehetővé teszi a forrásnak küldött kérésekben lévő fejlécek módosítását.

**Kötelező mezők**

Műveletek | HTTP-fejléc neve | Érték
-------|------------------|------
Hozzáfűzés | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer hozzáadja a fejlécben megadott fejlécet a kérelemhez a megadott értékkel. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő értékhez. | Sztring
Felülírja | Ha ez a beállítás be van jelölve, és a szabály megfelel, a rendszer hozzáadja a fejlécben megadott fejlécet a kérelemhez a megadott értékkel. Ha a fejléc már létezik, akkor az érték felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, és a szabály egyezik, és a szabályban megadott fejléc található, akkor a rendszer törli a kérésből. | Sztring

## <a name="modify-response-header"></a>Válasz fejlécének módosítása

Ez a művelet lehetővé teszi a végfelhasználóknak visszaadott válaszokban lévő fejlécek módosítását

**Kötelező mezők**

Műveletek | HTTP-fejléc neve | Érték
-------|------------------|------
Hozzáfűzés | Ha ez a beállítás be van jelölve, és a szabály megfelel, a fejlécben megadott fejléc a megadott értékkel lesz hozzáadva a válaszhoz. Ha a fejléc már létezik, a rendszer hozzáfűzi az értéket a meglévő értékhez. | Sztring
Felülírja | Ha ez a beállítás be van jelölve, és a szabály megfelel, a fejlécben megadott fejléc a megadott értékkel lesz hozzáadva a válaszhoz. Ha a fejléc már létezik, akkor az érték felülírja a meglévő értéket. | Sztring
Törlés | Ha ez a beállítás be van jelölve, és a szabály egyezik, és a szabályban megadott fejléc található, akkor a válaszból törlődik. | Sztring

## <a name="url-redirect"></a>URL-átirányítás

Ez a művelet lehetővé teszi a végső ügyfelek átirányítását egy új URL-címre. 

**Kötelező mezők**

Mező | Leírás 
------|------------
Típus | Válassza ki azt a választ, amelyet a kérelmezőnek vissza fog adni. A következő lehetőségek közül választhat:-302 found, 301 mozgott, 307 ideiglenes átirányítás és 308 végleges átirányítás
Protokoll | Egyeztetési kérelem, HTTP vagy HTTPS
Gazdanév | Válassza ki azt a gazdagépet, amelyre a rendszer átirányítja a kérést. Hagyja üresen a bejövő gazdagép megőrzését.
Útvonal | Az átirányítás során használandó elérési út megadása. Hagyja üresen a bejövő elérési út megőrzését.  
Lekérdezési sztring | Adja meg az átirányítás során használt lekérdezési karakterláncot. Hagyja üresen a bejövő lekérdezési karakterlánc megőrzését. 
Töredék | Adja meg az átirányítás során használandó töredéket. Hagyja üresen a bejövő töredék megőrzése érdekében. 

Erősen ajánlott abszolút URL-címet használni. A relatív URL-cím használata a CDN URL-címek érvénytelen elérési útra való átirányítására használható. 

## <a name="url-rewrite"></a>URL-átírás

Ez a művelet lehetővé teszi, hogy újraírja a kérelem elérési útját a forráshoz.

**Kötelező mezők**

Mező | Leírás 
------|------------
Forrás mintája | Adja meg a forrás mintát a lecserélni kívánt URL-útvonalon. Jelenleg a forrás minta előtag-alapú egyezést használ. Az összes URL-elérési út megfeleltetéséhez használja a "/" értéket a forrás mintázat értékeként.
Cél | Adja meg az újraíráshoz használandó célhely elérési útját. Ez felülírja a forrás mintát
Páratlan elérési út megőrzése | Ha igen, a forrás minta után megmaradó elérési út az új célhelyhez lesz hozzáfűzve. 


[Vissza a tetejére](#actions)

</br>

## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabálymotor-referencia](cdn-standard-rules-engine-reference.md)
- [Szabálymotor egyezési feltételei](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS kikényszerítés a standard szabályok motor használatával](cdn-standard-rules-engine.md)
