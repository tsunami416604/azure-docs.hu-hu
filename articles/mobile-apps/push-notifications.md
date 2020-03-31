---
title: A leküldéses értesítések fontossága a mobilalkalmazásokban a Visual Studio App Center és az Azure Értesítési központok segítségével
description: Ismerje meg az olyan szolgáltatásokat, mint például a Visual Studio App Center, amelynek segítségével kapcsolatba léphet a mobilalkalmazás-felhasználókkal.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: bc59857cdb843dfff54e69f2226f13cfe70df8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235334"
---
# <a name="engage-with-your-application-users-by-sending-push-notifications"></a>Lépjen kapcsolatba az alkalmazás felhasználóival leküldéses értesítések küldésével

Akár fogyasztói, akár vállalati alkalmazásokat hoz, azt szeretné, hogy a felhasználók aktívan használják az alkalmazást. Gyakran előfordul, hogy meg szeretné osztani a legfrissebb híreket, játékfrissítéseket, izgalmas ajánlatokat, termékfrissítéseket vagy bármilyen más releváns információt a felhasználókkal. A felhasználókkal való elköteleződés növeléséhez és az alkalmazáshoz való visszatéréshez szükséges módon valós időben kell kommunikálnia a felhasználókkal.

A leküldéses értesítések gyors és hatékony módot biztosítanak az alkalmazás felhasználóival való kommunikációra. A megfelelő időben felveheti a felhasználókat, és értesítheti őket a kívánt információkról, általában egy mobileszközön lévő előugró elemben vagy párbeszédpanelen, függetlenül attól, hogy mit csinálnak.

## <a name="value-of-push-notifications"></a>Leküldéses értesítések értéke
A leküldéses értesítések értéket biztosítanak a felhasználók és a vállalkozások számára.

A vállalkozások:
- Közvetlenül kommunikáljon a felhasználókkal a támogatott platformokon a megfelelő időben küldött üzenetekkel.
- Növelje a felhasználói elköteleződést azáltal, hogy valós idejű frissítéseket és emlékeztetőket küld a felhasználóknak, és arra ösztönzi őket, hogy rendszeresen vegyenek részt az alkalmazással.
- Őrizze meg a felhasználókat, és vegye fel újra az alkalmazást letöltött, de ne használja azt, hogy újra aktívvá váljon.
- Növelje a konverziós arányokat a felhasználói viselkedés elemzésével, a felhasználók szegmentálásával és a mobilleküldéses értesítések alapján a kampányok kihasználásával.
- Termékeit és szolgáltatásait leküldéses üzenetek küldésével és a felhasználók nak történő időben történő frissítéssel népszerűsítheti.
- A felhasználókat személyre szabott leküldéses üzenetek küldésével célozhatja meg.

Alkalmazásfelhasználók számára leküldéses értesítések:
- Adjon értéket és információt valós időben.
- Emlékeztesse a felhasználókat az alkalmazás használatára.

Az alábbi szolgáltatásoksegítségével engedélyezheti a leküldéses értesítéseket a mobilalkalmazásokban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
Az [App Center Push](/appcenter/push/)segítségével célzott üzeneteket küldhet iOS, Android és Windows rendszerű felhasználóknak anélkül, hogy a leküldéses értesítési szolgáltatások (PNS) használatával kellene kezelnie az értesítések eszközökre történő küldésének folyamatát. Az Azure Értesítési központokra épülő szolgáltatás hatékony irányítópult biztosításával kiküszöböli a manuális leküldéses értesítések hez kapcsolódó bonyodalmakat.

**A legfontosabb jellemzők**
- Leküldéses értesítéseket küldhet mobileszközökre különböző platformokon.
- Az értesítésekkel adatokat küldhet egy alkalmazásnak, üzenetet jeleníthet meg a felhasználónak, vagy aktiválhatja az alkalmazás műveletét.
- Értesítési célok használatával: 
    - Üzenetek közvetítése az összes regisztrált eszközre.
    - Értesítésekküldése a közönségeknek az eszközadatok és az egyéni tulajdonságok alapján.
    - Értesítések küldése adott felhasználóknak.
    - Értesítések küldése adott eszközökre.
- Használja ki a gazdag telemetriai adatok at leküldések, eszközök és hibák, amelyek az App Center portálon elérhető.
- Platformtámogatás az iOS, Android, macOS, Xamarin, React Native, Unity és Cordova rendszerhez.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Az App Center push alkalmazásokkal való ismerkedése](/appcenter/push/)

## <a name="azure-notification-hubs"></a>Azure Notification Hubs
[Az Értesítési központok](/azure/notification-hubs/notification-hubs-push-notification-overview) könnyen használható és kicsinyített leküldéses motort biztosít. Segítségével értesítéseket küldhet bármely platformra és a felhőben vagy a helyszíni háttértartalékból.

**A legfontosabb jellemzők**
- Leküldéses értesítésekküldéses értesítéseket küldhet bármely platformra bármely háttértartalékból, a felhőből vagy a helyszíni adatokból.
- Egyetlen API-hívással gyors szórásküldése több millió mobileszközre.
- Személyre szabhatja a leküldéses értesítéseket az ügyfél, a nyelv és a hely szerint.
- A vevői szegmensek dinamikus meghatározása és értesítése.
- Azonnal több millió mobileszközre méretezhető.
- Platformtámogatás az iOS, Android, Windows, Kindle és Baidu rendszerhez.
        
**Referencia**
- [Azure-portál](https://portal.azure.com) 
- [Ismerkedés az Azure Értesítési központtal](/azure/notification-hubs/)
- [Rövid útmutatók](/azure/notification-hubs/create-notification-hub-portal)
- [Minták](/azure/notification-hubs/samples)
