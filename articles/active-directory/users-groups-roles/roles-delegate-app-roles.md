---
title: Alkalmazás rendszergazdai szerepkörei – Azure Active Directory delegálásának |} A Microsoft Docs
description: Szerepkörök, engedélyek jogosultságokat, az Azure Active Directory delegálásának alkalmazáshozzáférés-felügyeletet
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 2db33a6a2f086d5dacaff04ae226841d32cdc99a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513553"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Alkalmazás-rendszergazdai szerepkörök az Azure Active Directoryban delegálása

 Az Azure AD felügyeleti alkalmazás-hozzáférés körét a beépített rendszergazdai szerepkörök delegálása teszi lehetővé. Mellett globális rendszergazdai terhelés csökkentését, alkalmazás-hozzáférés feladatait speciális jogosultságokat delegálása is javíthatja biztonsági helyzetét és az esetleges jogosulatlan hozzáférést. Általános irányelvek és a delegálási problémák ismertetett [az Azure Active Directoryban történő delegálásához](roles-concept-delegation.md).

## <a name="delegate-app-administration"></a>Alkalmazások felügyelete

A következő szerepkörök alkalmazásregisztrációkat, egyszeri bejelentkezési beállításainak, felhasználó és csoport-hozzárendelések kezeléséhez, és hogy engedélyt adjanak az delegált engedélyek és alkalmazás engedélyeket (kivéve a Microsoft Graph és az Azure AD Graph) engedélyek megadása. Az egyetlen különbség, hogy az alkalmazás-rendszergazda szerepkör is engedélyt ad Application Proxy beállításainak kezelése. Szerepkörök sem tudja felügyelni a feltételes hozzáférés beállításait.
> [!IMPORTANT]
> Ehhez a szerepkörhöz rendelt felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz és ezeket a hitelesítő adatokat használja az alkalmazás identitás megszemélyesítése. Ez az alkalmazás azonosítóját a megszemélyesítési lehet egy megszerzését keresztül a felhasználó mit tehetnek a többi szerepkör-hozzárendelések alapján az Azure ad-ben. Ehhez a szerepkörhöz hozzárendelt felhasználó esetlegesen létrehozása vagy frissítése a felhasználók és más objektumok az alkalmazás megszemélyesítése közben.

Képes kezelni az alkalmazás-hozzáférés az Azure Portalon megadását:

