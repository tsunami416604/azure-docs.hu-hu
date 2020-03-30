---
title: Bérlői rendszergazdai hozzájárulás megadása egy alkalmazáshoz – Azure AD
description: Ismerje meg, hogyan adhat bérlői szintű hozzájárulást egy alkalmazásnak, hogy a végfelhasználók ne kérhessenek beleegyezést az alkalmazásba való bejelentkezéskor.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480917"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Bérlői rendszergazdai hozzájárulás megadása egy alkalmazáshoz

Ismerje meg, hogyan egyszerűsítheti a felhasználói élményt azáltal, hogy bérlői szintű rendszergazdai jóváhagyást ad egy alkalmazásnak. Ez a cikk ad a különböző módon elérni ezt. A módszerek az Azure Active Directory (Azure AD) bérlője összes végfelhasználóra vonatkoznak.

Az alkalmazásokhoz való hozzájárulásról az [Azure Active Directory hozzájárulási keretrendszerben](../develop/consent-framework.md)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

A bérlői rendszergazdai hozzájárulás megadásához [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [felhőalkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)kell bejelentkeznie.

> [!IMPORTANT]
> Ha egy alkalmazás megkapta a bérlői szintű rendszergazdai jóváhagyást, minden felhasználó be tud jelentkezni az alkalmazásba, kivéve, ha úgy van beállítva, hogy felhasználói hozzárendelést igényeljen. Ha korlátozni szeretné, hogy mely felhasználók jelentkezhetnek be egy alkalmazásba, felhasználói hozzárendelést igényel, majd felhasználókat vagy csoportokat rendelhet az alkalmazáshoz. További információ: [Metódusok és csoportok hozzárendelése.](methods-for-assigning-users-and-groups.md)

> [!WARNING]
> Bérlőszintű rendszergazdai hozzájárulás megadása egy alkalmazáshoz hozzáférést biztosít az alkalmazásnak és az alkalmazás közzétevőjének a szervezet adataihoz. A jóváhagyás megadása előtt figyelmesen tekintse át az alkalmazás által kért engedélyeket.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Rendszergazdai hozzájárulás megadása az Azure Portaltól

### <a name="grant-admin-consent-in-enterprise-apps"></a>Rendszergazdai hozzájárulás megadása az Enterprise-alkalmazásokban

A bérlői szintű rendszergazdai jóváhagyást *vállalati alkalmazásokon* keresztül adhat, ha az alkalmazás már ki van építve a bérlőben. Például egy alkalmazás kiépíthető a bérlőben, ha legalább egy felhasználó már hozzájárult az alkalmazáshoz. További információ: [Hogyan és miért kerülnek hozzá alkalmazások az Azure Active Directoryhoz.](../develop/active-directory-how-applications-are-added.md)

A vállalati alkalmazásokban felsorolt alkalmazások bérlői szintű rendszergazdai hozzájárulás **megadása:**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [felhőalkalmazás-rendszergazdaként.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Válassza az **Azure Active Directory,** majd **a Vállalati alkalmazások lehetőséget.**
3. Válassza ki azt az alkalmazást, amelynek bérlői rendszergazdai jóváhagyást szeretne adni.
4. Válassza **az Engedélyek lehetőséget,** majd kattintson **a Rendszergazda ijedésének megadása gombra.**
5. Figyelmesen tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha egyetért az alkalmazás által megkövetelő engedélyekkel, adja meg a hozzájárulást. Ha nem, kattintson a **Mégse gombra,** vagy zárja be az ablakot.

### <a name="grant-admin-consent-in-app-registrations"></a>Rendszergazdai hozzájárulás megadása az alkalmazásregisztrációkban

A szervezet által kifejlesztett vagy közvetlenül az Azure AD-bérlőben regisztrált alkalmazások esetében az Azure Portalon **az alkalmazásregisztrációk** bérlői szintű rendszergazdai jóváhagyást is megadhat.

Az **alkalmazásregisztrációk**bérlői szintű rendszergazdai hozzájárulás megadása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) [globális rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator), [alkalmazás-rendszergazdaként](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)vagy [felhőalkalmazás-rendszergazdaként.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)
2. Válassza az **Azure Active Directory,** majd **az alkalmazásregisztrációk lehetőséget.**
3. Válassza ki azt az alkalmazást, amelynek bérlői rendszergazdai jóváhagyást szeretne adni.
4. Válassza az **API-engedélyek lehetőséget,** majd kattintson **a Rendszergazdai jóváhagyás megadása gombra.**
5. Figyelmesen tekintse át az alkalmazás által igényelt engedélyeket.
6. Ha egyetért az alkalmazás által megkövetelő engedélyekkel, adja meg a hozzájárulást. Ha nem, kattintson a **Mégse gombra,** vagy zárja be az ablakot.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>A bérlői szintű rendszergazdai jóváhagyás megadásához szükséges URL-cím kialakítása

Ha bérlői szintű rendszergazdai jóváhagyást ad a fent leírt mindkét módszer használatával, egy ablak nyílik meg az Azure Portalról, hogy kérje a bérlői szintű rendszergazdai jóváhagyást. Ha ismeri az alkalmazás ügyfélazonosítóját (más néven az alkalmazásazonosítót), létrehozhatja ugyanazt az URL-címet a bérlői szintű rendszergazdai jóváhagyás megadásához.

A bérlői szintű rendszergazdai hozzájárulási URL a következő formátumban jelenik meg:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

ahol:

* `{client-id}`az alkalmazás ügyfélazonosítója (más néven alkalmazásazonosító).
* `{tenant-id}`a szervezet bérlői azonosítója vagy bármely ellenőrzött tartománynév.

Mint mindig, a hozzájárulás megadása előtt alaposan tekintse át az alkalmazáskérelmek hez szükséges engedélyeket.

## <a name="next-steps"></a>További lépések

[Annak konfigurálása, hogy a végfelhasználók hogyan járuljanak hozzá az alkalmazásokhoz](configure-user-consent.md)

[A rendszergazdai hozzájárulási munkafolyamat konfigurálása](configure-admin-consent-workflow.md)

[Engedélyek és hozzájárulás a Microsoft identitásplatformján](../develop/active-directory-v2-scopes.md)

[Azure AD a StackOverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)
