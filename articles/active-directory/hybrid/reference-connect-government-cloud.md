---
title: 'Azure AD Connect: a Azure Government Hybrid Identity szempontjai'
description: A Azure AD Connect a kormányzati felhővel való üzembe helyezésének különleges szempontjai.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81378924"
---
# <a name="hybrid-identity-considerations-for-azure-government"></a>Az Azure Government hibrididentitás-kezelési szempontjai 
A következő dokumentum ismerteti a hibrid környezetek Azure Government felhővel való megvalósításának szempontjait.  Ez az információ a Azure Government felhővel dolgozó rendszergazdák és építészek számára készült referenciául szolgál.  
> [!NOTE] 
> A helyszíni AD-környezet Azure Governemnt-felhővel való integrálásához frissítenie kell a [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594)legújabb kiadására. 

> [!NOTE] 
> Az Egyesült államokbeli kormányzati DoD-végpontok teljes listájáért tekintse meg a [dokumentációt](https://docs.microsoft.com/office365/enterprise/office-365-u-s-government-dod-endpoints) . 

## <a name="pass-through-authentication"></a>Átmenő hitelesítés 
Az alábbi információk az átmenő hitelesítés (PTA) és a Azure Government felhő megvalósítására szolgálnak.

### <a name="allow-access-to-urls"></a>URL-címek elérésének engedélyezése  
Az átmenő hitelesítési ügynök üzembe helyezése előtt ellenőrizze, hogy van-e tűzfal a kiszolgálók és az Azure AD között. Ha a tűzfal vagy a proxy engedélyezi a DNS-engedélyezési lehetőséget, adja hozzá a következő kapcsolatokat: 
> [!NOTE]
> Az alábbi útmutatás a Azure Government környezetekhez készült [alkalmazásproxy-összekötő](https://aka.ms/whyappproxy) telepítésére is vonatkozik.

|URL-cím |Használatuk módja|
|-----|-----| 
|*. msappproxy.us *. servicebus.usgovcloudapi.net|Kommunikáció az ügynök és az Azure AD Cloud Service között |
|mscrl.microsoft.us:80 crl.microsoft.us:80 </br>ocsp.msocsp.us:80 www.microsoft.us:80| Az ügynök ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez.| 
|login.Windows.us Secure.aadcdn.microsoftonline-p.com *. microsoftonline.us </br> *. microsoftonline-p.us </br>*. msauth.net </br> *. msauthimages.net </br>*. msecnd.net</br>*. msftauth.net </br>*. msftauthimages.net</br>*. phonefactor.net </br>enterpriseregistration.windows.net</br>management.azure.com </br>policykeyservice.dc.ad.msft.net</br>ctdl.windowsupdate.us:80| Az ügynök ezeket az URL-címeket használja a regisztrációs folyamat során.| 

### <a name="install-the-agent-for-the-azure-government-cloud"></a>A Azure Government-felhő ügynökének telepítése 
A Azure Government felhőhöz tartozó ügynök telepítéséhez hajtsa végre az alábbi lépéseket: a parancssori terminálban navigáljon ahhoz a mappához, ahol az ügynök telepítésére szolgáló végrehajtható fájl található. Futtassa a következő parancsot, amely meghatározza, hogy a telepítés Azure Government. 

Áteresztő hitelesítés esetén: 
```
AADConnectAuthAgentSetup.exe ENVIRONMENTNAME="AzureUSGovernment"
```

Alkalmazásproxy esetén:
```
AADApplicationProxyConnectorInstaller.exe ENVIRONMENTNAME="AzureUSGovernment" 
```

## <a name="single-sign-on"></a>Egyszeri bejelentkezés 
A Azure AD Connect-kiszolgáló beállítása: Ha a bejelentkezési módszere átmenő hitelesítést használ, nincs szükség további előfeltételek ellenőrzésére. Ha a bejelentkezési módszerként jelszó-kivonatolási szinkronizálást használ, és ha tűzfal van Azure AD Connect és az Azure AD között, ügyeljen a következőkre:
- A Azure AD Connect 1.1.644.0 vagy újabb verzióját használja. 
- Ha a tűzfal vagy a proxy engedélyezi a DNS-engedélyezési lehetőséget, vegye fel a kapcsolatot a *. msapproxy.us URL-címekkel a 443-es porton keresztül. Ha nem, engedélyezze a hozzáférést az Azure Datacenter IP-tartományokhoz, amelyek hetente frissülnek. Ez az előfeltétel csak akkor alkalmazható, ha engedélyezi a szolgáltatást. A tényleges felhasználói bejelentkezésekhez nem szükséges. 

### <a name="rolling-out-seamless-sso"></a>Zökkenőmentes egyszeri bejelentkezés 
A zökkenőmentes SSO-t fokozatosan kiderítheti a felhasználók számára az alábbi utasítások alapján. Első lépésként adja hozzá a következő Azure AD URL-címet az összes vagy a kiválasztott felhasználó intranetes zónájának beállításaihoz a Active Directory Csoportházirend használatával:https://autologon.microsoft.us 

Emellett engedélyeznie kell egy intranetes zóna házirend-beállítását, amelynek neve a frissítés engedélyezése az állapotsoron parancsfájlon keresztül Csoportházirend. Böngészővel kapcsolatos megfontolások Mozilla Firefox (minden platform) a Mozilla Firefox nem használ automatikusan Kerberos-hitelesítést. Az alábbi lépések segítségével minden felhasználónak manuálisan hozzá kell adnia az Azure AD URL-címét a Firefox-beállításaihoz: 
1. Futtassa a Firefox programot, és írja be a about: config parancsot a címsorba. Zárja be a megjelenő értesítéseket. 
2. Keressen rá a Network. Negotiate – auth. megbízható – URI-k beállításra. Ez a beállítás felsorolja a Firefox megbízható helyeit a Kerberos-hitelesítéshez. 
3. Kattintson a jobb gombbal, és válassza a módosítás lehetőséget. 
4. Adja https://autologon.microsoft.us meg a mezőt a mezőben.
5. Kattintson az OK gombra, majd nyissa meg újra a böngészőt. 

### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge a Chromium alapján (minden platform) 
Ha felülbírálta a `AuthNegotiateDelegateAllowlist` környezetben található `AuthServerAllowlist` házirend-beállításokat, és az Azure ad URL-címét is hozzáadja (https://autologon.microsoft.us) hozzájuk). 

### <a name="google-chrome-all-platforms"></a>Google Chrome (minden platform) 
Ha felülbírálta a `AuthNegotiateDelegateWhitelist` környezetben található `AuthServerWhitelist` házirend-beállításokat, és az Azure ad URL-címét is hozzáadja (https://autologon.microsoft.us) hozzájuk). 

## <a name="next-steps"></a>További lépések
[Áteresztő hitelesítés](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)
[egyszeri bejelentkezéssel](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) 
