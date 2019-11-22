---
title: Távoli hozzáférés a helyszíni alkalmazásokhoz – Azure AD Application Proxy
description: Azure Active Directory alkalmazásproxy biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címen vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztal, a SharePoint, a Teams, a tabló, a Qlik és az üzletági (LOB) alkalmazások számára.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4780786f0caea2c211b6b93fb0736feaade8de80
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274835"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Távoli hozzáférés helyszíni alkalmazásokhoz Azure Active Directory alkalmazásproxy használatával 

Azure Active Directory alkalmazásproxy biztonságos távoli hozzáférést biztosít a helyszíni webalkalmazásokhoz. Az Azure AD-be való egyszeri bejelentkezés után a felhasználók külső URL-címen vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz. Az alkalmazásproxy például távoli hozzáférést és egyszeri bejelentkezést biztosíthat a Távoli asztal, a SharePoint, a Teams, a tabló, a Qlik és az üzletági (LOB) alkalmazások számára.

Az Azure AD-alkalmazásproxy van:

- **Egyszerűen használható**. A felhasználók ugyanúgy férhetnek hozzá a helyszíni alkalmazásokhoz, mint az Azure AD-vel integrált O365 és más SaaS-alkalmazásokhoz. Nem kell módosítani vagy frissíteni az alkalmazások a proxyval működjenek. 

- **Biztonságos**. A helyszíni alkalmazások az Azure engedélyezési vezérlőit és biztonsági elemzéseit is használhatják. A helyszíni alkalmazások például használhatják a feltételes hozzáférést és a kétlépéses ellenőrzést. Az alkalmazásproxy nem igényli a bejövő kapcsolatok megnyitását a tűzfalon keresztül.
 
- **Költséghatékony**. A helyszíni megoldások általában vagy demilitarizált zónák (DMZ), peremhálózati kiszolgálók vagy más összetett infrastruktúra beállításához és karbantartásához szükségesek. Az alkalmazásproxy a felhőben fut, így könnyen használható. Az alkalmazásproxy használatához nem kell módosítania a hálózati infrastruktúrát, vagy további berendezéseket kell telepítenie a helyszíni környezetben.

## <a name="what-is-application-proxy"></a>Mi az alkalmazásproxy?
Az alkalmazásproxy az Azure AD egyik funkciója, amely lehetővé teszi a felhasználók számára a helyszíni webalkalmazások távoli ügyfélről való elérését. Az alkalmazásproxy magában foglalja a felhőben futó alkalmazásproxy-szolgáltatást, valamint az alkalmazásproxy-összekötőt, amely egy helyszíni kiszolgálón fut. Az Azure AD, az alkalmazásproxy szolgáltatás és az alkalmazásproxy-összekötő együttműködve biztonságosan továbbítja a felhasználói bejelentkezési tokent az Azure AD-ből a webalkalmazásba.

Az alkalmazásproxy a következőket támogatja:

* Webes alkalmazások által használt [integrált Windows-hitelesítés](application-proxy-configure-single-sign-on-with-kcd.md) hitelesítéshez  
* A webalkalmazásokat, használja az űrlap-alapú vagy [fejlécalapú](application-proxy-configure-single-sign-on-with-ping-access.md) hozzáférés  
* Webes API-kat, hogy a különböző eszközökön részletgazdag alkalmazásokat közzétenni kívánt  
* Mögött üzemeltetett alkalmazások a [távoli asztali átjáró](application-proxy-integrate-with-remote-desktop-services.md)  
* Gazdag ügyfél alkalmazások integrált a az Active Directory Authentication Library (ADAL)

