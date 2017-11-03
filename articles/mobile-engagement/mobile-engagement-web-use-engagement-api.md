---
title: Az Azure Mobile Engagement webes SDK API-k |} Microsoft Docs
description: "A legújabb frissítéseket és a webszolgáltatási SDK az Azure Mobile Engagement eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 54c22ce6a03e382b1bbde102bccc97deec249b30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>A webalkalmazás az Azure Mobile Engagement API használata
Ez a dokumentum kiegészítése, a dokumentumot, amely azt ismerteti, hogyan a [integrálja a Mobile Engagement egy webalkalmazásban](mobile-engagement-web-integrate-engagement.md). Azt ismerteti, hogyan használható az Azure Mobile Engagement API jelentést az alkalmazás statisztikái részletes részleteit.

A Mobile Engagement API által biztosított a `engagement.agent` objektum. Az Azure Mobile Engagement webes SDK alias van alapértelmezett `engagement`. Ez az alias az SDK-konfigurációból is definiálja újra.

## <a name="mobile-engagement-concepts"></a>Mobile Engagement – fogalmak
A következő részekből pontosítsa közös [Mobile Engagement – fogalmak](mobile-engagement-concepts.md) a webes platform jöhet létre.

### <a name="session-and-activity"></a>`Session` és `Activity`
Ha a felhasználó két tevékenység között csak néhány másodpercig inaktív marad, a felhasználó elvégzendő tevékenységek sorrendje a oszlik két különböző munkamenetek. Ezek néhány másodpercet a munkamenet időkorlátja nevezzük.

Ha a webes alkalmazás önmagában nem deklarálható felhasználói tevékenység végén (meghívásával a `engagement.agent.endActivity` függvény), a Mobile Engagement-kiszolgáló automatikusan a felhasználói munkamenet az alkalmazás lap bezárása után három percen belül lejár. Ez a lehetőség a kiszolgálói munkamenet időkorlátja.

### `Crash`
Nem kezelt JavaScript-kivételeknek az automatizált jelentések nem jönnek létre, alapértelmezés szerint. Összeomlások jelentést azonban manuálisan használatával a `sendCrash` (lásd a jelentéskészítő összeomlik) működik.

## <a name="reporting-activities"></a>Jelentéskészítési tevékenység
Felhasználói tevékenység Reporting tartalmazza, amikor a felhasználó elindít egy új tevékenységet, és amikor a felhasználó az aktuális tevékenység befejeződik.

### <a name="user-starts-a-new-activity"></a>Felhasználó elindítja az új tevékenység
    engagement.agent.startActivity("MyUserActivity");

Meg kell hívnia `startActivity()` minden felhasználói tevékenység változik. Ez a függvény az első hívás új felhasználói munkamenet indítása.

### <a name="user-ends-the-current-activity"></a>Felhasználói karakterlánccal végződik-e a jelenlegi tevékenység
    engagement.agent.endActivity();

Meg kell hívnia `endActivity()` legalább egyszer amikor a felhasználó befejezte a legutolsó tevékenység. Ebben értesíti a Mobile Engagement webes SDK-t, hogy a felhasználó jelenleg inaktív-e, és, hogy kell-e a felhasználói munkamenet időtúllépését lejárata után is be kell zárni. Ha meghívja a `startActivity()` előtt a munkamenet időkorlátja lejár, egyszerűen folytatni a munkamenetet.

Mivel a nem megbízható meghívja a Navigátor ablak lezárt, akkor gyakran nehéz vagy lehetetlen dolgozza fel a felhasználói tevékenységek egy webes környezeten belül végén. Ezért a Mobile Engagement-kiszolgáló automatikusan a felhasználói munkamenet az alkalmazás lap bezárása után három percen belül lejár.

## <a name="reporting-events"></a>Jelentési eseményeket
Jelentési események foglalkozik, munkamenet és önálló események.

### <a name="session-events"></a>Munkamenet-események
Munkamenet-események általában jelentésére használhatók a felhasználói munkamenet során a felhasználó által végrehajtott műveletekről.

**További adatok nélkül. példa:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Példa a további adatokat:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Önálló események
Munkamenet-események, eltérően önálló események a munkameneten kívül is előfordulhatnak.

Ezzel ``engagement.agent.sendEvent`` helyett ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Hibát jelentett
Hibákkal Reporting munkamenet hibák és önálló hibákat tartalmazza.

