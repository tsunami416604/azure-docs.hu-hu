---
title: "A probléma nem galéria-alkalmazás hozzáadása |} Microsoft Docs"
description: "A gyakori problémák személyek arcfelismerési áttekinteni egyéni nem galéria-alkalmazások hozzáadása"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9abc05dd835d2ec803e32351c75534ebe628a8d0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="problem-adding-a-non-gallery-application"></a>A probléma nem galéria-alkalmazás hozzáadása

Ez a cikk segítenek megérteni a gyakori problémák személyek arcfelismerési hozzáadásakor **egyéni nem galéria alkalmazások** és mit tehet a megoldásukkal együtt. 

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>A "Hozzáadás" gombra kattintáskor és az alkalmazás hosszú időt vett igénybe jelenik meg

Bizonyos körülmények percig is eltarthat, 1 – 2 (és egyes esetekben hosszabb) az alkalmazás számára, hogy a címtárban való hozzáadását követően jelennek meg. Ez nem a normál várt teljesítményét, az alkalmazás hozzáadása folyamatban van a kattintva megtekintheti a **értesítések** (a harang) ikonra a képernyő jobb felső sarkában a [Azure Portal](https://portal.azure.com/) és keresése az egy **folyamatban lévő** vagy **befejezve** feliratú értesítési **alkalmazás létrehozása**.

Ha az alkalmazás soha nem adtak hozzá vagy hibát észlel a gombra kattintva a **Hozzáadás** gombra kattint, megjelenik egy **értesítési** a egy **hiba** állapotát. További információk, vagy egy támogatási engingeer megosztása a hibával kapcsolatos további adatokra van szüksége, ha a hibával kapcsolatos további információkat a lépéseket követve megtekintheti a [a portál értesítései részleteinek megtekintése](#how-to-see-the-details-of-a-portal-notification) szakasz.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>A "Hozzáadás" gombra kattintáskor és az alkalmazás nem jelenik meg

Egyes esetekben átmeneti hibái miatt hálózati problémák, vagy egy hiba hozzáadása egy kérelem sikertelen lesz. Beállíthatja, hogy ez akkor fordul elő, amikor kattint a **értesítések** (a harang) ikonra a képernyő jobb felső sarkában az Azure portálon, és a piros (!) ikon mellett látható a **alkalmazás létrehozása** értesítést. Ez azt jelzi, hogy hiba történt az alkalmazás létrehozásakor.

Ha hibát észlel a gombra kattintva a **Hozzáadás** gombra kattint, megjelenik egy **értesítési** a egy **hiba** állapotát. További információk, vagy egy támogatási engingeer megosztása a hibával kapcsolatos további adatokra van szüksége, ha a hibával kapcsolatos további információkat a lépéseket követve megtekintheti a [a portál értesítései részleteinek megtekintése](#how-to-see-the-details-of-a-portal-notification) szakasz.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nem tudom, ha felvett, az alkalmazás beállítása

Ha segítségre van szüksége egyéni alkalmazások megtanulni a [az Azure AD-alkalmazások dokumentumtár](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) segítséget nyújtanak további információt az egyszeri bejelentkezés az Azure ad-vel és annak működéséről.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>A portál értesítései részleteinek megtekintése

A portál értesítései részleteit láthatja az alábbi lépéseket követve:

1.  Kattintson a **értesítések** (a harang) ikonra a képernyő jobb felső sarkában az Azure portálon

2.  Válassza ki az értesítésekhez egy **hiba** állapota (amelyek a piros (!) mellett).

   >[!NOTE]
   >Nem kattint az értesítések egy **sikeres** vagy **folyamatban lévő** állapotát.
   >
   >

3.  A Megnyitás a **értesítési részletek** panelen.

4.  Ez a témakör a problémával kapcsolatos további részletekért megértéséhez.

5.  Ha további segítségre van, a ezek az információk megosztása a támogatási szakember vagy a csoport segítség a probléma megoldásában.

6.  Kattintson a **másolás ikon** jobb oldalán a **hiba másolása** szövegmező megosztani a támogatási szolgálathoz vagy a termék csoport mérnöke értesítés részleteinek másolása.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség kérése értesítési részletek küld egy támogatási szakértőhöz

Nagyon fontos, hogy megosztott **alább felsorolt összes részletes** , ha segítségre van szüksége, így gyorsan segít a támogatási szakértőhöz. Ehhez az egyszerű **elkészíti a képernyőfelvételt** vagy kattintson a **másolási hibaikon**, míg a talált jobb oldalán a **hiba másolása** szövegmező.

## <a name="notification-details-explained"></a>Értesítési részletek alapján

Az alábbiakban azt ismerteti, több milyen az értesítés azt jelenti, hogy elemeket, és azok példákat.

### <a name="essential-notification-items"></a>Alapvető értesítési elemek

-   **Cím** – az értesítés informatív címet
   *  Példa – **Application proxy beállításai**

-   **Leírás** – Mi történt a művelet leírása

   *  Példa – **megadott belső URL-cím már használatban van egy másik alkalmazás.**

-   **Értesítés azonosítója** – az értesítés egyedi azonosítója

   *  Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Ügyfélkérelem-azonosító** – a böngésző által végrehajtott egyedi azonosítója

   *  Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Stamp UTC-idő** – a timestamp, amely során az értesítés történt UTC szerint

   *  Példa – **2017-03-23T19:50:43.7583681Z**

-   **Belső tranzakció azonosítója** – belső azonosítója a Microsoft segítségével, a rendszer keresse meg a hiba

   *  Példa – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Egyszerű felhasználónév** – a műveletet végző felhasználó

   *  – Példa**tperkins@f128.info**

-   **A bérlői azonosító** – az egyedi azonosító a bérlő által, hogy a művelet a felhasználó tagja volt.

   *  Példa – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Felhasználói objektum azonosítója** – a művelet a felhasználó egyedi azonosítója

 *  Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

-   **Megjelenítendő név** – **(üres is lehet)** a hiba részletes megjelenítendő neve

  *  Példa – **Application proxy beállításai**

-   **Állapot** – az értesítésre adott állapota

   *  Példa – **nem sikerült**

-   **Objektumazonosító:** – **(üres is lehet)** az objektum azonosítója, amely alapján a művelet végrehajtása

   *  Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Részletek** – a részletes Mi történt a művelet leírása

   *  Példa – **belső URL-cím "http://bing.com/" érvénytelen, mert már használatban van**

-   **Másolja át a hiba** – kattintson a **másolás ikon** jobb oldalán a **hiba másolása** szövegmező megosztani a támogatási szolgálathoz vagy a termék csoport mérnöke értesítés részleteinek másolása

   *  Példa```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Következő lépések
[Alkalmazások kezelése az Azure Active Directoryban](active-directory-enable-sso-scenario.md)



