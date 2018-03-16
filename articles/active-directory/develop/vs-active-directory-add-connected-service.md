---
title: "Egy Azure Active Directory hozzáadása a kapcsolódó szolgáltatások a Visual Studio használatával |} Microsoft Docs"
description: "Egy Azure Active Directory hozzáadása a Visual Studio kapcsolódó szolgáltatások hozzáadása párbeszédpanelen"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.service: active-directory
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: kraigb
ms.openlocfilehash: b21761b6fc166ecbb2fec9c13e5e207481fa9a39
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Egy Azure Active Directory hozzáadása a kapcsolódó szolgáltatások a Visual Studio használatával

Azure Active Directory (Azure AD) segítségével a webes API-szolgáltatásokban támogathat egyszeri bejelentkezés (SSO) az ASP.NET MVC webes alkalmazásokhoz, vagy az Active Directory-hitelesítéssel. Az Azure AD-alapú hitelesítés, a felhasználók használhatják a fiókok az Azure Active Directory a webes alkalmazásokhoz. A webes API-t az Azure AD Authentication előnyei közé tartozik továbbfejlesztett adatok biztonsága, amikor egy API-webalkalmazások kitettségének. Az Azure ad-vel nincs egy külön hitelesítési rendszere saját fiók és felhasználói felügyeleti kezeléséhez.

Ez a cikk és a kiegészítő cikkeket, adja meg a Visual Studio kapcsolódó szolgáltatás használatával az Active Directory adatait. A funkció érhető el a Visual Studio 2017 és a Visual Studio 2015-öt is.

Az Active Directory kapcsolódó szolgáltatás jelenleg nem támogatja az ASP.NET Core alkalmazások.

## <a name="prerequisites"></a>Előfeltételek

- Azure-fiók: Ha az Azure-fiók nem rendelkezik, akkor [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>És az Azure Active Directory, a kapcsolódó szolgáltatások párbeszédpanelen

1. A Visual Studio hozzon létre, vagy nyisson meg egy ASP.NET MVC projekt vagy egy ASP.NET Web API-projektet. Az MVC, Web API, egyetlen oldal alkalmazás, Azure API App, Azure Mobile Apps és az Azure Mobile Services mobilszolgáltatás sablonok is használhatja.

1. Válassza ki a **Project > csatlakoztatott szolgáltatás hozzáadása...**  parancs, vagy kattintson duplán a **kapcsolódó szolgáltatások** csomópont alatt a projektre a Solution Explorer található.

1. Az a **kapcsolódó szolgáltatások** lapon jelölje be **hitelesítés az Azure Active Directory**.

    ![Kapcsolódó szolgáltatások](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. Az a **bemutatása** lapon jelölje be **következő**. Ha hibába ütközik, ezen az oldalon, tekintse meg a [az Azure Active Directory szolgáltatással kapcsolódó hibák diagnosztizálása](vs-active-directory-error.md).

    ![Bemutató lapja](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. Az a **egyszeri bejelentkezést a** lapján jelöljön ki egy tartományt a a **tartomány** legördülő listából. A lista tartalmaz minden olyan tartománynál, elérhető a Visual Studio Fiókbeállítások párbeszédpanel fiókjaihoz (**fájl > Fiókbeállítások...** ). Alternatív megoldásként adhat meg egy tartomány nevét, ha nem találja a keresett, például egy `mydomain.onmicrosoft.com`. Dönthet úgy is Azure Active Directory-alkalmazás létrehozása, vagy használjon egy meglévő Azure Active Directory-alkalmazáshoz megadott beállításokkal. Válassza ki **következő** végzett.

    ![Egyszeri bejelentkezés az oldalon](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. Az a **Directory elérés** lapon jelölje be a **címtáradatok olvasása** a kívánt beállítást. A fejlesztők rendszerint ezt a beállítást.

    ![Directory adatelérési lap](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. Válassza ki **Befejezés** elindítani a projekthez az Azure AD-alapú hitelesítés engedélyezéséhez módosításokat. Ebben az időszakban a Visual Studio megjeleníti folyamatban van:

    ![Az Active Directory kapcsolódó szolgáltatás folyamatban](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. A folyamat be nem fejeződik, megnyílik a Visual Studio és az a böngészőt, hogy a projekt típusától függően az alábbi cikkekben:

    - [Ismerkedés a .NET MVC-projektek](vs-active-directory-dotnet-getting-started.md)
    - [Ismerkedés a WebAPI projektek](vs-active-directory-webapi-getting-started.md)

1. Azt is láthatja az Active Directory-tartomány, a a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).

## <a name="how-your-project-is-modified"></a>A projekt módosítása hogyan

A csatlakoztatott szolgáltatás a varázsló hozzáadásakor a Visual Studio Azure Active Directory és a projekthez kapcsolódó hivatkozások hozzáadása. A konfigurációs fájlok és a projekt kódfájlok támogatásához az Azure AD is módosítani. A megadott módosításokat, amelynek eredményeképpen a Visual Studio projekt-típustól függnek. További részletek a következő cikkekben talál:

- [Mi történt a .NET MVC projekt?](vs-active-directory-dotnet-what-happened.md)
- [Mi történt a Web API-projektet?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>További lépések

- [Az Azure Active Directory hitelesítési forgatókönyvei](active-directory-authentication-scenarios.md)
- [Bejelentkezés a Microsoft ASP.NET webalkalmazás hozzáadása](guidedsetups/active-directory-aspnetwebapp-v1.md)
