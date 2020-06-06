---
title: A leküldéses értesítések fontossága Visual Studio App Center és az Azure Notification Hubs
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a Visual Studio App Center, amelyekkel felhasználhatja a mobil alkalmazás felhasználóit.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: c9ae5e268066e498b3a341a9c54391ee5d15d592
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450653"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Leküldéses értesítések küldése az alkalmazás felhasználóinak

Akár fogyasztói, akár vállalati alkalmazásokat készít, azt szeretné, hogy a felhasználók aktívan használják az alkalmazást. Gyakran előfordul, hogy meg szeretné osztani a feltört híreket, a játékok frissítéseit, az izgalmas ajánlatokat, a termékfrissítéseket, vagy bármely más releváns információt a felhasználókkal. A felhasználók és az alkalmazáshoz való visszatérés megnövekedéséhez a felhasználóknak valós időben kell kommunikálni a felhasználókkal.

A leküldéses értesítések gyors és hatékony módot biztosítanak az alkalmazás felhasználóival való kommunikációra. A megfelelő időben elérheti a felhasználókat, és értesítheti őket a kívánt információkról, általában egy előugró elemben vagy egy mobileszközön, függetlenül attól, hogy mit csinálnak.

## <a name="value-of-push-notifications"></a>Leküldéses értesítések értéke
A leküldéses értesítések értéket biztosítanak a felhasználóknak és a vállalkozásoknak.

A vállalatok a következőket tehetik:
- A megfelelő időben a támogatott platformokon küldött üzenetek küldésével közvetlenül kommunikálhat a felhasználókkal.
- A felhasználók bevonásának növelésével valós idejű frissítéseket és emlékeztetőket küldhet a felhasználóknak, és arra bátoríthatja őket, hogy rendszeresen vegyenek részt az alkalmazásban.
- Megtarthatja a felhasználókat, és újból felhasználhatja az alkalmazást letöltő inaktív felhasználókat, de ne használja újra az aktív állapotba.
- A felhasználói viselkedés elemzésével, a felhasználók szegmentálásával és a kampányok a mobil leküldéses értesítések alapján történő kihasználásával növelheti a konverziós arányt.
- Termékek és szolgáltatások reklámozása leküldéses üzenetek és időben történő frissítések küldésével a felhasználók számára.
- Személyre szabott leküldéses üzenetek küldésével megcélozhatja a felhasználókat.

Alkalmazás felhasználóinak leküldéses értesítések:
- Valós időben adja meg az értéket és az információt.
- Emlékeztesse a felhasználókat az alkalmazás használatára.

A következő szolgáltatások segítségével engedélyezheti a leküldéses értesítéseket a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center leküldéses](/appcenter/push/)szolgáltatással az iOS-, Android-és Windows-felhasználók számára megcélozható üzeneteket küldhet anélkül, hogy a leküldéses értesítési szolgáltatások (PNS) használatával kellene kezelnie az értesítések küldésének folyamatát. Az Azure Notification Hubs-ra épülő szolgáltatás a hatékony irányítópult megadásával kiküszöböli az értesítések manuális leküldésével kapcsolatos bonyodalmakat.

**A legfontosabb jellemzők**
- Leküldéses értesítések küldése a mobileszközök számára különböző platformokon.
- Értesítéseket használhat az alkalmazásba való adatküldéshez, a felhasználónak küldött üzenet megjelenítéséhez vagy az alkalmazás által indított művelet elindításához.
- Értesítési célok használata a következőhöz: 
    - Üzenetek küldése az összes regisztrált eszközre.
    - Értesítések küldése a célközönségnek az eszköz adatai és az egyéni tulajdonságok alapján.
    - Értesítések küldése adott felhasználóknak.
    - Értesítések küldése adott eszközökre.
- Használja ki a App Center portálon elérhető leküldések, eszközök és hibák gazdag telemetria.
- Az iOS, az Android, a macOS, a Xamarin, a Native, az Unity és a Cordova platform támogatását veheti igénybe.

**Referencia**
- [Regisztráció Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center leküldéssel](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
A [Notification Hubs](/azure/notification-hubs/notification-hubs-push-notification-overview) egy könnyen használható és méretezhető leküldéses motort biztosít. A használatával értesítéseket küldhet bármilyen platformra, illetve a felhőben vagy a helyszínen lévő összes háttérből is.

**A legfontosabb jellemzők**
- Leküldéses értesítések küldése bármilyen platformra bármilyen háttérből, a felhőben vagy a helyszínen.
- Gyors szórásos küldés több millió mobil eszközre egyetlen API-hívással.
- Leküldéses értesítések kiszabása az ügyfél, a nyelv és a hely alapján.
- Az ügyfelek szegmensének dinamikus definiálása és értesítése.
- A méretezést azonnal elvégzi több millió mobileszköz számára.
- Platformfüggetlen támogatás iOS, Android, Windows, Kindle és Baidu platformokhoz.
        
**Referencia**
- [Azure Portal](https://portal.azure.com) 
- [Ismerkedés az Azure Notification Hubs](/azure/notification-hubs/)
- [Rövid útmutatók](/azure/notification-hubs/create-notification-hub-portal)
- [Példák](/azure/notification-hubs/samples)
