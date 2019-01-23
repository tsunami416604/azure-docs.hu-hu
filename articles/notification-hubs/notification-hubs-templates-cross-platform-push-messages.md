---
title: Sablonok
description: Ez a témakör ismerteti a sablonokat az Azure notification hubs szolgáltatásban.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450234"
---
# <a name="templates"></a>Sablonok

Sablonok lehetővé teszik, hogy egy ügyfélalkalmazás az értesítéseket szeretné kapni, hogy a pontos adatformátum megadásához. Alkalmazás-sablonok használatával megvalósításához is számos különböző előnnyel jár, többek között a következőket:

- A platform-agnosztikus háttérrendszer
- Személyre szabott értesítések
- Ügyfélverzió függetlenség
- A honosítás egyszerűen

Ez a szakasz példákat két részletes értesítéseket is küldhet platform-agnosztikus eszközeit több platformon, és minden egyes eszközhöz szórási értesítés testreszabása sablonok használatával.

## <a name="using-templates-cross-platform"></a>Platformfüggetlen sablonok használatával

A szabványos módon küldhet leküldéses értesítéseket szeretne küldeni, minden egyes értesítést, amelyet a küldött, egy adott tartalom platformértesítési szolgáltatásoknak (WNS, az APNS) használatával. Ha például az APNS riasztást küld, a hasznos JSON-objektum, a következő formátumot követi:

```json
{"aps": {"alert" : "Hello!" }}
```

A Windows Store alkalmazás hasonló bejelentési üzenet küldéséhez az XML-tartalma a következőképpen történik:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Az MPNS (Windows Phone) és az FCM (Android) platform hozhat létre hasonló is észleltünk adattartalmakat.

Ez a követelmény arra kényszeríti az alkalmazási háttérrendszer különböző is észleltünk adattartalmakat. az egyes platformokra vonatkozó előállításához, és hatékonyan lehetővé teszi a háttérrendszer felelős a megjelenítési réteg az alkalmazás része. Bizonyos problémák közé tartozik a honosítás és a grafikus elrendezések (különösen a csempék különféle típusú értesítések tartalmazó Windows Store apps).

A Notification Hubs sablon funkciója lehetővé teszi, hogy egy ügyfélalkalmazás speciális regisztrációk nevű sablonregisztrációk, többek között, a címkék, készletét mellett egy sablon létrehozásához. A Notification Hubs sablon funkció lehetővé teszi, hogy egy ügyfélalkalmazás eszközök társítása sablonok telepítések (preferált) vagy a regisztrációk dolgozik-e. Adja meg a fenti hasznos példákat, az csak a platformfüggetlen információ áll a tényleges riasztási üzenete (szia!). Egy sablon olyan készlete, megtudhatja, hogyan formázhatja a regisztráció az adott ügyfél alkalmazás platformfüggetlen üzenet az értesítési központ utasításokat. Az előző példában a platformfüggetlen üzenetek egy adott tulajdonságra: `message = Hello!`.

Az alábbi kép illusztrálja a folyamatot:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

A sablon az iOS-alkalmazás regisztráció a következőképpen történik:

```json
{"aps": {"alert": "$(message)"}}
```

A Windows Store ügyfélalkalmazás a megfelelő sablon a következő:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Figyelje meg, hogy a tényleges üzenet helyettesített-e a kifejezés $ (üzenet). Ez a kifejezés az értesítési központ arra utasítja a, amikor egy üzenetet küld a adott regisztrációt, egy üzenet, amely követi, és a közös value kapcsolók létrehozásához.

Ha telepítési modellel dolgozik, a telepítés "sablon" kulcsot tárolja, több sablon egy JSON. Ha a regisztráció modellel dolgozik, az ügyfélalkalmazás több sablon; használatához hozhat létre több regisztrációk például egy sablont a figyelmeztető üzeneteket és a egy sablont a csempe frissül. Ügyfélalkalmazások keverheti is natív regisztrációk (nincs sablonnal regisztrációk) és sablonregisztrációk.

Az értesítési központ minden sablon egy értesítést küld, anélkül, hogy figyelembe véve, hogy ügyfél ugyanahhoz az alkalmazáshoz tartoznak. Ez a viselkedés, platformfüggetlen értesítések be további értesítések lefordítja használható. Például az adott platformtól független üzenet az értesítési központhoz zökkenőmentesen fordíthatók le egy bejelentési értesítés és a egy csempe update anélkül, hogy tudni, hogy a háttérrendszer. Egyes platformokon (például iOS) több értesítés is érkezett összeomlás ugyanarra az eszközre, ha azokat egy rövid idő alatt küldi.

