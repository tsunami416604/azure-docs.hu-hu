---
title: Szolgáltatási alkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Megtudhatja, hogyan regisztrálhat egy szolgáltatásbeli ügyfélalkalmazás Azure Active Directoryban, amely a jogkivonatok hitelesítésére és beszerzésére használható.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871847"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Szolgáltatásbeli ügyfélalkalmazás regisztrálása a Azure Active Directoryban

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy szolgáltatás-ügyfélalkalmazás Azure Active Directoryban. Az ügyfélalkalmazások regisztrációja olyan alkalmazások Azure Active Directory, amelyek a jogkivonatok hitelesítéséhez és beszerzéséhez használhatók. A szolgáltatási ügyfelet arra tervezték, hogy egy alkalmazás a felhasználó interaktív hitelesítése nélkül szerezze be a hozzáférési jogkivonatot. A hozzáférési jogkivonatok beszerzése során bizonyos alkalmazási engedélyekkel fog rendelkezni, és az alkalmazás titkos kulcsát (jelszót) kell használnia.

Az alábbi lépéseket követve hozzon létre egy új szolgáltatás-ügyfelet.

## <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. A **Azure Active Directory** panelen kattintson **Alkalmazásregisztrációk**:

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson az **Új regisztráció** elemre.

## <a name="service-client-application-details"></a>Szolgáltatás ügyfélalkalmazás adatai

* A szolgáltatás ügyfelének megjelenítendő névnek kell lennie, és a válasz URL-címét is megadhatja, de általában nem lesz használatban.

    ![Azure Portal. Új szolgáltatás-ügyfélalkalmazás regisztrálása.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-engedélyek

Meg kell adnia a szolgáltatás ügyfélalkalmazások szerepköreit. 

1. Nyissa meg az **API-engedélyeket** , és válassza ki a [FHIR API Resource Application regisztrációját](register-resource-azure-ad-client-app.md). Ha az Azure API-t használja a FHIR-hez, az Azure Healthcare API-kat a **saját szervezetem által használt API**-k alapján kell megkeresnie.

    ![Azure Portal. Szolgáltatás ügyféloldali API-engedélyei](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Válassza ki az erőforrás-alkalmazásban definiált alkalmazási szerepköröket:

    ![Azure Portal. Szolgáltatás ügyfélalkalmazás engedélyei](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Adja meg az alkalmazás jóváhagyását. Ha nem rendelkezik a szükséges engedélyekkel, forduljon a Azure Active Directory rendszergazdájához:

    ![Azure Portal. Szolgáltatás-ügyfél rendszergazdai engedélye](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Alkalmazás titkos kódja

A szolgáltatás ügyfelének szüksége van egy titkos kulcsra (jelszóra), amelyet a tokenek beszerzéséhez fog használni.

1. Kattintson a **tanúsítványok &amp; titkok** elemre.

2. Kattintson az **Új titkos ügyfélkód** elemre.

    ![Azure Portal. Szolgáltatás-ügyfél titka](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Adja meg a titok időtartamát.

4. Miután létrejött, csak egyszer jelenik meg a portálon. Jegyezze fel, és tárolja biztonságosan.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy szolgáltatás-ügyfélalkalmazás Azure Active Directoryban. Ezután helyezzen üzembe egy FHIR API-t az Azure-ban.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló üzembe helyezése](fhir-oss-powershell-quickstart.md)