Az alkalmazásproxy támogatja az egyszeri bejelentkezést. További információ a támogatott módszerekről: [egyszeri bejelentkezés módszerének kiválasztása](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

Az alkalmazásproxy használata ajánlott a távoli felhasználók számára a belső erőforrásokhoz való hozzáférés biztosítása érdekében. Az alkalmazásproxy a VPN vagy fordított proxy igényét váltja fel. Nem a vállalati hálózat belső felhasználói számára készült.  Azok a felhasználók, akik szükségtelenül használják az alkalmazásproxy-t, váratlan és nemkívánatos teljesítménnyel kapcsolatos problémákat okozhatnak.

## <a name="how-application-proxy-works"></a>Az alkalmazásproxy működése

Az alábbi ábra bemutatja, hogyan működik együtt az Azure AD és az alkalmazásproxy az egyszeri bejelentkezés biztosításához a helyszíni alkalmazásokhoz.

![Azure ad-alkalmazásproxy diagramja](./media/application-proxy/azureappproxxy.png)

1. Miután a felhasználó az alkalmazás érhető el egy végponton keresztül, az Azure AD bejelentkezési oldal a felhasználó van átirányítva. 
2. Sikeres bejelentkezés után az Azure AD jogkivonatot küld a felhasználó ügyfelének.
3. Az ügyfél elküldi a jogkivonatot az alkalmazásproxy szolgáltatásnak, amely lekéri az egyszerű felhasználónevet (UPN) és a rendszerbiztonsági tag nevét (SPN) a jogkivonatból. Az alkalmazásproxy ezt követően elküldi a kérést az alkalmazásproxy-összekötőnek.
4. Ha egyszeri bejelentkezésre konfigurálta, az összekötő a felhasználó nevében szükséges további hitelesítést hajt végre.
5. Az összekötő a helyszíni alkalmazás elküldi a kérelmet.  
6. A választ az összekötő és az alkalmazásproxy szolgáltatás továbbítja a felhasználónak.

| Összetevő | Leírás |
| --------- | ----------- |
| Végpont  | A végpont egy URL-cím vagy egy [végfelhasználói portál](end-user-experiences.md). Felhasználók által egy külső URL-cím elérése a hálózatán kívüli alkalmazások érhető el. A hálózaton belüli felhasználók elérhetik az alkalmazást egy URL-címet vagy egy végfelhasználói portálon keresztül. Amikor a felhasználók nyissa meg a végpontok egyikéhez, hitelesítéséhez az Azure ad-ben, és majd az összekötőt a helyszíni alkalmazások kerülnek.|
| Azure AD | Az Azure AD a felhőben tárolt bérlői könyvtár használatával végzi a hitelesítést. |
| Alkalmazásproxy szolgáltatás | Ez az alkalmazásproxy-szolgáltatás a felhőben fut az Azure AD részeként. Továbbítja a bejelentkezési jogkivonatot a felhasználótól az alkalmazásproxy-Összekötőbe. Az alkalmazásproxy továbbítja az elérhető fejléceket a kérelemben, és a protokoll szerint állítja be a fejléceket az ügyfél IP-címére. Ha a proxyhoz tartozó bejövő kérelem már tartalmazza ezt a fejlécet, a rendszer hozzáadja az ügyfél IP-címét a vesszővel elválasztott lista végéhez, amely a fejléc értéke.|
| Alkalmazásproxy-összekötő | Az összekötő egy egyszerűsített ügynök, amely egy Windows Serveren fut a hálózaton belül. Az összekötő kezeli a felhőben és a helyszíni alkalmazásban futó alkalmazásproxy szolgáltatás közötti kommunikációt. Az összekötő csak kimenő kapcsolatokat használ, így nem kell megnyitnia a bejövő portokat, vagy semmit sem kell tennie a DMZ-ben. Az összekötők állapot nélküli, és kérje le adatokat a felhőben, igény szerint. További információ az összekötők, többek között azok terheléselosztás és hitelesítéséhez, lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).|
| Active Directory (AD) | Active Directory a tartományi fiókok hitelesítésének végrehajtásához a helyszínen fut. Ha az egyszeri bejelentkezés konfigurálva van, az összekötő az AD-vel kommunikálva további hitelesítést hajt végre.
| Helyszíni alkalmazás | Végül a felhasználó hozzáférhet egy helyszíni alkalmazáshoz. 

## <a name="next-steps"></a>Következő lépések
Az alkalmazásproxy használatának megkezdéséhez lásd [: oktatóanyag: helyszíni alkalmazás hozzáadása a táveléréshez alkalmazásproxy](application-proxy-add-on-premises-application.md)használatával. 

A legfrissebb híreket és frissítéseket az [alkalmazásproxy blogjában](https://blogs.technet.com/b/applicationproxyblog/) találja.