### <a name="session-errors"></a>Munkamenet-hibák
Munkamenet-hibák általában jelentésére használhatók a hibákat, amelyek hatással vannak a felhasználó a felhasználói munkamenet során.

**További adatok nélkül. példa:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Példa a további adatokat:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Önálló hibák
Munkamenet hibák eltérően önálló hiba akkor fordulhat elő, a munkameneten kívül.

Ezzel `engagement.agent.sendError` helyett `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Feladatok jelentése
Jelentés a jelentéskészítési hibák és események, a feladatok során felmerülő, és az összeomlások feladatokat tartalmazza.

**Példa**

Ha azt szeretné, az AJAX-kérelmek figyeléséhez, használja a következő:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>A feladat során hibát jelentett
Hibák a jelenlegi felhasználói munkamenetet ahelyett, hogy egy futó feladat kapcsolódhat.

**Példa**

Ha szeretne jelentést készíteni a hiba, ha egy AJAX-kérelem sikertelen:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>A feladat során jelentési eseményeket
A jelenlegi felhasználói munkamenetet ahelyett, hogy egy futó feladat események kapcsolódhat köszönet a következőknek a `engagement.agent.sendJobEvent` függvény.

Ez a funkció ugyanúgy működik, mint `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Összeomlások Reporting
Használja a `sendCrash` működnek, mint a jelentés manuálisan összeomlik.

A `crashid` argumentum egy karakterlánc, amely azonosítja a összeomlási típusú.
A `crash` általában argumentum egy karakterlánc összeomlási veremkiíratást.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>További paraméterek
Tetszőleges adatok csatolhat egy esemény, a hiba, a tevékenység vagy a feladatot.

Lehet, hogy az az adatokat bármely JSON-objektum (de nem tömb vagy egyszerű típusú).

**Példa**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Korlátok
Kiegészítő paraméterekkel vonatkozó határértékeket területein reguláris kifejezéseket a kulcsokat, értéktípusokat és méretét.

#### <a name="keys"></a>Kulcsok
Az objektum minden egyes kulcsot meg kell egyeznie a következő reguláris kifejezésnek:

    ^[a-zA-Z][a-zA-Z_0-9]*

Ez azt jelenti, hogy a kulcsokat legalább egy betűvel kell kezdődnie követ betűk, számok és aláhúzásjelek (\_).

#### <a name="values"></a>Értékek
Értékek: karakterlánc, szám és logikai típusú korlátozott.

#### <a name="size"></a>Méret
Kiegészítő funkciók korlátozódnak 1024 karakter / hívás (miután a Mobile Engagement webes SDK kódolja a JSON-ban).

## <a name="reporting-application-information"></a>Jelentéskészítési alkalmazással kapcsolatos adatok
Manuálisan jelentheti a nyomkövetési adatokat (vagy bármely más alkalmazás-specifikus adatait) használatával a `sendAppInfo()` függvény.

Vegye figyelembe, hogy ezt az információt elküldi Növekményesen. A megadott kulcs csak a legutóbbi értékét egy adott eszközhöz tárolni fogja.

Esemény kiegészítő funkciók, például a JSON-objektum segítségével absztrakt alkalmazással kapcsolatos adatok. Vegye figyelembe, hogy a tömb, vagy az alárendelt objektumok számít-e egyszerű karakterlánc (JSON-szerializálás).

**Példa**

Íme egy kódminta küldéséhez, a felhasználó nem és születési dátuma:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Korlátok
Alkalmazással kapcsolatos adatok vonatkozó határértékeket területein reguláris kifejezéseket a kulcsokat és méretét.

#### <a name="keys"></a>Kulcsok
Az objektum minden egyes kulcsot meg kell egyeznie a következő reguláris kifejezésnek:

    ^[a-zA-Z][a-zA-Z_0-9]*

Ez azt jelenti, hogy a kulcsokat legalább egy betűvel kell kezdődnie követ betűk, számok és aláhúzásjelek (\_).

#### <a name="size"></a>Méret
Az alkalmazásadatok korlátozódik 1024 karakter / hívás (miután a Mobile Engagement webes SDK kódolja a JSON-ban).

A fenti példában az a kiszolgálóra küldött JSON-ja 44 karakter:

    {"birthdate":"1983-12-07","gender":"female"}
