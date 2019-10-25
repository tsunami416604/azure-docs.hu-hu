---
title: A leküldéses értesítések fontossága Visual Studio App Center és az Azure Notification Hubs
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek lehetővé teszi a mobil alkalmazások felhasználóinak való részvételt.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 05a9bc6ccd26aaecd3e0bf615880e0543b8604f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795912"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Leküldéses értesítések küldése az alkalmazás felhasználóinak 

Akár fogyasztói, akár vállalati alkalmazásokat készít, azt szeretné, hogy a felhasználók aktívan használják az alkalmazást. Gyakran előfordul, hogy meg szeretné osztani a feltörési híreket, a játék frissítéseit, az izgalmas ajánlatokat, a termékfrissítéseket vagy a felhasználókkal kapcsolatos releváns információkat. Annak érdekében, hogy növelje a felhasználókat és az alkalmazásba való beszerzését, valós időben kell kommunikálni a felhasználókkal.

A leküldéses értesítések gyors és hatékony módot biztosítanak az alkalmazás felhasználóival való kommunikációra. A megfelelő időben elérheti a felhasználókat, és értesítheti őket a kívánt információkról, általában egy mobileszközön lévő előugró ablakban vagy párbeszédpanelen, függetlenül attól, hogy mit csinálnak.

## <a name="value-of-push-notifications"></a>Leküldéses értesítések értéke
A leküldéses értesítések a végfelhasználók és a vállalkozások számára egyaránt biztosítanak értéket.

A vállalatok a következőket tehetik:
- A **közvetlenül a felhasználókkal kommunikálva** üzeneteket küldhet nekik a támogatott platformra a megfelelő időben.
- A felhasználók **részvételének növelésével** valós idejű frissítéseket és emlékeztetőket küldhet a felhasználóknak, és bátoríthatja őket, hogy rendszeres időközönként vegyenek részt az alkalmazásban.
- **Tartsa** meg a felhasználókat, és ismételje meg az alkalmazást letöltő inaktív felhasználókkal, de ne használja újra az aktív állapotba.
- A felhasználói viselkedés elemzésével, a végfelhasználók szegmentálásával és a kampányok a mobil leküldéses értesítések alapján történő kihasználásával **növelheti a konverziós arányt** .
- **Termékek és szolgáltatások reklámozása** leküldéses üzenetek és időben történő frissítések küldésével a felhasználók számára.
- **A felhasználók megcélzása** személyre szabott leküldéses üzenetek küldésével.

Alkalmazás felhasználóinak leküldéses értesítések:
- Valós időben adja meg az **értéket és az információt** .
- **Emlékeztesse a felhasználókat** az alkalmazás használatára.

A következő szolgáltatások segítségével engedélyezheti a leküldéses értesítéseket a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center leküldéses](/appcenter/push/) szolgáltatás lehetővé teszi a felhasználók számára, hogy az iOS-, Android-és Windows-felhasználók számára megcélozt üzeneteket küldjön, anélkül, hogy a leküldéses Notification Services (PNS) használatával kellene kezelnie az értesítések küldésének folyamatát. Az Azure Notification Hubs-ra épülő szolgáltatás a hatékony irányítópult megadásával kiküszöböli az értesítések manuális leküldésével kapcsolatos bonyodalmakat.

**Főbb funkciók**
- **Leküldéses értesítések küldése a mobileszközök számára** különböző platformokon.
- Értesítéseket használhat az alkalmazásba való adatküldéshez, a felhasználónak küldött üzenet megjelenítéséhez vagy az alkalmazás által indított művelet elindításához.
- Értesítési célok: 
    - Szórási üzenetek küldése az **összes regisztrált eszköznek**.
    - Értesítések küldése az eszköz információi és egyéni tulajdonságok alapján létrehozott **célközönségeknek** .
    - Értesítések küldése **adott felhasználóknak**.
    - Értesítések küldése **adott eszközökre**.
- A leküldéses, az eszközön és a hibán alapuló **telemetria** app Center-portálon érhetők el.
- **Platform-támogatás** – iOS, Android, MacOS, Xamarin, inaktív, Unity, Cordova.

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center leküldéssel](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) egy könnyen használható és méretezhető leküldéses motort biztosít, amely lehetővé teszi, hogy az értesítéseket bármilyen platformra és bármilyen háttérből (felhőből vagy helyszíni) küldje.

**Főbb funkciók**
- **Leküldéses értesítések küldése** bármilyen platformra bármilyen háttérből, a felhőben vagy a helyszínen.
- **Gyors szórásos** küldés több millió mobil eszközre egyetlen API-hívással.
- Leküldéses értesítések kiszabása az ügyfél, a nyelv és a hely alapján.
- Az **ügyfelek szegmensének**dinamikus definiálása és értesítése.
- A **méretezést azonnal** elvégzi több millió mobileszköz számára.
- **Platform-támogatás** – iOS, Android, Windows, Kindle, Baidu.
        
**Hivatkozik**
- [Azure Portalra](https://portal.azure.com) 
- [Ismerkedés az Azure Notification Hubs](/azure/notification-hubs/)   
- [Rövid útmutatók](/azure/notification-hubs/create-notification-hub-portal)
- [Minták](/azure/notification-hubs/samples)
