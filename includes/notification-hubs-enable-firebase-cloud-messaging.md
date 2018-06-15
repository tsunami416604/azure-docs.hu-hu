---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 4dc67175f2ccc569ab6dfe5338607fa6bebe3882
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814696"
---
1. Jelentkezzen be a [Firebase konzoljába](https://firebase.google.com/console/). Hozzon létre egy új Firebase-projektet, ha nem rendelkezik még ilyennel.
2. A projekt létrehozása után válassza az **Add Firebase to your Android app** (A Firebase hozzáadása az Android-alkalmazáshoz) lehetőséget. Ezután kövesse a megjelenő utasításokat. Töltse le a **google-services.json** fájlt. 

    ![Firebase hozzáadása Android-alkalmazáshoz](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. A Firebase konzolján kattintson a projektjéhez tartozó fogaskerékre. Ezután kattintson a **Project Settings** (Projektbeállítások) elemre.

    ![A projektbeállítások kiválasztása](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. A projektbeállítások között válassza a **General** (Általános) lapot. Ezután töltse le a **google-services.json** fájlt, amely a kiszolgáló API-kulcsát és az ügyfél-azonosítót tartalmazza.
5. A projektbeállítások között válassza a **Cloud Messaging** (Felhőalapú üzenetkezelés) lapot, majd másolja a **Legacy server key** (Régi kiszolgálókulcs) értékét. Erre az értékre az értesítési központ hozzáférési szabályzatának konfigurálásához lesz szükség.
