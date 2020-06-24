---
title: Probléma az Azure AD Gallery-alkalmazás hozzáadásakor | Microsoft Docs
description: Ismerje meg az Azure AD Gallery-alkalmazások hozzáadásakor felmerülő gyakori problémákat, valamint azt, hogy mit tehet a megoldásban?
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4387a3ca02297311cd9ab025c315ca3324cbbbe
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760405"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Probléma az Azure AD Gallery-alkalmazás hozzáadásakor

Ez a cikk segítséget nyújt az Azure AD Gallery-alkalmazások hozzáadásakor felmerülő gyakori problémák megismerésében, valamint a megoldásához.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Rákattintottam a "Hozzáadás" gombra, és az alkalmazásom hosszú ideig tartott

Bizonyos körülmények között akár 1-2 percet is igénybe vehet, hogy egy alkalmazás megjelenjen a címtárhoz való hozzáadás után. Habár ez nem a szokásosan várt teljesítmény, az alkalmazás hozzáadása folyamatban van. ehhez kattintson az **értesítések** ikonra (a harangra) a [Azure Portal](https://portal.azure.com/) jobb felső sarkában, és keressen az **alkalmazás hozzáadása** feliratú **folyamatban** vagy **befejezett** értesítésben.

Ha az alkalmazása soha nem lett hozzáadva, vagy ha a **Hozzáadás** gombra kattint **, hibaüzenet** **jelenik meg.** Ha további információra van szüksége a hibáról, és többet szeretne megtudni a támogatási szakemberrel kapcsolatban, további információt a következő témakörben talál: a [portál értesítési adatainak megtekintése](#how-to-see-the-details-of-a-portal-notification) című szakasz lépéseit követve.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Rákattintok a "Hozzáadás" gombra, és az alkalmazásom nem jelenik meg

Időnként átmeneti problémák, hálózati problémák vagy hiba miatt az alkalmazások hozzáadása meghiúsul. Ez akkor fordulhat elő, ha a Azure Portal jobb felső sarkában található **értesítések** ikonra (a harangra) kattint, és az **alkalmazás hozzáadása** értesítés mellett vörös (!) ikon jelenik meg. Ez azt jelzi, hogy hiba történt az alkalmazás létrehozásakor.

Ha **a** **Hozzáadás** gombra kattintva hibát tapasztal, **hibaüzenet** jelenik meg. Ha további információra van szüksége a hibáról, és többet szeretne megtudni a támogatási szakemberrel kapcsolatban, további információt a következő témakörben talál: a [portál értesítési adatainak megtekintése](#how-to-see-the-details-of-a-portal-notification) című szakasz lépéseit követve.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nem tudom, hogyan kell beállítani az alkalmazást a hozzáadásom után

Ha segítségre van szüksége az alkalmazások megismeréséhez, tekintse át az SaaS-alkalmazások Azure Active Directory cikkekkel való [integrálására szolgáló oktatóanyagok listáját](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) .

Ezen kívül az [Azure ad Applications dokumentumtára](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) segítséget nyújt az Azure ad-vel való egyszeri bejelentkezésről és annak működéséről.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Portál értesítés részleteinek megtekintése

Az alábbi lépéseket követve megtekintheti a portál összes értesítésének részleteit:

1.  Válassza ki az **értesítések** ikont (a harangot) a Azure Portal jobb felső sarkában.

2.  Válassza ki a **hibás** állapotú értesítéseket (ezek a következők: piros (!)).

    >[!NOTE]
    >Az értesítések **sikeres** vagy **folyamatban** lévő állapotában nem kattinthat.
    >
    >

4.  A probléma részletes ismertetését a **Notification Details (értesítési adatok** ) szakaszban tekintheti meg.

5.  Ha továbbra is segítségre van szüksége, megoszthatja ezeket az információkat egy támogatási szakemberrel vagy a termék csoporttal, hogy segítséget kapjon a problémával kapcsolatban.

6.  A **másolási hiba** szövegmező jobb oldalán kattintson a **Másolás** **ikonra** , és másolja az összes értesítési részletet egy támogatási vagy termékcsoport-szakmérnöknek való megosztásra.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Segítség kérése támogatási szakembernek értesítési adatok küldésével

Nagyon fontos, hogy **az alább felsorolt részleteket** egy támogatási szakemberrel ossza meg, ha segítségre van szüksége, így gyorsan elvégezheti a segítségét. Ezt egyszerűen elvégezheti **egy képernyőkép** készítésével vagy a másolási **hiba ikonra**kattintva, amely a **másolási hiba** szövegmezőtől jobbra található.

## <a name="notification-details-explained"></a>Értesítés részletei – magyarázat

Az értesítésekkel kapcsolatos további információkért tekintse meg az alábbi leírásokat.

### <a name="essential-notification-items"></a>Alapvető értesítési elemek

- **Title (cím** ) – az értesítés leíró címe

  * Példa – **alkalmazásproxy-beállítások**

- **Leírás** – a művelet eredményeképpen történtek leírása

  -   Példa – **a megadott belső URL-címet már egy másik alkalmazás használja**

- **Értesítési azonosító** – az értesítés egyedi azonosítója

  -   Példa – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Ügyfél-kérelem azonosítója** – a böngésző által megadott kérelem-azonosító

  -   Példa – **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Időbélyeg UTC** – az az időbélyegző, amely alatt az értesítés bekövetkezett, UTC szerint

  -   Példa – **2017-03-23T19:50:43.7583681 z**

- **Belső tranzakció azonosítója** – az a belső azonosító, amellyel megkeresheti a hibákat a rendszerekben

  -   Példa – **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN** – a műveletet végrehajtó felhasználó

  -   Példa – **tperkins \@ f128.info**

- **Bérlő azonosítója** – annak a bérlőnek az egyedi azonosítója, aki a műveletet végrehajtó felhasználó tagja volt

  -   Példa – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Felhasználói objektum azonosítója** – a műveletet végrehajtó felhasználó egyedi azonosítója

  -   Példa – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Részletes értesítési elemek

-   **Megjelenítendő név** – **(üres is lehet)** a hiba részletesebb megjelenítendő neve

    -   Példa – **alkalmazásproxy-beállítások**

-   **Status (állapot** ) – az értesítés konkrét állapota

    -   Példa – **sikertelen**

-   **Objektumazonosító** – **(üres is lehet)** a művelet végrehajtásának alapjául szolgáló objektumazonosító

    -   Példa – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Részletek** – a művelet eredményének részletes leírása

    -   Példa – a **belső URL-cím `https://bing.com/` érvénytelen, mert már használatban van**

-   **Másolási hiba** – a **másolási hiba** szövegmezőtől jobbra lévő **másolási ikonra** kattintva másolhatja az összes értesítési részletet, hogy a megosztás egy támogatási vagy termékcsoport használatával történjen 
-   mérnök

    -   Például```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

