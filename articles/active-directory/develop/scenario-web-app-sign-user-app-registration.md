---
title: Felhasználókban bejelentkező webalkalmazás regisztrálása – Microsoft identity platform | Azure
description: További információ a felhasználókban bejelentkező webalkalmazások regisztrálásáról
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7d2eb5356b1abc54508fd6bf8d35fd9fc39d02ec
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881579"
---
# <a name="web-app-that-signs-in-users-app-registration"></a>A felhasználókban bejelentkező webalkalmazás: Alkalmazásregisztráció

Ez a cikk ismerteti az alkalmazás regisztrációs sajátosságait egy webalkalmazás, amely aláírja a felhasználók.

Az alkalmazás regisztrálásához a következőket használhatja:

- A [webalkalmazás rövid útmutatói](#register-an-app-by-using-the-quickstarts). Amellett, hogy nagyszerű első élmény egy alkalmazás létrehozásában, az Azure Portalon található rövid útmutatók tartalmaznak egy : Legyen ez a módosítás nekem nevű **gombot.** Ezzel a gombbal beállíthatja a szükséges tulajdonságokat, még egy meglévő alkalmazás esetében is. Ezen tulajdonságok értékeit a saját esetéhez kell igazítania. Különösen az alkalmazás webes API-URL-címe valószínűleg eltér a javasolt alapértelmezetttől, ami a kijelentkezési URI-ra is hatással lesz.
- Az Azure [Portalon manuálisan regisztrálhatja az alkalmazást.](#register-an-app-by-using-the-azure-portal)
- PowerShell és parancssori eszközök.

## <a name="register-an-app-by-using-the-quickstarts"></a>Alkalmazás regisztrálása a rövid útmutatók használatával

Használhatja ezeket a linkeket bootstrap létrehozását a webes alkalmazás:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-by-using-the-azure-portal"></a>Alkalmazás regisztrálása az Azure Portal használatával

> [!NOTE]
> A használandó portál attól függően eltérő, hogy az alkalmazás a Microsoft Azure nyilvános felhőben, illetve nemzeti vagy szuverén felhőben fut.The portal to use is different on whether your application runs in the Microsoft Azure public cloud or in a national or sovereign cloud. További információ: [National clouds](./authentication-national-cloud.md#app-registration-endpoints).


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal. Másik lehetőségként jelentkezzen be a nemzeti felhőhöz kiválasztott Azure-portálra.
1. Ha a fiók több bérlőhöz is hozzáférést biztosít, válassza ki a fiókját a jobb felső sarokban. Ezután állítsa be a portálmunkamenetet a kívánt Azure Active Directory (Azure AD) bérlőre.
1. A bal oldali ablaktáblában válassza ki az **Azure Active Directory** szolgáltatást, majd válassza az Új**regisztrációk** **alkalmazásregisztrációk** > lehetőséget.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. Válassza ki az alkalmazás támogatott fióktípusait. [(Lásd: Támogatott fióktípusok.)](./v2-supported-account-types.md)
   1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például az **AspNetCore-WebApp .**
   1. Az **átirányításURI**esetén adja hozzá az alkalmazás típusát és az URI-célt, amely a sikeres hitelesítés után elfogadja a visszaadott jogkivonat-válaszokat. Írja be **https://localhost:44321**például a beírását. Ezután válassza a **Regisztráció**lehetőséget.
1. Válassza a **Hitelesítés** menüt, majd adja meg a következő adatokat:
   1. A **Válasz URL-címéhez**adja hozzá **https://localhost:44321/signin-oidc** a **Web**típust.
   1. A **Speciális beállítások** csoportban állítsa **https://localhost:44321/signout-oidc**a **Kijelentkezés URL-címét** a-ra.
   1. Az **Implicit engedély** területen válassza az **Azonosítói jogkivonatok** elemet.
   1. Kattintson a **Mentés** gombra.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait:
   1. Válassza ki az alkalmazás támogatott fióktípusait. [(Lásd: Támogatott fióktípusok.)](./v2-supported-account-types.md)
   1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például a **MailApp-openidconnect-v2 értéket.**
   1. Az **Átirányítás URI (nem kötelező)** csoportban válassza a **Web** elemet **https://localhost:44326/** a kombinált listában, és írja be a következő átirányítási URI-t: .
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Válassza a **Hitelesítés** menüt.
1. A **Speciális beállítások** | **Implicit támogatás** csoportban válassza az **Azonosítótokenek lehetőséget.** Ez a minta megköveteli, hogy az [implicit támogatási folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezve legyen a felhasználó bejelentkezéséhez.
1. Kattintson a **Mentés** gombra.

# <a name="java"></a>[Java](#tab/java)

1. Amikor megjelenik a **Jelentkezés regisztrálása lap,** adja meg az alkalmazás megjelenítendő nevét. Írja be például a **java-webapp**.
1. Válassza **a Fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com),** majd válassza a Web **app/API** for **Application Type**lehetőséget.
1. Válassza a **Regisztráció** lehetőséget az alkalmazás regisztrálásához.
1. A bal oldali menüben válassza a **Hitelesítés**lehetőséget. Az **Irányított urik átirányítása csoportban**válassza a **Web (Web**) lehetőséget.

1. Adjon meg két átirányítási URI-t: egyet a bejelentkezési oldalhoz, egyet pedig a grafikonoldalhoz. Mindkét esetben használja ugyanazt az állomás- és portszámot, majd a **/msal4jsample/secure/aad** kapcsolót a bejelentkezési oldalhoz, az **msal4jsample/graph/me-t** pedig a felhasználói információs laphoz.

   Alapértelmezés szerint a minta a következőket használja:

   - **http://localhost:8080/msal4jsample/secure/aad**
   - **http://localhost:8080/msal4jsample/graph/me**

  Ezután válassza a **Mentés gombot.**

1. Válassza **a Tanúsítványok & titkos kulcsok at** a menüből.
1. Az **Ügyféltitok csoportban** válassza az **Új ügyféltitok**lehetőséget, majd:

   1. Adja meg a kulcs leírását.
   1. Válassza ki a kulcs **időtartamát egy év alatt.**
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Amikor megjelenik a kulcsérték, másolja későbbre. Ez az érték nem jelenik meg újra, és nem lehet visszakereshető semmilyen más módon.

# <a name="python"></a>[Python](#tab/python)

1. Amikor megjelenik a **Jelentkezési lap regisztrálása,** adja meg a jelentkezési lap regisztrációs adatait:
   1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára. Írja be például a **python-webapp**.
   1. Módosítsa **a Támogatott fióktípusokat** fiókok ra **bármely szervezeti címtárban és személyes Microsoft-fiókban (pl. Skype, Xbox, Outlook.com).**
   1. Az **Átirányítás URI (nem kötelező)** csoportban válassza a **Web** elemet **http://localhost:5000/getAToken**a kombinált listában, és írja be a következő átirányítási URI-t: .
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **áttekintése** lapon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és rögzítse későbbre. Szüksége lesz rá a Visual Studio konfigurációs fájljának konfigurálásához ehhez a projekthez.
1. A bal oldali menüben válassza **a Tanúsítványok & titkos kulcsok**lehetőséget.
1. Az **Ügyféltitkok** csoportban válassza az **Új ügyféltitok**lehetőséget, majd:

   1. Adja meg a kulcs leírását.
   1. Válassza ki a legfontosabb időtartama **Az 1 év .**
   1. Válassza a **Hozzáadás** lehetőséget.
   1. Amikor megjelenik a kulcsérték, másolja azt. Erre később még szüksége lesz.
---

## <a name="register-an-app-by-using-powershell"></a>Alkalmazás regisztrálása a PowerShell használatával

> [!NOTE]
> Jelenleg az Azure AD PowerShell csak a következő támogatott fióktípusokkal hoz létre alkalmazásokat:
>
> - MyOrg (csak ebben a szervezeti címtárban lévő fiókok)
> - AnyOrg (fiókok bármely szervezeti címtárban)
>
> Létrehozhat egy olyan alkalmazást, amely személyes Microsoft-fiókjukkal (például Skype- vagy Outlook.com) írja alá a felhasználókat. Először hozzon létre egy több-bérlős alkalmazást. A támogatott fióktípusok bármely szervezeti címtárban lévő fiókok. Ezután módosítsa `signInAudience` a tulajdonságot az alkalmazásjegyzékben az Azure Portalról. További információ: [1.3 lépés](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) a ASP.NET Core tutorial. Ezt a lépést általánosíthatja a webalkalmazásokbármely nyelven.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az alkalmazás kódkonfigurációja](scenario-web-app-sign-user-app-configuration.md)
