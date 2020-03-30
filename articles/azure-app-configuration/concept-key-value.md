---
title: Az Azure App konfigurációs kulcsérték-tárolójának ismertetése
description: Ismerje meg, hogyan tárolja a konfigurációs adatokat az Azure App konfigurációja.
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: 0b83a35d912c97ae25bc2d69d076e8eae8ca490f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523604"
---
# <a name="keys-and-values"></a>Kulcsok és értékek

Az Azure App Configuration kulcs-érték párokként tárolja a konfigurációs adatokat. A kulcsérték-párok a fejlesztők által használt alkalmazásbeállítások egyszerű és rugalmas ábrázolása.

## <a name="keys"></a>Kulcsok

A kulcsok a kulcs-érték párok azonosítóiként szolgálnak, és a megfelelő értékek tárolására és beolvasására szolgálnak. Általános gyakorlat, hogy a kulcsokat hierarchikus névtérbe rendezik karakterhatároló `/` `:`használatával, például vagy . Az alkalmazásnak leginkább megfelelő konvenciót használjon. Az Alkalmazáskonfiguráció a kulcsok egészét kezeli. Nem elemzi a kulcsokat, hogy kitaláljuk, hogyan vannak felépítve a nevük, vagy kényszerítsük a rájuk vonatkozó szabályokat.

Íme két példa a hierarchiába strukturált kulcsnevekre:

* Komponensszolgáltatások alapján

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* Telepítési régiók alapján

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

A konfigurációs adatok használata az alkalmazáskeretrendszereken belül adott elnevezési sémákat diktálhat a kulcsértékekhez. A Java tavaszi felhőkeretrendszere például olyan erőforrásokat `Environment` határoz meg, amelyek a tavaszi alkalmazások beállításait biztosítják.  Ezeket az *alkalmazás nevét* és *profilját*tartalmazó változók paraméterezik. A tavaszi felhővel kapcsolatos konfigurációs adatok kulcsai általában ezekkel a két elválasztóval elválasztott két elemmel kezdődnek.

Az alkalmazáskonfigurációban tárolt kulcsok a kis- és nagybetűk megkülönböztetése, unicode-alapú karakterláncok. A kulcsok *app1* és *App1* különböznek az App Configuration Store. Ezt tartsa szem előtt, ha egy alkalmazáson belül konfigurációs beállításokat használ, mert egyes keretrendszerek nem szidják a konfigurációs kulcsokat. A kulcsok megkülönböztetéséhez nem javasoljuk a kis- és nagybetűk használatát.

