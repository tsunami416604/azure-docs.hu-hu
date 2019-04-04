---
title: Hogyan kezelheti a felhasználói fiókokat az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan létrehozása vagy meghívása a felhasználók az Azure API Management szolgáltatásban
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
ms.openlocfilehash: 6a4ad827c39ec106acdc7b52a5b769ab6e7febf8
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893945"
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Az Azure API Management a felhasználói fiókok kezelése

Az API Management szolgáltatásban a fejlesztők az API-t, amely elérhetővé teszi az API Management segítségével a felhasználók. Ez az útmutató ismerteti, hogyan hozhat létre, és meghívhatja a fejlesztők az API-k és termékek használatára, hogy elérhetővé számukra az API Management-példánnyal. A felhasználói fiókok kezelése programozott módon további információkért lásd: a [felhasználó típusú entitás](https://docs.microsoft.com/en-us/rest/api/apimanagement/user) dokumentációjában találhatók a [API Management REST](/rest/api/apimanagement/) hivatkozást.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Előfeltételek

Végezze el a cikkben leírt feladatok: [Az Azure API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-developer"> </a>Hozzon létre egy új fejlesztő

Új felhasználó hozzáadásához kövesse a jelen szakaszban ismertetett lépéseket:

1. Válassza ki a **felhasználók** lapon, a képernyő bal oldalán.
2. Nyomja meg **+ Hozzáadás**.
3. Adja meg a felhasználó megfelelő adatait.
4. Nyomja meg az **Add** (Hozzáadás) gombot.

    ![Új felhasználó hozzáadása](./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png)

Alapértelmezés szerint az újonnan létrehozott fejlesztői fiókok olyan **aktív**, és társított a **fejlesztők** csoport. Fejlesztői fiókok egy **aktív** állapot használható összes, amelyhez előfizetéssel rendelkezik, az API-k eléréséhez. Az újonnan létrehozott fejlesztői további csoporthoz rendeléséről, lásd: [hogyan csoportok társítása a fejlesztőkhöz][How to associate groups with developers].

## <a name="invite-developer"> </a>A fejlesztő meghívása
Meghívása a fejlesztői, kövesse a jelen szakaszban ismertetett lépéseket:

1. Válassza ki a **felhasználók** lapon, a képernyő bal oldalán.
2. Nyomja meg **+ meghívása**.

Egy megerősítő üzenet jelenik meg, de az újonnan meghívott fejlesztői nem jelenik meg a listában, amíg azok a meghívás elfogadása után. 

Amikor egy fejlesztő felkérik, e-mailt küldeni a fejlesztői van. Ez az e-mail sablon használatával jön létre, és testre szabható. További információkért lásd: [konfigurálása e-mail-sablonok][Configure email templates].

Miután elfogadták a meghívót, a fiók aktívvá válik.

## <a name="block-developer"> </a> Inaktiválása, és a egy fejlesztői fiók újraaktiválása

Alapértelmezés szerint az újonnan létrehozott vagy a meghívott fejlesztői fiókok olyan **aktív**. Egy fejlesztői fiók nevében inaktiválásához kattintson **blokk**. Egy letiltott fejlesztői fiók nevében újraaktiválásához kattintson **aktiválás**. Egy letiltott fejlesztői fiók nevében nem érhetik el a fejlesztői portált, és bármely API-jainak hívására. A felhasználói fiók törléséhez kattintson **törlése**.

Felhasználó blokkolása, kövesse az alábbi lépéseket.

1. Válassza ki a **felhasználók** lapon, a képernyő bal oldalán.
2. Kattintson a letiltani kívánt felhasználó.
3. Nyomja meg **blokk**.

## <a name="reset-a-user-password"></a>Felhasználói jelszó alaphelyzetbe állítása

Felhasználói fiókok programozott módon dolgozni, tekintse meg a [felhasználó típusú entitás](https://docs.microsoft.com/en-us/rest/api/apimanagement/user) dokumentációjában találhatók a [API Management REST](/rest/api/apimanagement/) referencia. Egy adott értékre állítsa vissza egy felhasználói fiók jelszavát, használhatja a [frissíteni egy felhasználó](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-user-entity#UpdateUser) műveletet, és adja meg a kívánt jelszót.

## <a name="next-steps"></a>Következő lépések
Egy fejlesztői fiók létrehozása után társítsa a szerepkörök, és előfizetés-termékek és API-k. További információkért lásd: [csoportok létrehozása és használata annak][How to create and use groups].

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
