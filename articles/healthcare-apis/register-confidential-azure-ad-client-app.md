---
title: Bizalmas ügyfélalkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Regisztráljon egy bizalmas ügyfélalkalmazás Azure Active Directoryban, amely hitelesíti a felhasználó nevében, és hozzáférést kér az erőforrás-alkalmazásokhoz.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852548"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Bizalmas ügyfélalkalmazás regisztrálása a Azure Active Directoryban

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat egy bizalmas ügyfélalkalmazás Azure Active Directoryban. 

Az ügyfélalkalmazás regisztrálása egy olyan alkalmazás Azure Active Directory ábrázolása, amely a felhasználó nevében történő hitelesítéshez és az [erőforrás-alkalmazásokhoz](register-resource-azure-ad-client-app.md)való hozzáféréshez használható. A bizalmas ügyfélalkalmazás egy olyan alkalmazás, amely megbízhatónak tartja a titkos kulcsot, és a hozzáférési jogkivonatok kérésekor megmutatják a titkos kulcsot. Példa a bizalmas alkalmazásokra a kiszolgálóoldali alkalmazások.

Ha új bizalmas alkalmazást szeretne regisztrálni a portálon, kövesse az alábbi lépéseket.

## <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. A **Azure Active Directory** panelen kattintson **Alkalmazásregisztrációk**:

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson az **új regisztrációra**.

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

1. Adja meg az alkalmazás megjelenítendő nevét.

2. Adja meg a válasz URL-címét. Ezeket az adatokat később is megváltoztathatja, de ha ismeri az alkalmazás válaszának URL-címét, adja meg most.

    ![Új bizalmas ügyfélalkalmazás regisztrálása.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-engedélyek

További API-engedélyek hozzáadása:

1. Nyissa meg az **API-engedélyeket**:

    ![Bizalmas ügyfél. API-engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Kattintson **az engedély hozzáadása** elemre.

3. Válassza ki a megfelelő erőforrás-API-t:

    A FHIR-hez készült Azure API-hoz (felügyelt szolgáltatás) kattintson a **szervezetem által használt API** -k elemre, és keresse meg az "Azure Healthcare API-k" Az Azure-hoz készült nyílt forráskódú FHIR-kiszolgáló esetében válassza ki a [FHIR API-erőforrás alkalmazásának regisztrációját](register-resource-azure-ad-client-app.md):

    ![Bizalmas ügyfél. Saját API-k](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Válassza ki azokat a hatóköröket (engedélyeket), amelyeket a bizalmas alkalmazásnak meg kell tudnia kérni a felhasználó nevében:

    ![Bizalmas ügyfél. Delegált engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Alkalmazás titkos kódja

1. Alkalmazás titkos kódjának létrehozása (ügyfél titkos kulcsa):

    ![Bizalmas ügyfél. Alkalmazás titka](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Adja meg a titok leírását és időtartamát.

3. A létrehozást követően csak egyszer fog megjelenni a portálon. Jegyezze fel, és tárolja biztonságosan.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy bizalmas ügyfélalkalmazás Azure Active Directoryban. Most már készen áll a [FHIR készült Azure API](fhir-paas-powershell-quickstart.md)üzembe helyezésére.

Miután telepítette az Azure API-t a FHIR-hez, tekintse át a további elérhető beállításokat.
 
>[!div class="nextstepaction"]
>[Az Azure API for FHIR üzembe helyezése](fhir-paas-powershell-quickstart.md)