---
title: Erőforrás-alkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Egy erőforrás-(vagy API-) alkalmazás regisztrálása Azure Active Directoryban, így az ügyfélalkalmazások hozzáférést igényelhetnek az erőforráshoz hitelesítéskor.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3c5486e31dd3227cfcc8bd3b433602ce58cfa4fb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852990"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Erőforrás-alkalmazás regisztrálása Azure Active Directory

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy erőforrás-(vagy API-) alkalmazást a Azure Active Directory. Az erőforrás-alkalmazás a FHIR-kiszolgáló API-nak Azure Active Directory ábrázolása, és az ügyfélalkalmazások hitelesítéskor igényelhetnek hozzáférést az erőforráshoz. Az erőforrás-alkalmazást a OAuth szóhasználatában *célközönségének* is nevezzük.

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

Ha a FHIR készült Azure API-t használja, a szolgáltatás telepítésekor automatikusan létrejön egy erőforrás-alkalmazás. Ha a FHIR tartozó Azure API-t ugyanabban a Azure Active Directory bérlőben használja, mint az alkalmazás üzembe helyezése során, kihagyhatja ezt a útmutatóból, és helyette üzembe helyezheti az Azure API-t a FHIR-hoz az első lépésekhez.

Ha más Azure Active Directory bérlőt használ (az előfizetéséhez nem társítva), importálhatja a FHIR-erőforrás-alkalmazáshoz tartozó Azure API-t a bérlőbe a PowerShell segítségével:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

vagy használhatja az Azure CLI-t is:

```azurecli-interactive
az ad sp create --id 4f6778d8-5aef-43dc-a1ff-b073724b9495
```

## <a name="fhir-server-for-azure"></a>FHIR-kiszolgáló az Azure-hoz

Ha a nyílt forráskódú FHIR-kiszolgálót használja az Azure-hoz, az alábbi lépéseket követve regisztrálhat egy erőforrás-alkalmazást.

### <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. A **Azure Active Directory** panelen kattintson **Alkalmazásregisztrációk**:

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson az **új regisztrációra**.

### <a name="add-a-new-application-registration"></a>Új alkalmazás regisztrációjának hozzáadása

Adja meg az új alkalmazás részleteit. A megjelenített név nem rendelkezik konkrét követelményekkel, de a FHIR-kiszolgáló URI-azonosítójának beállításával könnyen megtalálhatja a következőket:

![Új alkalmazásregisztráció](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

### <a name="set-identifier-uri-and-define-scopes"></a>Azonosító URI beállítása és hatókörök meghatározása

Az erőforrás-alkalmazások azonosító URI-ja (alkalmazásspecifikus azonosító URI), amelyet az ügyfelek használhatnak az erőforráshoz való hozzáférés kérelmezése során. Ez az érték feltölti a `aud` hozzáférési jogkivonat jogcímeit. Azt javasoljuk, hogy ezt az URI-t úgy állítsa be, hogy a FHIR-kiszolgáló URI-ja legyen. A FHIR-alkalmazások intelligens alkalmazásai esetében feltételezhető, hogy a *célközönség* a FHIR-kiszolgáló URI azonosítója.

1. Kattintson **az API közzététele** lehetőségre

2. Kattintson az alkalmazás- *azonosító URI*elem melletti **beállítás** gombra.

3. Adja meg az azonosító URI-t, majd kattintson a **Mentés**gombra. A jó azonosító URI a FHIR-kiszolgáló URI-ja lenne.

4. Kattintson **a hatókör hozzáadása** lehetőségre, és adja hozzá az API-hoz definiálni kívánt hatóköröket. Legalább egy hatókört hozzá kell adnia ahhoz, hogy a jövőben engedélyeket adjon az erőforrás-alkalmazásnak. Ha nem rendelkezik olyan konkrét hatókörökkel, amelyeket hozzá szeretne adni, hatókörként adhat hozzá user_impersonation.

![Célközönség és hatókör](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

### <a name="define-application-roles"></a>Alkalmazási szerepkörök definiálása

A FHIR-hez készült Azure API és az Azure-beli OSS FHIR-kiszolgáló a szerepköralapú hozzáférés-vezérléshez [Azure Active Directory alkalmazás-szerepköröket](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) használ. Ha meg szeretné határozni, hogy mely szerepkörök legyenek elérhetők a FHIR-kiszolgáló API-hoz, nyissa meg az erőforrás-alkalmazás [jegyzékfájlját](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/):

1. Kattintson a **jegyzékfájlra**:

    ![Alkalmazás szerepkörei](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. A `appRoles` (z) tulajdonságban adja hozzá azokat a szerepköröket, amelyekhez a felhasználók vagy az alkalmazások rendelkeznek:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User",
          "Application"
        ],
        "description": "FHIR Server Administrators",
        "displayName": "admin",
        "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
        "isEnabled": true,
        "value": "admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "description": "Users who can read",
        "displayName": "reader",
        "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
        "isEnabled": true,
        "value": "reader"
      }
    ],
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy erőforrás-alkalmazást Azure Active Directoryban. Ezután telepítse a FHIR készült Azure API-t.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-powershell-quickstart.md)