A kulcsnevekben bármilyen unicode karaktert `,`használhat, kivéve a `\` `*`, és a.  Ha a fenntartott karakterek egyikét kell megadnia, a használatával meneküljön meg a karakter elől. `\{Reserved Character}` 

Egy kulcs-érték párra 10 KB összesített méretkorlát van. Ez a korlát a kulcs összes karakterét, értékét és az összes kapcsolódó választható attribútumot tartalmazza. Ezen a korláton belül a kulcsokhoz számos hierarchikus szint tartozhat.

### <a name="design-key-namespaces"></a>Kulcsnévterek tervezése

A konfigurációs adatokhoz használt kulcsok elnevezésének két általános megközelítése van: egyenlésű vagy hierarchikus. Ezek a módszerek az alkalmazáshasználati szempontból hasonlóak, de a hierarchikus elnevezés számos előnnyel jár:

* Könnyebb olvasni. A hierarchikus kulcsnévhatárolók szóközként működnek egy mondatban. Természetes szüneteket is biztosítanak a szavak között.
* Könnyebb kezelni. A kulcsnév-hierarchia a konfigurációs adatok logikai csoportjait jelöli.
* Könnyebb encikba használni. Egyszerűbb olyan lekérdezést írni, amely minta megegyezik a kulcsok hierarchikus struktúrában, és csak egy részét olvassa be a konfigurációs adatoknak. Emellett számos újabb programozási keretrendszer natív támogatást nyújt a hierarchikus konfigurációs adatokhoz, így az alkalmazás meghatározott konfigurációkészleteket használhat.

A kulcsokat hierarchikusan, hierarchikusan rendezheti. Gondoljon olyan kulcsokra, mint [URI-kra.](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) Minden hierarchikus kulcs egy *erőforrás-elérési út,* amely egy vagy több összetevőből áll, amelyeket határolók egyesítenek. Válassza ki, hogy milyen karaktert használjon határolóként az alkalmazás, a programozási nyelv vagy a keretrendszer igényei alapján. Az alkalmazás konfigurációjában használjon több határolóeszközt a különböző kulcsokhoz.

### <a name="label-keys"></a>Címkebillentyűk

Az Alkalmazáskonfiguráció kulcsértékei tetszés szerint rendelkezhetnek címkeattribútummal. A címkék a kulcsértékek azonos kulcsmal való megkülönböztetésére szolgálnak. Az *A* és *B* címkékkel rendelkező *kulcsalkalmazás1* két külön kulcsot alkot az Alkalmazáskonfigurációs tárolóban. Alapértelmezés szerint a kulcsértéknek nincs címkéje. Ha kifejezetten címke nélküli kulcsértékre `\0` szeretne hivatkozni, `%00`használja a (URL-kódolva)

A címke kényelmes módot kínál a kulcs változatainak létrehozására. A címkék gyakori használata, ha több környezetet ad meg ugyanahhoz a kulcshoz:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Verziókulcs-értékek

Az alkalmazáskonfiguráció nem verziószámokkal verziószámokkal használja automatikusan a kulcsértékeket. Címkék használatával több verziója is létrehozható egy kulcsértékből. Például megadhat egy alkalmazás verziószámát vagy egy Git véglegesítési azonosítót a címkékben egy adott szoftverbuildhez társított kulcsértékek azonosításához.

A címkékben bármilyen unicode karaktert `*` `,`használhat, `\`kivéve a , és a. Ezek a karakterek le vannak foglalva. Ha fenntartott karaktert szeretne felvenni, `\{Reserved Character}`el kell menekülnie a használatával.

### <a name="query-key-values"></a>Kulcsértékek lekérdezése

Minden kulcsértéket egyedileg azonosít a kulcsa, `null`valamint egy címke, amely lehet. Az Alkalmazáskonfigurációs tároló kulcsértékeinek lekérdezése minta megadásával. Az App Configuration Store minden olyan kulcsértéket visszaad, amely megfelel a mintának és a hozzájuk tartozó értékeknek és attribútumoknak. Használja a következő kulcsmintákat a REST API-hívásokban az alkalmazás konfigurációjához:

| Kulcs | |
|---|---|
| `key`kimarad, vagy`key=*` | Megfelel az összes billentyűnek |
| `key=abc` | Pontosan egyezteti **az abc** kulcsnevet |
| `key=abc*` | Az **abc-vel** kezdődő kulcsneveket egyezteti |
| `key=abc,xyz` | Megegyezik a kulcsnevek **abc** vagy **xyz**. Legfeljebb öt csv-re korlátozva |

A következő címkemintákat is felveheti:

| Címke | |
|---|---|
| `label`kimarad, vagy`label=*` | Bármely címkének megfelel, amely`null` |
| `label=%00` | Megfelel `null` a címke |
| `label=1.0.0` | Pontosan megegyezik **az 1.0.0** címkén |
| `label=1.0.*` | Az 1.0-val kezdődő címkéket **egyezteti.** |
| `label=%00,1.0.0` | Egyezik `null` a címkékkel vagy **az 1.0.0-val**, amely öt csv-re korlátozódik |

## <a name="values"></a>Értékek

A kulcsokhoz rendelt értékek szintén unicode karakterláncok. Az értékekhez az összes unicode karaktert használhatja. Minden értékhez tartozik egy opcionális, felhasználó által definiált tartalomtípus. Ezzel az attribútummal olyan értékekadatait tárolhatja, amelyek segítik az alkalmazás megfelelő feldolgozását.

Az App Configuration Store-ban tárolt konfigurációs adatok titkosítva vannak az inaktív és az átvitel során. A kulcsok nincsenek titkosítva. Az alkalmazáskonfiguráció nem helyettesíti az Azure Key Vault ot. Ne tárolja benne az alkalmazástitkákat.

## <a name="next-steps"></a>További lépések

* [Adott időpontban készült pillanatképek](./concept-point-time-snapshot.md)  
* [Funkciókezelés](./concept-feature-management.md)  
