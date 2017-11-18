---
title: "Az Azure IoT Suite és az Azure Active Directory |} Microsoft Docs"
description: "Ismerteti, hogyan Azure IoT Suite az Azure Active Directory használatával kezeli az engedélyeket."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 246228ba-954a-4d96-b6d6-e53e4590cb4f
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: dobett
ms.openlocfilehash: 4c4916037ad762dbb9dee803dfd45ec2fd89272b
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Engedélyek az azureiotsuite.com webhelyen

## <a name="what-happens-when-you-sign-in"></a>Mi történik, amikor bejelentkezik

Az első bejelentkezéskor, [azureiotsuite.com][lnk-azureiotsuite], a hely határozza meg a jogosultsági szintek rendelkezik a kijelölt Azure Active Directory (AAD) bérlői és az Azure-előfizetés alapján.

1. Először töltse fel adatokkal a bérlők mellett a felhasználónévnek látható listáját, a hely szerzi meg az Azure-ból mely AAD-bérlő tartozik. Jelenleg a hely csak szerezhet be egy bérlői felhasználói jogkivonatokhoz egyszerre. Ezért amikor a bérlők a legördülő lista használatával jobb felső sarokban, a hely naplózza a arra a bérlőre, hogy a bérlő a tokenek beszerzése.

2. A hely a következő megkeresése, melyik előfizetések vannak társítva a kiválasztott bérlő az Azure-ból. Megjelenik az elérhető előfizetésekkel, amikor létrehoz egy új előkonfigurált megoldást.

3. Végezetül a hely veszi át összes lévő erőforrásokat a előfizetésekhez és erőforráscsoportokhoz előkonfigurált megoldásokat címkével rendelkeznek, és feltölti a csempék a kezdőlapon.

A következő szakaszok ismertetik a szerepköröket, amelyek hozzáférést az előre konfigurált megoldásokhoz.

## <a name="aad-roles"></a>Az AAD-szerepkörök

Az AAD-szerepkörök szabályozhatja a képes előre konfigurált rendelkezés megoldások, és előre konfigurált megoldás a felhasználók kezeléséhez.

Tudnivalók a rendszergazdai szerepkörökről további információkat talál az aad-ben a [rendszergazdai szerepkörök hozzárendelése az Azure AD][lnk-aad-admin]. A jelen cikkben összpontosít a **globális rendszergazda** és a **felhasználói** directory szerepkörök az előkonfigurált megoldásokat által használt.

### <a name="global-administrator"></a>Globális rendszergazda

Az AAD bérlőnként globális rendszergazdák közül sokan lehet:

* Amikor létrehoz egy AAD-bérlőt, áll alapértelmezés szerint, hogy a bérlő globális rendszergazdája.
* A globális rendszergazda előkonfigurált megoldást hozhat létre, és hozzá van rendelve egy **Admin** szerepkör az alkalmazás az AAD-bérlőt belül.
* Ha egy másik felhasználója ugyanazt az AAD-bérlőt alkalmazást hoz létre, az alapértelmezett számára a globális rendszergazdai szerepköre **ReadOnly**.
* A globális rendszergazda felhasználók hozzárendelése a szerepkörök használó alkalmazások esetében a [Azure-portálon][lnk-portal].

### <a name="domain-user"></a>Tartományi felhasználó

AAD-bérlőt sok tartományi felhasználók lehet:

* A tartományi felhasználók építhető ki előkonfigurált megoldást a [azureiotsuite.com] [ lnk-azureiotsuite] hely. Alapértelmezés szerint a tartományi felhasználó számára biztosított a **Admin** szerepkör a kiépített alkalmazásban.
* Egy olyan tartományi felhasználó hozhat létre egy alkalmazást a build.cmd parancsfájl a [azure iot-távoli-ellenőrző][lnk-rm-github-repo], [azure iot – prediktív-karbantartás] [ lnk-pm-github-repo], vagy [azure iot-csatlakoztatott-gyári] [ lnk-cf-github-repo] tárházba. Azonban az alapértelmezett a tartományi felhasználó számára biztosított szerepköre **ReadOnly**, mert egy olyan tartományi felhasználó nem jogosult rendelhet szerepköröket.
* Az AAD-bérlőt egy másik felhasználója alkalmazást hoz létre, ha a tartományi felhasználó van-e hozzárendelve a **ReadOnly** szerepkör az alkalmazás alapértelmezés szerint.
* Egy olyan tartományi felhasználó nem rendelhető hozzá a szerepkört az alkalmazások számára. ezért egy olyan tartományi felhasználó nem vehető fel felhasználók vagy szerepkörök a felhasználók számára az alkalmazás akkor is, ha azok kiépítése azt.

