---
title: Az Azure Notification Hubs és a Google Firebase Cloud Messaging (FCM) áttelepítése
description: Ismerteti, hogyan Azure Notification hubs használatával szünteti meg a Google GCM-be való migrálás FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59506402"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Az Azure Notification Hubs és a Google Firebase Cloud Messaging (FCM) áttelepítése

## <a name="current-state"></a>Aktuális állapot

Ha Google jelentettük be az áttelepítést a Google Cloud Messaging (GCM) a Firebase Cloud Messaging (FCM), leküldéses szolgáltatásokat nyújt, mint a miénk kellett állítsa be, hogyan elküldtük az értesítések az Android-eszközök befogadására a módosítást.

Azt a service háttérrendszer frissítve, majd a közzétett frissítéseket az API-hoz és az SDK-k, igény szerint. Az implementáció azt a döntést meglévő GCM értesítési sémák ügyfél gyakorolt hatás minimalizálása érdekében való kompatibilitás megőrzése érdekében. Ez azt jelenti, hogy azt jelenleg értesítések küldése Android-eszközök használatával FCM FCM örökölt módban. Végső soron szeretnénk FCM, beleértve az új funkciók és adattartalom formátuma igaz támogatása. A hosszabb távú változás történik, és az aktuális migrálás arra összpontosít, hogy a meglévő alkalmazások és az SDK-kkal való kompatibilitás megőrzése. A GCM vagy az FCM SDK-k is használható (együtt az SDK-t) az alkalmazásban, és biztosítjuk, hogy az értesítés hibásan küldi el.

Egyes ügyfelek kapcsolatos értesítéseket a GCM-végpont használatával alkalmazásokat a Google figyelmeztetés legutóbb kapott e-mailt. Ez volt, csak figyelmeztetés, és semmi nem működik – az alkalmazás Android-értesítések továbbra is érkeznek a Google feldolgozásra, és a Google továbbra is feldolgozza őket. Egyes ügyfelek, akik a GCM-végpont a szolgáltatás konfigurációjában explicit módon megadott továbbra is használja az elavult endpoint. Azt már korábban azonosított a gap, és dolgozott az e-mail elküldésekor a Google kompatibilitását.

Tudjuk, hogy az elavult endpoint lecseréli a javítást, és telepítve van.

## <a name="going-forward"></a>A jövőben

A Google FCM – gyakori kérdések szerint, nem kell tennie semmit. Az a [FCM – gyakori kérdések](https://developers.google.com/cloud-messaging/faq), Google mondta "ügyféloldali SDK-k és a GCM-jogkivonatokat továbbra is működni fog határozatlan időre. Azonban hogy nem célozhat meg az Android-alkalmazást a Google Play-szolgáltatások legújabb verzióját, kivéve, ha áttelepíti a FCM."

Ha az alkalmazás a GCM-kódtárat használja, lépjen tovább, és kövesse az utasításokat a Google frissítése az alkalmazásban a FCM-erőforrástárhoz. Az SDK, sem, kompatibilis, így nem kell frissíteni, semmit a mi oldalunkon jelentkező az alkalmazásban (feltéve, mindig naprakészek lehetnek az SDK-verzió az Ön).

## <a name="questions-and-answers"></a>Kérdések és válaszok

Íme néhány gyakori kérdéseket hallottuk az ügyfelektől származó válasz:

**K:** Mit kell tennie a Megszakítás dátuma szerint kompatibilis legyen (Google aktuális a Megszakítás dátuma május 29, előfordulhat, hogy módosítani az)?

**V:** Nincs érték. Működik minden meglévő GCM értesítési séma való kompatibilitás érdekében. A GCM-kulcs továbbra is működik, ahogyan bármely GCM SDK-k és tárak az alkalmazása által használt szokásos módon.

Ha úgy dönt, hogy frissítsen az új szolgáltatások előnyeinek FCM SDK-k és tárak, a GCM-kulcs továbbra is működni fognak. Előfordulhat, hogy váltson az FCM-kulcs használatával, ha kívánja, de győződjön meg arról, hozzáadandó Firebase a meglévő GCM-projektet az új Firebase-projekt létrehozásakor. Ez garantálja a visszamenőleges kompatibilitás érdekében az ügyfelekkel, amely futtatja az alkalmazás régebbi verzióinak GCM SDK-k és tárak továbbra is használhatja.

Ha létrehoz egy új FCM-projektet, és nem a meglévő GCM-projekt csatlakoztatása Notification hubs szolgáltatás az FCM új titkos kulcsot a frissítés után lehetővé teszi leküldéses értesítések való elveszíti az aktuális alkalmazások telepítésére, mivel az új FCM-kulcsot a régi GCM kapcsolódás a projekt.

**K:** Miért jelenik meg ez az e-mail használt régi GCM végpontok kapcsolatos? Mit kell tennie?

**V:** Nincs érték. A Microsoft rendelkezik lett áttelepítése az új végpontjaira, és hamarosan kész, így nincs módosítás nem szükséges. Semmi nem működik, az egyik kihagyott végpont egyszerűen okozott figyelmeztető üzenetek a Google.

**K:** Hogyan tudok váltáshoz új FCM SDK-k és tárak a meglévő felhasználók megszakítása nélkül?

V: Frissítse a tetszőleges időpontban. Google még nem jelentette be minden olyan meglévő GCM SDK-k és tárak elavulása. Annak érdekében, ne felosztása a leküldéses értesítések a meglévő felhasználók számára, győződjön meg arról, mikor az új Firebase-projektet, amelyeket társít meglévő GCM-projekt létrehozása. Ez biztosítja a titkos kulcsokat fog működni a felhasználók az alkalmazás régebbi verzióit futtató GCM SDK-k és tárak, valamint az új felhasználók az alkalmazás FCM SDK-k és tárak az új Firebase.

**K:** Mikor használhatok új FCM-szolgáltatások és -sémákat az értesítéseket?

**V:** Miután a frissítés közzétesszük az API-hoz és az SDK-k kínálatot. kövessen bennünket – várhatóan, hogy az Ön számára az elkövetkező hónapokban.
