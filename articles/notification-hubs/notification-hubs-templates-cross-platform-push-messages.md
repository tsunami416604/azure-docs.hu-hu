---
title: Azure Értesítési központok sablonjai
description: Ismerje meg az Azure Értesítési központok sablonjainak használatát.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263744"
---
# <a name="templates"></a>Sablonok

A sablonok lehetővé teszik, hogy az ügyfélalkalmazás pontosan megadja a fogadni kívánt értesítések pontos formátumát. A sablonok használatával az alkalmazások számos különböző előnyt érhetnek el, többek között a következőket:

- Platformfüggetlen háttér
- Személyre szabott értesítések
- Ügyfélverzió függetlensége
- Egyszerű lokalizáció

Ez a szakasz két részletes példát mutat be arra, hogyan használhat sablonokat platformfüggetlen értesítések küldésére, amelyek az összes eszközt platformon keresztül célozzák meg, és hogyan szabhatja személyre a szórásos értesítéseket az egyes eszközökre.

## <a name="using-templates-cross-platform"></a>Sablonok használata platformfüggetlen

A leküldéses értesítések küldésének szokásos módja, hogy minden egyes elküldendő értesítéshez küldjön egy adott hasznos terhet a platformértesítési szolgáltatásoknak (WNS, APNS). Ha például riasztást szeretne küldeni az APNS-nek, a hasznos adat a következő űrlap JSON-objektuma:

```json
{"aps": {"alert" : "Hello!" }}
```

Ha hasonló bejelentési üzenetet szeretne küldeni egy Windows Áruházbeli alkalmazásban, az XML-tartalom a következő:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Hasonló hasznos adatokat hozhat létre MPNS (Windows Phone) és FCM (Android) platformokhoz.

Ez a követelmény arra kényszeríti az alkalmazás háttér-létrehozásához különböző hasznos adatokat az egyes platformok, és hatékonyan teszi a háttérrendszer felelős része a megjelenítési réteg az alkalmazás. Egyes aggályok közé tartozik a honosítás és a grafikus elrendezések (különösen a Különböző típusú csempékre vonatkozó értesítéseket tartalmazó Windows Áruházbeli alkalmazások esetében).

Az Értesítési központok sablonfunkció lehetővé teszi, hogy az ügyfélalkalmazás speciális regisztrációkat, úgynevezett sablonregisztrációkat hozzon létre, amelyek a címkék készletén kívül egy sablont is tartalmaznak. Az Értesítési központok sablonfunkció lehetővé teszi, hogy az ügyfélalkalmazás hozzátársítsa az eszközöket a sablonokhoz, függetlenül attól, hogy telepítésekkel (előnyben részesített) vagy regisztrációkkal dolgozik.The Notification Hubs template feature enables a client app to associate devices with templates whether you are working with Installations (preferred) or Registrations. Az előző hasznos adatra vonatkozó példákat figyelembe véve az egyetlen platformfüggetlen információ a tényleges figyelmeztető üzenet (Hello!). A sablon az értesítési központ utasítása aplatformfüggetlen üzenetek formálásához az adott ügyfélalkalmazás regisztrálására. Az előző példában a platformfüggetlen üzenet egyetlen `message = Hello!`tulajdonság: .

Az alábbi képen a folyamat látható:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Az iOS-ügyfélalkalmazás-regisztráció sablonja a következő:

```json
{"aps": {"alert": "$(message)"}}
```

A Windows Áruházbeli ügyfélalkalmazás megfelelő sablonja:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Figyelje meg, hogy a tényleges üzenet a $(message) kifejezést helyettesíti. Ez a kifejezés arra utasítja az értesítési központot, amikor üzenetet küld az adott regisztrációnak, hogy hozzon létre egy üzenetet, amely követi azt, és átvált a közös értékre.

Ha a telepítési modellel dolgozik, a telepítési "sablonok" kulcs több sablonJSON-t tartalmaz. Ha a Regisztrációs modellel dolgozik, az ügyfélalkalmazás több regisztrációt is létrehozhat több sablon használatához; például egy figyelmeztető üzeneteksablon és egy sablon a csempefrissítésekhez. Az ügyfélalkalmazások a natív regisztrációkat (sablon nélküli regisztrációkat) és a sablonregisztrációkat is keverhetik.

Az értesítési központ minden sablonhoz egy értesítést küld anélkül, hogy figyelembe venné, hogy ugyanahhoz az ügyfélalkalmazáshoz tartoznak-e. Ez a viselkedés platformfüggetlen értesítések további értesítésekké fordítására használható. Például ugyanazt a platformfüggetlen üzenetet az értesítési központ zökkenőmentesen lefordítani egy pirítós riasztást, és egy csempe frissítés, anélkül, hogy a háttérrendszer, hogy tudatában legyenek. Egyes platformok (például az iOS) összecsukhatnak több értesítést ugyanarra az eszközre, ha rövid időn belül elküldik őket.