## <a name="using-templates-for-personalization"></a>Sablonok használata személyre szabása

Sablonok használatának másik előnye hajtsa végre a regisztráció személyre szabása, az értesítések a Notification Hubs használatával lehetővé teszi. Vegyük példaként egy időjárás-alkalmazást, amely egy csempe az időjárási viszonyok megjeleníti az adott helyen. A felhasználó választhat Celsius vagy Fahrenheit-fokot, és a egy- vagy ötnapos előrejelzését. A sablonokkal, minden egyes ügyfél-alkalmazás telepítése regisztrálhat a szükséges formátumban (1 napos Celsius, 1 napos Fahrenheit, 5 napos időszakban Celsius, 5 napos időszakban Fahrenheit), és a egy üzenet, amely tartalmazza ezeket a sablonokat töltse ki a szükséges összes információt küld a háttérrendszeri rendelkezik (például egy ötnapos Celsius és Fahrenheit-fokot korrekciókhoz).

Az egynapos előrejelzés a sablon a Celsius hőmérsékletek a következőképpen történik:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Az üzenet az értesítési központhoz a következő tulajdonságokat tartalmazza:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Ez a minta használata esetén a háttérrendszer csak egyetlen üzenetet küld tárolására az alkalmazás felhasználók adott testreszabási beállítások nélkül. A következő kép azt mutatja be, ebben a forgatókönyvben:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Sablonok regisztrálása

A telepítési modell (preferált) vagy az alkalmazásregisztrációs modell segítségével sablonok regisztrálni, lásd: [Regisztrációkezelés](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Sablon kifejezés nyelve

Sablonok korlátozva, XML vagy JSON-dokumentum formátumban. Emellett csak elhelyezheti kifejezések adott helyeken; például a csomópont attribútumait vagy XML-értékeket karakterlánc-tulajdonságértékek JSON.

Az alábbi táblázat a sablonok engedélyezett jelenik meg:

| Kifejezés       | Leírás |
| ---------------- | --- |
| $(prop)          | A megadott nevű vlastnost události hivatkozás. A tulajdonságnevek nem különböznek. Ez a kifejezés megszünteti a tulajdonságérték szöveg vagy üres karakterlánc, ha a tulajdonság nem található. |
| $(prop, n)       | A fenti de a szöveg explicit módon levágva n karakter, például $(title, 20) levágja a cím tulajdonság tartalmát 20 karakter. |
| . (prop, n)       | A fenti de a szöveg van három pontra utótaggal, mivel a keretnél legyen levágva. A lerövidített karakterlánc és az utótag teljes mérete legfeljebb n karakterből állhat. . (title, 20) az "Ez a cím sor az" az eredmények a bemeneti tulajdonsággal rendelkező **címe...** |
| %(prop)          | Hasonló $(name) azzal a különbséggel, hogy a kimenet a URI-ként kódolt. |
| #(prop)          | Használt JSON-sablonok (például az iOS és Android sablonok).<br><br>Ez a függvény pontosan ugyanaz, mint korábban megadott, kivéve, ha a JSON-sablonokat (például az Apple-sablonok) használt $(prop) működik. Ebben az esetben, ha ez a funkció nem övezi "{","}" (például "myJsonProperty": "#(név)"), és az eredmény egy számot a Javascript-formátumban, például regexp: (0&#124;(&#91;1 – 9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, akkor a kimenet JSON értéke egy szám.<br><br>Például "jelvény:"#(név)"válik jelvények": 40 (és nem 40). |
| "szöveg" vagy "szöveg" | Egy konstans. Literálok egyszeres vagy kettős idézőjelek közé tetszőleges szöveget tartalmaznak. |
| Kif1 + Kif2    | A két kifejezések egyetlen karakterlánccá való csatlakozás összefűzési operátor. |

A kifejezések lehetnek az előző űrlapokat.

Összefűzési használata esetén a teljes kifejezésnek kell lennie elemet `{}`. Például: `{$(prop) + ‘ - ’ + $(prop2)}`.

Ha például a következő sablon nem egy érvényes XML-sablont:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Leírtak korábban összefűző használatakor, kifejezések kell csomagolni, kapcsos zárójelek közé. Példa:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
