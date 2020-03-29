---
title: Probléma egy nem galéria alkalmazás hozzáadása | Microsoft dokumentumok
description: Ismerje meg azokat a gyakori problémákat, amelyekkel az emberek szembesülnek, amikor egyéni, nem galériaalkalmazásokat adnak hozzá
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38a9ef04389318d3588649117c930ff6efa3fe4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784478"
---
# <a name="problem-adding-a-non-gallery-application"></a>Probléma nem galériaalkalmazás hozzáadásakor

Ez a cikk segít megérteni azokat a gyakori problémákat, amelyekkel az emberek szembesülnek **az egyéni, nem galériaalkalmazások** hozzáadásakor, és hogy mit tehet azok megoldása érdekében. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Rákattintottam a "hozzáadás" gombra, és az alkalmazásom hosszú időt vett igénybe, hogy megjelenjen

Bizonyos körülmények között 1-2 percet (és néha hosszabb időt) is igénybe vehet, amíg egy alkalmazás megjelenik, miután hozzáadja a könyvtárhoz. Bár ez nem a szokásos várt teljesítmény, az alkalmazás hozzáadása folyamatban van, ha az [Azure Portal](https://portal.azure.com/) jobb felső részén található **Értesítések** ikonra (a harangra) kattint, és egy **Folyamatban** lévő vagy **Befejezett** értesítést keres, amely nek az **Alkalmazás létrehozása**felirata látható.

Ha az alkalmazás soha nem kerül hozzáadásra, vagy ha hibát észlel, amikor a **Hozzáadás** gombra kattint, **hibaüzenet** **jelenik** meg. Ha további részleteket szeretne megtudni a hibáról, hogy többet tudjon meg egy támogatási szakemberről, vagy megoszthatja a hibát, a [portálértesítés részleteinek megtekintéséhez](#how-to-see-the-details-of-a-portal-notification) című szakasz lépéseit követve további információkat kaphat.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Rákattintottam az "összeadás" gombra, és az alkalmazásom nem jelent meg

Néha átmeneti problémák, hálózati problémák vagy hiba miatt egy alkalmazás hozzáadása sikertelen. Ez akkor történik meg, ha az Azure Portal jobb felső részén az **Értesítések** ikonra (a csengőre) kattint, és egy piros (!) ikon jelenik meg az alkalmazás létrehozása értesítés **mellett.** Ez azt jelzi, hogy hiba történt az alkalmazás létrehozásakor.

Ha hibát észlel, amikor a **Hozzáadás** gombra **Notification** kattint, **hibaüzenet** jelenik meg. Ha további részleteket szeretne megtudni a hibáról, hogy többet tudjon meg egy támogatási szakemberről, vagy megoszthatja a hibát, a [portálértesítés részleteinek megtekintéséhez](#how-to-see-the-details-of-a-portal-notification) című szakasz lépéseit követve további információkat kaphat.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nem tudom, hogyan kell beállítani a kérelmet, ha már hozzá adta

Ha segítségre van szüksége az egyéni alkalmazások megismeréséhez, az [Azure AD Applications dokumentumtár](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) segítségével többet tudhat meg az Azure AD-vel való egyszeri bejelentkezésről és annak működéséről.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>A portálértesítés részleteinek megtekintése

Az alábbi lépések végrehajtásával megtekintheti a portálértesítések részleteit:

1. kattintson az Azure Portal jobb felső részén található **Értesítések** ikonra (a csengőre)

2. Jelöljön ki egy **hibaállapotban** lévő értesítést (azokat, amelyek mellett piros (!) van).

   >[!NOTE]
   >Az értesítésekre nem kattinthat **sikeres** vagy folyamatban **lévő állapotban.**
   >
   >

4. Az Értesítés **részletei** csoportban található információk segítségével további részleteket tudmeg a problémáról.

5. Ha továbbra is segítségre van szüksége, megoszthatja ezeket az információkat egy támogatási szakemberrel vagy a termékcsoporttal, hogy segítséget kapjon a problémával kapcsolatban.

6. Kattintson a Másolás hiba szövegdoboztól **jobbra** található **másolási ikonra** az értesítés részleteinek másolásához, hogy megossza őket egy támogatási vagy termékcsoport-mérnökkel.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség beszerezése az értesítés részleteinek elküldésével egy támogatási szakembernek

Nagyon fontos, hogy az **alább felsorolt részleteket** megossza egy támogatási szakemberrel, ha segítségre van szüksége, hogy gyorsan segíthessenek. Ezt egyszerűen megteheti **egy képernyőkép készítésével,** vagy a **Másolási hiba ikonra**kattintva, amely a Másolás hiba szövegdoboztól **jobbra** található.

## <a name="notification-details-explained"></a>Az értesítés részleteinek magyarázata

Az értesítésekről az alábbi leírásokban talál további részleteket.

### <a name="essential-notification-items"></a>Alapvető értesítési elemek

- **Cím** – az értesítés leíró címe
  *  Példa – **Alkalmazásproxy-beállítások**

- **Leírás** – a művelet eredményeként történt esemény leírása

  *  Példa – **A beírt belső URL-t már egy másik alkalmazás használja**

- **Értesítési azonosító** – az értesítés egyedi azonosítója

  *  Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Ügyfélkérelem-azonosító** – a böngésző által megadott kérésazonosító

  *  Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Időbélyegző UTC** – az az időbélyeg, amely alatt az értesítés történt, UTC-ben

  *  Példa – **2017-03-23T19:50:43.7583681Z**

- **Belső tranzakcióazonosító** – a belső azonosító, amelyet a rendszereinkben a hiba felnézésére használhatunk

  *  Példa – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – a műveletet végző felhasználó

  *  Példa – **tperkins\@f128.info**

- **Bérlőazonosító** – annak a bérlőnek az egyedi azonosítója, amelynek a műveletet végző felhasználó tagja volt

  *  Példa – **7918d4b5-0442-4a97-be2d-36f9962ece**

- **Felhasználói objektum azonosítója** – a műveletet végző felhasználó egyedi azonosítója

  *  Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési tételek

- **Megjelenítendő név** – **(lehet üres)** a hiba részletesebb megjelenítendő neve

  *  Példa – **Alkalmazásproxy-beállítások**

- **Állapot** – az értesítés konkrét állapota

  *  Példa – **Sikertelen**

- **Objektumazonosító** – **(lehet üres)** az objektumazonosító, amelyen a műveletet végrehajtották

  *  Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

- **Részletek** – a művelet eredményeként történt adatok részletes leírása

  *  Példa – **A belső URL `https://bing.com/` érvénytelen, mivel már használatban van**

- **Másolási hiba** – Kattintson a Másolás hiba szövegdoboztól **jobbra** található **másolási ikonra** a támogatási vagy termékcsoporttal megosztani tetsző értesítési részletek másolásához 
- mérnök

  *  Példa```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```




