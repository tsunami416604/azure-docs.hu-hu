---
title: Közzétett alkalmazások egyéni kezdőlapja – Azure AD Application Proxy
description: Ismerteti az alapvető tudnivalók az Azure AD-alkalmazásproxy-összekötők
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275597"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy használatával állítsa be a közzétett alkalmazások egy egyéni kezdőlapja

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy alkalmazást egy felhasználó egyéni kezdőlapra való irányításához. Ha alkalmazásproxy-alkalmazással tesz közzé egy alkalmazást, belső URL-címet kell beállítania, de néha ez nem az a lap, amelyet a felhasználónak először látnia kell. Állítson be egy egyéni kezdőlapot, hogy a felhasználó megkapja a jobb oldalt, amikor hozzáférnek az alkalmazáshoz. A felhasználó megtekintheti a beállított egyéni kezdőlapot, függetlenül attól, hogy az alkalmazást a Azure Active Directory hozzáférési paneljéről vagy az Office 365 app launcherről érik-e el.

Amikor egy felhasználó elindítja az alkalmazást, a rendszer alapértelmezés szerint a közzétett alkalmazás gyökértartomány URL-címére irányítja. A kezdőlap általában a kezdőlap URL-címe van beállítva. Az Azure AD PowerShell-modullal definiálhat egy egyéni Kezdőlap URL-címet, ha azt szeretné, hogy egy alkalmazás egy adott oldalon az alkalmazáson belül egy adott lapra szálljon.

Íme egy forgatókönyv, amely leírja, hogy a vállalat miért állította be az egyéni kezdőlapot:

- A vállalati hálózaton belül egy felhasználó `https://ExpenseApp/login/login.aspx` a bejelentkezéshez és az alkalmazáshoz való hozzáféréshez.
- Mivel más eszközökkel (például képekkel) kell hozzáférnie az alkalmazásproxy legfelső szintjén, az alkalmazást `https://ExpenseApp` belső URL-címként teheti közzé.
- Az alapértelmezett külső URL-cím `https://ExpenseApp-contoso.msappproxy.net`, amely nem végez külső felhasználót a bejelentkezési oldalon.
- Ehelyett a Kezdőlap URL-címét szeretné beállítani `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`ként, így a külső felhasználó először a bejelentkezési oldalt látja.

