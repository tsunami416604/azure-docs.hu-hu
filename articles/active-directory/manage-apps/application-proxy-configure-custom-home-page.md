---
title: Egyéni kezdőlap a közzétett alkalmazásokhoz – Azure AD alkalmazásproxy
description: Az Azure AD alkalmazásproxy-összekötők alapanyagait ismerteti
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275597"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Egyéni kezdőlap beállítása a közzétett alkalmazásokhoz az Azure AD alkalmazásproxy használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy alkalmazást úgy, hogy a felhasználót egy egyéni kezdőlapra irányítsa. Amikor alkalmazásproxyval tesz közzé egy alkalmazást, belső URL-címet állít be, de néha nem ezt az oldalt kell először látnia, amelyet a felhasználónak először látnia kell. Egyéni kezdőlap beállítása úgy, hogy a felhasználó a megfelelő lapot kapja meg az alkalmazás elérésekor. A felhasználó látni fogja a beállított egyéni kezdőlapot, függetlenül attól, hogy az Azure Active Directory Access Panelről vagy az Office 365 alkalmazásindítójából férnek-e hozzá.

Amikor egy felhasználó elindítja az alkalmazást, alapértelmezés szerint a közzétett alkalmazás gyökértartományának URL-címére irányítja őket. A céloldal általában kezdőlap URL-címként van beállítva. Az Azure AD PowerShell modul segítségével egyéni kezdőlap URL-címét határozhatja meg, ha azt szeretné, hogy egy alkalmazásfelhasználó az alkalmazáson belül egy adott oldalon landoljon.

Az alábbi forgatókönyv ből megtudhatja, hogy a vállalat miért állított be egyéni kezdőlapot:

- A vállalati hálózaton belül a `https://ExpenseApp/login/login.aspx` felhasználó bejelentkezik, és hozzáférhet az alkalmazáshoz.
- Mivel más eszközök (például képek) is rendelkeznek, amelyekhez az alkalmazásproxynak a `https://ExpenseApp` mappastruktúra legfelső szintjén kell hozzáférnie, az alkalmazást belső URL-címként teheti közzé.
- Az alapértelmezett külső `https://ExpenseApp-contoso.msappproxy.net`URL-cím a , amely nem visz külső felhasználót a bejelentkezési oldalra.
- Ehelyett kezdőlap `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` URL-címként szeretné beállítani, így egy külső felhasználó először a bejelentkezési lapot látja.

> [!NOTE]
> Amikor hozzáférést ad a felhasználóknak a közzétett alkalmazásokhoz, az alkalmazások megjelennek az [Azure AD Access Panelen](../user-help/my-apps-portal-end-user-access.md) és az [Office 365 alkalmazásindítójában.](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)

## <a name="before-you-start"></a>Előkészületek

A kezdőlap URL-címének beállítása előtt tartsa szem előtt az alábbi követelményeket:

- A megadott elérési útnak a gyökértartomány URL-címének altartomány-elérési útjának kell lennie.

  Ha például a gyökértartomány `https://apps.contoso.com/app1/`URL-címe a rendszer, a `https://apps.contoso.com/app1/`konfigurált kezdőlap URL-címének a alkalmazással kell kezdődnie.

- Ha módosítja a közzétett alkalmazást, a módosítás visszaállíthatja a kezdőlap URL-címének értékét. Amikor a jövőben frissíti az alkalmazást, újra ellenőriznie kell, és szükség esetén frissítenie kell a kezdőlap URL-címét.

Beállíthatja a kezdőlap URL-címét az Azure Portalon keresztül vagy a PowerShell használatával.

## <a name="change-the-home-page-in-the-azure-portal"></a>A kezdőlap módosítása az Azure Portalon

