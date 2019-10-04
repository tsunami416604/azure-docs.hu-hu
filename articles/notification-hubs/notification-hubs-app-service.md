---
title: App Service Mobile Apps-integráció
description: Ismerje meg, hogyan működik az Azure Notification Hubs Azure App Service Mobile Apps.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: d6747193b8c82119e45a24e3e4bffc065db14e51
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212159"
---
# <a name="integration-with-app-service-mobile-apps"></a>App Service Mobile Apps-integráció

> [!NOTE]
> A Microsoft elkötelezett a Azure App Service Mobile Apps teljes támogatása mellett, beleértve a legújabb operációsrendszer-kiadás, a hibajavítások, a dokumentációs javítások és a közösségi PR-felülvizsgálatok támogatását. Vegye figyelembe, hogy a termék csapata jelenleg nem fektet semmilyen új funkcióra az Azure Mobile Apps-ban. Nagyra értékeljük a közösségi hozzájárulásokat az Azure Mobile Apps összes területére.

A különböző Azure-szolgáltatásokon elérhető zökkenőmentes és egységes élmény érdekében az [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) a leküldéses értesítések beépített támogatását nyújtja a Notification Hubs használatával. Az [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) jól skálázható, világszerte elérhető, vállalati fejlesztők és rendszerintegrátorok számára készült mobilalkalmazás-fejlesztő platform, amely számos lehetőséget kínál a mobilos fejlesztésekhez.

A Mobile Apps-fejlesztők a következő munkafolyamatoknál használhatják a Notification Hubs szolgáltatást:

1. Eszköz PNS-leírójának lekérése
2. Eszközök regisztrálása a Notification Hubsban a Mobile Apps-ügyfél kényelmesen használható SDK-regisztrációs API-ján keresztül

    > [!NOTE]
    > Vegye figyelembe, hogy a Mobile Apps biztonsági okokból eltávolítja az összes regisztrációs címkét. Közvetlenül a háttérrendszerről használhatja a Notification Hubst a címkék és eszközök társításához.

3. Értesítések küldése az alkalmazás háttérrendszeréből a Notification Hubs használatával

Íme néhány hasznos szolgáltatás a fejlesztők számára, amelyeket ez az integráció tett elérhetővé:

- **Mobile apps ügyfél SDK**-k: Ezek a többplatformos SDK-k egyszerű API-kat biztosítanak a regisztrációhoz, illetve a mobilalkalmazáshoz automatikusan kapcsolódó értesítési központtal való kommunikációhoz. A fejlesztőknek nem kell átrágniuk magukat a Notification Hubs hitelesítő adatain, és egy másik szolgáltatást használniuk.
  - *Leküldés a felhasználónak*: Az SDK-k automatikusan felcímkézik az adott eszközt a Mobile Apps által hitelesített felhasználói azonosítóval, így lehetővé teszik a felhasználóknak küldött leküldéses értesítéseket.
  - *Leküldés az eszközre*: Az SDK-k a Mobile Apps telepítési azonosítót automatikusan GUID-ként használják a Notification Hubsban való regisztrációhoz, így a fejlesztőknek nem kell több szolgáltatásbeli GUID azonosítót fenntartaniuk.
- **Telepítési modell**: A Mobile Apps a Notification Hubs legújabb leküldési modelljével mutatja be az eszközök leküldéses tulajdonságait egy JSON-telepítésben, amely egyszerűen használható, és igazodik a leküldéses értesítési szolgáltatásokhoz.
- **Rugalmasság**: A fejlesztők mindig választhatják a Notification Hubs közvetlen használatát, még az integráció megvalósítása után is.
- **Integrált felhasználói élmény [Azure Portalban](https://portal.azure.com)** : A leküldési funkciót vizuálisan megjelenik a Mobile Appsban, és a fejlesztők könnyedén használhatják a kapcsolódó értesítési központtal a Mobile Appson keresztül.
