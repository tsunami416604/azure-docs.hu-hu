---
title: Alkalmazás regisztrálása az Azure Active Directory - FHIR az Azure API-hoz
description: Ez a cikk bemutatja, hogyan regisztrálhat egy szolgáltatásalkalmazást az Azure Active Directoryban.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824094"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Regisztrálja a Szolgáltatásoldali ügyfélalkalmazás az Azure Active Directoryban

Ebben a cikkben megismerheti, hogyan regisztrálja a Szolgáltatásoldali ügyfélalkalmazás az Azure Active Directory lesz. Ügyfél alkalmazást az alkalmazásregisztrációk olyan alkalmazásokat, amelyek a jogkivonatok lekérésére és hitelesítéséhez használható Azure Active Directory ábrázolásai. A szolgáltatásügyfél anélkül, hogy az interaktív hitelesítéshez a felhasználó hozzáférési jogkivonat beszerzése az alkalmazások által használt célja. Ez egyes alkalmazás-engedélyekkel rendelkezik, és egy alkalmazás titkos kulcs (jelszó) használja, ha a hozzáférési tokenek beszerzése.

Kövesse az alábbi lépéseket egy új szolgáltatásügyfél létrehozásának.

## <a name="app-registrations-in-azure-portal"></a>Az Azure Portalon alkalmazásregisztrációk

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. Az a **Azure Active Directory** panelen kattintson **alkalmazásregisztrációk (előzetes verzió)**:

    ![Az Azure Portalon. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson a **új regisztrációs**.

## <a name="service-client-application-details"></a>Szolgáltatás ügyfél-alkalmazás részletei

* A szolgáltatás az ügyfélnek kell egy megjelenített nevet és egy válasz URL-cím is megadhatja, de ez általában nem használjuk.

    ![Az Azure Portalon. Új szolgáltatás ügyfél Alkalmazásregisztráció.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-engedélyek

A szolgáltatásügyfél-alkalmazás-szerepkörök biztosítania kell. 

1. Nyissa meg a **API-engedélyek** , és válassza ki a [FHIR API erőforrás Alkalmazásregisztráció](register-resource-azure-ad-client-app.md):

    ![Az Azure Portalon. Ügyfél API-engedélyek](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Válassza ki az alkalmazás-szerepkörök, amelyekre az erőforrás-alkalmazás meg vannak adva:

    ![Az Azure Portalon. Szolgáltatás ügyfél-Alkalmazásengedélyek](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Engedélyezés hozzájárulás az alkalmazáshoz. Ha nem rendelkezik a szükséges engedélyekkel, egyeztessen az Azure Active Directory-rendszergazda:

    ![Az Azure Portalon. Szolgáltatás-ügyfél rendszergazdai jóváhagyás](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Alkalmazáskulcs

A szolgáltatás az ügyfélnek kell egy titkos kulcsot (jelszó), amely akkor használatos, ha a jogkivonatok beszerzéséhez.

1. Kattintson a **tanúsítványok &amp; titkos kulcsok**

2. Kattintson a **új titkos ügyfélkulcsot**

    ![Az Azure Portalon. Szolgáltatás titkos Ügyfélkódja](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Adja meg az időtartamot, a titkos kulcsot.

4. Miután létrejött, csak megjelenik egyszer a portálon. Jegyezze fel, és tárolja a egy biztonságos.

## <a name="next-steps"></a>További lépések

Ebben a cikkben bemutattuk, hogyan kell regisztrálni egy Szolgáltatásoldali ügyfélalkalmazás az Azure Active Directoryban. Ezután telepítse egy FHIR API-t az Azure-ban.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-oss-powershell-quickstart.md)