### <a name="guest-user"></a>Vendég felhasználó

Az AAD bérlőnként számos Vendég felhasználó lehet. Vendégfelhasználók rendelkeznek korlátozott jogok az AAD-bérlőben. Ennek eredményeképpen a vendégfelhasználók az AAD-bérlőt az előkonfigurált megoldás nem használhatók.

Felhasználók és szerepkörök az aad-ben kapcsolatos további információkért lásd a következőket:

* [Felhasználók létrehozása az Azure ad-ben][lnk-create-edit-users]
* [Felhasználók hozzárendelése alkalmazásokhoz][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Azure-előfizetéshez rendszergazdai szerepkörök

Az Azure rendszergazdai szerepkörök az Azure-előfizetés van leképezve az AD-bérlő általi szabályozása.

További információk a cikkben az Azure rendszergazdai szerepkörök [hozzáadása vagy módosítása az Azure Társadminisztrátoraként, a szolgáltatás-rendszergazda és a fiók rendszergazdájához][lnk-admin-roles].

## <a name="application-roles"></a>Alkalmazás-szerepkörök

A szerepkörökhöz az előkonfigurált megoldás eszközökre való hozzáférés szabályozása.

Két megadott szerepkörök és meghatározott egy telepített alkalmazás egy implicit szerepkör vannak:

* **Felügyeleti:** hozzáadása, kezelése, távolítsa el az eszközök és beállítások módosítása teljes körű vezérléssel rendelkezik.
* **Csak olvasható:** eszközök, szabályok, műveletek, feladatok és telemetriai adatok megtekintéséhez.

A jogosultságait az egyes szerepkör található a [RolePermissions.cs] [ lnk-resource-cs] forrásfájl.

### <a name="changing-application-roles-for-a-user"></a>A felhasználó alkalmazás-szerepkörök módosítása

Az alábbi eljárás segítségével végezze el a felhasználó az Active Directory rendszergazdája az előkonfigurált megoldás.

Egy felhasználó szerepköreinek módosítása egy aad-ben globális rendszergazdának kell lennie:

1. Nyissa meg az [Azure Portalt][lnk-portal].
2. Válassza ki **az Azure Active Directory**.
3. Ellenőrizze, hogy használ a könyvtárat úgy döntött, hogy a azureiotsuite.com létesített a megoldás. Ha az Ön előfizetéséhez rendelve több címtárral rendelkezik, válthat őket, ha a fiók nevére, a portál felső – jobb kattint.
4. Kattintson a **vállalati alkalmazások**, majd **összes alkalmazás**.
4. Megjelenítése **összes alkalmazás** rendelkező **bármely** állapotát. Majd keresse meg az előkonfigurált megoldás nevű kérelmet.
5. Kattintson a nevére, az alkalmazás, amely megfelel az előkonfigurált megoldás neve.
6. Kattintson a **felhasználók és csoportok**.
7. Válassza ki a használni kívánt szerepkörök felhasználót.
8. Kattintson a **hozzárendelése** , válassza ki a szerepkört (például **Admin**) szeretné rendelni a felhasználóhoz, kattintson a pipa jelre.

## <a name="faq"></a>GYIK

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-complete-this-task"></a>A szolgáltatás-rendszergazdáknak vagyok, és szeretnék módosítani a könyvtár leképezése az előfizetésem és egy adott AAD-bérlőt között. Hogyan hajthatja végre ezt a feladatot?

1. Lépjen a [a klasszikus Azure portálon][lnk-classic-portal], kattintson a **beállítások** a bal oldalon található szolgáltatások közül.
2. Válassza ki az előfizetést, módosítsa a könyvtárat a leképezés csak akkor szeretne.
3. Kattintson a **könyvtár**.
4. Válassza ki a **Directory** szeretné használni a legördülő listában. A továbbítási nyílra.
5. Erősítse meg a könyvtár leképezése és társrendszergazdák hatással. Ha áthelyez egy másik címtárból, a rendszer eltávolítja minden társrendszergazdák az eredeti könyvtárból.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Egy tartományi felhasználói/tagja az AAD-bérlőt vagyok, és létrehozott egy előre konfigurált megoldás. Hogyan tegye beolvasása kiosztott egy szerepkört az alkalmazáshoz?

Kérje meg a globális rendszergazdák is képesek adja meg az AAD-bérlőt a globális rendszergazdaként, majd rendelje hozzá szerepkörök a felhasználók számára saját maga. Azt is megteheti kérje meg egy globális rendszergazda, hogy rendeljen Önhöz közvetlenül egy szerepkört. Ha szeretné módosítani az AAD-bérlőt az előkonfigurált megoldás már alkalmazva van, tekintse meg a következő kérdés.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Az AAD-bérlőt, a távoli felügyeleti előkonfigurált megoldás és az alkalmazás hozzárendelni átváltása?

A felhő üzembe helyezése a futtatása <https://github.com/Azure/azure-iot-remote-monitoring> és telepítse újra az újonnan létrehozott AAD-bérlőt. Mert, alapértelmezés szerint egy globális rendszergazda, amikor létrehoz egy AAD-bérlőt Ön jogosult felhasználók hozzáadásához és szerepkörök hozzárendelése azoknak a felhasználóknak.

1. Hozzon létre egy AAD-címtárában lévő a [Azure-portálon][lnk-portal].
2. Ugrás a <https://github.com/Azure/azure-iot-remote-monitoring>.
3. Futtatás `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (például `build.cmd cloud debug myRMSolution`)
4. Amikor a rendszer kéri, állítsa be a **tenantid** kell lennie az újonnan létrehozott bérlő az előző bérlő helyett.

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>A szolgáltatás rendszergazdai vagy társadminisztrátori Ha egy szervezeti fiókkal jelentkezik módosítása

Tekintse meg a támogatási cikk [módosítása szolgáltatás-rendszergazda és Társadminisztrátoraként, amikor egy szervezeti fiókkal jelentkezik be][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Miért jelenik meg ezt a hibát? "A fiók nem rendelkezik a megoldás létrehozása a megfelelő engedélyekkel. Ellenőrizze a fiók rendszergazdájához, vagy próbálkozzon egy másik fiókkal."

Tekintse meg az alábbi ábrában útmutatást:

![][img-flowchart]

> [!NOTE]
> Ha továbbra is megjelenik a hiba akkor ellenőrzése után egy globális rendszergazdája az AAD-bérlőt és egy az előfizetés társadminisztrátoraként, a fiók rendszergazdájához, távolítsa el a felhasználót, és újra hozzárendelnie az itt megadott sorrendben szükséges engedélyekkel rendelkezik. Először adja hozzá a felhasználót egy globális rendszergazdaként, és adja hozzá a felhasználó az Azure-előfizetés társadminisztrátoraként. Ha a probléma továbbra is fennáll, forduljon a [Súgó és támogatás][lnk-help-support].

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Miért jelenik meg a hiba, ha az Azure-előfizetésre van? "Előkonfigurált megoldások létrehozásához Azure-előfizetés szükséges. Létrehozhat egy ingyenes próbafiók néhány percig."

Ha bizonyos Azure-előfizetéssel rendelkezik, a bérlő hozzárendelése az előfizetéshez, és ellenőrizheti a megfelelő bérlő a legördülő listában kiválasztott. Ha ellenőrizte, hogy a kívánt bérlő megfelelő, kövesse a fenti ábrán és az előfizetés és az AAD-bérlőt a leképezés érvényesítése.

## <a name="next-steps"></a>Következő lépések
És folytathatja az IoT Suite, tekintse meg, hogyan zajlik [előkonfigurált megoldás testreszabása][lnk-customize].

[img-flowchart]: media/iot-suite-v1-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-cf-github-repo]: https://github.com/Azure/azure-iot-connected-factory
[lnk-aad-admin]: ../active-directory/active-directory-assign-admin-roles.md
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-portal]: https://portal.azure.com/
[lnk-create-edit-users]: ../active-directory/active-directory-create-users.md
[lnk-assign-app-roles]: ../active-directory/active-directory-coreapps-assign-user-azure-portal.md
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: ../billing/billing-add-change-azure-subscription-administrator.md
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
