---
title: Sablonok
description: Ez a témakör ismerteti a sablonok az Azure notification hubs használatával.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 1ca24a4bf08ecdbe1c1e47a931613144309a04a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="templates"></a>Sablonok
## <a name="overview"></a>Áttekintés
Sablonok engedélyezéséhez adja meg az értesítéseket, azt kéri a formátummal ügyfélalkalmazást. Sablonok használatával, az alkalmazások is valósíthat meg több különböző előnyöket, többek között a következők:

* A platform-független háttér
* Személyre szabott értesítések
* Ügyfélverzió függetlenség
* Egyszerű honosítása

Ez a témakör két részletes példákat a célcsoport-kezelési az eszközök különböző platformokon platform-független értesítések küldéséhez, és személyre szabása minden eszközre szórási értesítési sablonok használatával.

## <a name="using-templates-cross-platform"></a>Sablonok platformfüggetlen használatával
A szabványos leküldéses értesítések küldéséhez módja küldi, minden értesítés, amely küldött, a megadott platform értesítéseket kezelő szolgáltatása (WNS, APNS) a hasznos adatok között. Például APNS riasztást küld, a tartalom nem egy Json-objektum a következő formátumban:

    {"aps": {"alert" : "Hello!" }}

Hasonló bejelentési üzenet küldése egy Windows Áruházbeli alkalmazásra, az XML-forgalma a következőképpen történik:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

Hasonló hasznos adat található MPNS (Windows Phone) és a GCM (Android) platformokhoz hozhat létre.

Ez a követelmény kényszeríti a háttéralkalmazás létrehozásához minden egyes platform különböző hasznos adat található, és hatékonyan miatt a háttérrendszer felelős a bemutató réteg az alkalmazás a része. Egyes okok közé tartoznak a honosítás és grafikus elrendezések (különösen a Windows Áruházbeli alkalmazások, amelyek tartalmazzák a csempék különféle típusú értesítések).

A Notification Hubs sablon funkció lehetővé teszi, hogy egy ügyfélalkalmazás különleges regisztrációk nevű sablon regisztrációk, többek között, a címkék, készlete mellett egy sablon létrehozásához. A Notification Hubs sablon funkció lehetővé teszi, hogy egy ügyfélalkalmazás eszközök társítandó sablonok, hogy telepítések (ajánlott) vagy a regisztráció dolgozik. A fenti példákban hasznos megadott, csak a platformfüggetlen információ a tényleges riasztási üzenete (Hello!). A sablon olyan utasítások az adott ügyfél-alkalmazás regisztrációjának platformfüggetlen üzenetet formázásának módja az értesítési központ. Az előző példában a platform független üzenet egyetlen tulajdonság: **message = Hello!**.

A következő képen a fenti folyamatát mutatja be:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

A sablon az iOS alkalmazás-regisztráció a következőképpen történik:

    {"aps": {"alert": "$(message)"}}

A megfelelő sablon a Windows áruház-ügyfélalkalmazás van:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Figyelje meg, hogy a tényleges üzenet van helyére a kifejezés $(üzenet). Ebben a kifejezésben az értesítési központ arra utasítja, amikor egy üzenetet küld az adott regisztrációs hozhat létre, és a közös érték kapcsolókat a következő üzenetet.

Ha telepítési modell dolgozik, a telepítés "sablon" billentyű rendelkezik több sablonok JSON. Ha alkalmazásregisztrációs modell dolgozik, az ügyfélalkalmazás több regisztrációk hozhat létre ahhoz, hogy több sablon; például egy sablon figyelmeztetések és a csempe frissítések sablonját. Ügyfélalkalmazások adjunk natív regisztrációk (regisztráció nélküli sablonnal) és a sablon regisztráció.

Az értesítési központ minden sablon egy értesítést küld, figyelembe véve, hogy tartoznak-e az azonos ügyfélalkalmazás nélkül. Ez a viselkedés platformfüggetlen értesítések be további értesítések lefordítani használható. Például az értesítési központba ugyanazon platform független üzenet zökkenőmentesen fordítható egy bejelentési értesítés és egy csempe frissítése érdemes figyelembe vennie, hogy a háttér nélkül. Vegye figyelembe, hogy néhány platformon (például iOS) összeomlás több értesítés is érkezett ugyanarra az eszközre, ha elküldi őket egy rövid időn belül.

