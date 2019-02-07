---
title: Egy nyilvános ügyfél alkalmazás regisztrálása az Azure Active Directory - FHIR az Azure API-hoz
description: Ez a cikk bemutatja, hogyan kell regisztrálni egy nyilvános ügyfél alkalmazást az Azure Active Directoryban.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 69504bc9ba0420b47a26519a0b112ff102171254
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824082"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Egy nyilvános ügyfél alkalmazás regisztrálása az Azure Active Directoryban

Ebből a cikkből elsajátíthatja lesz nyilvános alkalmazás regisztrálása az Azure Active Directoryban. Ügyfél alkalmazást az alkalmazásregisztrációk jelképezik Azure Active Directory-alkalmazások, amelyek hitelesítést, majd kérje meg az API-engedélyek egy felhasználó nevében. Nyilvános ügyfelek olyan alkalmazásokhoz, mint a mobil és a egy oldalon javascript alkalmazásokhoz, hogy az nem titkos kódok bizalmas. Az eljárás hasonlít a [bizalmas ügyfél regisztrálása](register-confidential-azure-ad-client-app.md), de mivel nyilvános ügyfelek nem megbízható egy Alkalmazáskulcs tárolására, nincs szükség hozzá egy.

## <a name="app-registrations-in-azure-portal"></a>Az Azure Portalon alkalmazásregisztrációk

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. Az a **Azure Active Directory** panelen kattintson a **alkalmazásregisztrációk (előzetes verzió)**:

    ![Az Azure Portalon. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson a **új regisztrációs**.

## <a name="application-registration-overview"></a>Regisztráció – áttekintés

1. Adja meg az alkalmazás megjelenített neve.

2. Adjon meg egy válasz URL-cím. A válasz URL-címe, ahol hitelesítési kódok visszakerül az ügyfélalkalmazás. Adjon hozzá további válasz URL-címek, és később szerkesztheti a meglévőket.

    ![Az Azure Portalon. Új nyilvános Alkalmazásregisztráció.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-engedélyek

Hasonlóan a [ügyfélalkalmazás bizalmas](register-confidential-azure-ad-client-app.md), válassza ki, melyik API-engedélyek az alkalmazás képesnek kell lennie a felhasználók nevében kérelem kell:

1. Nyissa meg a **API-engedélyek** , és válassza ki a [FHIR API erőforrás Alkalmazásregisztráció](register-resource-azure-ad-client-app.md):

    ![Az Azure Portalon. Új nyilvános API-engedélyek.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-API-PERMISSIONS.png)

2. Válassza ki a hatókörök, amelyeket szeretne igényelni az alkalmazást.

## <a name="next-steps"></a>További lépések

Ebben a cikkben bemutattuk, hogyan kell regisztrálni egy nyilvános ügyfél alkalmazást az Azure Active Directoryban. Ezután helyezze üzembe az Azure-ban FHIR API.
 
>[!div class="nextstepaction"]
>[Nyílt forráskódú FHIR-kiszolgáló telepítése](fhir-oss-powershell-quickstart.md)
