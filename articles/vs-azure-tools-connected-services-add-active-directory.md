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
ms.date: 03/01/2017
ms.author: kraigb
ms.openlocfilehash: a767c93fb271f3aa33d9556c69c511bcac7cb0d5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="adding-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Egy Azure Active Directory hozzáadása a kapcsolódó szolgáltatások a Visual Studio használatával
Azure Active Directory (Azure AD) segítségével a webes API-szolgáltatásokban támogathat egyszeri bejelentkezés (SSO) az ASP.NET MVC webes alkalmazásokhoz, vagy az Active Directory-hitelesítéssel. Az Azure Active Directory-hitelesítéssel, a felhasználók használhatják a fiókok az Azure Active Directoryból a webes alkalmazásokhoz. Azure Active Directory Authentication webes API-t a előnyei közé tartozik továbbfejlesztett adatok biztonsági, amikor egy API-webalkalmazások kitettségének. Az Azure ad-vel nincs egy külön hitelesítési rendszere saját fiók és felhasználói felügyeleti kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek
- Azure-fiók – Ha nem rendelkezik Azure-fiókot, akkor [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>És az Azure Active Directory, a kapcsolódó szolgáltatások párbeszédpanelen
1. A Visual Studio hozzon létre, vagy nyisson meg egy ASP.NET MVC projekt vagy egy ASP.NET Web API-projektet.

1. A Megoldáskezelőben, kattintson a jobb gombbal a **kapcsolódó szolgáltatások** csomópont, és a helyi menüből válassza ki a **kapcsolódó szolgáltatások hozzáadása**.

1. Az a **kapcsolódó szolgáltatások** lapon jelölje be **hitelesítés az Azure Active Directory**.
   
    ![Kapcsolódó szolgáltatások](./media/vs-azure-tools-connected-services-add-active-directory/connected-services-add-active-directory.png)

1. Az a **bemutatása** oldalán a **konfigurálása Azure AD-alapú hitelesítés** varázslóban válassza **következő**.
   
    ![Bemutató lapja](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-1.png)

1. A a **egyszeri bejelentkezést a** oldalán a **konfigurálása Azure AD-alapú hitelesítés** varázsló, válasszon ki egy tartományt a a **tartomány** legördülő listából. A tartományok listáját a Fiókbeállítások párbeszédpanel fiókjaihoz által elérhető összes tartományt tartalmazza. Alternatív megoldásként adhat meg egy tartomány nevét, ha nem találja a keresett, például egy `mydomain.onmicrosoft.com`. Dönthet úgy is Azure Active Directory-alkalmazás létrehozása, vagy használjon egy meglévő Azure Active Directory-alkalmazáshoz megadott beállításokkal. Válassza ki **következő** végzett.
   
    ![Egyszeri bejelentkezés az oldalon](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-2.png)

1. Az a **Directory elérés** oldalán a **konfigurálása Azure AD-alapú hitelesítés** varázsló, ügyeljen arra, hogy a **címtáradatok olvasása** beállítást. 
   
    ![Directory adatelérési lap](./media/vs-azure-tools-connected-services-add-active-directory/configure-azure-ad-wizard-3.png)

1. Válassza ki **Befejezés** hozzá a szükséges konfigurációs kódot, és hivatkozásokat a projekthez az Azure AD-alapú hitelesítés engedélyezéséhez. Az Active Directory-tartomány tekintheti meg a [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. A Visual Studio megjeleníti a [Mi történt](#how-your-project-is-modified) cikk mutatjuk be, hogyan a projekthez lett módosítva. Ha azt szeretné, ellenőrizze, hogy még működött nyisson meg egy módosított konfigurációs fájlt, és ellenőrizze, hogy a beállításokat, a cikkben említett vannak-e. 

## <a name="how-your-project-is-modified"></a>A projekt módosítása hogyan
A varázsló futtatásakor a Visual Studio Azure Active Directory és a projekthez kapcsolódó hivatkozások hozzáadása. A konfigurációs fájlok és a projekt kódfájlok támogatásához az Azure AD is módosítani. A megadott módosításokat, amelynek eredményeképpen a Visual Studio projekt-típustól függnek. Hogyan módosultak a ASP.NET MVC-projektek kapcsolatos részletes információkért lásd: [milyen happened – MVC projektek](http://go.microsoft.com/fwlink/p/?LinkID=513809). Webes API-projektet, lásd: [Mi történt – a webes API-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513810).

## <a name="next-steps"></a>Következő lépések
* [Az Azure Active Directory MSDN fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)
* [Azure Active Directory dokumentációja](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blogbejegyzés: Bevezetés az Azure Active Directoryban](http://blogs.msdn.com/b/brunoterkaly/archive/2014/03/03/introduction-to-windows-azure-active-directory.aspx)

