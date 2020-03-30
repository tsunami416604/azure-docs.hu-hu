---
title: Értesítések és e-mail sablonok konfigurálása
titleSuffix: Azure API Management
description: Ismerje meg, hogyan konfigurálhat értesítéseket és e-mail sablonokat az Azure API Management ben.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 786a9e26003a7afb98307e0bd7fae94c42a2f00d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244068"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Az értesítések és e-mail sablonok konfigurálása az Azure API Management szolgáltatásban

Az API Management lehetővé teszi az értesítések konfigurálását adott eseményekhez, valamint az API Management-példány rendszergazdáival és fejlesztőivel való kommunikációhoz használt e-mail sablonok konfigurálását. Ez a cikk bemutatja, hogyan konfigurálhatja az értesítéseket az elérhető eseményekhez, és áttekintést nyújt az ezekhez az eseményekhez használt e-mail sablonok konfigurálásáról.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik API Management szolgáltatáspéldánysal, hajtsa végre a következő rövid útmutatót: [Hozzon létre egy Azure API Management-példányt.](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-notifications"></a><a name="publisher-notifications"> </a>Értesítések konfigurálása

1.  Válassza ki az API MANAGEMENT-példányt. **API MANAGEMENT**
2.  Kattintson **az Értesítések** elemre az elérhető értesítések megtekintéséhez.

    ![Közzétevői értesítések][api-management-publisher-notifications]

    Az események alábbi listája konfigurálható az értesítésekhez.

    -   **Előfizetési kérelmek (jóváhagyásszükséges)** – A megadott e-mail címzettek és a felhasználók e-mail értesítéseket kapnak a jóváhagyást igénylő API-termékek előfizetési kérelmeiről.
    -   **Új előfizetések** – A megadott e-mail címzettek és felhasználók e-mailben értesítést kapnak az új API-termék-előfizetésekről.
    -   **Alkalmazásgaléria-kérelmek** – A megadott e-mail címzettek és felhasználók e-mailértesítéseket kapnak, amikor új jelentkezéseket nyújtanak be az alkalmazásgalériába.
    -   **BCC** - A megadott e-mail címzettek és a felhasználók e-mailben kapnak vak szén-dioxid-másolatokat a fejlesztőknek küldött e-mailekről.
    -   **Új probléma vagy megjegyzés** – A megadott e-mail címzettek és felhasználók e-mailben értesítést kapnak, amikor új problémát vagy megjegyzést küld a fejlesztői portálon.
    -   **Fióküzenetek bezárása** – A megadott e-mail címzettek és felhasználók e-mailben értesítést kapnak, amikor egy fiókot bezárnak.
    -   **Az előfizetési kvótakorlát közelítése** – A következő e-mail címzettek és felhasználók e-mail-értesítéseket kapnak, amikor az előfizetés használata megközelíti a használati kvótát.

        > [!NOTE]
        > Az értesítéseket csak az [előfizetési szabályzat aktiválja](api-management-access-restriction-policies.md#SetUsageQuota) a kvóta. [A kulcsházirend szerinti kvóta](api-management-access-restriction-policies.md#SetUsageQuotaByKey) nem hoz létre értesítéseket.

    Az e-mail címzetteket minden eseményhez megadhatja az e-mail cím szövegmezőjében, vagy kiválaszthatja a felhasználókat a listából.

3.  Az értesítendő e-mail címek megadásához írja be őket az e-mail cím szövegmezőjébe. Ha több e-mail címmel rendelkezik, vesszővel válassza el őket.

    ![Értesítés címzettjei][api-management-email-addresses]

4.  Nyomja meg az **Add** (Hozzáadás) gombot.

## <a name="configure-notification-templates"></a><a name="email-templates"> </a>Értesítési sablonok konfigurálása

Az API Management értesítési sablonokat biztosít a szolgáltatás felügyelete és használata során küldött e-mail üzenetekhez. A következő e-mail sablonok állnak rendelkezésre.

-   A jelentkezési galéria beküldése jóváhagyva
-   Fejlesztőbúcsú levél
-   Az értesítéshez közeledő fejlesztői kvótakorlát
-   Felhasználó meghívása
-   Új megjegyzés hozzáadva egy problémához
-   Új probléma érkezett
-   Új előfizetés aktiválva
-   Előfizetés megújított visszaigazolása
-   Előfizetési kérelem elutasítása
-   Megérkezett előfizetési kérelem

Ezek a sablonok igény szerint módosíthatók.

Az API Management-példány e-mail sablonjainak megtekintéséhez és konfigurálásához kattintson **az Értesítések sablonok**elemre.

![E-mail-sablonok][api-management-email-templates]

Minden e-mail sablonnak van egy tárgya egyszerű szövegként, és egy törzsdefiníció HTML formátumban. Minden elem igény szerint testreszabható.

![E-mail sablon szerkesztő][api-management-email-template]

A **Paraméterek** lista tartalmazza a paraméterek listáját, amelyek a tárgyba vagy törzsbe való beillesztéskor az e-mail elküldésekor a kijelölt értéket cserélik. Paraméter beszúrásához helyezze a kurzort oda, ahová a paramétert szeretné, és kattintson a paraméter nevétől balra lévő nyílra.

> [!NOTE]
> A paramétereket nem cserélik le a tényleges értékeka teszt megtekintésekor vagy küldésekor.

Az e-mail sablon módosításainak mentéséhez kattintson a **Mentés**gombra, vagy a módosítások megszakításához kattintson az **Elvetés gombra.**

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
[configure publisher notifications]: #publisher-notifications
[configure email templates]: #email-templates
[how to create and use groups]: api-management-howto-create-groups.md
[how to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[get started with azure api management]: get-started-create-service-instance.md
[create an api management service instance]: get-started-create-service-instance.md
