---
title: "Közzétett alkalmazások egyéni kezdőlapját beállítása az Azure AD-alkalmazásproxy használatával |} Microsoft Docs"
description: "Ismerteti az alapvető tudnivalók az Azure AD-alkalmazásproxy-összekötő"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 811adc81424b8e53a740ec34f77a7610fc2a72a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Közzétett alkalmazások egyéni kezdőlapját beállítása az Azure AD-alkalmazásproxy használatával

A cikk ismerteti a felhasználók számára egy egyéni kezdőlap közvetlen alkalmazások konfigurálása. Amikor közzétesz egy alkalmazást az alkalmazásproxy, be kell állítani egy belső URL-Címeket de időnként nem a lapon, a felhasználók először kell megjelennie. Egy egyéni kezdőlap beállításáról, hogy a felhasználók Ugrás a jobb oldalon, amikor azok az alkalmazások eléréséhez. A felhasználók látni fogják az Ön által meghatározott egyéni kezdőlap, hogy amikor az alkalmazás férnek hozzá az Azure Active Directory-hozzáférési panelre vagy az Office 365 alkalmazás indító.

Amikor a felhasználók indítsa el az alkalmazást, azokat a legfelső szintű tartomány URL-címet a közzétett alkalmazás alapértelmezés szerint van irányítva. Általában be van állítva az kezdőlapja a kezdőlap URL-CÍMÉT. Az Azure AD PowerShell modul segítségével egyéni kezdőlap URL-címek megadása, ha azt szeretné, hogy a felhasználók számára az alkalmazáson belül egy adott lap meg. 

Itt csak egy példa, ezért egy vállalat kell állítania egy egyéni kezdőlap:
- A vállalati hálózaton belüli felhasználók Ugrás *https://ExpenseApp/login/login.aspx* jelentkezzen be, és az alkalmazás eléréséhez.
- Mivel más eszközök, például alkalmazásproxy a mappaszerkezet legfelső szintjén hozzáférésre van szüksége a lemezképeket, tesz közzé az alkalmazást *https://ExpenseApp* az belső URL-címet.
- Az alapértelmezett külső URL-címe *https://ExpenseApp-contoso.msappproxy.net*, amely nem veszi a felhasználókat, hogy a bejelentkezési oldal.  
- Állítsa be *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* az kezdőlap URL-címet. 

>[!NOTE]
>Amikor a felhasználók hozzáférést adhat közzétett alkalmazások, az alkalmazások jelennek meg a [Azure AD hozzáférési Panel](active-directory-saas-access-panel-introduction.md) és a [Office 365 alkalmazás indító](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Előkészületek

Mielőtt a kezdőlap URL-Címének beállításához vegye figyelembe az alábbi követelményeknek:

* Győződjön meg arról, hogy a megadott elérési út egy altartomány elérési útját a legfelső szintű tartomány URL-CÍMÉT.

  Ha a legfelső szintű tartományi URL-CÍMÉT, például https://apps.contoso.com/app1/, a kezdőlap URL-címet, konfigurálnia kell kezdődnie https://apps.contoso.com/app1/.

* Ha módosítja a közzétett alkalmazáshoz, a módosítás lehet, hogy állítsa vissza a kezdőlap URL-címet. Ha a jövőben frissíti az alkalmazás, inkább újbóli ellenőrzéséhez és, ha szükséges, frissítse a kezdőlap URL-címe.

## <a name="change-the-home-page-in-the-azure-portal"></a>Módosítsa a kezdőlap, az Azure-portálon

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Navigáljon a **Azure Active Directory** > **App regisztrációk** , és válassza ki az alkalmazást a listából. 
3. Válassza ki **tulajdonságok** beállításai közül.
4. Frissítés a **Kezdőlap** mező az új elérési úttal. 

   ![Új kezdőlap URL-Címének megadása](./media/application-proxy-office365-app-launcher/homepage.png)

5. Válassza ki **mentése**

## <a name="change-the-home-page-with-powershell"></a>Módosítsa a kezdőlap a PowerShell használatával

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

Mielőtt egy egyéni kezdőlap URL-Címének megadása PowerShell segítségével, telepítse az Azure AD PowerShell modult. A csomagot a programot letöltheti a [PowerShell-galériában](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), amely használja, a Graph API-végpont. 

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a standard PowerShell-ablakot, és futtassa a következő parancsot:

    ```
     Install-Module -Name AzureAD
    ```
    Ha a parancs fut, a nem rendszergazda, használja a `-scope currentuser` lehetőséget.
2. Válassza ki a telepítés során **Y** Nuget.org két csomagok telepítését. Mindkét csomagot szükség. 

### <a name="find-the-objectid-of-the-app"></a>Az alkalmazás ObjectID keresése

Szerezze be az alkalmazás ObjectID, majd keresse meg az alkalmazás által a kezdőlap.

1. Az ugyanazon PowerShell-ablakban importálja az Azure AD-modullal.

    ```
    Import-Module AzureAD
    ```

2. Jelentkezzen be az Azure AD-modullal a bérlői rendszergazda felhasználóként.

    ```
    Connect-AzureAD
    ```
3. Megkeresheti az alkalmazást, a kezdőlap URL-címe alapján. Az URL-címet az található a portál a **Azure Active Directory** > **vállalati alkalmazások** > **összes alkalmazás**. Ez a példa *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. Az itt látható egy hasonló eredményt kapja meg. Másolja a ObjectID globálisan egyedi Azonosítót a következő szakaszban található.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>A kezdőlap URL-Címének frissítése

A kezdőlap URL-cím létrehozása, és frissítse az alkalmazás ezt az értéket. Továbbra is, az ugyanazon PowerShell-ablak segítségével futtassa a következő parancsokat. Vagy, ha egy új PowerShell-ablakot használ, jelentkezzen be újra az Azure AD-modullal `Connect-AzureAD`. 

1. Győződjön meg arról, hogy rendelkezik a megfelelő alkalmazást, és cserélje le *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* az objektumazonosító, amelyet az előző szakaszban leírt vágólapra másolt.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Most, hogy az alkalmazás megerősítését készen áll a kezdőlap, az alábbiak szerint frissítse.

2. Hozzon létre egy üres alkalmazásobjektum ahhoz, hogy engedélyezni szeretné a módosításokat. Ez a változó a frissíteni kívánt értékeket tartalmazza. Ezt a lépést nem jön létre.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. A kezdőlap URL-Címének beállítása a kívánt értékre. Az érték lehet egy altartomány elérési utat a közzétett alkalmazás. Például, ha módosítja a kezdőlap URL-CÍMÉT *https://sharepoint-iddemo.msappproxy.net/* való *https://sharepoint-iddemo.msappproxy.net/hybrid/*, az alkalmazásaik felhasználóit közvetlenül Ugrás az egyéni webhelyre.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. A frissítéshez a GUID azonosítója (ObjectID) használatával "1. lépés: az alkalmazás ObjectID található."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Győződjön meg arról, hogy sikeres volt-e a módosítást, indítsa újra az alkalmazást.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Az alkalmazás végzett módosításokat előfordulhat, hogy alaphelyzetbe állítja a kezdőlap URL-CÍMÉT. Ha alaphelyzetbe állítja a kezdőlap URL-CÍMÉT, ismételje meg az ebben a szakaszban állítja vissza.

## <a name="next-steps"></a>Következő lépések

- [Az Azure AD alkalmazásproxy SharePoint távoli hozzáférés engedélyezése](application-proxy-enable-remote-access-sharepoint.md)
- [Alkalmazásproxy engedélyezése az Azure-portálon](active-directory-application-proxy-enable.md)
