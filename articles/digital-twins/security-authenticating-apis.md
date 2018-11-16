---
title: Megismerheti az Azure digitális Twins API-hitelesítés |} A Microsoft Docs
description: Az Azure digitális Twins használatával csatlakozhat, és API-hitelesítés
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: 4ea4479d77e06940bed50859341952ffbcbbda46
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711055"
---
# <a name="connect-and-authenticate-to-apis"></a>Csatlakozás és hitelesítés API-k

Az Azure digitális Twins felhasználók hitelesítéséhez és alkalmazások védelmét az Azure Active Directory (Azure AD) használja. Az Azure AD hitelesítési modern architektúrák különböző támogatja. Ezek mindegyike az iparági szabványnak megfelelő protokoll az OAuth 2.0 vagy OpenID Connect alapul. Emellett a fejlesztők használatával az Azure AD egybérlős és az üzletági (LOB) alkalmazásokat hozhat létre. A fejlesztők is használhatja az Azure AD több-bérlős alkalmazások fejlesztéséhez.

Az Azure AD áttekintést, látogasson el a [fundamentals oldal](https://docs.microsoft.com/azure/active-directory/fundamentals/index) részletes útmutatókat, fogalmak és gyors útmutatók.

Az alkalmazás vagy szolgáltatás az Azure AD-vel történő integrálásához a fejlesztőnek először regisztrálnia kell az alkalmazást az Azure AD-ben. Részletes utasításokat és képernyőképek: [ebben a rövid útmutatóban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

[Öt elsődleges alkalmazási](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) az Azure AD által támogatott:

* Egyoldalas alkalmazás (SPA): egy felhasználónak szüksége van a bejelentkezni egy egyoldalas alkalmazás Azure AD által védett.
* Webes alkalmazás webböngészőben: egy felhasználónak jelentkezzen be az Azure AD által védett webalkalmazás van szüksége.
* Natív alkalmazás webes API-hoz: egy natív alkalmazást, amely egy telefonon, táblagépen vagy számítógépen futtat egy felhasználói erőforrásokat lekérni a webes API-k az Azure AD által védett hitelesítenie kell.
* Webes alkalmazás webes API-hoz: webalkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.
* Webes API-hoz démon vagy a kiszolgáló alkalmazása: egy démon, alkalmazás vagy a nem a webes felhasználói felületének kiszolgálói alkalmazás kell-erőforrásokat az Azure AD által biztonságossá tett webes API.

A Windows Azure-hitelesítési tár az Active Directory-jogkivonatok beszerzésére számos lehetőséget kínál. A szalagtár és a kód minták a részletekért lásd: [Ez a cikk](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki).

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digitális Twins hívása a középső rétegbeli webes API-k

Amikor a fejlesztők számára a digitális Twins megoldások tervezhet, azokat általában hozzon létre egy középső rétegű alkalmazás vagy API-t. Az alkalmazás- vagy API ekkor meghívja a digitális Twins API aktiválásához. Támogatja a szabványos webes megoldás architektúrája, győződjön meg arról, hogy felhasználók első:

1. A középső rétegbeli alkalmazás hitelesítése

1. Az OAuth 2.0-alapú meghatalmazásos jogkivonat-hitelesítés során igényelve

1. A megszerzett jogkivonattal majd használt a hitelesítéshez, vagy az On-meghatalmazásos folyamat aktiválásához további használó API-jainak hívására

Az a-meghatalmazásos folyamat szervezését kapcsolatos útmutatásért lásd: [OAuth 2.0-alapú meghatalmazásos folyamat](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). A Kódminták is megtekintheti [alsóbb rétegbeli webes API-k hívása](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/).

## <a name="next-steps"></a>További lépések

Az OAuth 2.0 típusú implicit engedélyezés folyamat használata az Azure digitális Twins tesztelése és konfigurálása, hogy olvassa el a [Postman konfigurálása](./how-to-configure-postman.md).

Az Azure digitális Twins biztonságával kapcsolatban, olvassa el [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md).