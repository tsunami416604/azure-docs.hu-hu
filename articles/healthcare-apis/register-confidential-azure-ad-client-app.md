---
title: Egy bizalmas ügyfél alkalmazás regisztrálása az Azure Active Directory - FHIR az Azure API-hoz
description: Ez a cikk bemutatja, hogyan kell regisztrálni egy bizalmas ügyfél alkalmazást az Azure Active Directoryban.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 064cf4bb0b6e9454a4049cc6c32b51b09e5b2a53
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824064"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Egy bizalmas ügyfél alkalmazás regisztrálása az Azure Active Directoryban

Ebből a cikkből elsajátíthatja fog egy bizalmas ügyfél alkalmazás regisztrálása az Azure Active Directoryban. Egy ügyfél alkalmazásregisztráció egy olyan alkalmazás, amely egy felhasználó és a kérés hozzáférést nevében hitelesítéséhez használható Azure Active Directory ábrázolása [erőforrás alkalmazások](register-resource-azure-ad-client-app.md). A bizalmas ügyfél alkalmazás-e olyan alkalmazás, amely megbízható tárolásához egy titkos kulcsot, és bemutatja, hogy a titkos kulcs hozzáférési jogkivonatok kérésekor. Bizalmas alkalmazások olyan kiszolgálóoldali alkalmazások.

A portálon bizalmas új alkalmazás regisztrálásához kövesse az alábbi lépéseket.

## <a name="app-registrations-in-azure-portal"></a>Az Azure Portalon alkalmazásregisztrációk

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. Az a **Azure Active Directory** panelen kattintson **alkalmazásregisztrációk (előzetes verzió)**:

    ![Az Azure Portalon. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson a **új regisztrációs**.

## <a name="register-a-new-application"></a>Új alkalmazás regisztrálása

1. Adjon meg egy megjelenített nevet az alkalmazás.

2. Adjon meg egy válasz URL-cím. Ezeket az adatokat később módosítható, de ha tudja, hogy az alkalmazás válasz URL-cím, most adja meg.

    ![Új alkalmazás regisztrálása a bizalmas ügyfél.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>API-engedélyek

Ezután adja hozzá az API-engedélyek:

1. Nyissa meg a **API-engedélyek** , és válassza ki a [FHIR API erőforrás Alkalmazásregisztráció](register-resource-azure-ad-client-app.md):

    ![Bizalmas ügyfél. API-engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Kattintson a **adjon hozzá egy engedélyt**

3. Válassza ki az erőforrás API:

    ![Bizalmas ügyfél. Saját API-k](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Válassza ki a hatóköröket (engedélyeket), amely a bizalmas alkalmazás kérni, egy felhasználó nevében képesnek kell lennie:

    ![Bizalmas ügyfél. Delegált engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Alkalmazáskulcs

1. Hozzon létre egy (ügyfélkulcs):

    ![Bizalmas ügyfél. Alkalmazáskulcs](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Adjon meg egy leírást és egy, a titkos kulcs időtartam.

3. Miután jönnek létre, megjelenik a portálon csak egyszer. Jegyezze fel, és tárolja biztonságos helyen.

## <a name="next-steps"></a>További lépések

Ebben a cikkben bemutattuk, hogyan kell regisztrálni egy bizalmas ügyfél alkalmazást az Azure Active Directoryban. Ezután telepítse egy FHIR API-t az Azure-ban.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-oss-powershell-quickstart.md)