1. Jelentkezzen be a [Azure AD-bérlő](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult a bérlő globális rendszergazdai szerepkörrel.
2. Ha rendelkezik megfelelő engedélyekkel, nyissa meg a [szerepkörök és a rendszergazdák lap](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Nyissa meg a tag hozzárendeléseiről megtekintéséhez a következő szerepkörök egyikét:
  * **Alkalmazás-rendszergazda**
  * **Felhőalkalmazás-rendszergazda**
4. Az a **tagok** a szerepkörhöz, válassza a lap **tag hozzáadása**.
5. Válassza ki egy vagy több tag hozzáadása a szerepkörhöz. <!--Members can be users or groups.-->

Megtekintheti, hogy ezek a szerepkörök leírása [elérhető szerepkörök](directory-assign-admin-roles.md#available-roles).

## <a name="delegate-app-registration"></a>Delegált alkalmazásregisztráció

Alapértelmezés szerint minden felhasználója létrehozhat alkalmazásregisztrációkat, de külön-külön biztosítson engedélyt alkalmazásregisztrációkat hozhat létre, vagy engedélyt adni az alkalmazás hitelesítéséhez.

1. Jelentkezzen be a [Azure AD-bérlő](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult a bérlő globális rendszergazdai szerepkörrel.
2. Ha beszerezte a megfelelő engedélyekkel, és állítsa a következők közül:
  * Az a [a bérlőhöz tartozó felhasználói beállítások lapon](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)állítsa be **felhasználók regisztrálhatnak alkalmazásokat** nem.
  * Az a [vállalati alkalmazások felhasználói beállításait](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)állítsa be **felhasználók engedélyezhetik alkalmazások hozzáférjenek a vállalati adatokhoz a felhasználók nevében** nem.
3. Rendelje hozzá a felhasználók ezt az engedélyt az alkalmazás fejlesztői igény szerint szerepkör tagjai.

Amikor egy felhasználó egy alkalmazás regisztrál, a rendszer automatikusan felveszi az alkalmazás első tulajdonosként.

## <a name="delegate-app-ownership"></a>Delegált alkalmazás tulajdonosa

Az alkalmazástulajdonosok és az alkalmazás regisztrációs tulajdonosai egyes kezelheti csak az alkalmazás alkalmazások vagy a saját alkalmazásregisztrációk. Például amikor hozzáadja a Salesforce alkalmazás tulajdonosát, a tulajdonos kezelheti a hozzáférést és konfigurációja a Salesforce-ban, de nem minden más alkalmazást. Egy alkalmazás számos tulajdonosokkal rendelkezhetnek, és a felhasználó lehet számos alkalmazás tulajdonosa.

Az alkalmazás tulajdonosa végezheti el:

* Módosítsa az alkalmazás tulajdonságait, például a nevét és az engedélyek az alkalmazás kérelmek
* Hitelesítő adatok kezelése
* Egyszeri bejelentkezés konfigurálása
* Felhasználói hozzáférés hozzárendelése
* Adja hozzá, vagy eltávolíthat más tulajdonosokat
* Az alkalmazásjegyzék szerkesztése
* Az alkalmazás közzététele az alkalmazáskatalógusban

> [!IMPORTANT]
> Ehhez a szerepkörhöz rendelt felhasználók hitelesítő adatok hozzáadása egy alkalmazáshoz és ezeket a hitelesítő adatokat használja az alkalmazás identitás megszemélyesítése. Ez az alkalmazás azonosítóját a megszemélyesítési lehet egy megszerzését keresztül a felhasználó mit tehetnek a többi szerepkör-hozzárendelések alapján az Azure ad-ben. Ehhez a szerepkörhöz hozzárendelt felhasználó esetlegesen létrehozása vagy frissítése a felhasználók és más objektumok az alkalmazás megszemélyesítése közben.

Az alkalmazásregisztráció tulajdonosa megtekinthetik és szerkeszthetik az alkalmazás regisztrációját.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>A tulajdonos hozzárendelése egy alkalmazáshoz

1. Jelentkezzen be a [Azure AD-bérlő](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult az alkalmazás-rendszergazda vagy a Felhőalkalmazás-rendszergazda a bérlő számára.
2. Az a [alkalmazásoldalán regisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) a bérlő, válassza ki egy alkalmazást arra a **áttekintése** az alkalmazás lapját.
3. Válassza ki **tulajdonosok** az alkalmazás a tulajdonosok listájának megtekintéséhez.
4. Válassza ki **Hozzáadás** válassza ki egy vagy több tulajdonosok hozzáadása az alkalmazáshoz.

### <a name="to-assign-an-owner-to-an-application-registration"></a>Tulajdonos hozzárendelése egy alkalmazás regisztrálása

1. Jelentkezzen be a [Azure AD-bérlő](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) egy olyan fiókkal, amely jogosult az alkalmazás-rendszergazda vagy a felhőalapú alkalmazás rendszergazdai szerepkör a bérlőben.
2. Ha rendelkezik megfelelő engedélyekkel a a [vállalati alkalmazások lap](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) a bérlő, válassza ki egy alkalmazás regisztrációját a megnyitásához.
3. Válassza ki **beállítások**.
4. Válassza ki **tulajdonosok** a a **beállítások** oldalon tekintheti meg az alkalmazás a tulajdonosok listája.
5. Válassza ki **Hozzáadás tulajdonosa** válassza ki egy vagy több tulajdonosok hozzáadása az alkalmazáshoz.

## <a name="next-steps"></a>További lépések

* [Az Azure AD rendszergazdai szerepkör leírása](directory-assign-admin-roles.md)
