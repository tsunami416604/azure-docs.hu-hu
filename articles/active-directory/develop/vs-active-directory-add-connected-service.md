---
title: A Visual Studio csatlakoztatott szolgáltatásai segítségével egy Azure Active Directory hozzáadása
description: Adjon hozzá egy Azure Active Directory használatával a Visual Studio csatlakoztatott szolgáltatás hozzáadása párbeszédpanel
services: active-directory
author: ghogen
manager: douge
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: 9b61362525962c5e01d7fef6d317cf9861dfd0a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977893"
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>A Visual Studio csatlakoztatott szolgáltatásai segítségével egy Azure Active Directory hozzáadása

Az Azure Active Directory (Azure AD) használatával is támogatottak egyszeri bejelentkezéses (SSO) az ASP.NET MVC webes alkalmazásokhoz, vagy az Active Directory-hitelesítést a webes API-szolgáltatást. Azure AD-hitelesítés a felhasználók használhatják fiókjukat az Azure Active Directoryból a szeretne csatlakozni a webes alkalmazások. A webes API-val az Azure AD-hitelesítés előnyei közé tartozik fokozott adatbiztonságot, ha egy API-webalkalmazások is közzéteheti. Az Azure ad-vel nem kell egy külön hitelesítési rendszere a saját fiók és felhasználó management kezeléséhez.

Ez a cikk és a kiegészítő cikkek adja meg az adatait a Visual Studio csatlakoztatott szolgáltatása az Active Directory szolgáltatással. A funkció érhető el a Visual Studio 2017-et és a Visual Studio 2015-ben.

Az Active Directory csatlakoztatott szolgáltatás jelenleg nem támogatja az ASP.NET Core-alkalmazások.

## <a name="prerequisites"></a>Előfeltételek

- Azure-fiók: Ha nem rendelkezik Azure-fiókkal, akkor [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
- **A Visual Studio 2015** vagy újabb. [A Visual Studio 2017 letöltése](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>Csatlakozzon az Azure Active Directory a csatlakoztatott szolgáltatás párbeszédpanelen

1. A Visual Studióban hozzon létre vagy nyisson meg egy ASP.NET MVC-projektben vagy egy ASP.NET webes API projektet. Az MVC, webes API-t, egyoldalas alkalmazás, Azure API App, Azure Mobile Apps és az Azure Mobile szolgáltatássablonok is használhatja.

1. Válassza ki a **Project > csatlakoztatott szolgáltatás hozzáadása...**  parancs, vagy kattintson duplán a **csatlakoztatott szolgáltatás** csomópont a Megoldáskezelőben a projekt alatt található.

1. Az a **csatlakoztatott szolgáltatás** lapon jelölje be **az Azure Active Directory hitelesítési**.

    ![Összekapcsolt szolgáltatások lap](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Az a **bemutatása** lapon jelölje be **tovább**. Ha hibába ütközik, ezen az oldalon, [az Azure Active Directory csatlakoztatott szolgáltatás-hibák diagnosztizálása](vs-active-directory-error.md).

    ![Bemutató lapja](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Az a **egyszeri bejelentkezést a** lapra, jelölje be a tartomány a **tartomány** legördülő listából válassza ki. A lista tartalmazza az összes tartomány elérhető-e a fiókok szerepel a Fiókbeállítások párbeszédpanel a Visual Studio által (**fájl > Fiókbeállítások...** ). Alternatív megoldásként adhat meg egy tartománynevet, ha nem találja a keresett, mint például egy `mydomain.onmicrosoft.com`. Hozzon létre egy Azure Active Directory-alkalmazást, vagy használjon egy meglévő Azure Active Directory-alkalmazást a beállításokat a lehetőséget is választja. Válassza ki **tovább** végeztével.

    ![Egyszeri bejelentkezés az oldalon](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Az a **címtárhozzáférés** lapon válassza ki a **címtáradatok olvasása** lehetőséget igény szerint. A fejlesztők általában adni ezt a lehetőséget.

    ![Könyvtár hozzáférés lap](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Válassza ki **Befejezés** , indítsa el a módosításokat az Azure AD-hitelesítés engedélyezéséhez a projekthez. A Visual Studio folyamatot mutatja, ez idő alatt:

    ![Az Active Directory csatlakoztatott szolgáltatás folyamatban](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. A folyamat befejeződése után a Visual Studio megnyitása a böngészőben a egyet az alábbi cikkeket, a projekt típusa megfelelő módon:

    - [Bevezetés a .NET MVC-projektek használatába](vs-active-directory-dotnet-getting-started.md)
    - [Bevezetés a WebAPI-projektek használatába](vs-active-directory-webapi-getting-started.md)

1. Emellett megtekintheti az Active Directory-tartomány az a [az Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>Hogyan módosul a projekthez

Amikor hozzáadja a csatlakoztatott szolgáltatás a varázslót, a Visual Studio hozzáadja a projekthez kapcsolódó referenciák és az Azure Active Directory. Az Azure ad-támogatás hozzáadása is módosítják a konfigurációs fájlokat és a projekt kódfájlokat. A megadott módosítások a Visual Studio lehetővé teszi a projekt típusa függenek. Tekintse meg a részleteket a következő cikkeket:

- [Mi történt a .NET MVC-projektemmel?](vs-active-directory-dotnet-what-happened.md)
- [Mi történt a webes API-projektemmel?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>További lépések

- [Hitelesítési forgatókönyvek az Azure Active Directory](authentication-scenarios.md)
- [Jelentkezzen be a Microsoft ASP.NET-webalkalmazás hozzáadása](quickstart-v1-aspnet-webapp.md)
