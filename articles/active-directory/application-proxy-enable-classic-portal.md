---
title: "A klasszikus portálon az Azure AD alkalmazásproxy engedélyezése |} Microsoft Docs"
description: "A klasszikus Azure portálon kapcsolja be az alkalmazásproxyt, majd telepítse a fordított proxyhoz tartozó összekötőket."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 073d81e495b9cacbe81f375b09bfcad23aadb22e
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>Alkalmazásproxy engedélyezése a klasszikus portálon, és töltse le az összekötők
Ez a cikk útmutatást nyújt a felhőcímtárhoz tartozó Microsoft Azure AD alkalmazásproxy engedélyezéséhez szükséges lépésekről az Azure AD-ben.

Ha még nem ismerkedett meg az alkalmazásproxy szolgáltatásaival, a következő cikkben talál erre vonatkozó információkat: [How to provide secure remote access to on-premises applications](active-directory-application-proxy-get-started.md) (Helyszíni alkalmazások biztonságos távoli hozzáférése).

## <a name="application-proxy-prerequisites"></a>Az alkalmazásproxy használatának előfeltételei
Az alkalmazásproxy szolgáltatásainak engedélyezése és használata előtt a következőkkel kell rendelkeznie:

* [Microsoft Azure AD Prémium vagy Alapszintű előfizetés](active-directory-editions.md) és egy globális rendszergazdaként használt Azure AD-címtár.
* Egy Windows Server 2012 R2 vagy 2016, amelyre telepítheti az alkalmazásproxy-összekötő futtató kiszolgáló. A kiszolgáló kéréseket küld a felhőben az alkalmazásproxy-szolgáltatásoknak, és HTTP- vagy HTTPS-kapcsolatra van szükség a közzétett alkalmazásokhoz.
  * A közzétett alkalmazásokban elérhető egyszeri bejelentkezéshez ennek a gépnek egy tartományhoz kell csatlakoznia, hogy ugyanabban az AD-tartományban legyen, mint amelyikben az alkalmazást közzéteszi. További információ: [egyszeri bejelentkezéshez az alkalmazásproxy](active-directory-application-proxy-sso-using-kcd.md)
* Ha a szervezet proxykiszolgálót használ az internetkapcsolat működését, olvassa el [együttműködnek a meglévő helyszíni proxykiszolgálókat](application-proxy-working-with-proxy-servers.md) részleteinek a konfigurálásuk módját.

## <a name="open-your-ports"></a>A portok megnyitása

A környezet előkészítése az Azure AD-alkalmazásproxy, először az Azure-adatközpont kommunikáció. Ha az útvonalon tűzfal található, győződjön meg arról, hogy az nyitva van, így az összekötő el tudja küldeni a HTTPS- (TCP-) kéréseket az alkalmazásproxynak.

1. Nyissa meg a következő portokat a **kimenő** forgalmat:

   | Portszám | Hogyan használja fel azokat |
   | --- | --- |
   | 80 | Az SSL-tanúsítvány érvényesítése közben letöltése a tanúsítvány-visszavonási listákat (CRL) |
   | 443 | Az alkalmazásproxy minden kimenő kommunikáció |

   Ha a tűzfal a felhasználók helye szerint szabályozza az adatforgalmat, nyissa meg ezeket a portokat a hálózati szolgáltatásként futó Windows-szolgáltatások adatforgalma számára.

   > [!IMPORTANT]
   > A táblázat által adott jelentéseket tükrözik a connector verziók 1.5.132.0 portokra vonatkozó követelmények és újabb. Ha továbbra is fennáll összekötő régebbi verziójú, akkor is engedélyeznie kell a következő portokat: 5671, 8080-as, 9090, 9091, 9350, 9352 és 10100 – 10120.
   >
   >Az összekötők a legújabb verzióra frissítésével kapcsolatos információkért lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-understand-connectors.md#automatic-updates).

2. Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezett, akkor az engedélyezett kapcsolatok msappproxy.net és servicebus.windows.net. Ha nem szeretné a hozzáférést a [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653), amely minden héten frissítése.

