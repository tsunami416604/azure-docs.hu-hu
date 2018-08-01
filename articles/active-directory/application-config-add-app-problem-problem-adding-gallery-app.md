---
title: Egy Azure AD katalógusából származó alkalmazás hozzáadása során |} A Microsoft Docs
description: A gyakori problémák személyek face megismerheti az Azure AD katalógusából származó alkalmazásokat, és mit tehet a problémák megoldásához hozzáadásakor
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: acde4dff332a6cafad98a7d82938225861b7ec71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366851"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Egy Azure AD katalógusából származó alkalmazás hozzáadása során

Ez a cikk segítségével megismerheti a gyakori problémák személyek face hozzáadásakor, az Azure AD katalógusából származó alkalmazásokat, és mit tehet a problémák megoldásához.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Szeretnék az "add" gombra való kattintás és az alkalmazásom hosszú időbe telt a jelennek meg

Bizonyos körülmények között, 1-2 percbe is telhet (és egyes esetekben több) az alkalmazás számára a címtár felvett jelennek meg. Bár ez nem a szokásos várt teljesítmény, az alkalmazás hozzáadása folyamatban van, kattintson a láthatja a **értesítések** ikonra (harang) a jobb felső sarkában a [az Azure portal](https://portal.azure.com/) és az egy **folyamatban lévő** vagy **befejezve** feliratú értesítési **-alkalmazás létrehozása.**

Ha az alkalmazás soha nem kerül, vagy hibát tapasztal, amikor kattint a **Hozzáadás** gomb, látni fog egy **értesítési** a egy **hiba** állapota. Ha azt szeretné, hogy további részleteket a hibáról további információ a, vagy megoszthatja a támogatási szakértővel, láthatja a lépéseket követve a hibával kapcsolatos további információkat a [a portál értesítései részleteinek megtekintése](#how-to-see-the-details-of-a-portal-notification) szakaszban.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Szeretnék az "add" gombra való kattintás és az alkalmazás nem jelenik meg

Egyes esetekben átmeneti problémák okozzák hálózati problémák vagy hibát, hozzáadása egy kérelem sikertelen. Is megadhatja, hogy ez akkor fordul elő, amikor kattint a **értesítések** ikonra (harang) a jobb felső sarkában az Azure Portalon, és a egy piros (!) ikon látható a **-alkalmazás létrehozása** értesítést. Ez azt jelzi, hogy hiba történt az alkalmazás létrehozásakor.

Ha hibát tapasztal, amikor kattint a **Hozzáadás** gomb, látni fog egy **értesítési** a egy **hiba** állapota. Ha azt szeretné, hogy további részleteket a hibáról további információ a, vagy megoszthatja a támogatási szakértővel, láthatja a lépéseket követve a hibával kapcsolatos további információkat a [a portál értesítései részleteinek megtekintése](#how-to-see-the-details-of-a-portal-notification) szakaszban.

 ## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nem tudom, hogyan állítható be az alkalmazásom, miután hozzá van adva

Ha alkalmazások megismerése segítségre van szüksége a [oktatóanyagok listája SaaS-alkalmazások integrálása az Azure Active Directoryval való](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) cikk egy nagyszerű hely az induláshoz.

Emellett a [az Azure AD-alkalmazások dokumentumtár](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) segítséget nyújtanak további információt az egyszeri bejelentkezés az Azure ad-vel, és hogyan működik.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>A portál értesítései részleteinek megtekintése

Bármilyen portál értesítési részleteit az alábbi lépéseket követve tekintheti meg:

1.  Kattintson a **értesítések** ikonra (harang) a jobb felső sarkában az Azure Portalon

2.  Válassza ki az értesítésekhez egy **hiba** állapota (amelyek mellettük a piros (!)).

    >[!NOTE]
    >Nem kattint az értesítések egy **sikeres** vagy **folyamatban lévő** állapota.
    >
    >

4.  Az információk alapján **értesítés részletei** tudni, hogy a problémával kapcsolatos további részletekért.

5.  Ha segítségre van szüksége, a ezeket az információkat megosztani a támogatási szakember vagy a csoport, kérjen segítséget a probléma.

6.  Kattintson a **másolási** **ikon** jobb oldalán a **hiba másolása** szövegmező másolása egy támogatási vagy a termékverzió csoport mérnök megosztása az összes értesítés részletei

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség kérése, ha a támogatási szakember küld értesítés részletei

Nagyon fontos, hogy megosztott **az alábbiakban a részletekről** a támogatási szakértővel, ha segítségre van szüksége, így meghatározhatja, hogy gyorsan. Ehhez egyszerűen a **elkészíti a képernyőfelvételt** vagy kattintson a **másolási hibajelző ikon**, jobb oldalán található a **hiba másolása** szövegmezőbe.

## <a name="notification-details-explained"></a>Értesítés részletei ismertetése

Az alábbi leírásokat, az értesítések kapcsolatos további részletekért tekintse meg.

### <a name="essential-notification-items"></a>Fontos értesítés elemek

-   **Cím** – a leíró címet az értesítés

  * Példa – **alkalmazásproxy-beállítások**

-   **Leírás** – Mi történt a művelet leírása

    -   Példa – **megadott belső URL-címet már használja egy másik alkalmazás**

-   **Értesítés azonosítója** – az értesítés egyedi azonosítója

    -   Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Ügyfélkérés azonosítója** – a megadott kérés azonosítója, a böngésző által készített

    -   Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Szolgáltatásblokk UTC idő** – az időbélyeg, amely során az értesítés történt (UTC)

    -   Példa – **2017-03-23T19:50:43.7583681Z**

-   **Belső Tranzakcióazonosító** – belső azonosítója használhatjuk rendszereinkben keresse ki a hibát

    -   Example – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **Egyszerű felhasználónév** – a műveletet végrehajtó felhasználó

    -   – Példa **tperkins@f128.info**

-   **Bérlőazonosító** –, amelyek a műveletet végrehajtó felhasználó tagja volt. a bérlő egyedi azonosítója

    -   Example – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Felhasználói objektum azonosítója** – a műveletet végrehajtó felhasználó egyedi azonosítója

    -   Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

-   **Megjelenítendő név** – **(üres is lehet)** a hiba részletes megjelenített neve

    -   Példa – **alkalmazásproxy-beállítások**

-   **Állapot** – az értesítésre adott állapota

    -   Példa – **nem sikerült**

-   **Objektumazonosító:** – **(üres is lehet)** az Objektumazonosító, amelyek hajtottak végre a műveletet

    -   Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Részletek** – a részletes Mi történt a művelet leírása

    -   Példa – **belső URL-cím "http://bing.com/" értéke érvénytelen, mert már használatban van**

-   **Másolási hiba** – kattintson a **másolás ikonra** jobb oldalán a **hiba másolása** szövegmező másolása vagy a termékverzió támogatási csoport megosztása az összes értesítés részletei 
-   mérnök

    -   Példa ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](manage-apps/what-is-application-management.md)
