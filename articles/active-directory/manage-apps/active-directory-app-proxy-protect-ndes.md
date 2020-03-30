---
title: Integráció Az AD alkalmazásproxyval NDES-kiszolgálón
titleSuffix: Azure Active Directory
description: Útmutató az NDES-kiszolgáló védelmére szolgáló Azure Active Directory alkalmazásproxy üzembe helyezéséhez.
services: active-directory
author: CelesteDG
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2020
ms.author: baselden
ms.reviewer: mimart
ms.openlocfilehash: 4ccd8834671725ace72497391090f81eb197ad6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77032256"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrálás az Azure AD alkalmazásproxyval hálózati eszközigénylési szolgáltatás (NDES) kiszolgálón

Az Azure Active Directory (AD) alkalmazásproxy lehetővé teszi, hogy alkalmazásokat tegyen közzé a hálózaton belül. Ezek az alkalmazások olyanok, mint a SharePoint-webhelyek, a Microsoft Outlook Web App és más webes alkalmazások. Emellett biztonságos hozzáférést biztosít a hálózaton kívüli felhasználók számára az Azure-on keresztül.

Ha most ismerkedik az Azure AD alkalmazásproxyval, és szeretne többet megtudni, olvassa el [a távoli hozzáférés a helyszíni alkalmazások hoz az Azure AD alkalmazásproxy n keresztül.](application-proxy.md)

Az Azure AD alkalmazásproxy az Azure-ra épül. Ez ad ön egy hatalmas mennyiségű hálózati sávszélesség és a szerver infrastruktúra jobb védelmet nyújt az elosztott denial-of-service (DDOS) támadások és kiváló rendelkezésre állás. Továbbá nincs szükség külső tűzfalportok megnyitására a helyszíni hálózatra, és nincs szükség DMZ-kiszolgálóra. Minden forgalom bejövő. A kimenő portok teljes listáját az [Oktatóanyag: Egy helyszíni alkalmazás hozzáadása az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréséhez című témakörben tartalmazza.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)

