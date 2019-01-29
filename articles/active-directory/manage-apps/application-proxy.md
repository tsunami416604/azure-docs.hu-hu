---
title: Távoli hozzáférést a helyszíni alkalmazások – Azure Active Directory-alkalmazásproxyval |} A Microsoft Docx
description: Az Azure Active Directory Application Proxy a helyi webes alkalmazásokhoz való biztonságos távoli hozzáférést biztosít. Egy egyszeri bejelentkezés az Azure ad-hez, miután felhasználók hozzáférhessenek felhő- és a helyszíni alkalmazásokhoz egy külső URL-cím vagy egy belső alkalmazás portálon keresztül. Például az alkalmazásproxy biztosíthat távoli hozzáférést és egyszeri bejelentkezést, a távoli asztal, a SharePoint, a Teams, a Tableau, a Qlik és a üzletági (LOB) alkalmazások.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: af605f4ad7c4167936a07f2ae8ffd3d2850cda75
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55185208"
---
# <a name="remote-access-to-on-premises-applications-through-azure-active-directorys-application-proxy"></a>Távoli hozzáférés a helyszíni alkalmazások Azure Active Directory Application Proxy használatával 

Az Azure Active Directory Application Proxy a helyi webes alkalmazásokhoz való biztonságos távoli hozzáférést biztosít. Egy egyszeri bejelentkezés az Azure ad-hez, miután felhasználók hozzáférhessenek felhő- és a helyszíni alkalmazásokhoz egy külső URL-cím vagy egy belső alkalmazás portálon keresztül. Például az alkalmazásproxy biztosíthat távoli hozzáférést és egyszeri bejelentkezést, a távoli asztal, a SharePoint, a Teams, a Tableau, a Qlik és a üzletági (LOB) alkalmazások.

Az Azure AD-alkalmazásproxy van:

- **Könnyen használható**. Felhasználók férnek hozzá Office 365 és más SaaS-alkalmazások az Azure AD-vel integrált azonos módon érhetik el a helyszíni alkalmazások. Nem kell módosítani vagy frissíteni az alkalmazások a proxyval működjenek. 

- **Biztonságos**. A helyszíni alkalmazások az Azure engedélyeztetési vezérlőit és biztonsági elemzési használhatja. A helyszíni alkalmazások használhatja például a feltételes hozzáférés és a kétlépéses ellenőrzést. Application Proxy nem igényel, meg kell nyitni bejövő kapcsolatok a tűzfalon keresztül.
 
- **Költséghatékony**. A helyszíni megoldások használatához általában szükség van, és demilitarizált zóna (DMV-kkel), biztonsági kiszolgálókat és más bonyolult infrastruktúra karbantartása. Az alkalmazásproxy fut a felhőben, ami lehetővé teszi az egyszerűen használható. Az alkalmazásproxy használatához nem kell módosítani a hálózati infrastruktúrát, vagy telepítse a további berendezésekre a helyszíni környezetben.

## <a name="what-is-application-proxy"></a>Mi az Application Proxy?
Az alkalmazásproxy funkciója az Azure ad-ben, amely lehetővé teszi a felhasználók számára hozzáférést a helyszíni webalkalmazások egy távoli ügyfélhez. Alkalmazásproxy tartoznak az alkalmazásproxy-szolgáltatás, amely a felhőben fut, és az alkalmazásproxy-összekötő, amely egy helyszíni kiszolgálón fut. Az Azure AD-ben az alkalmazásproxy-szolgáltatás, és az Application Proxy connector együttműködve biztonságos módon adnak át a felhasználói bejelentkezési jogkivonat Azure AD-ből a webalkalmazás.

Az alkalmazásproxy működik együtt:

* Webes alkalmazások által használt [integrált Windows-hitelesítés](application-proxy-configure-single-sign-on-with-kcd.md) hitelesítéshez  
* A webalkalmazásokat, használja az űrlap-alapú vagy [fejlécalapú](application-proxy-configure-single-sign-on-with-ping-access.md) hozzáférés  
* Webes API-kat, hogy a különböző eszközökön részletgazdag alkalmazásokat közzétenni kívánt  
* Mögött üzemeltetett alkalmazások a [távoli asztali átjáró](application-proxy-integrate-with-remote-desktop-services.md)  
* Gazdag ügyfél alkalmazások integrált a az Active Directory Authentication Library (ADAL)

