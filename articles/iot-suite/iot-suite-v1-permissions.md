---
title: Az Azure IoT Suite és az Azure Active Directory |} A Microsoft Docs
description: Ismerteti, hogyan Azure IoT Suite-ot használja engedélyek kezelése Azure Active Directoryban.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: a56d535ee06a097c7c18bcd507c25708f6a33f91
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296924"
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Engedélyek az azureiotsuite.com webhelyen

## <a name="what-happens-when-you-sign-in"></a>Mi történik, ha bejelentkezik

Az első alkalommal bejelentkezik, [azureiotsuite.com][lnk-azureiotsuite], a hely határozza meg a jogosultsági szintek rendelkezik az aktuálisan kijelölt Azure Active Directory (AAD) bérlői és az Azure-előfizetés alapján.

1. Először is feltöltheti a listát, a bérlők a felhasználónév mellett látható, hogy a hely házigazdája, az Azure-ból melyik AAD-bérlők, amelyekhez tartozik. Jelenleg a hely csak szerezhet be egy bérlő felhasználói jogkivonatok egyszerre. Ezért amikor használatával a legördülő listában a jobb felső sarokban a bérlők, a hely naplózza a arra a bérlőre, a jogkivonatok lekérésére, hogy a bérlő számára.

2. Ezután a hely házigazdája melyik előfizetések társítva van a kiválasztott bérlő Azure-ból. Az elérhető előfizetésekkel megjelenik egy új, előre konfigurált megoldás létrehozásakor.

3. Végül a hely lekéri az összes az erőforrásokat az előfizetésekhez és erőforráscsoportokhoz megjelölve az előre konfigurált megoldások és tölti fel a csempéket a kezdőlapon.

A következő szakaszok ismertetik a szerepköröket, amelyek szabályozzák a hozzáférést, az előre konfigurált megoldások.

## <a name="aad-roles"></a>AAD-szerepkörök

Az AAD-szerepkörök képessége kiépítése, előre konfigurált megoldások vezérlőelemet, és kezelni a felhasználókat az előre konfigurált megoldást.

Tudnivalók a rendszergazdai szerepkörökről további információ található az AAD [rendszergazdai szerepkörök hozzárendelése az Azure ad-ben][lnk-aad-admin]. A jelen cikkben összpontosít a **globális rendszergazdai** és a **felhasználói** címtárbeli szerepkörök az előre konfigurált megoldások által használt.

### <a name="global-administrator"></a>Globális rendszergazda

AAD bérlőnként számos globális rendszergazda is lehet:

* Amikor létrehoz egy AAD-bérlőt, Ön alapértelmezés szerint a bérlő globális rendszergazdája.
* A globális rendszergazda is egy előre konfigurált megoldás kiépítése, és hozzá van rendelve egy **rendszergazdai** szerepkör esetében az alkalmazás az AAD-bérlő belül.
* Az azonos AAD-bérlő egy másik felhasználója létrehoz egy alkalmazást, az alapértelmezett szerepkör a globális rendszergazda kap-e **ReadOnly**.
* Globális rendszergazda felhasználók hozzárendelése a szerepköröket használó alkalmazások számára a [az Azure portal][lnk-portal].

### <a name="domain-user"></a>Tartomány felhasználó

AAD bérlőnként több Tartományfelhasználók is lehetnek:

* Tartományi felhasználó építhető ki egy előre konfigurált megoldás keretében a [azureiotsuite.com] [ lnk-azureiotsuite] hely. Alapértelmezés szerint a tartományi felhasználó számára biztosított a **rendszergazdai** az üzembe helyezett alkalmazás szerepkörben.
* Egy tartományi felhasználó egy alkalmazást a build.cmd-parancsprogram használatával hozhat létre a [azure-iot-remote-monitoring][lnk-rm-github-repo], [azure-iot-predictive-maintenance] [ lnk-pm-github-repo], vagy [azure-iot-connected-factory] [ lnk-cf-github-repo] tárház. Viszont az alapértelmezett szerepkör a tartományi felhasználó számára biztosított, **ReadOnly**, mert egy tartományi felhasználó nincs engedélye rendelhet szerepköröket.
* Ha az AAD-bérlő egy másik felhasználója létrehoz egy alkalmazást, a tartományi felhasználó van hozzárendelve a **ReadOnly** szerepkör az adott alkalmazáshoz alapértelmezés szerint.
* Tartományi felhasználó nem rendelhető hozzá a szerepkört az alkalmazások számára. ezért egy tartományi felhasználó nem adható hozzá felhasználókkal vagy szerepkörökkel, a felhasználók számára az alkalmazás akkor is, ha azok építette ki azt.

### <a name="guest-user"></a>A vendégfelhasználók

AAD bérlőnként számos vendég felhasználók is lehet. Vendég felhasználók rendelkeznek jogosultságokkal korlátozott számú az AAD-bérlő. Ennek eredményeképpen a vendégfelhasználók nem sikerült kiépíteni egy előre konfigurált megoldásban az AAD-bérlő.

További információ a felhasználók és szerepkörök az aad-ben a következő forrásanyagokban talál:

