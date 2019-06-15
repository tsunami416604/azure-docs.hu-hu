---
title: A JavaScript egyoldalas alkalmazás forgatókönyv áttekintése – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (forgatókönyv áttekintése), amely integrálható a Microsoft identitásplatformjához.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076360"
---
# <a name="scenario-single-page-application"></a>Forgatókönyv: Egyoldalas alkalmazás

Ismerje meg, mindössze egy egyoldalas alkalmazás (SPA).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

A JavaScript SPA rövid útmutatót követve hozhat létre az első alkalmazás:

> [!div class="nextstepaction"]
> [Rövid útmutató: Egyoldalas alkalmazás](./quickstart-v2-javascript.md)

## <a name="overview"></a>Áttekintés

Számos modern webes alkalmazások beépített JavaScript- vagy például az Angularrel, oldalon a Vue.js és React.js SPA keretrendszer használatával írt ügyféloldali egyoldalas alkalmazásokként. Ezek az alkalmazások futtatásához egy webböngészőben, és különböző hitelesítési jellemzőit, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft identity platform lehetővé teszi, hogy az egyoldalas alkalmazások a felhasználók és a tokenekhez Háttérszolgáltatásokhoz vagy webes API-k használatával való eléréséhez a [OAuth 2.0 implicit folyamat](./v2-oauth2-implicit-grant-flow.md). Az implicit folyamatot lehetővé teszi, hogy az alkalmazás és a hitelesített felhasználó is elérhetik a védett API-k meghívásához szükséges jogkivonatok azonosító tokenekhez.

![Egylapos alkalmazások](./media/scenarios/spa-app.svg)

A hitelesítési folyamat nem tartalmazza az alkalmazás-forgatókönyvek Electron, React natív JavaScript keretrendszereket használó platformfüggetlen használatával és így tovább. azóta igényelnek további képességek a natív platformokhoz való együttműködéshez szükséges.

## <a name="specifics"></a>Tulajdonságairól

Ezt a forgatókönyvet, az alkalmazás a következő szempontok alapján szükséges:

* Az Azure AD alkalmazás regisztrálása az implicit folyamat engedélyezése és a egy átirányítási URI-t, amelyhez a tokenek adott vissza beállítása magában foglalja.
* Alkalmazás konfigurációja a regisztrált alkalmazás tulajdonságai, például az alkalmazás azonosítóját.
* A hitelesítési folyamat jelentkezik be, és szerzi be a jogkivonatokat ehhez az MSAL kódtár használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-spa-app-registration.md)