## <a name="using-templates-for-personalization"></a>Sablonok használata személyre szabása
Egy másik sablonokkal előnye, hogy a nem tudják használni a Notification Hubs regisztrációs megszemélyesítési értesítések végrehajtásához. Vegyük példaként a időjárás-alkalmazást, amely egy csempe, amely a időjárási feltételek megjeleníti egy adott helyen. A felhasználó Celsius vagy Fahrenheit fok, és egyetlen vagy öt nap előrejelzés választhat. Sablonokkal, minden ügyfél az alkalmazástelepítés regisztrálhatja a kötelező formátum (1 napos Celsius, 1 napos Fahrenheit, 5 nap Celsius, 5-nap Fahrenheit), és a backend kötelező kitölteni, ezeket a sablonokat az összes információt tartalmazó egyetlen üzenet küldése (például egy öt nap az előrejelzések Celsius és Fahrenheit fokban megadva).

A sablon az egy egynapos előrejelzés Celsius hőmérsékletek nem az alábbiak szerint:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

Az értesítési központnak küldött üzenet tartalmazza a következő tulajdonságokkal:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Ez a kialakítás használatával a háttér csak egyetlen üzenetet küld anélkül, hogy a felhasználók az adott testreszabási beállítások tárolására. A következő kép bemutatja, ebben a forgatókönyvben:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Hogyan kell regisztrálni a sablonok
A telepítési modell (ajánlott), vagy az alkalmazásregisztrációs modell segítségével sablonok regisztrálni, lásd: [regisztrációs felügyeleti](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Sablon kifejezés nyelve
XML- vagy JSON-dokumentum formátumok sablonok korlátozódnak. Emellett csak elhelyezheti kifejezések adott helyek; például csomópont attribútumok vagy XML-értékek JSON karakterlánc-tulajdonságok értékeit.

Az alábbi táblázat a sablonok engedélyezett nyelv:

| Kifejezés | Leírás |
| --- | --- |
| $(prop) |A megadott nevű egy eseménytulajdonság hivatkozás. A tulajdonságnevek nem nagybetűk között. Ebben a kifejezésben oldja fel a tulajdonságérték szöveg vagy üres karakterlánc, ha a tulajdonság nem található. |
| $(prop, n) |A fenti de a szöveg explicit módon levágva n karakterek, például $(cím, 20) levágja a cím tulajdonság tartalmának: 20 karakter. |
| . (prop, n) |A fenti de három pont utótaggal a szöveg, amelyhez hozzá van kapcsolva. A lerövidített karakterlánc és az utótag teljes mérete nem haladja meg a n karakter. . (cím, 20) és az "Ez a cím sor az" eredményeinek tulajdonsága bemeneti **címe...** |
| %(prop) |Hasonló $(name) azzal a különbséggel, hogy a kimeneti URI-kódolású. |
| #(prop) |A JSON-sablonokban használt (például az iOS és Android sablonok).<br><br>Ez a funkció ugyanúgy működik mint a korábban megadott, kivéve ha szerepel a JSON-sablonokat (például Apple-sablonok) $(prop). Ebben az esetben, ha ez a funkció nem körülvett "{","}" (például "myJsonProperty':"#(név)"), és az eredmény egy számot a Javascript-formátumban, például RegExp szolgáltatást: (0&#124;(&#91;1 – 9&#93;&#91;0-9&#93;*)) (\. &#91;0-9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, akkor a kimenet JSON egy számot.<br><br>Például "jelvény:"#(név)"válik"jelvények": 40 (és nem"40"). |
| "text" vagy "text" |A szövegkonstans. Literálok egyetlen vagy dupla idézőjelek közé tetszőleges szöveget tartalmaznak. |
| Kif1 + Kif2 |A csatlakozás egyetlen karakterlánccá egyesít két kifejezések összefűzése operátor. |

A kifejezés előző űrlapokat bármelyike lehet.

Ha kapott, a teljes kifejezést kell körülvett az {}. Például {$(prop) + '-' + $(prop2)}. |

Például a következő értéke nem egy érvényes XML-sablont:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Amint azt fent összefűzése használatakor kifejezések kell csomagolni, kapcsos zárójelek közé. Példa:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>

