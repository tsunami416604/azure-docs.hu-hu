---
title: JavaScript egyoldalas alkalmazás forgatókönyvének áttekintése – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre egy egyoldalas alkalmazást (forgatókönyv áttekintése), amely integrálja a Microsoft Identity platformot.
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852521"
---
# <a name="scenario-single-page-application"></a>Forgatókönyv: Egyoldalas alkalmazás

Ismerje meg az egyoldalas alkalmazások (SPA) létrehozásához szükséges tudnivalókat.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Első lépések

Az első alkalmazást az alábbi JavaScript SPA-útmutató segítségével hozhatja létre:

> [!div class="nextstepaction"]
> [Rövid útmutató: Egyoldalas alkalmazás](./quickstart-v2-javascript.md)

## <a name="overview"></a>Áttekintés

Számos modern webalkalmazás úgy van kialakítva, mint a JavaScript vagy egy SPA-keretrendszer, például a szögletes, a Vue. js és a reakciós. js segítségével írt ügyféloldali egyoldalas alkalmazások. Ezek az alkalmazások webböngészőben futnak, és különböző hitelesítési jellemzőkkel rendelkeznek, mint a hagyományos kiszolgálóoldali webes alkalmazások. A Microsoft Identity platform lehetővé teszi, hogy az egyoldalas alkalmazások bejelentkezzenek a felhasználókba, és jogkivonatokat kérjenek a háttér-szolgáltatások vagy webes API-k elérésére a [OAuth 2,0 implicit folyamat](./v2-oauth2-implicit-grant-flow.md)használatával. Az implicit folyamat lehetővé teszi, hogy az alkalmazás azonosító jogkivonatokat kapjon a hitelesített felhasználó képviseletére, valamint a védett API-k meghívásához szükséges jogkivonatok elérésére is.

![Egyoldalas alkalmazások](./media/scenarios/spa-app.svg)

Ez a hitelesítési folyamat nem tartalmaz olyan alkalmazási helyzeteket, amelyek platformfüggetlen JavaScript-keretrendszereket, például elektronokat, reagáló natív és így tovább. mivel további képességeket igényelnek a natív platformokkal való interakcióhoz.

## <a name="specifics"></a>Sajátosságai

A forgatókönyvnek az alkalmazáshoz való engedélyezéséhez a következő szempontokat kell figyelembe vennie:

* Az Azure AD-beli alkalmazások regisztrálása magában foglalja az implicit folyamat engedélyezését és egy átirányítási URI-t, amely a visszaadott tokeneket adja vissza.
* Az alkalmazás konfigurációja a regisztrált alkalmazás tulajdonságaival, például az alkalmazás azonosítójával.
* A MSAL-kódtár használatával jelentkezzen be a hitelesítési folyamatba, és szerezzen be jogkivonatokat.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazásregisztráció](scenario-spa-app-registration.md)
