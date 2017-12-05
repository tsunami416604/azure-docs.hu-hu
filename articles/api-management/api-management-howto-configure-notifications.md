---
title: "Értesítések konfigurálása és az e-mail sablonok az Azure API Management |} Microsoft Docs"
description: "Útmutató a értesítések konfigurálása és az e-mail sablonok az Azure API Management."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: ec560bbab3caf4cde090ed3c9a47ccc0afcb2492
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Az értesítések és e-mail sablonok konfigurálása az Azure API Management szolgáltatásban
API-kezelés lehetővé teszi bizonyos események értesítéseinek konfigurálásához, és konfigurálhatja az e-mail-sablonokkal a rendszergazdák és fejlesztők számára az API Management-példány folytatott kommunikációhoz használt. Ez a témakör bemutatja, hogyan használható eseményt az értesítések konfigurálása, és ezeket az eseményeket az e-mail-sablonokat konfigurálásának áttekintése.

## <a name="publisher-notifications"></a>Publisher értesítések konfigurálása
Értesítések konfigurálásához kattintson **Publisher portal** az Azure portálon az API Management szolgáltatás. Ezzel továbblép az API Management közzétevő portáljára.

![Közzétevő portál][api-management-management-console]

> [!NOTE] 
> Ha még nem hozott létre az API Management szolgáltatáspéldány, lásd: [hozzon létre egy API-kezelés szolgáltatás példányt][Create an API Management service instance].

Kattintson a **értesítések** a a **API Management** megtekintéséhez az elérhető értesítések a bal oldali menüben.

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

Adja meg az e-mail címeket, adja meg azokat az e-mail cím szövegmezőben. Ha az e-mail címeket, külön azokat vesszővel válassza el őket.

![Értesítés címzettjeinek][api-management-email-addresses]

Adja meg a felhasználóknak szeretne értesítést kapni, kattintson a **címzettet adjon hozzá**, jelölje be a jelölőnégyzetet, kattintson a felhasználók értesítést kapnak, és kattintson a **OK**.

> [!NOTE] 
> Csak a rendszergazdák a listában jelennek meg.


Miután az értesítési címzettek, kattintson a **mentése** a frissített értesítés címzettjeinek alkalmazni.

> [!NOTE] 
> Ha manuálisan lép távolabb a **Publisher értesítések** lapon a közzétevő portal riasztást küld a hiba nem mentett módosítások vannak.


## <a name="email-templates"></a>E-mail sablonok konfigurálása
Az API Management e-mail sablonok biztosít a felügyelete, és a szolgáltatás használata során küldött e-mailek. A következő e-mail sablonok találhatók.

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

Tekintheti meg és konfigurálhatja az e-mail sablonok az API Management-példány, kattintson a **értesítések** a a **API Management** menüt, és válassza ki a **E-mail sablonok**fülre.

![E-mail-sablonok][api-management-email-templates]

Megtekintése vagy módosítása egy adott sablon, válassza ki azt a **sablonok** legördülő listából.

![E-mail sablonok listája][api-management-email-templates-list]

Minden e-mail sablon egyszerű szöveges tárgy és törzs HTML formátumban definícióját rendelkezik. Minden elem testre szabható, igény szerint.

![E-mail sablon szerkesztő][api-management-email-template]

A **paraméterek** lista felsorolja azokat a paramétereket, amikor a tárgya és törzse beszúrt fogja cserélni a kijelölt érték, ha az e-mailt küld. Paraméter beszúrása, vigye a kurzort, ahol szeretné nyissa meg a paramétert, majd kattintson a a paraméternév balra mutató nyílra.

Kattintson a **előzetes** vagy **egy teszt küldése** hogyan az e-mailt fog keresse meg vagy egy teszt e-mailek küldése.

> [!NOTE] 
> A paraméterek nem cserélhető le tényleges értékek előnézet vagy egy teszt küldése.

Az e-mail sablon a módosítások mentéséhez kattintson **mentése**, vagy visszavonhatja a módosításokat kattintson **Mégse**.
 

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
