---
title: "Hogyan kezelheti a felhasználói fiókokat az Azure API Management |} Microsoft Docs"
description: "Megtudhatja, hogyan hozható létre, vagy a meghívott felhasználóknak az Azure API Management"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 078abfa5-1e4f-4c9d-b9c7-a172bd19c1a2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: d3a50f6d22cbf1797f580078bc0d2cc9cefe5064
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Az Azure API Management felhasználói fiókok kezelése
Az API Management fejlesztők akkor teszi közzé az API Management használata API-k felhasználók. Ez az útmutató bemutatja, hogyan hozhat létre, és a fejlesztők meghívása a az API-k és termékek használatára, hogy elérhetővé számukra az API Management-példánnyal. A felhasználói fiókok kezelése programozott módon információkért lásd: a [felhasználói entitás](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentációjában találhatók a [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) hivatkozás.

## <a name="create-developer"></a>Hozzon létre egy új fejlesztő
Hozzon létre egy új fejlesztő, kattintson a **Publisher portal** az Azure portálon az API Management szolgáltatás. Ezzel továbblép az API Management közzétevő portáljára. Ha még nem hozott létre API Management szolgáltatáspéldányt, tekintse meg az [Ismerkedés az Azure API Management szolgáltatással][Get started with Azure API Management] oktatóanyag [API Management szolgáltatáspéldány létrehozása][Create an API Management service instance] című szakaszát.

![Közzétevő portál][api-management-management-console]

Kattintson a **felhasználók** a a **API Management** a bal oldali menüben, majd **felhasználó hozzáadása**.

![Fejlesztői létrehozása][api-management-create-developer]

Adja meg a **E-mail**, **jelszó**, és **neve** a új fejlesztői, majd kattintson a **mentése**.

![Fejlesztői létrehozása][api-management-add-new-user]

Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok vannak **aktív**, és a társított a **fejlesztők** csoport.

![Új fejlesztői][api-management-new-developer]

A fejlesztői fiókok egy **aktív** állapota az API-kat, amelyeknél az előfizetések összes eléréséhez használható. Az újonnan létrehozott fejlesztői társítandó további csoportokat, lásd: [csoportok társítása fejlesztők][How to associate groups with developers].

## <a name="invite-developer"></a>Egy fejlesztő meghívása
Egy fejlesztő, kattintson a **felhasználók** a a **API Management** a bal oldali menüben, majd **hívhat meg felhasználói**.

![Fejlesztői meghívása][api-management-invite-developer]

Adja meg a fejlesztői név és e-mail címét, és kattintson a **meghívása**.

![Fejlesztői meghívása][api-management-invite-developer-window]

Egy megerősítő üzenet jelenik meg, de az újonnan meghívott fejlesztői nem szerepelnek a listán csak azok elfogadja a meghívót. 

![Jóváhagyás meghívása][api-management-invite-developer-confirmation]

Amikor egy fejlesztő felkérik, egy e-mailt küld a fejlesztőnek. Ez az e-mail sablon használatával jön létre, és testre szabható. További információkért lásd: [konfigurálás e-mail sablonok][Configure email templates].

Ha elfogadja a meghívót, a fiók aktívvá válik.

## <a name="block-developer"></a> Inaktiválja vagy újraaktiválásához fejlesztői fiók létrehozása
Alapértelmezés szerint az újonnan létrehozott vagy a meghívott fejlesztői fiókok vannak **aktív**. Fejlesztői fiók létrehozása inaktiválásához kattintson **blokk**. Letiltott fejlesztői fiók létrehozása újraaktiválásához kattintson **aktiválás**. Letiltott fejlesztői fiók létrehozása nem a fejlesztői portálján érheti el és bármely API-k hívása. A felhasználói fiók törléséhez kattintson **törlése**.

![Blokk fejlesztői][api-management-new-developer]

## <a name="reset-a-user-password"></a>Felhasználói jelszó alaphelyzetbe állítása
A felhasználói fiók jelszavának alaphelyzetbe állításához kattintson a fiók nevét.

![Új jelszó létrehozása][api-management-view-developer]

Kattintson a **jelszó-átállítási** elküld egy hivatkozást a felhasználót, hogy a jelszó alaphelyzetbe állítása.

![Új jelszó létrehozása][api-management-reset-password]

Felhasználói fiókok programozott módon dolgozni, tekintse meg a [felhasználói entitás](https://msdn.microsoft.com/library/azure/dn776330.aspx) dokumentációjában találhatók a [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) hivatkozás. A felhasználói fiók jelszavának visszaállítása egy adott értékre, használhatja a [egy felhasználó frissítéséhez](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) műveletet, és adja meg a kívánt jelszót.

## <a name="pending-verification"></a>Függőben lévő ellenőrzési
![Függőben lévő ellenőrzési][api-management-pending-verification]

## <a name="next-steps"> </a>Következő lépések
A fejlesztői fiók létrehozása után szerepkörökhöz társítandó, és előfizetni termékek és API-kat. További információkért lásd: [létrehozásáról és-csoportok használata][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
