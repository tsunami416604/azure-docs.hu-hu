---
title: Az Active Directoryhoz csatlakoztatott szolgáltatás használata (Visual Studio)
description: Azure Active Directory hozzáadása a Visual Studio Csatlakoztatott szolgáltatások hozzáadása párbeszédpanelen
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: d5b6452684757aead684356fd9bb032b90c58c4a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535825"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Azure Active Directory hozzáadása a Csatlakoztatott szolgáltatások használatával a Visual Studióban

Az Azure Active Directory (Azure AD) használatával támogathatja az egyszeri bejelentkezést (SSO) ASP.NET MVC webalkalmazásokhoz, vagy az Active Directory-hitelesítést a webes API-szolgáltatásokban. Az Azure AD-hitelesítéssel a felhasználók az Azure Active Directoryból származó fiókjaik segítségével csatlakozhatnak a webalkalmazásokhoz. Az Azure AD-hitelesítés webes API-val a fokozott adatbiztonság, amikor egy webalkalmazás api-t tesz ki. Az Azure AD-vel nem kell külön hitelesítési rendszert kezelnie saját fiókkal és felhasználókezeléssel.

Ez a cikk és társcikkei az Active DirectoryHoz csatlakoztatott Visual Studio connected szolgáltatás használatának részleteit ismertetik. A funkció a Visual Studio 2015-ben és újabb verzióiban érhető el.

Jelenleg az Active Directorycsatlakoztatott szolgáltatás nem támogatja ASP.NET Core alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

- Azure-fiók: ha nem rendelkezik Azure-fiókkal, [regisztrálhat egy ingyenes próbaverzióra,](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit.](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)
- **Visual Studio 2015-ös** vagy újabb verzió. [Töltse le most a Visual Studio alkalmazást.](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Csatlakozás az Azure Active Directoryhoz a Csatlakoztatott szolgáltatások párbeszédpanelen

1. A Visual Studio-ban hozzon létre vagy nyisson meg egy ASP.NET MVC-projektet vagy egy ASP.NET Webes API-projektet. Használhatja az MVC, web API, egyoldalas alkalmazás, Azure API App, Azure Mobile App és az Azure Mobile Service sablonokat.

1. Válassza a **Project > Csatlakoztatott szolgáltatás hozzáadása...** menüparancsot, vagy kattintson duplán a Megoldáskezelőben a projekt alatt található Csatlakoztatott **szolgáltatások** csomópontra.

1. A **Csatlakoztatott szolgáltatások** lapon válassza a **Hitelesítés az Azure Active Directoryval**lehetőséget.

    ![Csatlakoztatott szolgáltatások lap](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. A **Bevezetés** lapon válassza a **Tovább gombot.** Ha hibákat lát ezen a lapon, olvassa el [a Hibák diagnosztizálása az Azure Active Directoryhoz csatlakoztatott szolgáltatással című témakört.](vs-active-directory-error.md)

    ![Bevezetés oldal](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Az **Egyszeres bejelentkezés lapon** válasszon ki egy tartományt a **Tartomány** legördülő listából. A lista tartalmazza a Visual Studio Fiókbeállítások párbeszédpanelén felsorolt fiókok által elérhető összes tartományt (**Fájl > Fiókbeállítások...**). Másik lehetőségként megadhat egy domain nevet, ha nem találja a keresett nevet, például `mydomain.onmicrosoft.com`. Kiválaszthatja az Azure Active Directory-alkalmazás létrehozásának lehetőségét, vagy használhatja a beállításokat egy meglévő Azure Active Directory-alkalmazásból. Ha végzett, válassza a **Tovább gombot.**

    ![Egyszeres bejelentkezés az oldalon](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. A **Címtár-hozzáférés** lapon válassza a **Címtáradatok olvasása** lehetőséget. A fejlesztők általában tartalmazzák ezt a lehetőséget.

    ![Címtárelérési lap](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Válassza **a Befejezés** lehetőséget a projekt módosításának megkezdéséhez az Azure AD-hitelesítés engedélyezéséhez. A Visual Studio az ez idő alatt mutatja az előrehaladást:

    ![Active Directory csatlakoztatott szolgáltatás állapota](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. Amikor a folyamat befejeződött, a Visual Studio megnyitja a böngészőt az alábbi cikkek egyikére, a projekt típusának megfelelően:

    - [Bevezetés a .NET MVC-projektek használatába](vs-active-directory-dotnet-getting-started.md)
    - [Bevezetés a WebAPI-projektek használatába](vs-active-directory-webapi-getting-started.md)

1. Az Active Directory-tartomány az [Azure Portalon](https://go.microsoft.com/fwlink/p/?LinkID=525040)is látható.

## <a name="how-your-project-is-modified"></a>A projekt módosításának időpontja

Amikor hozzáadja a csatlakoztatott szolgáltatást a varázslóhoz, a Visual Studio hozzáadja az Azure Active Directoryt és a kapcsolódó hivatkozásokat a projekthez. A projekt konfigurációs fájljai és kódfájljai is módosulnak, hogy az Azure AD-t is támogassák. A Visual Studio által végzett módosítások a projekt típusától függenek. A részleteket lásd a következő cikkekben:

- [Mi történt a .NET MVC-projektemmel?](vs-active-directory-dotnet-what-happened.md)
- [Mi történt a webes API-projektemmel?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](authentication-scenarios.md)
- [Bejelentkezés hozzáadása a Microsofttal egy ASP.NET webalkalmazáshoz](quickstart-v2-aspnet-webapp.md)
