---
title: Az Azure AD-alkalmazásproxy használatával állítsa be a közzétett alkalmazások egy egyéni kezdőlapja |} A Microsoft Docs
description: Ismerteti az alapvető tudnivalók az Azure AD-alkalmazásproxy-összekötők
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: ecdf0f8f5fd15096dc6bd00461aaa78a14672a04
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472619"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy használatával állítsa be a közzétett alkalmazások egy egyéni kezdőlapja

Ez a cikk ismerteti az alkalmazások számára a felhasználók számára egy egyéni kezdőlap konfigurálása. Amikor közzétesz egy alkalmazást a proxyval, beállíthat egy belső URL-cím azonban néha, amely nem a lapon, a felhasználók először kell megjelennie. Állíthat be egy egyéni kezdőlap, így a felhasználók nyissa meg a jobb oldalon az alkalmazások elérésekor. A felhasználók e férnek hozzá az alkalmazást az Azure Active Directory hozzáférési paneljére vagy az Office 365 appindítóban beállított egyéni kezdőlap jelenik meg.

Amikor a felhasználók indítsa el az alkalmazást, azokat a legfelső szintű tartomány URL-címe, a közzétett alkalmazás alapértelmezés szerint a van irányítva. A kezdőlap általában a kezdőlap URL-címe van beállítva. Az Azure AD PowerShell-modul segítségével egyéni kezdőlap URL-címek megadása, ha azt szeretné, hogy a felhasználók számára a megjelenni az alkalmazásban egy adott oldalra. 

Ezért egy vállalat állíthatja be egy egyéni kezdőlap, Íme egy példa:
- A vállalati hálózaton belüli felhasználók Ugrás *https://ExpenseApp/login/login.aspx* jelentkezik be, és az alkalmazás eléréséhez.
- Más tartalmaktól, például képek, amelynek a legfelső szinten, a gyökérmappa-szerkezetében hozzá kell Application Proxy van, mert az alkalmazást közzéteszi *https://ExpenseApp* , a belső URL-cím.
- A külső URL-címét *https://ExpenseApp-contoso.msappproxy.net*, amely nem használ a felhasználók a bejelentkezési lapot.  
- Állítsa be *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx* kezdőlap URL-címként. 

>[!NOTE]
>Amikor engedélyezi a felhasználók elérését a közzétett alkalmazásokat, az alkalmazások megjelennek a [Azure AD hozzáférési Panel](../user-help/active-directory-saas-access-panel-introduction.md) és a [Office 365 appindítóban](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher).

## <a name="before-you-start"></a>Előkészületek

Mielőtt beállítaná a kezdőlap URL-címe, vegye figyelembe az alábbi követelményeknek:

* Győződjön meg arról, hogy a megadott elérési út egy altartomány elérési útját a legfelső szintű tartomány URL-címe.

  Ha a legfelső szintű tartományi URL-CÍMÉT, például https://apps.contoso.com/app1/, a kezdőlap URL-címe, konfigurálnia kell kezdődnie https://apps.contoso.com/app1/.

* Ha módosítja a közzétett alkalmazáshoz, a módosítás a kezdőlap URL-Címének értékét előfordulhat, hogy alaphelyzetbe. Ha a jövőben frissíti az alkalmazást, akkor kell újbóli ellenőrzése ennyi idő, és ha szükséges, frissítse a kezdőlap URL-címe.

## <a name="change-the-home-page-in-the-azure-portal"></a>Az Azure Portalon a kezdőlap módosítása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Navigáljon a **Azure Active Directory** > **alkalmazásregisztrációk** , és válassza ki az alkalmazást a listából. 
3. Válassza ki **tulajdonságok** beállításokkal.
4. Frissítés a **kezdőlap URL-címe** az új elérési út mezőben. 

   ![Adja meg az új kezdőlap URL-címe](./media/application-proxy-configure-custom-home-page/homepage.png)

5. Válassza ki **mentése**

## <a name="change-the-home-page-with-powershell"></a>Módosítsa a kezdőlapon a PowerShell-lel

