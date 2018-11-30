---
title: Értesítések és e-mail sablonok az Azure API Management konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan értesítések és e-mail sablonok az Azure API Management konfigurálása.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 16beaadae36dfc7445a88875d36786bd97889599
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445074"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Az értesítések és e-mail sablonok konfigurálása az Azure API Management szolgáltatásban
Az API Management lehetővé teszi bizonyos események értesítéseinek konfigurálásához, és konfigurálhatja az e-mail-sablonok, amelyek segítségével a rendszergazdák és a egy API Management-példány a fejlesztők kommunikálni. Ez a cikk bemutatja, hogyan konfigurálja a rendelkezésre álló események értesítéseket, és ezek az események használt e-mail sablonok konfigurálása az nyújt áttekintést.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik az API Management szolgáltatáspéldányt, hajtsa végre a következő rövid útmutatót: [Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Értesítések konfigurálása

1. Válassza ki a **az API MANAGEMENT** példány.
2. Kattintson a **értesítések** az elérhető értesítések megtekintéséhez.

    ![Közzétevő értesítések][api-management-publisher-notifications]

    Az alábbi listában szereplő események értesítések konfigurálhatók.

    * **(Jóváhagyásra van szükség) feliratkozási kérelmeket** – a megadott e-mail-címzettek és a felhasználók e-mailes feliratkozási kérelmeket jóváhagyásra van szükség API-termékekre vonatkozó értesítést fog kapni.
    * **Új előfizetések** – a megadott e-mail-címzettek és a felhasználók kapnak e-mailes értesítést az új API termékhez előfizetések.
    * **Alkalmazáskérések katalógus** – a megadott e-mail-címzettek és a felhasználók e-mail értesítéseket kap, amikor új kérelmeket az alkalmazás-katalógusba.
    * **Titkos másolat** – a megadott e-mail-címzettek és a felhasználók e-mail titkos másolatot a fejlesztők számára küldött összes e-mailt fog kapni.
    * **Új probléma, vagy amelyekhez megjegyzést** – a megadott e-mail-címzettek és a felhasználók e-mail értesítéseket, amikor új problémát kap, vagy a fejlesztői portál küldése megjegyzést.
    * **Fiók bezárása üzenet** – a megadott e-mail-címzettek és a felhasználók e-mail értesítéseket kap, ha a fiók le van zárva.
    * **Közelgő előfizetési kvótája korlátjának** – a következő e-mailek címzettjeinek és a felhasználók e-mail értesítéseket kap, ha közeli használati kvóta lekérdezi előfizetés használata.

    Az egyes eseményekhez is megadhat, az e-mail cím beviteli mező használatával e-mailek címzettjeinek, vagy kiválaszthatja a felhasználók listájából.

3. Adja meg az e-mail-címeket, értesítést kapjon, adja meg azokat az e-mail cím szövegmezőbe. Ha az e-mail címeket, külön azokat vesszőkkel válassza el egymástól.

    ![Értesítés címzettjeinek][api-management-email-addresses]
4. Nyomja meg az **Add** (Hozzáadás) gombot.

## <a name="email-templates"> </a>Értesítési sablonok konfigurálása
Az API Management biztosítja értesítési sablonok felügyelete és a szolgáltatás használata során küldött e-mail üzenetekhez. Az alábbi e-mail-sablonok találhatók.

* Jóváhagyott alkalmazás katalógus beküldése
* Fejlesztői farewell betűjele
* Hamarosan eléri az értesítési fejlesztői kvótahatárát
* Felhasználó meghívása
* Új megjegyzés hozzáadása egy problémához
* Új problémát észlelt
* Új előfizetés aktiválása
* Előfizetés megújítása megerősítése
* Előfizetési kérést elutasítja.
* Előfizetési kérés érkezett

Ezeket a sablonokat is módosítható igény szerint.

Tekintheti meg és konfigurálhatja az e-mail-sablonok, az API Management-példány számára, kattintson a **értesítések sablonok**.

![E-mail-sablonok][api-management-email-templates]

Minden e-mail-sablon egy egyszerű szöveges tárgy és a egy választörzs definíciójához HTML formátumban van. Minden elem testre szabható igény szerint.

![E-mail sablon szerkesztő][api-management-email-template]

A **paraméterek** lista felsorolja azokat a paramétereket, amikor beszúrja, a tárgya és törzse fogja cserélni a kijelölt érték, ha az e-mailt küld. Paraméter beszúrása, vigye a kurzort, ahol szeretne, nyissa meg a paramétert, és kattintson a nyílra a bal oldalon, a paraméter neve.

> [!NOTE] 
> A paraméterek nem cserélhető le tényleges értékekre megtekintés vagy küldése egy tesztet.

Az e-mail-sablont, a módosítások mentéséhez kattintson az **mentése**, vagy kattintson a módosítások visszavonásához **elveti**.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
