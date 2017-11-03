---
title: "Az Azure Mobile Engagement iOS SDK kibocsátási megjegyzései |} Microsoft Docs"
description: "Legújabb frissítések és az Azure Mobile Engagement SDK iOS eljárásai"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 9bdaa57f9902373ccf796ff109332b64c66bf9e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Az Azure Mobile Engagement iOS SDK kibocsátási megjegyzései

## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* Rögzített jelvények háttérben törölve lett.
* Rögzített figyelmeztetések a XCode 9 kapcsolatos API-k nem hívja a fő várakozási sorba.
* Rögzített memóriavesztés Reach lekérdezéseinél.
* Támogatás az iOS eldobott 6.X. A központi telepítés célja az alkalmazás ebben a verzióban kiindulva kell legalább iOS 7-es.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* Továbbfejlesztett naplózási kézbesítési háttérben.

## <a name="400-09122016"></a>4.0.0 (09/12/2016)
* Rögzített értesítési nem műveletet kiváltó iOS 10-eszközökön.
* XCode 7 érvényteleníthető.

## <a name="324-06302016"></a>3.2.4 (06/30/2016)
* Rögzített összesítési műszaki naplókat, valamint további naplófájlokat között.

## <a name="323-06072016"></a>3.2.3 (06/07/2016)
* A hiba rögzített, ahol kézbesítési visszajelzés jelentése nem alkalmazás esetén a háttérben.
* Optimalizált műszaki naplók küldése.

## <a name="322-04072016"></a>3.2.2 (04/07/2016)
* A HTTP kérelem megszakítása, ami néha összeomlások rögzített hiba.

## <a name="321-12112015"></a>3.2.1 (12/11/2015)
* A késleltetés meghatározni, ha egy új app-példány váltja ki, egy értesítés a mélyhivatkozással

## <a name="320-10082015"></a>3.2.0 (10/08/2015)
* A hatékony SDK Bitcode engedélyezett **Xcode 7**.
* Az alkalmazásbeli értesítésekben kapcsolódó javított hibákról.
* Az alkalmazásbeli értesítések alacsony töltöttségű telepre vonatkozó és egyéb ilyen forgatókönyvek esetén további megbízható végzett.
* 3. fél könyvtár által létrehozott további konzolnaplófájlokban eltávolítva.

## <a name="310-08262015"></a>3.1.0 (08/26/2015)
* Javítsa ki az iOS 9-es kompatibilitási hiba egy harmadik féltől származó könyvtárral. Összeomlások okozott amíg küldése kérdezze le az eredményeket, az alkalmazással kapcsolatos adatok vagy a további adatokat.

## <a name="300-06192015"></a>3.0.0 (06/19/2015)
* A Mobile Engagement csendes leküldéses értesítések használja.
* Támogatás az iOS eldobott 4.X. A központi telepítés célja az alkalmazás ebben a verzióban kiindulva kell legalább iOS 6.

## <a name="220-05212015"></a>2.2.0 (05/21/2015)
* A Mobile Engagement-eszközazonosítót az eszközök < iOS 6 alapján a telepítéskor létrehozott GUID.

## <a name="210-04242015"></a>2.1.0 (04/24/2015)
* A hozzáadott Swift kompatibilitási.
* Kattintva egy értesítést, ha a műveleti URL-cím már jobb végre, az alkalmazás megnyitása után.
* A hozzáadott hiányzó fejlécfájlt az SDK-csomagot.
* Rögzített problémát, ha a Mobile Engagement összeomlási jelentéskészítői le lett tiltva.

## <a name="200-02172015"></a>2.0.0 (02/17/2015)
* Az Azure Mobile Engagement eredeti kiadás
* a kapcsolódási karakterlánc konfigurációs appId/sdkKey konfigurációs cseréli le.
* API-t üzeneteket küldjön és fogadjon tetszőleges XMPP a tetszőleges XMPP entitásokból eltávolítva.
* Az eltávolított API használatával eszközök közötti üzeneteket küldjön és fogadjon.
* Biztonsági fejlesztések.
* SmartAd követési eltávolítva.
