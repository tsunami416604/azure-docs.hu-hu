---
title: Az Azure Digital Twins API-hitelesítés ismertetése | Microsoft Docs
description: Az Azure digitális ikrek használata az API-khoz való kapcsolódáshoz és hitelesítéshez
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: lyhughes
ms.openlocfilehash: c0b4b6a13143f613bec64c8507f1726e2450be44
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68815551"
---
# <a name="connect-and-authenticate-to-apis"></a>Kapcsolódás és hitelesítés API-kkal

Az Azure Digital Twins Azure Active Directory (Azure AD) használatával hitelesíti a felhasználókat és gondoskodik az alkalmazások biztonságáról. Az Azure AD számos modern architektúrához támogatja a hitelesítést. Ezek mindegyike az iparági szabványnak megfelelő protokollok (OAuth 2,0 vagy OpenID Connect) alapján történik. Emellett a fejlesztők az Azure AD használatával létrehozhatnak egy bérlői és üzletági (LOB) alkalmazásokat. A fejlesztők a több-bérlős alkalmazások fejlesztéséhez is használhatják az Azure AD-t.

Az Azure AD áttekintéséhez tekintse meg a részletes útmutatók, fogalmak és gyors útmutatókat ismertető [oldalt](https://docs.microsoft.com/azure/active-directory/fundamentals/index) .

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes útmutatást és képernyőképeket [ebben](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)a rövid útmutatóban talál.

Az Azure AD [öt elsődleges alkalmazási forgatókönyvet](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) támogat:

* Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít.
* Webböngészőből webes alkalmazásba: A felhasználónak be kell jelentkeznie egy Azure AD által védett webalkalmazásba.
* Natív alkalmazás webes API-hoz: Egy telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure AD által védett webes API erőforrásainak lekéréséhez.
* Webalkalmazás webes API-hoz: Egy webalkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat.
* Daemon vagy Server alkalmazás webes API-hoz: Egy démon alkalmazásnak vagy egy webes felhasználói felület nélküli kiszolgáló alkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat.

A Windows Azure hitelesítési függvénytár számos lehetőséget kínál Active Directory jogkivonatok beszerzésére. A könyvtár-és mintakód-mintákkal kapcsolatos részletekért tekintse meg [ezt a cikket](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális ikrek hívása egy középső rétegbeli webes API-ból

Amikor a fejlesztők a digitális Twins-megoldásokat használják, általában egy közepes szintű alkalmazást vagy API-t hoznak létre. Az alkalmazás vagy az API ezután meghívja a digitális Twins API-t az alsóbb rétegben. A standard web Solution architektúra támogatásához először győződjön meg arról, hogy a felhasználók a következőket teszik:

1. Hitelesítés a középső rétegbeli alkalmazással

1. A hitelesítés során a rendszer egy OAuth 2,0-as jogkivonatot szerez be

1. A rendszer ezután a beszerzett jogkivonatot használja a hitelesítéshez vagy hívja meg azokat az API-kat, amelyek további alsóbb rétegbeli folyamatokat használnak

A folyamaton belüli folyamat előkészítésével kapcsolatos utasításokért lásd: [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)-alapú folyamat. Az [alárendelt webes API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)-k meghívásakor is megtekintheti a kód mintáit.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins konfigurálásához és teszteléséhez a OAuth 2,0 implicit engedélyezési folyamatával olvassa el a [Poster konfigurálása](./how-to-configure-postman.md)című cikk utasításait.

Az Azure Digital Twins biztonságával kapcsolatos további tudnivalókért olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című témakört.