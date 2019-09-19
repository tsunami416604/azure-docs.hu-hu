---
title: Felhasználók számára bejelentkező webes alkalmazás (alkalmazás-regisztráció) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely bejelentkezik a felhasználók számára (alkalmazás regisztrálása)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bdf04014d7b0382913c0a4094f7474686658441
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086704"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Felhasználók számára bejelentkező webalkalmazás – alkalmazás regisztrálása

Ezen a lapon megtudhatja, hogy az alkalmazás regisztrálva van-e egy olyan webalkalmazáshoz, amely bejelentkezik a felhasználók számára.

Az alkalmazás regisztrálásához a következőket használhatja:

- A [webalkalmazás gyors](#register-an-app-using-the-quickstarts) üzembe helyezése – amellett, hogy nagyszerű első élményt nyújt egy alkalmazás létrehozásához, a Azure Portalban található gyors útmutatóban egy, a **módosítást**elnevező gombot is tartalmaz. Ezt a gombot használhatja a szükséges tulajdonságok beállításához még egy meglévő alkalmazáshoz is. A tulajdonságok értékeit a saját esetéhez kell igazítania. Az alkalmazás webes API URL-címe valószínűleg eltér a javasolt alapértelmezetttől, ami hatással lesz a kijelentkezési URI-ra is.
- Az [alkalmazás manuális regisztrálásának](#register-an-app-using-azure-portal) Azure Portal
- PowerShell és parancssori eszközök

## <a name="register-an-app-using-the-quickstarts"></a>Alkalmazás regisztrálása a gyors útmutatók használatával

Ha megnyitja ezt a hivatkozást, létrehozhat rendszerindítást a webalkalmazás létrehozásakor:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Alkalmazás regisztrálása a Azure Portal használatával

> [!NOTE]
> a használni kívánt portál eltérő lehet attól függően, hogy az alkalmazás a Microsoft Azure nyilvános felhőben, vagy egy nemzeti vagy szuverén felhőben fut-e. További információ: [National felhők](./authentication-national-cloud.md#app-registration-endpoints)

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com). Másik lehetőségként jelentkezzen be a nemzeti felhőbe Azure Portal.
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk** > **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. Válassza ki az alkalmazás támogatott fiókjának típusát (lásd a [támogatott fióktípus](./v2-supported-account-types.md))
   1. A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `AspNetCore-WebApp`).
   1. Az **átirányítási URI**-ban adja meg az alkalmazás típusát és az URI célhelyet, amely a sikeres hitelesítés után fogadja a visszaadott jogkivonat-válaszokat. Például: `https://localhost:44321/`.  Kattintson a **Register** (Regisztrálás) elemre.
1. Válassza a **hitelesítés** menüt, majd adja hozzá a következő adatokat:
   1. A **Válasz URL**-címe `https://localhost:44321/signin-oidc`mezőben adja hozzá a címet.
   1. A **Speciális beállítások** szakaszban állítsa be a **KIJELENTKEZÉSI URL-címet** a `https://localhost:44321/signout-oidc`következőre:.
   1. Az **implicit támogatás szakaszban adja**meg az **azonosító jogkivonatokat**.
   1. Kattintson a **Mentés** gombra.

### <a name="register-an-app-using-powershell"></a>Alkalmazás regisztrálása a PowerShell-lel

> [!NOTE]
> Az Azure AD PowerShell jelenleg csak a következő támogatott fióktípus-típusokat hozza létre:
>
> - MyOrg (csak ebben a szervezeti könyvtárban lévő fiókok)
> - AnyOrg (fiókok bármely szervezeti címtárban).
>
> Ha olyan alkalmazást szeretne létrehozni, amely a felhasználók személyes Microsoft-fiókjaival (például Skype, XBox, Outlook.com) jelentkezik be, először hozzon létre egy több-bérlős alkalmazást (támogatott fióktípus = fiókok bármely szervezeti címtárban), majd módosítsa az alkalmazás jegyzékfájljának tulajdonsága a Azure Portalból. `signInAudience` Ezt részletesen ismerteti a ASP.NET Core oktatóanyag [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) . lépésében (és bármilyen nyelven általánosítható a webalkalmazások számára).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódjának konfigurálása](scenario-web-app-sign-user-app-configuration.md)
