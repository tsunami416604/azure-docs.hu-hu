---
title: Bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazáshoz – Azure AD
description: Megtudhatja, hogyan adhat meg bérlői szintű jóváhagyást egy alkalmazáshoz, hogy a végfelhasználók ne kérjenek jóváhagyást egy alkalmazásba való bejelentkezéskor.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75480917"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bérlői szintű rendszergazdai jóváhagyás engedélyezése egy alkalmazás számára

Megtudhatja, hogyan egyszerűsítheti a felhasználói élményt a bérlői szintű rendszergazdai jóváhagyás megadásával egy alkalmazáshoz. Ez a cikk a megvalósításának különböző módjait ismerteti. A módszerek a Azure Active Directory (Azure AD) bérlő összes végfelhasználója számára érvényesek.

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

A bérlői szintű rendszergazdai jóváhagyás megadása megköveteli, hogy [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [felhőalapú alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)jelentkezzen be.

> [!IMPORTANT]
> Ha egy alkalmazás teljes körű rendszergazdai jogosultsággal rendelkezik, az összes felhasználó be tud jelentkezni az alkalmazásba, kivéve, ha úgy van konfigurálva, hogy felhasználói hozzárendelést igényeljen. Ha szeretné korlátozni, hogy mely felhasználók jelentkezhetnek be egy alkalmazásba, megkövetelheti a felhasználói hozzárendelést, majd felhasználókat vagy csoportokat rendelhet hozzá az alkalmazáshoz. További információ: [felhasználók és csoportok hozzárendelésének módszerei](methods-for-assigning-users-and-groups.md).

> [!WARNING]
> A bérlői szintű rendszergazdai jóváhagyás megadása egy alkalmazás számára megadja az alkalmazásnak és az alkalmazás közzétevője számára a szervezet adataihoz való hozzáférést. A jóváhagyás megadása előtt körültekintően tekintse át az alkalmazás által kért engedélyeket.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Rendszergazdai jóváhagyás megadása a Azure Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Rendszergazdai jóváhagyás megadása a vállalati alkalmazásokban

Ha az alkalmazás már üzembe lett helyezve a bérlőben, a bérlői szintű rendszergazdai jóváhagyást is megadhatja a *vállalati alkalmazásokon* keresztül. Egy alkalmazás például kiépíthető a bérlőben, ha legalább egy felhasználó már beleegyezett az alkalmazásba. További információ: [how és Why alkalmazások hozzáadása a Azure Active Directoryhoz](../develop/active-directory-how-applications-are-added.md).

A bérlői szintű rendszergazdai jóváhagyás biztosítása a **vállalati alkalmazásokban**felsorolt alkalmazásokhoz:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), alkalmazás- [rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [Felhőbeli alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Válassza **Azure Active Directory** majd a **vállalati alkalmazások**lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jogosultságot szeretne adni.
4. Válassza az **engedélyek** lehetőséget, majd kattintson a **rendszergazdai jóváhagyás megadása**lehetőségre.
5. Körültekintően tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha elfogadja az alkalmazás által igényelt engedélyeket, engedélyezze a jóváhagyást. Ha nem, kattintson a **Mégse** gombra, vagy az ablak bezárásához.

### <a name="grant-admin-consent-in-app-registrations"></a>Rendszergazdai jóváhagyás megadása Alkalmazásregisztrációk

A szervezet által fejlesztett, vagy közvetlenül az Azure AD-bérlőben regisztrált alkalmazások esetében a bérlői szintű rendszergazdai jóváhagyást is megadhatja **Alkalmazásregisztrációk** Azure Portal.

A bérlői szintű rendszergazdai jóváhagyás engedélyezése **Alkalmazásregisztrációktól**:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), alkalmazás- [rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [Felhőbeli alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Válassza a **Azure Active Directory** majd **Alkalmazásregisztrációk**lehetőséget.
3. Válassza ki azt az alkalmazást, amelyhez bérlői szintű rendszergazdai jogosultságot szeretne adni.
4. Válassza az **API-engedélyek** lehetőséget, majd kattintson a **rendszergazdai jóváhagyás megadása**lehetőségre.
5. Körültekintően tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha elfogadja az alkalmazás által igényelt engedélyeket, engedélyezze a jóváhagyást. Ha nem, kattintson a **Mégse** gombra, vagy az ablak bezárásához.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Az URL-cím létrehozása a bérlői szintű rendszergazdai jóváhagyás biztosításához

Amikor a fent ismertetett módon megadta a bérlői szintű rendszergazdai jóváhagyást, egy ablak nyílik meg a Azure Portalból, amely a bérlői szintű rendszergazdai hozzájárulás megadására kéri. Ha ismeri az alkalmazás ügyfél-AZONOSÍTÓját (más néven az alkalmazás AZONOSÍTÓját), ugyanazt az URL-címet is létrehozhatja a bérlői szintű rendszergazdai jóváhagyás biztosításához.

A bérlői szintű rendszergazdai beleegyező URL-cím a következő formátumot követi:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

ahol:

* `{client-id}`az alkalmazás ügyfél-azonosítója (más néven alkalmazás-azonosító).
* `{tenant-id}`a szervezete bérlői azonosítója vagy bármely ellenőrzött tartománynév.

Ahogy mindig, körültekintően tekintse át az alkalmazások kérelmének engedélyeit a jóváhagyás megadása előtt.

## <a name="next-steps"></a>További lépések

[Annak konfigurálása, hogy a végfelhasználók hogyan hozzájárulásukat az alkalmazásokhoz](configure-user-consent.md)

[Rendszergazdai engedélyezési munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[Engedélyek és beleegyezett a Microsoft Identity platform](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
