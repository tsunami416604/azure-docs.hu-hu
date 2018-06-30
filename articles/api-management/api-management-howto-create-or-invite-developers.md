---
title: Hogyan kezelheti a felhasználói fiókokat az Azure API Management |} Microsoft Docs
description: Megtudhatja, hogyan hozható létre, vagy a meghívott felhasználóknak az Azure API Management
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
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 3d9a4454a1b3f65b42a46a26e8d483fad83f65f6
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111269"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Az Azure API Management felhasználói fiókok kezelése
Az API Management fejlesztők akkor teszi közzé az API Management használata API-k felhasználók. Ez az útmutató bemutatja, hogyan hozhat létre, és a fejlesztők meghívása a az API-k és termékek használatára, hogy elérhetővé számukra az API Management-példánnyal. A felhasználói fiókok kezelése programozott módon információkért lásd: a [felhasználói entitás](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity) dokumentációjában találhatók a [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) hivatkozás.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feladat-: [hozzon létre egy Azure API Management példányt](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Hozzon létre egy új fejlesztő

Új felhasználó hozzáadásához kövesse a jelen szakaszban szereplő lépéseket:

1. Válassza ki a **felhasználók** lap bal oldalán a képernyőn.
2. Nyomja le az **+ Hozzáadás**.
3. Adja meg a felhasználó megfelelő információkat.
4. Nyomja meg az **Add** (Hozzáadás) gombot.

    ![Új felhasználó hozzáadása](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok vannak **aktív**, és a társított a **fejlesztők** csoport. A fejlesztői fiókok egy **aktív** állapota az API-kat, amelyeknél az előfizetések összes eléréséhez használható. Az újonnan létrehozott fejlesztői társítandó további csoportokat, lásd: [csoportok társítása fejlesztők][How to associate groups with developers].

## <a name="invite-developer"> </a>Egy fejlesztő meghívása
Meghívni egy fejlesztő, kövesse a jelen szakaszban szereplő lépéseket:

1. Válassza ki a **felhasználók** lap bal oldalán a képernyőn.
2. Nyomja le az **+ meghívása**.

Egy megerősítő üzenet jelenik meg, de az újonnan meghívott fejlesztői nem szerepelnek a listán csak azok elfogadja a meghívót. 

Amikor egy fejlesztő felkérik, egy e-mailt küld a fejlesztőnek. Ez az e-mail sablon használatával jön létre, és testre szabható. További információkért lásd: [konfigurálás e-mail sablonok][Configure email templates].

Ha elfogadja a meghívót, a fiók aktívvá válik.

## <a name="block-developer"> </a> Fejlesztői fiók létrehozása újraaktiválása és inaktiválása

Alapértelmezés szerint az újonnan létrehozott vagy a meghívott fejlesztői fiókok vannak **aktív**. Fejlesztői fiók létrehozása inaktiválásához kattintson **blokk**. Letiltott fejlesztői fiók létrehozása újraaktiválásához kattintson **aktiválás**. Letiltott fejlesztői fiók létrehozása a nem fér hozzá a fejlesztői portálján, vagy minden API-k hívása. A felhasználói fiók törléséhez kattintson **törlése**.

Felhasználó letiltása, kövesse az alábbi lépéseket.

1. Válassza ki a **felhasználók** lap bal oldalán a képernyőn.
2. Kattintson a letiltani kívánt felhasználó.
3. Nyomja le az **blokk**.

## <a name="reset-a-user-password"></a>Felhasználói jelszó alaphelyzetbe állítása

Felhasználói fiókok programozott módon dolgozni, tekintse meg a [felhasználói entitás](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity) dokumentációjában találhatók a [API Management REST](https://msdn.microsoft.com/library/azure/dn776326.aspx) hivatkozás. A felhasználói fiók jelszavának visszaállítása egy adott értékre, használhatja a [egy felhasználó frissítéséhez](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) műveletet, és adja meg a kívánt jelszót.

## <a name="next-steps"></a>Következő lépések
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

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