> Az Azure AD alkalmazásproxy olyan szolgáltatás, amely csak akkor érhető el, ha az Azure Active Directory Prémium vagy Basic kiadásait használja. További információ: [Azure Active Directory díjszabása](https://azure.microsoft.com/pricing/details/active-directory/). 
> Ha nagyvállalati mobilitási csomag (EMS) licenccel rendelkezik, jogosult a megoldás használatára.
> Az Azure AD alkalmazásproxy-összekötő csak Windows Server 2012 R2 vagy újabb rendszeren települ. Ez az NDES-kiszolgáló követelménye is.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Az összekötő telepítése és regisztrálása az NDES-kiszolgálón

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) az alkalmazásproxyt használó könyvtár alkalmazás-rendszergazdájaként. Ha például a bérlői tartomány contoso.com, admin@contoso.com a rendszergazda kell, vagy bármely más rendszergazdai alias az adott tartományban.
1. Válassza ki a felhasználónevét a jobb felső sarokban. Ellenőrizze, hogy be van-e jelentkezve egy alkalmazásproxyt használó könyvtárba. Ha könyvtárakat kell módosítania, válassza a **Könyvtárváltás** lehetőséget, és válasszon egy alkalmazásproxyt használó könyvtárat.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
1. A **Kezelés csoportban**válassza **az Alkalmazásproxy lehetőséget.**
1. Válassza **az Összekötő szolgáltatás letöltése lehetőséget.**

    ![Az összekötő szolgáltatás letöltése a Szolgáltatási feltételek megtekintéséhez](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Olvassa el a Szolgáltatási feltételeket. Ha készen áll, válassza **a Feltételek elfogadása & letöltése**lehetőséget.
1. Másolja az Azure AD alkalmazásproxy-összekötő telepítőfájlját az NDES-kiszolgálóra. 
   > Az összekötőt a vállalati hálózat bármely kiszolgálójára telepítheti az NDES-hez való hozzáféréssel. Nem kell telepítenie magát az NDES-kiszolgálóra.
1. Futtassa a telepítőfájlt, például *az AADApplicationProxyConnectorInstaller.exe*fájlt. Fogadja el a szoftverlicenc-feltételeket.
1. A telepítés során a rendszer kéri, hogy regisztrálja az összekötőt az alkalmazásproxy val az Azure AD könyvtárban.
   * Adja meg a hitelesítő adatokat egy globális vagy alkalmazás-rendszergazda az Azure AD-címtárban. Az Azure AD globális vagy alkalmazás-rendszergazdai hitelesítő adatok eltérhetnek az Azure-hitelesítő adatok a portálon.

        > [!NOTE]
        > Az összekötő regisztrálásához használt globális vagy alkalmazás-rendszergazdai fióknak ugyanahhoz a könyvtárhoz kell tartoznia, ahol engedélyezi az Alkalmazásproxy szolgáltatást.
        >
        > Ha például az Azure AD-tartomány *contoso.com,* a `admin@contoso.com` globális/alkalmazás rendszergazdájának kell lennie, vagy egy másik érvényes aliasaz adott tartományban.

   * Ha az Internet Explorer fokozott biztonsági beállításai be vannak kapcsolva azon a kiszolgálón, amelyen az összekötőt telepíti, előfordulhat, hogy a regisztrációs képernyő le van tiltva. A hozzáférés engedélyezéséhez kövesse a hibaüzenet utasításait, vagy kapcsolja ki az Internet Explorer fokozott biztonságát a telepítési folyamat során.
   * Ha az összekötő regisztrációja nem sikerül, olvassa el az [Alkalmazásproxy hibaelhárítása című témakört.](application-proxy-troubleshoot.md)
1. A telepítés végén egy megjegyzés jelenik meg a kimenő proxyval rendelkező környezetekben. Ha az Azure AD alkalmazásproxy-összekötőt a kimenő proxyn keresztül `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`szeretné konfigurálni, futtassa a megadott parancsfájlt, például a.
1. Az Azure Portal alkalmazásproxy-lapján az új összekötő *aktív*állapotú, a következő példában látható módon jelenik meg:

    ![Az Azure Portalon aktívként megjelenített új Azure AD alkalmazásproxy-összekötő](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Az Azure AD alkalmazásproxyn keresztül hitelesítő alkalmazások magas rendelkezésre állásának biztosítása érdekében több virtuális gépre is telepíthet összekötőket. Ismételje meg az előző szakaszban felsorolt lépéseket az összekötő telepítéséhez az Azure AD DS felügyelt tartományhoz csatlakozott más kiszolgálókra.

1. Sikeres telepítés után lépjen vissza az Azure Portalra.

1. Válassza **a Vállalati alkalmazások lehetőséget**.

   ![győződjön meg arról, hogy a megfelelő érdekelt feleket vonja be](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Válassza a **+Új alkalmazás**lehetőséget, majd **a Helyszíni alkalmazás**lehetőséget. 

1. A **Saját helyszíni alkalmazás hozzáadása**területen konfigurálja a következő mezőket:

   * **Név**: Adja meg az alkalmazás nevét.
   * **Belső URL**: Adja meg annak az NDES-kiszolgálónak a belső URL-címét/teljes tartománynát, amelyre az összekötőt telepítette.
   * **Előhitelesítés:** Válassza **az Áthaladás**lehetőséget. Az előhitelesítés semmilyen formája nem használható. A tanúsítványkérelmekhez (SCEP) használt protokoll nem biztosít ilyen beállítást.
   * Másolja a vágólapra a megadott **külső URL-címet.**

1. Az alkalmazás mentéséhez válassza a **+Hozzáadás** lehetőséget.

1. Tesztelje, hogy az NDES-kiszolgáló az Azure AD alkalmazás proxyn keresztül érhető-e el, ha beilleszti a 10. Meg kell jelennie egy alapértelmezett IIS üdvözlőlapnak.

1. Végső tesztként adja hozzá az *mscep.dll* elérési útját az előző lépésben beillesztett meglévő URL-címhez:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Meg kell jelennie egy **HTTP-hiba 403 – Tiltott** válasz.

1. Módosítsa a (Microsoft Intune-on keresztül) megadott NDES-URL-címet eszközökre, ez a módosítás lehet a Microsoft Endpoint Configuration Center vagy az Intune Cloud.

   * A Configuration Center esetében nyissa meg a tanúsítványregisztrációs pontot (CRP), és módosítsa az URL-címet. Ez az URL az, amit az eszközök kihívnak és bemutatják a kihívásukat.
   * Az Intune Cloud Only, más néven Intune Standalone esetében vagy szerkesztvagy létrehoz egy új SCEP-házirendet, és hozzáadja az új URL-címet.

## <a name="next-steps"></a>További lépések

Az NDES-be integrált Azure AD alkalmazásproxyval közzéteheti a felhasználók számára elérhető alkalmazásokat. További információt az [Alkalmazások közzététele az Azure AD alkalmazásproxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)című témakörben talál.
