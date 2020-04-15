---
title: 'Azure AD Connect: Hibrid identitással kapcsolatos szempontok az Azure Government számára'
description: Az Azure AD Connect és a kormányzati felhő üzembe helyezésének speciális szempontjai.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad28beb68afb5207e7b36c5d76c4dac808c5114
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378924"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Hibrid identitással kapcsolatos szempontok az Azure Government számára 
Az alábbi dokumentum ismerteti a szempontokat egy hibrid környezet megvalósításához az Azure Government felhővel.  Ez az információ az Azure Government-felhővel együttműködő rendszergazdák és építészek számára nyújt tájékoztatást.  
> [!NOTE] 
> Annak érdekében, hogy integrálja a helyszíni AD-környezet ben az Azure Governemnt felhő, frissítenie kell a legújabb kiadásaz [Azure AD Connect.](https://www.microsoft.com/download/details.aspx?id=47594) 

> [!NOTE] 
> Az Egyesült Államok kormányának dod végpontjainak teljes listáját [a](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) 

## <a name="pass-through-authentication"></a>Átmenő hitelesítés 
Az alábbi információk az átmenő hitelesítés (PTA) és az Azure Government felhő megvalósításához szolgálnak.

### <a name="allow-access-to-urls"></a>Url-ekhez való hozzáférés engedélyezése  
Az átmenő hitelesítési ügynök telepítése előtt ellenőrizze, hogy van-e tűzfal a kiszolgálók és az Azure AD között. Ha a tűzfal vagy a proxy engedélyezi a DNS-engedélyezési listát, adja hozzá a következő kapcsolatokat: 
> [!NOTE]
> Az alábbi útmutató az Azure Government-környezetek [alkalmazásproxy-összekötőjének](https://aka.ms/whyappproxy) telepítésére is vonatkozik.

|URL-cím |Hogyan használják|
|-----|-----| 
|*.msappproxy.us *.servicebus.usgovcloudapi.net|Az ügynök és az Azure AD felhőszolgáltatás közötti kommunikáció |
|crl.microsoft.us:80 mscrl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Az ügynök ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez.| 
|secure.aadcdn.microsoftonline-p.com *.microsoftonline.us </br>.microsoftonline-p.us *login.windows.us </br>*.msauth.net </br> *.msauthimages.net </br>*.msecnd.net</br>*.msftauth.net </br>*.msftauthimages.net</br>*.phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Az ügynök ezeket az URL-címeket használja a regisztrációs folyamat során.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>Az Azure Government felhőügynök telepítése 
Az Ügynök az Azure Government-felhőhöz való telepítéséhez az alábbi lépéseket kell végrehajtania: A parancssori terminálon keresse meg azt a mappát, ahol az ügynök telepítéséhez végrehajtható végrehajtható fájl található. Futtassa a következő parancsot, amely megadja a telepítés az Azure Government. 

Áthaladási hitelesítés esetén: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Alkalmazásproxy esetén:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Egyszeri bejelentkezés 
Állítsa be az Azure AD Connect-kiszolgálót: Ha átmenő hitelesítést használ bejelentkezési módszerként, nincs szükség további előfeltétel-ellenőrzésre. Ha bejelentkezési módszerként jelszókivonat-szinkronizálást használ, és tűzfal van az Azure AD Connect és az Azure AD között, győződjön meg arról, hogy:
- Az Azure AD Connect 1.1.644.0-s vagy újabb verzióját használja. 
- Ha a tűzfal vagy a proxy engedélyezi a DNS-engedélyezési listát, adja hozzá a kapcsolatokat a *.msapproxy.us URL-címekhez a 443-as porton keresztül. Ha nem, engedélyezze a hozzáférést az Azure-adatközpont IP-tartományaihoz, amelyek hetente frissülnek. Ez az előfeltétel csak akkor alkalmazható, ha engedélyezi a funkciót. A tényleges felhasználói bejelentkezések nem szükségesek. 

### <a name="rolling-out-seamless-sso"></a>Zökkenőmentes egyszeri bejelentkezés 
Az alábbi utasítások nak megfelelően fokozatosan elvezetheti a zökkenőmentes egyszeri bejelentkezést a felhasználók számára. Először adja hozzá a következő Azure AD URL-címet az összes vagy a kiválasztott felhasználók intranetzóna-beállításaihoz az Active Directory csoportházirendjével:https://autologon.microsoft.us 

Ezenkívül engedélyeznie kell egy intranetes zónaházirend-beállítást, amelynek neve Frissítések engedélyezése az állapotsorhoz parancsfájlon keresztül a csoportházirenden keresztül. Böngésző szempontok Mozilla Firefox (minden platformon) Mozilla Firefox nem használja automatikusan Kerberos hitelesítést. Minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox beállításaihoz a következő lépések végrehajtásával: 
1. Futtassa a Firefoxot, és írja be a about:config címet a címsorba. Utasítsa el a megjelenő értesítéseket. 
2. Keresse meg a network.negotiate-auth.trusted-uris preferenciát. Ez a beállítás a Firefox Kerberos-hitelesítésre vonatkozó megbízható helyeit sorolja fel. 
3. Kattintson a jobb gombbal, és válassza a Módosítás parancsot. 
4. Írja https://autologon.microsoft.us be a mezőbe.
5. Válassza az OK gombot, majd nyissa meg újra a böngészőt. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge a Chromium alapján (minden platformon) 
Ha felülbírálta a `AuthNegotiateDelegateAllowlist` vagy `AuthServerAllowlist` a szabályzat beállításait a környezetben, győződjönhttps://autologon.microsoft.us) meg arról, hogy adja hozzá az Azure AD URL-címét (hozzájuk is. 

### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform) 
Ha felülbírálta a `AuthNegotiateDelegateWhitelist` vagy `AuthServerWhitelist` a szabályzat beállításait a környezetben, győződjönhttps://autologon.microsoft.us) meg arról, hogy adja hozzá az Azure AD URL-címét (hozzájuk is. 

## <a name="next-steps"></a>További lépések
[Átmenő hitelesítés](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[egyszeri bejelentkezés](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
