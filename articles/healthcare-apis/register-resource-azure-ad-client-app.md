---
title: Egy erőforrás-alkalmazás regisztrálása az Azure Active Directory - FHIR az Azure API-hoz
description: Ez a cikk azt ismerteti, hogy egy erőforrás-alkalmazás regisztrálása az Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: c0602d44a5b01e31cd2082e2b05d17c4289adaa1
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824072"
---
# <a name="register-a-resource-application-in-azure-active-directory"></a>Egy erőforrás-alkalmazás regisztrálása az Azure Active Directoryban

Ebből a cikkből megtudhatja, hogyan regisztrálja az erőforrás (vagy API-t) alkalmazás az Azure Active Directoryban. Egy erőforrás-alkalmazás egy Azure Active Directory ábrázolása a FHIR kiszolgáló API-t saját maga és ügyfélalkalmazások hitelesítésekor kérhetnek hozzáférést az erőforráshoz. Az erőforrás-alkalmazás is van a *célközönség* az OAuth-programozás nyelvhasználatában általában.

## <a name="app-registrations-in-azure-portal"></a>Az Azure Portalon alkalmazásregisztrációk

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. Az a **Azure Active Directory** panelen kattintson **alkalmazásregisztrációk (előzetes verzió)**:

    ![Az Azure Portalon. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson a **új regisztrációs**.

## <a name="add-a-new-application-registration"></a>Adjon hozzá egy új alkalmazás regisztrálása

Adja meg az adatokat az új alkalmazás. Nem vonatkoznak konkrét követelmények számára megjelenített neve, de állítja, az URI-ját a FHIR kiszolgáló megkönnyíti keresése:

![Új alkalmazásregisztráció](media/how-to-aad/portal-aad-register-new-app-registration-NAME.png)

## <a name="set-identifier-uri-and-define-scopes"></a>Azonosító URI-t és a hatókörök meghatározása

Egy erőforrás-alkalmazás rendelkezik identifikátor URI azonosító (Alkalmazásazonosító URI-ja), mely ügyfelek használhatják az erőforrásokhoz való hozzáférés kérésekor. Ezt az értéket fogja feltölteni a `aud` jogcím a hozzáférési jogkivonat. Javasoljuk, hogy beállította-e az URI-t a FHIR kiszolgáló URI azonosítója legyen. Az INTELLIGENS alkalmazások FHIR, feltételezzük, hogy a *célközönség* az URI-t a FHIR-kiszolgáló.

1. Kattintson a **teszi közzé az API-k**

2. Kattintson a **beállítása** melletti *Alkalmazásazonosító URI-ja*.

3. Kattintson a **hatókör hozzáadása** és bármely definiálásához, API-t szeretné hatókört is felvehet. Az Azure AD jelenleg nem engedélyezi a perjeleket (`/`) a hatókör neve. Azt javasoljuk, `$` helyette. A hatókör, például `patient/*.read` lenne `patient$*.read`.

    ![Célközönség és hatókör](media/how-to-aad/portal-aad-register-new-app-registration-AUD-SCOPE.png)

## <a name="define-application-roles"></a>Alkalmazás-szerepkörök definiálása

Az Azure API FHIR és a nyílt Forráskódú FHIR kiszolgáló Azure használatra [Azure Active Directory-alkalmazás-szerepkörök](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) szerepköralapú hozzáférés-vezérlést. Adja meg, mely szerepkörök FHIR Server API elérhetőnek kell lennie, nyissa meg az erőforrás-alkalmazás [manifest](https://docs.microsoft.com/azure/active-directory/active-directory-application-manifest/):

1. Kattintson a **Manifest**:

    ![Alkalmazás-szerepkörök](media/how-to-aad/portal-aad-register-new-app-registration-APP-ROLES.png)

2. Az a `appRoles` tulajdonság, a felhasználók vagy alkalmazások szeretné, hogy milyen szerepkörök hozzáadása:

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben bemutattuk, hogyan regisztrálhat egy erőforrás-alkalmazás az Azure Active Directoryban. Ezután telepítse egy FHIR API-t az Azure-ban.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-oss-powershell-quickstart.md)