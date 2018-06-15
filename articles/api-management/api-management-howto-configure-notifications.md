---
title: Értesítések konfigurálása és az e-mail sablonok az Azure API Management |} Microsoft Docs
description: Útmutató a értesítések konfigurálása és az e-mail sablonok az Azure API Management.
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
ms.openlocfilehash: 60788f76dac58ead10e43e892d587a86bdd3fcad
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33934284"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Az értesítések és e-mail sablonok konfigurálása az Azure API Management szolgáltatásban
API-kezelés lehetővé teszi bizonyos események értesítéseinek konfigurálásához, és konfigurálhatja az e-mail-sablonokkal a rendszergazdák és fejlesztők számára az API Management-példány folytatott kommunikációhoz használt. Ez a cikk bemutatja, hogyan használható eseményt az értesítések konfigurálása, és ezeket az eseményeket az e-mail-sablonokat konfigurálásának áttekintése.

## <a name="prerequisites"></a>Előfeltételek

Ha Ön nem rendelkezik az API Management szolgáltatáspéldány, hajtsa végre a következő gyorsindítási: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

## <a name="publisher-notifications"> </a>Értesítések konfigurálása

1. Válassza ki a **API MANAGEMENT** példány.
2. Kattintson a **értesítések** az elérhető értesítések megtekintéséhez.

    ![A Publisher értesítések][api-management-publisher-notifications]

    Az alábbi listán szereplő események értesítések konfigurálhatók.

    * **Előfizetési kérelmek (jóváhagyásra van szükség)** -a megadott e-mail címzettek és a felhasználók az előfizetés kérelmekről API termékek jóváhagyásra van szükség az e-mail értesítéseket kap.
    * **Új előfizetések** -a megadott e-mail címzettek és a felhasználók új API termék előfizetések kapcsolatos e-mail értesítéseket kap.
    * **Gyűjteményelem alkalmazáskérelmeinek** -a megadott e-mail címzettek és a felhasználók kapnak értesítő e-mailek, amikor az alkalmazás gyűjteményébe új kérelmeket.
    * **Titkos másolat** -a megadott e-mail címzettek és a felhasználók kapnak e-mailek Titkos másolatot a fejlesztők számára küldött összes e-maileket.
    * **Új probléma vagy megjegyzés** – a megadott e-mail címzettek és a felhasználók kapnak értesítő e-mailek, amikor új probléma vagy megjegyzés küldése a fejlesztői portálján.
    * **Zárja be a fiók üzenet** -a megadott e-mail címzettek és a felhasználók kapnak értesítő e-mailek, amikor egy fiók le van zárva.
    * **Megközelíti előfizetés kvótakorlátot** -a következő e-mailek címzettjeinek és felhasználók kapnak értesítő e-mailek, amikor az előfizetés használatának lekérdezi megközelíti a memóriahasználati kvóta.

    Az egyes eseményekhez megadhatja az e-mailek címzettjeinek az e-mail cím beviteli mezőbe, vagy választhat a felhasználók listájából.

3. Adja meg az e-mail címeket, adja meg azokat az e-mail cím szövegmezőben. Ha az e-mail címeket, külön azokat vesszővel válassza el őket.

    ![Értesítés címzettjeinek][api-management-email-addresses]
4. Nyomja meg az **Add** (Hozzáadás) gombot.

## <a name="email-templates"> </a>Értesítési sablonok konfigurálása
Az API Management értesítési sablonok biztosít a felügyelete, és a szolgáltatás használata során küldött e-mailek. A következő e-mail sablonok találhatók.

* Application gallery küldésének jóváhagyott
* Fejlesztői farewell levél
* Értesítési megközelítő fejlesztői kvótakorlátot
* Felhasználó meghívása
* Új megjegyzés hozzáadása egy problémához
* Kapott új probléma
* Új előfizetés aktiválása
* Előfizetés megújítása megerősítése
* Előfizetés visszautasítja
* Előfizetés kérelem érkezett

Ezek a sablonok módosítható igény szerint.

Tekintheti meg és konfigurálhatja az e-mail sablonok az API Management-példány, kattintson a **értesítések sablonok**.

![E-mail-sablonok][api-management-email-templates]

Minden e-mail sablon egyszerű szöveges tárgy és törzs HTML formátumban definícióját rendelkezik. Minden elem testre szabható, igény szerint.

![E-mail sablon szerkesztő][api-management-email-template]

A **paraméterek** lista felsorolja azokat a paramétereket, amikor a tárgya és törzse beszúrt fogja cserélni a kijelölt érték, ha az e-mailt küld. Paraméter beszúrása, vigye a kurzort, ahol szeretné nyissa meg a paramétert, majd kattintson a a paraméternév balra mutató nyílra.

> [!NOTE] 
> A paraméterek nem cserélhető le tényleges értékek előnézet vagy egy teszt küldése.

Az e-mail sablon a módosítások mentéséhez kattintson **mentése**, vagy kattintson a változtatások elvetését **elveti**.
 

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