3. Használja a [az Azure AD Application Proxy Connector portok teszt eszközét](https://aadap-portcheck.connectorporttest.msappproxy.net/) ellenőrzése, hogy az összekötő el lehet-e érni az alkalmazásproxy-szolgáltatás. Minimális győződjön meg arról, hogy a központi US régió és az Önhöz legközelebbi régiót összes zöld jelölők. Túl további zöld jelölők azt jelenti, hogy nagyobb rugalmasság.

## <a name="enable-application-proxy-in-azure-ad"></a>Az Azure AD alkalmazásproxy engedélyezése
1. Jelentkezzen be rendszergazdaként a [klasszikus Azure portálra](https://manage.windowsazure.com/).
2. Lépjen az Active Directory területre, majd válassza ki azt a címtárat, amelyen az alkalmazásproxyt engedélyezni kívánja.

    ![Active Directory – ikon](./media/active-directory-application-proxy-enable/ad_icon.png)
3. A címtár oldalán válassza a **Konfigurálás** lehetőséget, majd görgessen lefelé az **Alkalmazásproxy** elemhez.
4. Az **Enable Application Proxy Services for this Directory** (Alkalmazásproxy-szolgáltatások engedélyezése ehhez a címtárhoz) beállítása legyen **Enabled** (Engedélyezve).

    ![Alkalmazásproxy engedélyezése](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Válassza a **Download now** (Letöltés most) lehetőséget. A **az Azure AD Application Proxy Connector letöltése** nyílik meg. Olvassa el és fogadja el a licencfeltételeket, majd kattintson a **Download** (Letöltés) gombra az összekötőhöz tartozó Windows Installer-fájl (.exe) mentéséhez.

## <a name="install-and-register-the-connector"></a>Telepítse és regisztrálja az összekötő
1. Az előfeltételeknek megfelelően előkészített kiszolgálón futtassa az **AADApplicationProxyConnectorInstaller.exe** programot.
2. A telepítés végrehajtásához kövesse a varázsló utasításait.
3. A telepítés során kéri az összekötő regisztrálására az Azure AD-bérlő Alkalmazásproxyjával.

   * Adja meg Azure AD globális rendszergazdai hitelesítő adatait. A globális rendszergazdai bérlő adatai eltérhetnek a Microsoft Azure rendszerre vonatkozó hitelesítő adatoktól.
   * Győződjön meg arról, hogy az összekötőt regisztráló rendszergazda ugyanabban a címtárban található, ahol az alkalmazásproxy-szolgáltatás engedélyezve van. Ha például a bérlő tartománya a contoso.com, a rendszergazda admin@contoso.com vagy más, adott tartománybeli alias lehet.
   * Ha **Internet Explorer fokozott biztonsági beállítások** értéke **a** a kiszolgálón, a regisztrációs képernyő blokkolhatja. Engedélyezi a hozzáférést, kövesse az utasításokat a hibaüzenet. Győződjön meg arról, hogy az Internet Explorer fokozott biztonsági beállításai ki vannak kapcsolva.
   * Ha az összekötő regisztrálása meghiúsul, tekintse meg a [Troubleshoot Application Proxy](active-directory-application-proxy-troubleshoot.md) (Alkalmazásproxy hibaelhárítása) című anyagot.  
4. A telepítést követően a kiszolgáló két új szolgáltatással bővül:

   * a **Microsoft AAD alkalmazásproxy-összekötő** a kapcsolódást engedélyezi;

     * **Microsoft AAD Alkalmazásproxyösszekötő** az automatikus frissítési szolgáltatása. Rendszeres időközönként ellenőrzi az összekötő új verziója, és szükség szerint frissíti.

     ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Kattintson a **Befejezés** gombra a telepítő ablakában.

Az összekötőkre vonatkozó további információkat az [Azure AD-alkalmazásproxy összekötőit ismertető](application-proxy-understand-connectors.md) cikk tartalmaz.

A magas szintű rendelkezésre állás érdekében legalább kettő összekötőt üzembe kell helyeznie. További összekötőt üzembe helyezéséhez ismételje meg a 2. és 3. Minden egyes összekötőt külön kell regisztrálni.

Ha el kívánja távolítani az összekötőt, távolítsa el az összekötő- és a frissítési szolgáltatást is. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="next-steps"></a>Következő lépések
Most már készen áll az [alkalmazások közzétételére az alkalmazásproxy használatával](active-directory-application-proxy-publish.md).

Ha vannak olyan alkalmazásai, amelyek egy különálló hálózaton vagy különböző helyen találhatók, használhat összekötőcsoportokat a különböző összekötők logikai egységekbe rendezéséhez. További információ: [Az alkalmazásproxy-összekötők használata](active-directory-application-proxy-connectors-azure-portal.md).
