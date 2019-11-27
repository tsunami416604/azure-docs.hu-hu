---
title: Az API-hitelesítés ismertetése – Azure digitális Twins | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat az API-khoz, és hogyan hitelesíthető az Azure Digital Twins szolgáltatással.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 6c2b0ec5165652e77c92426bb62a30468eef04c2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456900"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Kapcsolódás és hitelesítés API-kkal

Az Azure Digital Twins Azure Active Directory (Azure AD) használatával hitelesíti a felhasználókat és gondoskodik az alkalmazások biztonságáról. Az Azure AD számos modern architektúrához támogatja a hitelesítést. Ezek mindegyike az iparági szabványnak megfelelő protokollok (OAuth 2,0 vagy OpenID Connect) alapján történik. Emellett a fejlesztők az Azure AD használatával létrehozhatnak egy bérlői és üzletági (LOB) alkalmazásokat. A fejlesztők a több- [bérlős alkalmazások](how-to-multitenant-applications.md)fejlesztéséhez is használhatják az Azure ad-t.

Az Azure AD áttekintéséhez tekintse meg a részletes útmutatók, fogalmak és gyors útmutatókat ismertető [oldalt](https://docs.microsoft.com/azure/active-directory/fundamentals/) .

> [!TIP]
> Kövesse az [oktatóanyagot](tutorial-facilities-setup.md) egy Azure digitális Twins-minta alkalmazásának beállításához és futtatásához.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes útmutatást és képernyőképeket [ebben](../active-directory/develop/quickstart-register-app.md)a rövid útmutatóban talál.

Az Azure AD [öt elsődleges alkalmazási forgatókönyvet](../active-directory/develop/v2-app-types.md) támogat:

* Egyoldalas alkalmazás (SPA): A felhasználónak be kell jelentkeznie egy egyoldalas alkalmazásba, amelyet az Azure AD biztosít.
* Webböngésző webes alkalmazásba: A felhasználónak be kell jelentkeznie egy Azure AD által védett webalkalmazásba.
* Natív alkalmazás webes API-hoz: a telefonon, táblaszámítógépen vagy számítógépen futó natív alkalmazásnak hitelesítenie kell egy felhasználót az Azure AD által védett webes API erőforrásainak lekéréséhez.
* Webalkalmazás webes API-hoz: egy webalkalmazásnak az Azure AD által védett webes API-erőforrásokból kell lekérnie az erőforrásokat.
* Daemon vagy Server Application to web API: egy webes felhasználói felület nélküli Daemon-alkalmazásnak vagy-kiszolgáló alkalmazásnak az Azure AD által védett webes API-val kell lekérnie az erőforrásokat.

> [!IMPORTANT]
> Az Azure Digital Twins a következő hitelesítési könyvtárakat támogatja:
> * A legújabb [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * A [Azure Active Directory hitelesítési könyvtár (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális ikrek hívása egy középső rétegbeli webes API-ból

Amikor a fejlesztők a digitális Twins-megoldásokat használják, általában egy közepes szintű alkalmazást vagy API-t hoznak létre. Az alkalmazás vagy az API ezután meghívja a digitális Twins API-t az alsóbb rétegben. A standard web Solution architektúra támogatásához először győződjön meg arról, hogy a felhasználók a következőket teszik:

1. Hitelesítés a középső rétegbeli alkalmazással

1. A hitelesítés során a rendszer egy OAuth 2,0-as jogkivonatot szerez be

1. A rendszer ezután a beszerzett jogkivonatot használja a hitelesítéshez vagy hívja meg azokat az API-kat, amelyek további alsóbb rétegbeli folyamatokat használnak

A folyamaton belüli folyamat előkészítésével kapcsolatos utasításokért lásd: [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)-alapú folyamat. Az [alárendelt webes API-k meghívásakor](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)is megtekintheti a kód mintáit.

## <a name="next-steps"></a>Következő lépések

Az Azure digitális Twins konfigurálásához és teszteléséhez a OAuth 2,0 implicit engedélyezési folyamatával olvassa el a [Poster konfigurálása](./how-to-configure-postman.md)című cikk utasításait.

Az Azure Digital Twins biztonságával kapcsolatos további tudnivalókért olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md)című témakört.