* [Felhasználók létrehozása az Azure ad-ben][lnk-create-edit-users]
* [Felhasználók hozzárendelése alkalmazások][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure-előfizetéshez, rendszergazdai szerepkörök

Az Azure-rendszergazdai szerepkörök szabályozása az Azure-előfizetés leképezése egy AD-bérlővel.

További információk a cikk az Azure-rendszergazdai szerepkörök [hozzáadása vagy módosítása az Azure-előfizetés rendszergazdái][lnk-admin-roles].

## <a name="application-roles"></a>Alkalmazás-szerepkörök

Az alkalmazás-szerepkörök eszközök az előre konfigurált megoldásban való hozzáférés szabályozása.

Nincsenek a két meghatározott szerepkör és a egy kiosztott alkalmazás meghatározott egy implicit szerepkör:

* **Rendszergazda:** hozzáadása, kezelése, távolítsa el az eszközt és beállításainak módosítása a teljes körű vezérléssel rendelkezik.
* **Írásvédett:** megtekintheti az eszközök, szabályok, műveletek, feladatok és telemetriai adatokat.

Annak az az egyes szerepkörökhöz rendelt engedélyeket a [RolePermissions.cs] [ lnk-resource-cs] forrásfájl.

### <a name="changing-application-roles-for-a-user"></a>Egy felhasználó alkalmazás-szerepkörök módosítása

A következő eljárást használhatja az előre konfigurált megoldás az rendszergazdájának, a felhasználók az Active Directoryban.

Felhasználó szerepkörök módosítása egy aad-ben globális rendszergazdának kell lennie:

1. Nyissa meg az [Azure Portalt][lnk-portal].
2. Válassza az **Azure Active Directory** elemet.
3. Ellenőrizze, hogy használja a kiválasztott könyvtár az azureiotsuite.com webhelyen, a megoldás üzembe helyezésekor. Ha az előfizetéshez társított több címtárral rendelkezik, közöttük a jobb felső sarkában a portálon, a fiók nevére kattintva válthat.
4. Kattintson a **vállalati alkalmazások**, majd **minden alkalmazás**.
4. Megjelenítés **minden alkalmazás** az **bármely** állapotát. Keressen rá az előre konfigurált megoldás nevű kérelmet.
5. Kattintson a nevére, az alkalmazás, amely megegyezik az előre konfigurált megoldás nevét.
6. Kattintson a **Felhasználók és csoportok** elemre.
7. Válassza ki a használni kívánt szerepkörök felhasználó.
8. Kattintson a **hozzárendelése** , és válassza ki a szerepkört (például **rendszergazdai**), amelyet szeretne hozzárendelni a felhasználóhoz, kattintson a pipa jelre.

## <a name="faq"></a>GYIK

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>Szolgáltatás-rendszergazda vagyok, és szeretném, ha az előfizetés és a egy adott AAD-bérlő közötti címtár leképezést módosítása. Hogyan végezhető el ez a feladat?

Lásd: [egy meglévő előfizetés hozzáadása az Azure AD-címtár](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Egy tartományi felhasználói/tagot az AAD-bérlő vagyok, és létrehozott egy előre konfigurált megoldás. Hogyan tegye első kiosztott szerepkör saját alkalmazás?

Kérje meg a globális rendszergazda végez az AAD-bérlő, és ezután szerepkörök hozzárendelése a felhasználók számára saját magának egy globális rendszergazdához. Azt is megteheti kérje meg egy globális rendszergazdai, szerepkör hozzárendelése közvetlenül. Ha az AAD-bérlő módosítása az előre konfigurált megoldás van telepítve, a következő kérdésben talál.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Hogyan válthatok az AAD-bérlő, a távoli figyelési előre konfigurált megoldás és az alkalmazás hozzá van rendelve?

A felhőbeli telepítés futtatása <https://github.com/Azure/azure-iot-remote-monitoring> és ismételt üzembe helyezése egy újonnan létrehozott AAD-bérlőben. Mivel Ön, amikor hoz létre az AAD-bérlő globális rendszergazdájának alapértelmezés szerint felhasználók hozzáadásához és szerepköröket rendelhet felhasználókhoz engedélyekkel rendelkezik.

1. Hozzon létre egy AAD-címtárában található a [az Azure portal][lnk-portal].
2. Nyissa meg a következőt: <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Futtatás `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (például `build.cmd cloud debug myRMSolution`)
4. Amikor a rendszer kéri, állítsa be a **tenantid** kell az előző bérlő helyett az újonnan létrehozott bérlőhöz.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Szeretném megváltoztatni a szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként szervezeti fiókkal bejelentkezve

Tekintse meg a támogatási cikk [módosítása szolgáltatás-rendszergazda és a társ-rendszergazdaként szervezeti fiókkal bejelentkezve][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg a hiba? "A fiók rendelkezik a megfelelő engedélyek megoldást hozhat létre. Ellenőrizze az a fiók rendszergazdája vagy próbálkozzon egy másik fiókkal."

Keresse meg a következő ábra útmutatást:

![][img-flowchart]

> [!NOTE]
> Ha továbbra is megjelenik a hiba, miután ellenőrizte, hogy-e az AAD-bérlő globális rendszergazdai és a egy társ-rendszergazdaként az előfizetésre, a fiók rendszergazdája eltávolítja a felhasználót és újbóli hozzárendelése ebben a sorrendben szükséges engedélyekkel rendelkezik. Először adja hozzá a felhasználót egy globális rendszergazdaként, és adja hozzá a felhasználó az Azure-előfizetés társadminisztrátorai. Ha a problémák továbbra is fennállnak, lépjen kapcsolatba [Súgó és támogatás][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért látok a hiba, amikor az Azure előfizetéssel rendelkezem? ", Előre konfigurált megoldásokat hozhat létre Azure-előfizetés szükséges. Létrehozhat egy ingyenes próbafiókot mindössze néhány perc alatt."

Ha Ön bizonyos Azure-előfizetéssel rendelkezik, a bérlő az előfizetés-leképezés érvényesítése, és győződjön meg arról, a megfelelő bérlő van kiválasztva a legördülő listában. Ha érvényesíteni kívánt bérlőre helyességéről, kövesse a fentebbi ábra, és az előfizetés és az AAD-bérlő a leképezés érvényesítése.

## <a name="next-steps"></a>További lépések
IoT Suite megismerését a folytatáshoz tekintse meg, hogyan zajlik [előkonfigurált megoldások testreszabása][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]:../active-directory/manage-apps/assign-user-or-group-access-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
