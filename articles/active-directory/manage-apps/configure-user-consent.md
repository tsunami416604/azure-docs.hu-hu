---
title: Felhasználói beleegyezikés beállítása egy alkalmazáshoz – Azure Active Directory
description: Megtudhatja, hogyan kezelheti a felhasználók engedélyeit az alkalmazás engedélyeivel. A felhasználói élményt a rendszergazdai jóváhagyás megadásával egyszerűsítheti le. Ezek a módszerek a Azure Active Directory (Azure AD) bérlő összes végfelhasználója számára érvényesek.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273989"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>A végfelhasználók beleegyezett az alkalmazásba Azure Active Directory
Megtudhatja, hogyan konfigurálhatja a felhasználók az alkalmazás engedélyeihez való hozzájárulásukat. A felhasználói élményt a rendszergazdai jóváhagyás megadásával egyszerűsítheti le. Ez a cikk a felhasználói beleegyezikés különböző lehetőségeit ismerteti. A módszerek a Azure Active Directory (Azure AD) bérlő összes végfelhasználója számára érvényesek. 

További információ az alkalmazásokkal való hozzájárulásról: [Azure Active Directory beleegyezési keretrendszer](../develop/consent-framework.md).

## <a name="prerequisites"></a>Előfeltételek

A rendszergazdai jóváhagyás megadása megköveteli, hogy globális rendszergazdaként, alkalmazás-rendszergazdaként vagy felhőalapú alkalmazás-rendszergazdaként jelentkezzen be.

Az alkalmazásokhoz való hozzáférés korlátozásához felhasználói hozzárendelést kell megkövetelni, majd hozzá kell rendelnie a felhasználókat vagy csoportokat az alkalmazáshoz.  További információ: [felhasználók és csoportok hozzárendelésének módszerei](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Rendszergazdai jóváhagyás megadása a vállalati alkalmazásoknak a Azure Portal

Rendszergazdai jóváhagyás megadása vállalati alkalmazásnak:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, alkalmazás-rendszergazdaként vagy Felhőbeli alkalmazás-rendszergazdaként.
2. A bal oldali navigációs menü tetején kattintson a **minden szolgáltatás** elemre. Megnyílik a **Azure Active Directory bővítmény** .
3. A szűrő keresése mezőbe írja be a **"Azure Active Directory"** kifejezést, majd válassza ki a **Azure Active Directory** elemet.
4. A navigációs menüben kattintson a **vállalati alkalmazások**elemre.
5. Válassza ki a kívánt alkalmazást.
6. Válassza az **engedélyek** lehetőséget, majd kattintson a **rendszergazdai jóváhagyás megadása**lehetőségre. A rendszer felszólítja, hogy jelentkezzen be az alkalmazás adminisztrálása céljából.
7. Jelentkezzen be egy olyan fiókkal, amely jogosultsággal rendelkezik az alkalmazás rendszergazdai jóváhagyásának megadásához. 
8. Beleegyezett az alkalmazás engedélyeibe.

Ez a beállítás csak akkor működik, ha az alkalmazás a következő: 

- Regisztrálva van a bérlőben, vagy
- Regisztrálva van egy másik Azure AD-bérlőben, és legalább egy végfelhasználó beleegyezett. Ha egy végfelhasználó beleegyezett egy alkalmazásba, az Azure AD felsorolja az alkalmazást a Azure Portal **vállalati alkalmazások** területén.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Rendszergazdai jóváhagyás megadása az alkalmazások regisztrálásakor a Azure Portal

Rendszergazdai jóváhagyás megadása egy alkalmazás regisztrálásakor: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Navigáljon az **alkalmazás-regisztrációk** panelre.
3. Válassza ki a beleegyező alkalmazást.
4. Válassza az **API-engedélyek**lehetőséget.
5. Kattintson a **rendszergazdai jóváhagyás megadása**lehetőségre.


## <a name="grant-admin-consent-through-a-url-request"></a>Rendszergazdai jóváhagyás megadása URL-kérelem alapján

Rendszergazdai jóváhagyás megadása egy URL-kérésen keresztül:

1. Állítson össze egy kérelmet az *login.microsoftonline.com* , és fűzze hozzá a `&prompt=admin_consent`. Az URL-cím a következőképpen fog kinézni: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. A rendszergazdai hitelesítő adatokkal való bejelentkezés után az alkalmazás minden felhasználó számára engedélyezte az engedélyt.


## <a name="force-user-consent-through-a-url-request"></a>Felhasználói engedély kényszerítése URL-kérelem alapján

Ha meg szeretné követelni, hogy a végfelhasználók a hitelesítés során minden alkalommal beleegyeznek az alkalmazásba, fűzze hozzá `&prompt=consent` a hitelesítési kérelem URL-címéhez.
Az URL-cím a következőképpen fog kinézni: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Következő lépések

[Alkalmazások belefoglalása és integrálása a AzureAD-be](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[A AzureAD 2.0-val konvergált alkalmazások beleegyezése és engedélyezése](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