## <a name="using-templates-for-personalization"></a>Sablonok használata személyre szabáshoz

A sablonok használatának másik előnye, hogy az értesítési központok segítségével végezheti el az értesítések regisztrációnkénti személyre szabását. Vegyünk például egy időjárás-alkalmazást, amely egy adott helyen az időjárási körülményeket tartalmazó csempét jelenít meg. A felhasználó választhat Celsius- vagy Fahrenheit-fok, valamint egy- vagy ötnapos előrejelzés között. Sablonok használatával minden ügyfélalkalmazás telepítése regisztrálhat a szükséges formátumra (1 napos Celsius, 1 napos Fahrenheit, 5 nap Celsius, 5 nap Fahrenheit), és a háttérrendszer egyetlen üzenetet küld, amely tartalmazza a sablonok kitöltéséhez szükséges összes információt (például egy ötnapos előrejelzés Celsius és Fahrenheit-fok).

A Celsius-hőmérséklettel rendelkező egynapos előrejelzés sablonja a következő:

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

Az értesítési központnak küldött üzenet a következő tulajdonságokat tartalmazza:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Ezzel a mintával a háttérrendszer csak egyetlen üzenetet küld anélkül, hogy az alkalmazás felhasználóinak adott személyre szabási beállításait tárolnia kellene. Az alábbi képen ez a forgatókönyv látható:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Sablonok regisztrálása

A telepítési modell (előnyben részesített) vagy a regisztrációs modell használatával történő sablonokkal való regisztrációról a Regisztráció kezelése című [témakörben](notification-hubs-push-notification-registration-management.md)olvashat.

## <a name="template-expression-language"></a>Sablonkifejezés nyelve

A sablonok XML- vagy JSON-dokumentumformátumokra korlátozódnak. Emellett csak bizonyos helyeken helyezhet el kifejezéseket; például az XML csomópontattribútumai vagy értékei, a JSON karakterlánctulajdonság-értékei.

Az alábbi táblázat a sablonokban engedélyezett nyelvet mutatja be:

| Kifejezés       | Leírás |
| ---------------- | --- |
| $(prop)          | Hivatkozás a megadott nevű eseménytulajdonságra. A tulajdonságnevek nem érzékenyek a kis- és nagybetűkre. Ez a kifejezés a tulajdonság szöveges értékébe vagy üres karakterláncba oldódik fel, ha a tulajdonság nincs jelen. |
| $(prop, n)       | Mint fent, de a szöveg kifejezetten levan vágva n karakterek, például $(cím, 20) klipek tartalmát a cím tulajdonság 20 karakter. |
| . (kellék, n)       | Mint fent, de a szöveg van utótag -val három pöttyök mint ez vág. A levágott karakterlánc és utótag teljes mérete nem haladja meg az n karaktereket. . (title, 20) egy input tulajdonsága "Ez a címsor" eredmények **Ez a cím ...** |
| %(prop)          | Hasonló a $(name) értékhez, azzal a különbséggel, hogy a kimenet URI-kódolású. |
| #(prop)          | JSON-sablonokban (például iOS és Android sablonokesetén).<br><br>Ez a függvény pontosan ugyanúgy működik, mint a korábban megadott $(prop), kivéve, ha JSON-sablonokban (például Apple-sablonokban) használja. Ebben az esetben, ha ezt a függvényt nem veszi körül "{',""}" (például "myJsonProperty" : '#(name)"), és javascript formátumú számot ad ki, például regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, akkor a kimeneti JSON egy szám.<br><br>Például a "jelvény: "#(name)" lesz a "jelvény" : 40 (és nem "40"). |
| "szöveg" vagy "szöveg" | Szó szerint. A konstansok tetszőleges, egy- vagy dupla idézőjelek közé zárt szöveget tartalmaznak. |
| Kif1 + kif2    | Az összefűző operátor két kifejezést egyetlen karakterláncba köt. |

A kifejezések az előző űrlapok bármelyike lehetnek.

Összefűzés esetén a teljes kifejezést a `{}`használatával kell körülvenni. Például: `{$(prop) + ‘ - ’ + $(prop2)}`.

A következő sablon például nem érvényes XML-sablon:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Amint azt korábban kifejtettük, az összefűzés során a kifejezéseket göndör zárójelbe kell csomagolni. Példa:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>További lépések

[További információ az Azure értesítési központokról](notification-hubs-push-notification-overview.md)