Ha módosítani szeretné az alkalmazás kezdőlapjának URL-címét az Azure AD portálon keresztül, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) rendszergazdaként.
1. Válassza az **Azure Active Directory**lehetőséget, majd az **alkalmazásregisztrációk lehetőséget.** Megjelenik a regisztrált alkalmazások listája.
1. Válassza ki az alkalmazást a listából. Megjelenik egy oldal, amely a regisztrált alkalmazás adatait mutatja.
1. A **Kezelés csoportban**válassza a **Márkajelzés**lehetőséget.
1. Frissítse a **kezdőlap URL-címét** az új elérési úttal.

   ![A kezdőlap URL-mezőjét megjelenítő regisztrált alkalmazás márkajelzési lapja](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Kattintson a **Mentés** gombra.

## <a name="change-the-home-page-with-powershell"></a>A kezdőlap módosítása a PowerShell használatával

Ha egy alkalmazás kezdőlapját powershell használatával szeretné konfigurálni, a következőket kell a következőkre használnia:

1. Telepítse az Azure AD PowerShell-modult.
1. Keresse meg az alkalmazás ObjectId értékét.
1. Frissítse az alkalmazás kezdőlapjának URL-címét a PowerShell-parancsokkal.

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell-modul telepítése

Mielőtt egyéni kezdőlap URL-címét definiálja a PowerShell használatával, telepítse az Azure AD PowerShell-modult.Letöltheti a csomagot a [PowerShell-galériából,](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)amely a Graph API-végpontot használja.

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy szabványos PowerShell-ablakot, majd futtassa a következő parancsot:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Ha a parancsot nem rendszergazdaként futtatja, `-scope currentuser` használja ezt a lehetőséget.

1. A telepítés során válassza az **Y** lehetőséget, ha két csomagot szeretne telepíteni Nuget.org. Mindkét csomag szükséges.

### <a name="find-the-objectid-of-the-app"></a>Az alkalmazás ObjectId azonosítójának megkeresése

Az alkalmazás ObjectId azonosítóját úgy kapja meg, hogy megjelenítendő neve vagy kezdőlapja alapján keresi az alkalmazást.

1. Ugyanabban a PowerShell-ablakban importálja az Azure AD-modult.

   ```powershell
   Import-Module AzureAD
   ```

1. Jelentkezzen be az Azure AD modulbérlői rendszergazdaként.

   ```powershell
   Connect-AzureAD
   ```

1. Keresse meg az alkalmazást. Ez a példa a PowerShell segítségével keresi meg az ObjectId azonosítót a megjelenítendő nevű alkalmazás keresésével. `SharePoint`

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Az itt láthatóhoz hasonló eredményt kell kapnia. Másolja az ObjectId GUID azonosítót a következő szakaszban való használatra.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Azt is megteheti, hogy csak lekéri az összes alkalmazás listáját, megkeresi a listát egy adott megjelenítendő névvel vagy kezdőlapdal, és másolja az alkalmazás ObjectId azonosítóját, miután megtalálta az alkalmazást.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>A kezdőlap URL-címének frissítése

Hozza létre a kezdőlap URL-címét, és frissítse az alkalmazást ezzel az értékkel. Továbbra is használja ugyanazt a PowerShell-ablakot, vagy ha egy új PowerShell-ablakot használ, jelentkezzen be újra az Azure AD modulba a használatával. `Connect-AzureAD` Ez után kövesse az alábbi lépéseket:

1. Hozzon létre egy változót az előző szakaszban másolt ObjectId érték tárolására. (Cserélje le a SharePoint-példában használt ObjectId értéket az alkalmazás ObjectId értékére.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. A következő parancs futtatásával ellenőrizze, hogy a megfelelő alkalmazással rendelkezik-e. A kimenetnek meg kell egyeznie az előző szakaszban látott kimenettel ([Az alkalmazás ObjectId jának megkeresése](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Hozzon létre egy üres alkalmazásobjektumot a végrehajtani kívánt módosítások tárolására.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Állítsa a kezdőlap URL-címét a kívánt értékre. Az értéknek a közzétett alkalmazás altartomány-elérési útjának kell lennie. Ha például a kezdőlap `https://sharepoint-iddemo.msappproxy.net/` URL-címét `https://sharepoint-iddemo.msappproxy.net/hybrid/`a rendszerre módosítja, az alkalmazás felhasználói közvetlenül az egyéni kezdőlapra lépnek.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. A kezdőlap frissítésének eltöltése.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. A módosítás sikeresse simolásának ellenőrzéséhez futtassa újra a következő parancsot a 2.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Példánkban a kimenetnek most a következőképpen kell megjelennie:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Indítsa újra az alkalmazást, és ellenőrizze, hogy a kezdőlap a várt módon első képernyőként jelenik-e meg.

> [!NOTE]
> Az alkalmazáson végzett módosítások alaphelyzetbe állíthatják a kezdőlap URL-címét. Ha a kezdőlap URL-címe alaphelyzetbe áll, ismételje meg az ebben a szakaszban található lépéseket a visszaállításhoz.

## <a name="next-steps"></a>További lépések

- [Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével](application-proxy-integrate-with-sharepoint-server.md)
- [Oktatóanyag: Az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréshez helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md)