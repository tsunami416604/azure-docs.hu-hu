---
title: Az Azure IoT-megoldásgyorsítók és az Azure Active Directory |} A Microsoft Docs
description: Ismerteti, hogyan Azure IoT-megoldásgyorsítók az engedélyek kezelése Azure Active Directory.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: c88ae933360b5040ad3d2b877041049512f08b0d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303404"
---
# <a name="permissions-on-the-azureiotsolutionscom-site"></a>Engedélyek az azureiotsolutions.com webhelyen

## <a name="what-happens-when-you-sign-in"></a>Mi történik, ha bejelentkezik

Az első alkalommal bejelentkezik, [azureiotsuite.com][lnk-azureiotsolutions], a hely határozza meg a jogosultsági szintek rendelkezik az aktuálisan kijelölt Azure Active Directory (AAD) bérlői és az Azure-előfizetés alapján.

1. Először is feltöltheti a listát, a bérlők a felhasználónév mellett látható, hogy a hely házigazdája, az Azure-ból melyik AAD-bérlők, amelyekhez tartozik. Jelenleg a hely csak szerezhet be egy bérlő felhasználói jogkivonatok egyszerre. Ezért amikor használatával a legördülő listában a jobb felső sarokban a bérlők, a hely naplózza a arra a bérlőre, a jogkivonatok lekérésére, hogy a bérlő számára.

2. Ezután a hely házigazdája melyik előfizetések társítva van a kiválasztott bérlő Azure-ból. Amikor létrehoz egy új megoldásgyorsító megjelenik az elérhető előfizetésekkel.

3. Végül a hely lekéri az összes az erőforrásokat az előfizetésekhez és erőforráscsoportokhoz megoldásgyorsítók címkével, és feltölti a csempéket a kezdőlapon.

A következő szakaszok ismertetik a szerepköröket, amelyek szabályozzák a hozzáférést a megoldásgyorsítókhoz.

## <a name="aad-roles"></a>AAD-szerepkörök

Az AAD-szerepkörök szabályozása a megoldásgyorsítók üzembe helyezése, felhasználók és a egy megoldásgyorsító bizonyos Azure-szolgáltatások kezelésére.

Tudnivalók a rendszergazdai szerepkörökről további információ található az AAD [rendszergazdai szerepkörök hozzárendelése az Azure ad-ben][lnk-aad-admin]. A jelen cikkben összpontosít a **globális rendszergazdai** és a **felhasználói** címtárbeli szerepkörök a megoldásgyorsítók által használt.

### <a name="global-administrator"></a>Globális rendszergazda

AAD bérlőnként számos globális rendszergazda is lehet:

* Amikor létrehoz egy AAD-bérlőt, Ön alapértelmezés szerint a bérlő globális rendszergazdája.
* A globális rendszergazda helyezhet üzembe egy alapszintű és standard megoldásgyorsítók (alapszintű központi telepítést használ egy Azure virtuális gépen).

### <a name="domain-user"></a>Tartomány felhasználó

AAD bérlőnként több Tartományfelhasználók is lehetnek:

* Tartományi felhasználó helyezhet üzembe egy alapszintű megoldásgyorsító keresztül a [azureiotsolutions.com] [ lnk-azureiotsolutions] hely.
* Tartományi felhasználó hozhat létre egy alapszintű megoldásgyorsító a parancssori felület használatával.

### <a name="guest-user"></a>A vendégfelhasználók

AAD bérlőnként számos vendég felhasználók is lehet. Vendég felhasználók rendelkeznek jogosultságokkal korlátozott számú az AAD-bérlő. Ennek eredményeképpen vendégfelhasználók nem használhatók a megoldásgyorsítók az AAD-bérlőben.

További információ a felhasználók és szerepkörök az aad-ben a következő forrásanyagokban talál:

* [Felhasználók létrehozása az Azure ad-ben][lnk-create-edit-users]
* [Felhasználók hozzárendelése alkalmazások][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure-előfizetéshez, rendszergazdai szerepkörök

Az Azure-rendszergazdai szerepkörök szabályozása az Azure-előfizetés hozzárendelése egy AAD-bérlőt.

További információk a cikk az Azure-rendszergazdai szerepkörök [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái][lnk-admin-roles].

## <a name="faq"></a>GYIK

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném, ha az előfizetés és a egy adott AAD-bérlő közötti címtár leképezést módosítása. Hogyan végezhető el ez a feladat?

Lásd: [egy meglévő előfizetés hozzáadása az Azure AD-címtár](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Szeretném megváltoztatni a szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként szervezeti fiókkal bejelentkezve

Tekintse meg a támogatási cikk [módosítása szolgáltatás-rendszergazda és a társ-rendszergazdaként szervezeti fiókkal bejelentkezve][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg a hiba? "A fiók rendelkezik a megfelelő engedélyek megoldást hozhat létre. Ellenőrizze az a fiók rendszergazdája vagy próbálkozzon egy másik fiókkal."

Keresse meg a következő ábra útmutatást:

![][img-flowchart]

> [!NOTE]
> Ha továbbra is megjelenik a hiba, miután ellenőrizte, hogy-e az AAD-bérlő globális rendszergazdája és az előfizetés társadminisztrátorának, a fiók rendszergazdája eltávolítja a felhasználót és újbóli hozzárendelése ebben a sorrendben szükséges engedélyekkel rendelkezik. Először adja hozzá a felhasználót egy globális rendszergazdaként, és adja hozzá a felhasználó az Azure-előfizetés társadminisztrátorai. Ha a problémák továbbra is fennállnak, lépjen kapcsolatba [Súgó és támogatás][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért látok a hiba, amikor az Azure előfizetéssel rendelkezem? ", Előre konfigurált megoldásokat hozhat létre Azure-előfizetés szükséges. Létrehozhat egy ingyenes próbafiókot mindössze néhány perc alatt."

Ha Ön bizonyos Azure-előfizetéssel rendelkezik, a bérlő az előfizetés-leképezés érvényesítése, és győződjön meg arról, a megfelelő bérlő van kiválasztva a legördülő listában. Ha érvényesíteni kívánt bérlőre helyességéről, kövesse a fentebbi ábra, és az előfizetés és az AAD-bérlő a leképezés érvényesítése.

## <a name="next-steps"></a>További lépések
Az IoT-megoldásgyorsítók szeretne részletesebben is megismerkedni, tekintse meg, hogyan zajlik [megoldásgyorsítók testreszabása][lnk-customize].

[img-flowchart]: media/iot-accelerators-permissions/flowchart.png

[lnk-azureiotsolutions]: https://www.azureiotsolutions.com
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]:../active-directory/users-groups-roles/directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com
[lnk-create-edit-users]:../active-directory/fundamentals/active-directory-users-profile-azure-portal.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-accelerators-remote-monitoring-customize.md
