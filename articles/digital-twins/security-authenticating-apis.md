---
title: Az API-hitelesítés ismertetése az Azure Digital Twins használatával | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az API-khoz, és hogyan hitelesíthető az Azure Digital Twins szolgáltatással.
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: lyhughes
ms.openlocfilehash: ad51fbe7d2f8e8f115adf03d6333c0747765ee43
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338603"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Kapcsolódás és hitelesítés API-kkal

Az Azure Digital Twins Azure Active Directory (Azure AD) használatával hitelesíti a felhasználókat és gondoskodik az alkalmazások biztonságáról. Az Azure AD számos modern architektúrához támogatja a hitelesítést. Ezek mindegyike az iparági szabványnak megfelelő protokollok (OAuth 2,0 vagy OpenID Connect) alapján történik. Emellett a fejlesztők az Azure AD használatával létrehozhatnak egy bérlői és üzletági (LOB) alkalmazásokat. A fejlesztők a több-bérlős alkalmazások fejlesztéséhez is használhatják az Azure AD-t.

Az Azure AD áttekintéséhez tekintse meg a részletes útmutatók, fogalmak és gyors útmutatókat ismertető [oldalt](https://docs.microsoft.com/azure/active-directory/fundamentals/) .

> [!TIP]
> Kövesse az [oktatóanyagot](tutorial-facilities-setup.md) egy Azure digitális Twins-minta alkalmazásának beállításához és futtatásához.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes útmutatást és képernyőképeket [ebben](../active-directory/develop/quickstart-register-app.md)a rövid útmutatóban talál.

Az Azure AD [öt elsődleges alkalmazási forgatókönyvet](../active-directory/develop/v2-app-types.md) támogat:

* Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít.
* Webböngészőből webes alkalmazásba: A felhasználónak be kell jelentkeznie egy Azure AD által védett webalkalmazásba.
* Natív alkalmazás webes API-hoz: Egy telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure AD által védett webes API erőforrásainak lekéréséhez.
* Webalkalmazás webes API-hoz: Egy webalkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat.
* Daemon vagy Server alkalmazás webes API-hoz: Egy démon alkalmazásnak vagy egy webes felhasználói felület nélküli kiszolgáló alkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat.

A Windows Azure hitelesítési függvénytár számos lehetőséget kínál Active Directory jogkivonatok beszerzésére. A könyvtár és a kód mintáinak részleteit [a ADAL.net wikiben](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)tekintheti meg.

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális ikrek hívása egy középső rétegbeli webes API-ból

Amikor a fejlesztők a digitális Twins-megoldásokat használják, általában egy közepes szintű alkalmazást vagy API-t hoznak létre. Az alkalmazás vagy az API ezután meghívja a digitális Twins API-t az alsóbb rétegben. A standard web Solution architektúra támogatásához először győződjön meg arról, hogy a felhasználók a következőket teszik:

1. Hitelesítés a középső rétegbeli alkalmazással

1. A hitelesítés során a rendszer egy OAuth 2,0-as jogkivonatot szerez be

1. A rendszer ezután a beszerzett jogkivonatot használja a hitelesítéshez vagy hívja meg azokat az API-kat, amelyek további alsóbb rétegbeli folyamatokat használnak

A folyamaton belüli folyamat előkészítésével kapcsolatos utasításokért lásd: [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)-alapú folyamat. Az [alárendelt webes API](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)-k meghívásakor is megtekintheti a kód mintáit.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins konfigurálásához és teszteléséhez a OAuth 2,0 implicit engedélyezési folyamatával olvassa el a [Poster konfigurálása](./how-to-configure-postman.md)című cikk utasításait.

Az Azure Digital Twins biztonságával kapcsolatos további tudnivalókért olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című témakört.