---
title: A felhasználói fiókok kezelése az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és hívhat meg felhasználókat az Azure API Management ben
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 672f145b638fa6b85f7139f2edb4fc41bb2855d9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260972"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Felhasználói fiókok kezelése az Azure API Managementben

Az API Management ben a fejlesztők az API Management használatával elérhető vétek felhasználói. Ez az útmutató bemutatja, hogyan hozhat létre és hívhat meg fejlesztőket az API-k és a számukra az API Management-példány segítségével elérhetővé teszelérhető API-k és termékek használatára. A felhasználói fiókok programozott kezeléséről az [API Management REST](/rest/api/apimanagement/) referenciadokumentumában található Felhasználói [entitás](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/user) dokumentációjában olvashat.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben szereplő feladatok végrehajtása: [Hozzon létre egy Azure API Management-példányt.](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-new-developer"></a><a name="create-developer"> </a>Új fejlesztő létrehozása

Új felhasználó hozzáadásához kövesse az alábbi lépéseket:

1. Válassza a képernyő bal oldalán található **Felhasználók** lapot.
2. Nyomja **le a +Add billentyűkombinációt.**
3. Adja meg a felhasználó nak megfelelő adatokat.
4. Nyomja meg az **Add** (Hozzáadás) gombot.

    ![Új felhasználó hozzáadása](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok **aktívak,** és a **Fejlesztők** csoporthoz vannak társítva. Az **aktív** állapotban lévő fejlesztői fiókok az összes olyan API-hoz való hozzáféréshez használhatók, amelyekhez előfizetéssel rendelkeznek. Ha az újonnan létrehozott fejlesztőt további csoportokkal szeretné társítani, olvassa el [a Csoportok társítása fejlesztőkkel][How to associate groups with developers]című témakört.

## <a name="invite-a-developer"></a><a name="invite-developer"> </a>Fejlesztő meghívása
Fejlesztő meghívásához kövesse az alábbi lépéseket:

1. Válassza a képernyő bal oldalán található **Felhasználók** lapot.
2. Nyomja **meg a +Invite billentyűkombinációt.**

Megjelenik egy megerősítő üzenet, de az újonnan meghívott fejlesztő csak a meghívás elfogadása után jelenik meg a listában. 

Amikor egy fejlesztőmeghívásra kerül, a rendszer e-mailt küld a fejlesztőnek. Ez az e-mail sablon használatával jön létre, és testreszabható. További információt a [Levelezési sablonok konfigurálása című témakörben talál.][Configure email templates]

A meghívás elfogadása után a fiók aktívvá válik.

## <a name="deactivate-or-reactivate-a-developer-account"></a><a name="block-developer"> </a> Fejlesztői fiók inaktiválása vagy újraaktiválása

Alapértelmezés szerint az újonnan létrehozott **Active**vagy meghívott fejlesztői fiókok aktívak. Fejlesztői fiók inaktiválásához kattintson **a Blokkolás gombra.** Blokkolt fejlesztői fiók újraaktiválásához kattintson **az Aktiválás gombra.** A letiltott fejlesztői fiók nem tud hozzáférni a fejlesztői portálhoz, és nem hívhat API-kat. Felhasználói fiók törléséhez kattintson a **Törlés gombra.**

Felhasználó letiltásához kövesse az alábbi lépéseket.

1. Válassza a képernyő bal oldalán található **Felhasználók** lapot.
2. Kattintson a blokkolni kívánt felhasználóra.
3. Nyomja **meg a Blokk**gombot.

## <a name="reset-a-user-password"></a>Új felhasználói jelszó beállítása

A felhasználói fiókokkal való programozott munka érdekében tekintse meg a Felhasználói entitás dokumentációját az API Management REST API-hivatkozásban. [API Management REST API](/rest/api/apimanagement/) Ha vissza szeretne állítani egy felhasználói fiók jelszavát egy adott értékre, [használhatja](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) a Felhasználói művelet frissítése műveletet, és megadhatja a kívánt jelszót.

## <a name="next-steps"></a><a name="next-steps"> </a>További lépések
A fejlesztői fiók létrehozása után társíthatja azt szerepkörökhöz, és feliratkozhat a termékekre és API-kra. További információt a Csoportok létrehozása és használata című témakörben [talál.][How to create and use groups]

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

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
