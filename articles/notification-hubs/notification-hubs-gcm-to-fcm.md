---
title: Az Azure Értesítési központok és a Google Firebase Cloud Messaging (FCM) áttelepítése
description: Ez a témakör azt ismerteti, hogy az Azure Notification Hubs hogyan kezeli a Google GCM-től az FCM-áttelepítésig való áttelepítést.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127028"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Az Azure Értesítési központok és a Google Firebase felhőüzenetek áttelepítése

## <a name="current-state"></a>Jelenlegi állapot

Amikor a Google bejelentette, hogy a Google Cloud Messaging (GCM) rendszerről a Firebase Cloud Messaging (FCM) szolgáltatásra vált, a mihez hasonló push szolgáltatásoknak módosítaniuk kellett, hogyan küldtük el az értesítéseket az Android-eszközökre a változás befogadására.

Frissítettük a szolgáltatási háttérrendszerünket, majd szükség szerint közzétettük az API-k és az SDK-k frissítéseit. Megvalósításunkkal úgy döntöttünk, hogy fenntartjuk a kompatibilitást a meglévő GCM értesítési sémákkal, hogy minimalizáljuk az ügyfelek rekegőit. Ez azt jelenti, hogy jelenleg fcm örökölt módban fcm-módban küldünk értesítéseket Android-eszközökre. Végső soron szeretnénk hozzá valódi támogatást FCM, beleértve az új funkciók és hasznos formátumban. Ez egy hosszabb távú változás, és a jelenlegi áttelepítés a meglévő alkalmazásokkal és SDK-kkal való kompatibilitás fenntartására összpontosít. Használhatja a GCM vagy FCM SDK-k az alkalmazásban (valamint a mi SDK), és mi győződjön meg arról, hogy az értesítést küldött helyesen.

Egyes ügyfelek a közelmúltban e-mailt kaptak a Google-tól, amely figyelmeztetést kapott az okról, hogy az alkalmazások GCM-végpontot használnak az értesítésekhez. Ez csak egy figyelmeztetés volt, és semmi sem hibás - az alkalmazás Android értesítéseit továbbra is elküldik a Google-nak feldolgozásra, és a Google továbbra is feldolgozza őket. Egyes ügyfelek, akik kifejezetten megadták a GCM-végpontot a szolgáltatáskonfigurációjukban, még mindig az elavult végpontot használták. Már azonosítottuk ezt a rést, és dolgoztunk a probléma megoldásán, amikor a Google elküldte az e-mailt.

Kicseréltük az elavult végpontot, és a javítás telepítve van.

## <a name="going-forward"></a>Továbbhaladva

A Google FCM GYIK azt mondja, nem kell semmit. Az [FCM GYIK,](https://developers.google.com/cloud-messaging/faq)a Google azt mondta: "ügyfél SDK-k és GCM tokenek továbbra is működik a végtelenségig. Az Android-alkalmazásban azonban csak akkor tudja megcélozni a Google Play-szolgáltatások legújabb verzióját, ha áttér az FCM-re."

Ha az alkalmazás a GCM-könyvtárat használja, kövesse a Google utasításait az alkalmazás FCM-könyvtárára való frissítéshez. Az SDK-nk bármelyikével kompatibilis, így nem kell semmit frissítenie az alkalmazásunkban a mi oldalunkon (feltéve, hogy naprakész az SDK-verzióval).

## <a name="questions-and-answers"></a>Kérdések és válaszok

Íme néhány válasz az ügyfelektől hallott gyakori kérdésekre:

**K:** Mit kell tennem, hogy kompatibilis legyen a cutoff dátum (a Google jelenlegi cutoff dátum május 29-én, és változhat)?

**A.** Semmi. A meglévő GCM értesítési sémával való kompatibilitást fenntartjuk. A GCM-kulcs továbbra is a szokásos módon fog működni, csakúgy, mint az alkalmazás által használt GCM SDK-k és tárak.

Ha/amikor úgy dönt, hogy az FCM SDK-kra és kódtárakra frissít az új funkciók kihasználása érdekében, a GCM-kulcs továbbra is működni fog. Az új Firebase-projekt létrehozásakor átválthat egy FCM-kulcs használatára, de győződjön meg arról, hogy firebase-t ad hozzá a meglévő GCM-projekthez. Ez garantálja a visszamenőleges kompatibilitást az alkalmazás régebbi verzióit futtató ügyfelekkel, amelyek továbbra is GCM SDK-kat és tárakat használnak.

Ha új FCM-projektet hoz létre, és nem kapcsolódik a meglévő GCM-projekthez, az értesítési központok frissítése az új FCM-tokkal, akkor elveszíti az értesítések leküldéselehetőségét a jelenlegi alkalmazástelepítésekre, mivel az új FCM-kulcs nem kapcsolódik a régi GCM-hez Projekt.

**K:** Miért jelenik meg ez az e-mail a régi GCM-végpontokról? Mit kell tennem?

**A.** Semmi. Az új végpontokra költöztünk, és hamarosan befejeződünk, így nincs szükség változtatásra. Semmi sem tört, az egyetlen kihagyott végpontunk egyszerűen figyelmeztető üzeneteket okozott a Google-tól.

**K:** Hogyan válthatok át az új FCM SDK-kra és kódtárakra a meglévő felhasználók feltörése nélkül?

A: Frissítés bármikor. A Google még nem jelentette be a meglévő GCM SDK-k és könyvtárak egesztését. Annak érdekében, hogy ne szakítsa meg a meglévő felhasználók leküldéses értesítéseit, győződjön meg arról, hogy amikor létrehozza a meglévő GCM-projekttel társáliába rendelt új Firebase-projektet. Ez biztosítja, hogy az új Firebase-titkok az alkalmazás régebbi verzióit GCM SDK-kkal és kódtárakkal futtató felhasználók, valamint az alkalmazás fcm SDK-kkal és kódtárakkal rendelkező új felhasználói számára is működjenek.

**K:** Mikor használhatom az új FCM-funkciókat és sémákat az értesítésekhez?

**A.** Miután közzétettük api-ink és SDK-ink frissítését, maradjanak velünk – az elkövetkező hónapokban elvárjuk, hogy legyen valamijük az Ön számára.