Az alkalmazásproxy egyszeri bejelentkezést támogatja. Támogatott módszerekkel kapcsolatos további információkért lásd: [egyszeri bejelentkezési módszer kiválasztása](what-is-single-sign-on.md#choosing-a-single-sign-on-method).

## <a name="how-application-proxy-works"></a>Application Proxy működése

Az alábbi ábrán látható, hogy az Azure AD és az alkalmazásproxy együttesen biztosít egyszeri bejelentkezést a helyszíni alkalmazások számára.

![Azure ad-alkalmazásproxy diagramja](./media/application-proxy/azureappproxxy.png)

1. Miután a felhasználó az alkalmazás érhető el egy végponton keresztül, az Azure AD bejelentkezési oldal a felhasználó van átirányítva. 
2. A sikeres bejelentkezést követően az Azure AD elküldi egy jogkivonatot a felhasználó ügyféleszközön.
3. Az ügyfél elküldi az alkalmazásproxy-szolgáltatás, amely lekéri az egyszerű felhasználónév (UPN) és a rendszerbiztonsági tag nevének (SPN) a jogkivonatot. Az alkalmazásproxy ezután elküldi a kérést az alkalmazásproxy-összekötő.
4. Ha egyszeri bejelentkezésre konfigurálta, az összekötő a felhasználó nevében szükséges további hitelesítést hajt végre.
5. Az összekötő a helyszíni alkalmazás elküldi a kérelmet.  
6. A válasz érkezik az összekötő és az alkalmazásproxy-szolgáltatás a felhasználó.

| Összetevő | Leírás |
| --------- | ----------- |
| Végpont  | A végpont URL-címe, vagy egy [végfelhasználói portál](end-user-experiences.md). Felhasználók által egy külső URL-cím elérése a hálózatán kívüli alkalmazások érhető el. A hálózaton belüli felhasználók elérhetik az alkalmazást egy URL-címet vagy egy végfelhasználói portálon keresztül. Amikor a felhasználók nyissa meg a végpontok egyikéhez, hitelesítéséhez az Azure ad-ben, és majd az összekötőt a helyszíni alkalmazások kerülnek.|
| Azure AD | Az Azure AD végrehajtja a hitelesítést, a bérlő címtárát a felhőben tárolt használatával. |
| Application Proxy szolgáltatás | Az alkalmazásproxy-szolgáltatás fut a felhőben az Azure AD részeként. Adja át, a bejelentkezési token a felhasználótól az alkalmazásproxy-összekötő. Proxy bármely elérhető-e a kérelem fejlécei továbbítja, és beállítja a fejlécek alapján a protokoll, az ügyfél IP-címre. Ha a bejövő kéréseket a proxy már, hogy a fejléc, az ügyfél IP-cím hozzáadódik a vesszővel tagolt lista, amely a fejléc értéke végére.|
| Alkalmazásproxy-összekötő | Az összekötő egy egyszerűsített, a hálózaton belüli Windows Serveren futó ügynök. Az összekötő a felhőben az alkalmazásproxy-szolgáltatás és a helyszíni alkalmazások közötti kommunikációt kezeli. Az összekötő kimenő kapcsolatok csak akkor használja, így nem kell minden bemenő portokat nyitni, vagy helyezze semmit a DMZ-t. Az összekötők állapot nélküli, és kérje le adatokat a felhőben, igény szerint. További információ az összekötők, többek között azok terheléselosztás és hitelesítéséhez, lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).|
| Az Active Directory (AD) | Az Active Directory fut, a helyszíni tartományi fiókok esetében a hitelesítés végrehajtásához. Ha az egyszeri bejelentkezés konfigurálva van, az összekötő végrehajtásához szükséges további hitelesítést AD kommunikál.
| Helyszíni alkalmazás | Végül a felhasználó nem érhetik el a helyszíni alkalmazások. 

## <a name="next-steps"></a>További lépések
Az alkalmazásproxy használatának megkezdéséhez lásd [oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása](application-proxy-add-on-premises-application.md). 

A legújabb híreket és frissítéseket, lásd: a [alkalmazásproxy blog](https://blogs.technet.com/b/applicationproxyblog/)


