---
title: Nyilvános ügyfélalkalmazás regisztrálása az Azure AD-ben – Azure API a FHIR-hez
description: Ez a cikk azt ismerteti, hogyan regisztrálhat egy nyilvános ügyfélalkalmazás Azure Active Directoryban, a FHIR API Azure-beli üzembe helyezésének előkészítése során.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 5aa9e5a33dbe66e3ebd787decfa3a520454fc6f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871791"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Nyilvános ügyfélalkalmazás regisztrálása Azure Active Directory

Ebből a cikkből megtudhatja, hogyan regisztrálhat egy nyilvános alkalmazást Azure Active Directory.  

Az ügyfélalkalmazások regisztrációja olyan alkalmazások Azure Active Directory, amelyek a felhasználók nevében hitelesíthetők és kérhetnek API-engedélyeket. A nyilvános ügyfelek olyan alkalmazások, mint például a mobil alkalmazások és az egyoldalas JavaScript-alkalmazások, amelyek nem tudják bizalmasan megőrizni a titkokat. Az eljárás hasonló a [bizalmas ügyfél regisztrálásához](register-confidential-azure-ad-client-app.md), de mivel a nyilvános ügyfelek nem megbízhatók egy alkalmazás titkos kódjának tárolására, nem kell hozzáadnia egyet.

## <a name="app-registrations-in-azure-portal"></a>Alkalmazásregisztrációk a Azure Portal

1. Az [Azure Portal](https://portal.azure.com) bal oldali navigációs paneljén kattintson az **Azure Active Directory** elemre.

2. A **Azure Active Directory** panelen kattintson **Alkalmazásregisztrációk**:

    ![Azure Portal. Új alkalmazás regisztrálása.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Kattintson az **új regisztrációra**.

## <a name="application-registration-overview"></a>Az alkalmazás regisztrációjának áttekintése

1. Adja meg az alkalmazás megjelenítendő nevét.

2. Adja meg a válasz URL-címét. A válasz URL-címe, ahol a hitelesítési kódokat a rendszer visszaadja az ügyfélalkalmazás számára. Hozzáadhat további válasz URL-címeket, és szerkesztheti a meglévőket is.

    ![Azure Portal. Új nyilvános alkalmazás regisztrálása.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)

## <a name="api-permissions"></a>API-engedélyek

A [bizalmas ügyfélalkalmazás](register-confidential-azure-ad-client-app.md)hasonlóan ki kell választania, hogy az alkalmazás milyen API-engedélyeket Kérjen a felhasználók nevében:

1. Nyissa meg az **API-engedélyeket**.

    Ha az Azure API-t használja a FHIR-hez, az Azure Healthcare API-kat a **szervezetem által használt API** -k alatt, az alábbi ábrán találhatja meg.
    
    Ha más erőforrás-alkalmazásra hivatkozik, válassza ki a korábban a **saját API**-k alatt létrehozott [FHIR API Resource Application-regisztrációt](register-resource-azure-ad-client-app.md) :

    ![Azure Portal. Új nyilvános API-engedélyek – FHIR alapértelmezett Azure API](media/public-client-app/api-permissions.png)


2. Válassza ki azokat az engedélyeket, amelyeket szeretne kérni az alkalmazásnak: ![ Azure Portal. Alkalmazásengedélyek](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>FHIR-kiszolgáló szolgáltatójának ellenőrzése
Ha a cikkben regisztrált alkalmazás és a FHIR-kiszolgáló ugyanabban az Azure AD-bérlőben található, akkor jó, ha folytatja a következő lépésekkel.

Ha az ügyfélalkalmazás egy másik Azure AD-bérlőben van konfigurálva a FHIR-kiszolgálóról, akkor frissítenie kell a **szolgáltatót**. A FHIR készült Azure API-ban a szolgáltatót a beállítások – > hitelesítés területen állíthatja be. Állítsa be a szolgáltatót a következőre: **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan regisztrálhat egy nyilvános ügyfélalkalmazás Azure Active Directoryban. Ezt követően tesztelje a hozzáférést a FHIR-kiszolgálóhoz a Poster használatával.
 
>[!div class="nextstepaction"]
>[Az Azure API elérése a Poster FHIR](access-fhir-postman-tutorial.md)