> [!NOTE]
> Amikor hozzáférést ad a felhasználóknak a közzétett alkalmazásokhoz, az alkalmazások megjelennek az [Azure ad hozzáférési paneljén](../user-help/my-apps-portal-end-user-access.md) és az [Office 365 app launcherben](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt beállítaná a kezdőlap URL-címe, vegye figyelembe az alábbi követelményeknek:

- A megadott elérési útnak a gyökértartomány URL-címéhez tartozó altartomány elérési útnak kell lennie.

  Ha például a gyökér-tartomány URL-címe `https://apps.contoso.com/app1/`, akkor a konfigurált Kezdőlap URL-címének a következővel kell kezdődnie: `https://apps.contoso.com/app1/`.

- Ha módosítja a közzétett alkalmazáshoz, a módosítás a kezdőlap URL-Címének értékét előfordulhat, hogy alaphelyzetbe. Ha a jövőben frissíti az alkalmazást, akkor kell újbóli ellenőrzése ennyi idő, és ha szükséges, frissítse a kezdőlap URL-címe.

A Kezdőlap URL-címét a Azure Portal vagy a PowerShell használatával is beállíthatja.

## <a name="change-the-home-page-in-the-azure-portal"></a>Az Azure Portalon a kezdőlap módosítása

Ha módosítani szeretné az alkalmazás kezdőlapjának URL-címét az Azure AD-portálon, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/) felületére rendszergazdaként.
1. Válassza a **Azure Active Directory**, majd a **Alkalmazásregisztrációk**lehetőséget. Megjelenik a regisztrált alkalmazások listája.
1. Válassza ki az alkalmazást a listából. Megjelenik a regisztrált alkalmazás részleteit megjelenítő oldal.
1. A **kezelés**területen válassza a **branding (védjegyezés**) lehetőséget.
1. Frissítse a **Kezdőlap URL-címét** az új elérési úttal.

   ![A Kezdőlap URL-címét tartalmazó regisztrált alkalmazás védjegyezési lapja](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Kattintson a **Mentés** gombra.

## <a name="change-the-home-page-with-powershell"></a>Módosítsa a kezdőlapon a PowerShell-lel

Egy alkalmazás kezdőlapjának a PowerShell használatával történő konfigurálásához a következőket kell tennie:

1. Telepítse az Azure AD PowerShell-modult.
1. Az alkalmazás ObjectId-értékének megkeresése.
1. Frissítse az alkalmazás kezdőlapjának URL-címét a PowerShell-parancsokkal.

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

PowerShell-lel megadhat egy egyéni kezdőlap URL-címe, előtt telepítse az Azure AD PowerShell-modul. A csomagot letöltheti a [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), amely a Graph API végpontot használja.

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy standard szintű PowerShell-ablakot, és futtassa a következő parancsot:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Ha nem rendszergazdaként futtatja a parancsot, használja a `-scope currentuser` kapcsolót.

1. A telepítés során a Nuget.org két csomag telepítéséhez válassza az **Y** lehetőséget. Mindkét csomagot kötelező megadni.

### <a name="find-the-objectid-of-the-app"></a>Az alkalmazás ObjectId megkeresése

Az alkalmazás ObjectId úgy érheti el, hogy a megjelenített név vagy a Kezdőlap alapján keresi az alkalmazást.

1. Ugyanebben a PowerShell ablakban az Azure AD-modul importálásához.

   ```powershell
   Import-Module AzureAD
   ```

1. Jelentkezzen be az Azure AD-modul a bérlői rendszergazda.

   ```powershell
   Connect-AzureAD
   ```

1. Keresse meg az alkalmazást. Ez a példa a PowerShell használatával keresi meg a ObjectId, ha az alkalmazást `SharePoint`megjelenített névvel keresi.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Az itt látható egy hasonló eredményt kell kapnia. Másolja a ObjectId GUID azonosítót a következő szakaszban használt értékre.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Azt is megteheti, hogy lekéri az összes alkalmazás listáját, megkeresi az alkalmazás listáját egy adott megjelenítendő névvel vagy kezdőlaptal, majd az alkalmazás ObjectId másolja az alkalmazást.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>A kezdőlap URL-Címének frissítése

Hozza létre a Kezdőlap URL-címét, és frissítse az alkalmazást az adott értékkel. Folytassa ugyanazt a PowerShell-ablakot, vagy ha új PowerShell-ablakot használ, jelentkezzen be ismét az Azure AD-modulba `Connect-AzureAD`használatával. Ezután kövesse az alábbi lépéseket:

1. Hozzon létre egy változót, amely az előző szakaszban másolt ObjectId értéket fogja tárolni. (Cserélje le az ebben a SharePoint-példában használt ObjectId-értéket az alkalmazás ObjectId értékével.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. A következő parancs futtatásával ellenőrizze, hogy rendelkezik-e a megfelelő alkalmazással. A kimenetnek azonosnak kell lennie az előző szakaszban látott kimenettel ([Keresse meg az alkalmazás ObjectId](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Hozzon létre egy üres alkalmazás-objektumot a kívánt módosításokat, győződjön meg arról, hogy tartsa.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. A kezdőlap URL-Címének beállítása a kívánt értéket. Az értéknek kell lennie a közzétett alkalmazás altartomány mutató elérési utat. Ha például a Kezdőlap URL-címét `https://sharepoint-iddemo.msappproxy.net/`ról `https://sharepoint-iddemo.msappproxy.net/hybrid/`re módosítja, az alkalmazás felhasználói közvetlenül az egyéni kezdőlapra kerülnek.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. A Kezdőlap frissítésének elkészítése.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. A módosítás sikerességének ellenőrzéséhez futtassa újra a 2. lépésből a következő parancsot.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Példánkban a kimenetnek ekkor a következőképpen kell megjelennie:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Indítsa újra az alkalmazást annak megerősítéséhez, hogy a Kezdőlap az elvárt módon jelenik meg az első képernyőn.

> [!NOTE]
> Módosításokat hajt végre az alkalmazás előfordulhat, hogy állítsa alaphelyzetbe a kezdőlap URL-címe. Ha alaphelyzetbe állítja a kezdőlap URL-címe, ismételje meg a állítja vissza a jelen szakaszban.

## <a name="next-steps"></a>További lépések

- [Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD Application Proxy](application-proxy-integrate-with-sharepoint-server.md)
- [Oktatóanyag: helyi alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory](application-proxy-add-on-premises-application.md)