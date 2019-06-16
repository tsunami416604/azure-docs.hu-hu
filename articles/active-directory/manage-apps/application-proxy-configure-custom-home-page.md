---
title: Az Azure AD-alkalmazásproxy használatával állítsa be a közzétett alkalmazások egy egyéni kezdőlapja |} A Microsoft Docs
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
ms.openlocfilehash: 0f4e71bd7fd7e0ed9a220619995ba108fdccabe4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66233747"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy használatával állítsa be a közzétett alkalmazások egy egyéni kezdőlapja

Ez a cikk ismerteti, hogyan konfigurálhat egy alkalmazást arra, hogy egy felhasználó egy egyéni kezdőlapjára irányítja. Amikor közzétesz egy alkalmazást a proxyval, beállíthat egy belső URL-cím, de egyes esetekben ez nem az oldal a felhasználó először kell megjelennie. Állítsa be egy egyéni kezdőlap, úgy, hogy egy felhasználó lekéri a jobb oldalon, ha szeretne hozzáférni az alkalmazáshoz. A felhasználó az egyéni kezdőlap jelenik meg, hogy beállította, függetlenül attól, hogy férnek hozzá az alkalmazást az Azure Active Directory hozzáférési paneljére vagy az Office 365 appindítóban.

Amikor egy felhasználó elindítja az alkalmazást, azokat a legfelső szintű tartomány URL-címet a közzétett alkalmazás alapértelmezés szerint van irányítva. A kezdőlap általában a kezdőlap URL-címe van beállítva. Az Azure AD PowerShell-modul segítségével határozza meg egy egyéni kezdőlap URL-címe, ha azt szeretné, hogy egy alkalmazás felhasználói kerül az alkalmazáson belül egy adott oldalon.

Íme egy forgatókönyvet, amely azt ismerteti, miért érdemes a vállalat állíthatja be egy egyéni kezdőlap:

- A vállalati hálózaton belüli egy felhasználó megnyitja a `https://ExpenseApp/login/login.aspx` jelentkezik be, és az alkalmazás eléréséhez.
- Egyéb eszközök (például képek), amelynek a legfelső szinten, a gyökérmappa-szerkezetében hozzá kell Application Proxy van, mert az alkalmazást közzéteszi `https://ExpenseApp` , a belső URL-cím.
- A külső URL-címét `https://ExpenseApp-contoso.msappproxy.net`, amely nem használ egy külső felhasználó a bejelentkezési lapot.
- Szeretné állítani, `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` kezdőlap URL-címként, így egy külső felhasználó látja a bejelentkezési oldal először.

>[!NOTE]
>Amikor engedélyezi a felhasználók elérését a közzétett alkalmazásokat, az alkalmazások megjelennek a [Azure AD hozzáférési Panel](../user-help/my-apps-portal-end-user-access.md) és a [Office 365 appindítóban](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Előkészületek

Mielőtt beállítaná a kezdőlap URL-címe, vegye figyelembe az alábbi követelményeknek:

- Az Ön által megadott elérési út egy altartomány elérési útját a legfelső szintű tartomány URL-címének kell lennie.

  Például, ha a legfelső szintű tartományi URL-cím `https://apps.contoso.com/app1/`, a kezdőlap URL-címe, konfigurálnia kell kezdődnie `https://apps.contoso.com/app1/`.

- Ha módosítja a közzétett alkalmazáshoz, a módosítás a kezdőlap URL-Címének értékét előfordulhat, hogy alaphelyzetbe. Ha a jövőben frissíti az alkalmazást, akkor kell újbóli ellenőrzése ennyi idő, és ha szükséges, frissítse a kezdőlap URL-címe.

A kezdőlap URL-címe, az Azure Portalon keresztül, vagy a PowerShell használatával is megadhatja.

## <a name="change-the-home-page-in-the-azure-portal"></a>Az Azure Portalon a kezdőlap módosítása

Ha módosítani szeretné a kezdőlap URL-címe az alkalmazás az Azure AD portálon keresztül, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/) felületére rendszergazdaként.
2. Válassza ki **Azure Active Directory**, majd **alkalmazásregisztrációk**. Megjelenik a regisztrált alkalmazások listája.
3. Válassza ki az alkalmazást a listából. A regisztrált alkalmazás részleteit megjelenítő lap jelenik meg.
4. A **kezelés**válassza **Branding**.
5. Frissítés a **kezdőlap URL-címe** az új elérési úttal.

   ![A kezdőlap URL-címe mező megjelenítése egy regisztrált alkalmazás márkajelzési lapját](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)
 
