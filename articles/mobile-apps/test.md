---
title: Mobilalkalmazások tesztelése több ezer valódi eszközön a Visual Studio App Center használatával
description: Ismerje meg a Visual Studio App Center szolgáltatását, amely folyamatosan teszteli a mobilalkalmazásokat több ezer valós eszközön.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-0000-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 05fa29e4b79cdbec7d925b2a35bd3fdf0bb03213
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240096"
---
# <a name="perform-automated-testing-on-thousands-of-mobile-devices-simultaneously"></a>Automatizált tesztelés egyidejű végrehajtása több ezer mobileszközön
A tesztelés kihívást jelenthet és egyre összetettebb lehet az eszköz töredezettsége, az alkalmazások összetettsége, a gyors kiadási ciklusok, a rövid munkamenetek és a magas felhasználói elvárások miatt. Rendszeresen meg kell győződnie arról, hogy alaposan tesztelt alkalmazásokat szállít, amelyek nagyszerű felhasználói élményt nyújtanak. A tesztelés nem könnyű feladat, és nem jó a fejlesztői idő használata az egyes kiadások manuális teszteléséhez.

A tesztelési kihívások hatékonyan kezelhetők. A mobilalkalmazásokat az eszközök széles körében tesztelheti automatizált teszteléssel. A felhőszolgáltatások segítségével felgyorsíthatja a mobilalkalmazások kiadását, és leküzdheti azokat a tesztelési kihívásokat, amelyek szűk keresztmetszeteket jelentenek az alkalmazások fejlesztésében és telepítésében.

## <a name="importance-of-automated-testing"></a>Az automatizált tesztelés fontossága
- Növelje a tesztlefedettséget a különböző alaktényezők, a kapcsolattípusok és az operációs rendszer verzióinak célzásával.
- Takarítson meg időt, pénzt és erőforrásokat a különböző eszközökön és az operációs rendszer lefedettségén végzett teszteléssel.
- Az ismétlődő manuális tesztek elkerülésével javíthatja a pontosságot, és több ideje van új automatizált szoftvertesztek létrehozására és az összetett funkciók kezelésére.
- Tesztelje folyamatosan, elemezze az alkalmazások teljesítményét, és keresse meg és javítsa ki a problémákat, mielőtt a felhasználók belefutnának, és gyorsan feloldanák.
- Kiváló minőségű alkalmazásokat biztosítva, biztosítva, hogy ügyfelei az eszközök széles skáláján a lehető legjobb élményben legyenrészek.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center-teszt](/appcenter/test-cloud/) egy felhőalapú szolgáltatás, amelynek segítségével automatizált felhasználói felületi teszteket futtathat a felhőben lévő több ezer valós eszközön az Android és az iOS rendszeren keresztül a natív és hibrid mobilalkalmazásokhoz. Több mint 2000 eszközzel olyan tesztparancsfájlokat írhat, amelyek automatikusan végrehajthatók a tesztelés egyszerűvé tételezése érdekében.

**A legfontosabb jellemzők**
   - Olyan tesztek, amelyek nem gyökerező vagy feltört valós eszközökön futnak, hogy a felhasználók környezetének legjobb közelítését biztosítsák.
   - A képernyőképek automatikusan rögzítésre kerülnek, és minden tesztlépéshez csatolva lesznek. Ezek megtekinthetők egymás mellett az összes célzott eszköz egy teszttávon.
   - Eszköznaplók az összes céleszköz egy tesztfuttatás és verem nyomkövetések sikertelen tesztlépéseket.
   - Az egyidejű tesztelés egyszerre több eszközön fut, hogy felgyorsítsa az eredményekhez való időt.
   - Népszerű teszt keretrendszer támogatása, mint az Appium (Java JUnit) Android és iOS, Espresso (Java) Android, Calabash Android és iOS, Xamarin.UITest Android és iOS, és XCUITest iOS.
   - Platformtámogatás iOS, Android Xamarin és React Native rendszerhez.

**Referencia**
   - [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
   - [Az App Center-teszt első lépései](/appcenter/test-cloud/)