### <a name="install-the-azure-ad-powershell-module"></a>Az Azure AD PowerShell modul telepítése

PowerShell-lel megadhat egy egyéni kezdőlap URL-címe, előtt telepítse az Azure AD PowerShell-modul. Letöltheti a csomagot a [PowerShell-galériából](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131), amely használja, a Graph API-végpont. 

A csomag telepítéséhez kövesse az alábbi lépéseket:

1. Nyisson meg egy standard szintű PowerShell-ablakot, és futtassa a következő parancsot:

    ```
     Install-Module -Name AzureAD
    ```
    Ha a parancsot futtatja, nem rendszergazda, a `-scope currentuser` lehetőséget.
2. A telepítés során válasszon **Y** Nuget.org a két csomagok telepítéséhez. Mindkét csomagot szükség. 

### <a name="find-the-objectid-of-the-app"></a>Az ObjectId azonosítóját, az alkalmazás keresése

Szerezze be az ObjectId azonosítóját, az alkalmazást, és keressen rá az alkalmazás által a kezdőlapon.

1. Ugyanebben a PowerShell ablakban az Azure AD-modul importálásához.

    ```
    Import-Module AzureAD
    ```

2. Jelentkezzen be az Azure AD-modul a bérlői rendszergazda.

    ```
    Connect-AzureAD
    ```
3. Keresse meg az alkalmazást, a kezdőlap URL-címe alapján. Annak az URL-címet a portálon a **Azure Active Directory** > **vállalati alkalmazások** > **minden alkalmazás**. Ez a példa *sharepoint-iddemo*.

    ```
    Get-AzureADApplication | where { $_.Homepage -like "sharepoint-iddemo" } | fl DisplayName, Homepage, ObjectID
    ```
4. Az itt látható egy hasonló eredményt kell kapnia. Másolja a következő szakaszban az ObjectID GUID Azonosítót.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>A kezdőlap URL-Címének frissítése

A kezdőlap URL-cím létrehozása, és frissítse az alkalmazás ezt az értéket. Továbbra is az ugyanebben a PowerShell-ablakban, futtassa a következő parancsokat. Vagy, ha egy új PowerShell-ablakot használja, jelentkezzen be az Azure AD-modul segítségével újból `Connect-AzureAD`. 

1. Győződjön meg arról, hogy a megfelelő alkalmazáshoz, és cserélje le *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4* az az előző szakaszban kimásolt objektumazonosító.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 Most, hogy az alkalmazást jóváhagyta, készen áll a kezdőlap módon frissíteni.

2. Hozzon létre egy üres alkalmazás-objektumot a kívánt módosításokat, győződjön meg arról, hogy tartsa. Ez a változó tárolja a frissíteni kívánt értékeket. Semmi nem jön létre ebben a lépésben.

    ```
    $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
    ```

3. A kezdőlap URL-Címének beállítása a kívánt értéket. Az értéknek kell lennie a közzétett alkalmazás altartomány mutató elérési utat. Például, ha módosítja a kezdőlap URL-címe a `https://sharepoint-iddemo.msappproxy.net/` való `https://sharepoint-iddemo.msappproxy.net/hybrid/`,. alkalmazás felhasználóinak lépjen közvetlenül az egyéni kezdőlapja.

    ```
    $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
    ```
4. A frissítés, amely a másolt GUID (ObjectID) használatával győződjön meg arról, "1. lépés: Az ObjectId azonosítóját, az alkalmazás található."

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. Győződjön meg arról, hogy sikeres volt-e a módosítást, indítsa újra az alkalmazást.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>Módosításokat hajt végre az alkalmazás előfordulhat, hogy állítsa alaphelyzetbe a kezdőlap URL-címe. Ha alaphelyzetbe állítja a kezdőlap URL-címe, ismételje meg a állítja vissza a jelen szakaszban.

## <a name="next-steps"></a>További lépések

- [Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Alkalmazásproxy engedélyezése az Azure Portalon](application-proxy-add-on-premises-application.md)