6. Kattintson a **Mentés** gombra.

## <a name="change-the-home-page-with-powershell"></a>Módosítsa a kezdőlapon a PowerShell-lel

A kezdőlap PowerShell-lel az alkalmazás konfigurálásához kell tennie:

1. Az Azure AD PowerShell modul telepítése.
2. Az alkalmazás ObjectId értékének megkeresése
3. Frissítse az alkalmazás kezdőlap URL-címe PowerShell-parancsokkal.

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

PowerShell-lel megadhat egy egyéni kezdőlap URL-címe, előtt telepítse az Azure AD PowerShell-modul. Letöltheti a csomagot a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), amely használja, a Graph API-végpont.

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy standard szintű PowerShell-ablakot, és futtassa a következő parancsot:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Ha a parancsot futtatja, nem rendszergazda, a `-scope currentuser` lehetőséget.

2. A telepítés során válasszon **Y** Nuget.org a két csomagok telepítéséhez. Mindkét csomagot szükség.

### <a name="find-the-objectid-of-the-app"></a>Az ObjectId azonosítóját, az alkalmazás keresése

Keresés az alkalmazás által megjelenített név vagy kezdőlapja kap az ObjectId azonosítóját, az alkalmazást.

1. Ugyanebben a PowerShell ablakban az Azure AD-modul importálásához.

   ```powershell
   Import-Module AzureAD
   ```

2. Jelentkezzen be az Azure AD-modul a bérlői rendszergazda.

   ```powershell
   Connect-AzureAD
   ```

3. Keresse meg az alkalmazást. Ez a példa Powershellt használja az alkalmazás megjelenítendő neve és keressen rá az ObjectId található `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Az itt látható egy hasonló eredményt kell kapnia. Másolja a következő szakaszban az ObjectId GUID Azonosítót.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   Azt is megteheti, sikerült csak kérje le az összes alkalmazás listájára, az alkalmazás egy adott megjelenítési névvel vagy kezdőlapja a listában, és másolja az alkalmazás ObjectId után az alkalmazás található.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>A kezdőlap URL-Címének frissítése

Hozzon létre a kezdőlap URL-címe, és az alkalmazást frissíti az értéket. Ugyanebben a PowerShell ablakban használatának folytatásához, vagy ha egy új PowerShell-ablakot használja, jelentkezzen be az Azure AD-modul segítségével újból `Connect-AzureAD`. Ezután kövesse az alábbi lépéseket:

1. Hozzon létre egy változót az előző szakaszban kimásolt érték ObjectId tárolásához. (Cserélje le az alkalmazás ObjectId értékkel rendelkező SharePoint példában használt érték ObjectId.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

2. Győződjön meg arról, hogy rendelkezik-e a megfelelő alkalmazást a következő parancs futtatásával. A kimenet meg kell egyezniük a kimenet az előző szakaszban látott ([objectid azonosítóját, az alkalmazás keresése](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

3. Hozzon létre egy üres alkalmazás-objektumot a kívánt módosításokat, győződjön meg arról, hogy tartsa.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

4. A kezdőlap URL-Címének beállítása a kívánt értéket. Az értéknek kell lennie a közzétett alkalmazás altartomány mutató elérési utat. Például, ha módosítja a kezdőlap URL-címe a `https://sharepoint-iddemo.msappproxy.net/` való `https://sharepoint-iddemo.msappproxy.net/hybrid/`,. alkalmazás felhasználóinak lépjen közvetlenül az egyéni kezdőlapja.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

5. Győződjön meg a frissítést, a kezdőlap.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

6. Annak ellenőrzéséhez, hogy sikeres volt-e a módosítást, a következő parancsot a 2. lépés újra.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   A jelen példában a kimeneti meg kell jelennie a következő:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

7. Indítsa újra az alkalmazást, meggyőződhet arról, hogy a kezdőlapon az első képernyőt elvárt módon.

>[!NOTE]
>Módosításokat hajt végre az alkalmazás előfordulhat, hogy állítsa alaphelyzetbe a kezdőlap URL-címe. Ha alaphelyzetbe állítja a kezdőlap URL-címe, ismételje meg a állítja vissza a jelen szakaszban.

## <a name="next-steps"></a>További lépések

- [Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](application-proxy-add-on-premises-application.md)