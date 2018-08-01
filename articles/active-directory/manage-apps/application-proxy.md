---
title: Hogyan lehet a helyszíni alkalmazások biztonságos távoli elérése
description: A cikk ismerteti, hogyan a helyszíni alkalmazások biztonságos távoli elérést biztosíthat az Azure AD-alkalmazásproxy használatával.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 428f094dae2b9a69b58912190d2959a7dfc467ec
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365262"
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Hogyan lehet a helyszíni alkalmazások biztonságos távoli elérést biztosíthat

Az alkalmazottak szeretnék még ma munkavégzéshez bárhol, bármikor és bármilyen eszközről. Szeretne saját eszközükön munkahelyi legyenek azok táblagépek, telefonokon vagy hordozható számítógépeken. És azt várja, érhetik el, hogy minden alkalmazásra, mindkét SaaS-alkalmazások a felhőben és a vállalati alkalmazásokhoz a helyszínen. Hozzáférést biztosít a helyszíni alkalmazások rendelkezik hagyományosan vesz részt a virtuális magánhálózatok (VPN) vagy demilitarizált zóna (DMZ-ket). Nem csak ezek a megoldások komplex és nehéz biztonságának érdekében, de azok költséges beállításához és kezeléséhez.

Hatékony megoldást van!

A mobil-és felhőközpontú a modern munkaerőt,-és felhőközpontú világ kell egy modern távelérési megoldás. Az Azure AD-alkalmazásproxy funkciója az Azure Active Directory által kínált a távelérési szolgáltatás. Ez azt jelenti, hogy könnyen központi telepítése, használata és kezelése.

[!INCLUDE [identity](../../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Mi az Azure Active Directory Application Proxy?
Az Azure AD-alkalmazásproxy egyszeri bejelentkezés (SSO) biztosít, és a helyileg üzemeltetett webes alkalmazások biztonságos távoli hozzáférést. Egyes alkalmazások közzétételéhez szeretne közé tartozik a SharePoint helyek, az Outlook Web Access vagy bármely más üzleti célú webes alkalmazások rendelkezik. Ezek a helyszíni webalkalmazások integrálva vannak az Azure AD, az ugyanazon identitás- és Office 365 által használt felügyeleti platformmal. A végfelhasználók elérhetik a helyszíni alkalmazásokhoz férnek hozzá Office 365 és más SaaS-alkalmazások az Azure AD-vel integrált azonos módon. Nem kell módosítani a hálózati infrastruktúrát vagy VPN ezt a megoldást biztosít a felhasználók számára szükséges.

## <a name="why-is-application-proxy-a-better-solution"></a>Miért érdemes az alkalmazásproxy jobb megoldás?
Azure AD-alkalmazásproxy a helyszíni alkalmazásait egy egyszerű, biztonságos és költséghatékony távoli hozzáférési megoldást kínál.

Az Azure AD-alkalmazásproxy van:

* **Egyszerű**
   * Nem kell módosítani vagy frissíteni az alkalmazások a proxyval működjenek. 
   * A felhasználók egy egységes hitelesítési tapasztalatokat szerezhet. A MyApps portálról be egyszeri bejelentkezést is SaaS-alkalmazások a felhőben és az alkalmazásokhoz a helyszínen is használnak. 
* **Biztonságos**
   * Amikor az alkalmazásokat az Azure AD-alkalmazásproxy használatával tesz közzé, kihasználhatja a sokoldalú engedélyeztetési vezérlőit és biztonsági elemzési az Azure-ban. A felhőméretű biztonsági és az Azure biztonsági funkciókkal, például a feltételes hozzáférés és a kétlépéses ellenőrzés kap.
   * A távoli hozzáférést biztosíthat felhasználóinak a tűzfalon áthaladó kimenő kapcsolatok nem kell. 
* **Cost-effective**
   * A felhőben az alkalmazásproxy működik, így időt és pénzt takaríthat meg. A helyszíni megoldások használatához általában szükség van, hogy termékeknél DMZ-ket, biztonsági kiszolgálókat és más bonyolult infrastruktúra.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Milyen típusú alkalmazások munkahelyi az alkalmazásproxy használatával?
Az Azure AD-alkalmazásproxy különböző típusú belső alkalmazás érhető el:

* Webes alkalmazások által használt [integrált Windows-hitelesítés](application-proxy-configure-single-sign-on-with-kcd.md) hitelesítéshez  
* A webalkalmazásokat, használja az űrlap-alapú vagy [fejlécalapú](application-proxy-configure-single-sign-on-with-ping-access.md) hozzáférés  
* Webes API-kat, hogy a különböző eszközökön részletgazdag alkalmazásokat közzétenni kívánt  
* Mögött üzemeltetett alkalmazások a [távoli asztali átjáró](application-proxy-integrate-with-remote-desktop-services.md)  
* Gazdag ügyfél alkalmazások integrált a az Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Hogyan működik az alkalmazásproxyt?
Hogy be kell állítania, hogy működik az alkalmazásproxy két összetevőből áll: egy összekötőt és a egy külső végpontot. 

Az összekötő egy egyszerűsített ügynök, amely a Windows Server a hálózaton belül helyezkedik el. Az összekötő lehetővé teszi, hogy a forgalom áramlását az alkalmazásproxy-szolgáltatás a felhőben az alkalmazás a helyi. Kimenő kapcsolatok csak így nem kell minden bemenő portokat nyitni, vagy helyezze semmit a DMZ-t használ. Az összekötők állapot nélküli, és kérje le adatokat a felhőben, igény szerint. További információ az összekötők, többek között azok terheléselosztás és hitelesítéséhez, lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md). 

Külső végpont hogyan a felhasználók számára érhető el az alkalmazások, kívül a hálózaton. Vagy megnyithatja közvetlenül egy külső URL-CÍMÉT, amelyek meghatározzák, hogy a, vagy az alkalmazás eléréséhez a MyApps portálon keresztül. Amikor a felhasználók nyissa meg a végpontok egyikéhez, hitelesítéséhez az Azure ad-ben, és majd az összekötőt a helyszíni alkalmazások kerülnek.

 ![Azure ad-alkalmazásproxy diagramja](./media/application-proxy/azureappproxxy.png)

1. A felhasználó hozzáfér az alkalmazáshoz az alkalmazásproxy-szolgáltatás, és van irányítva az Azure AD bejelentkezési oldal hitelesítéséhez.
2. A sikeres bejelentkezést követően egy jogkivonat jön létre, és az ügyfél eszközre elküldött.
3. Az ügyfél elküldi az alkalmazásproxy-szolgáltatás, amely lekéri az egyszerű felhasználónév (UPN) és a rendszerbiztonsági tag nevének (SPN) a jogkivonatot, majd arra utasítja a kérést az alkalmazásproxy-összekötő.
4. Ha egyszeri bejelentkezésre konfigurálta, az összekötő a felhasználó nevében szükséges további hitelesítést hajt végre.
5. Az összekötő a helyszíni alkalmazás elküldi a kérelmet.  
6. A válasz a felhasználó alkalmazásproxy-szolgáltatás és az összekötő keresztül zajlik.

### <a name="single-sign-on"></a>Egyszeri bejelentkezés
Az Azure AD-alkalmazásproxy biztosít egyszeri bejelentkezés (SSO) integrált Windows-hitelesítés (IWA) használó alkalmazásokat vagy jogcímeket használó alkalmazásokat. Ha az alkalmazás IWA, alkalmazásproxy megszemélyesíti a felhasználó a Kerberos által korlátozott delegálás az egyszeri bejelentkezés révén. Ha rendelkezik egy jogcímbarát alkalmazáshoz, amely megbízik az Azure Active Directory, SSO működik, mert a felhasználó Azure ad-ben már hitelesített.

A Kerberos kapcsolatos további információkért lásd: [összes érdemes figyelembe venni a kapcsolatos a Kerberos által korlátozott delegálás (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Alkalmazások felügyelete
Miután az alkalmazás közzététele az alkalmazásproxy használatával, kezelheti, mint bármely más vállalati alkalmazást az Azure Portalon. Használja az Azure Active Directory biztonsági funkciókkal, például a feltételes hozzáférés és a kétlépéses ellenőrzést, szabályozhatja a felhasználói engedélyek, és az alkalmazás márkajelzés testreszabása. 

## <a name="get-started"></a>Bevezetés

Mielőtt alkalmazásproxy állít be, ellenőrizze, hogy egy támogatott [Azure Active Directory-kiadás](https://azure.microsoft.com/pricing/details/active-directory/) és az Azure AD-címtárral, amelyhez Ön globális rendszergazda.

Ismerkedés az Application Proxy két lépésben:

1. [Alkalmazásproxy engedélyezése és konfigurálása az összekötő](application-proxy-enable.md).    
2. [Alkalmazások közzététele](application-proxy-publish-azure-portal.md) -töltse le a helyszíni alkalmazásait közzétett és elérhető távolról a gyors és egyszerű varázsló segítségével.

## <a name="whats-next"></a>A következő lépések
Miután az első alkalmazás közzétételéhez nincs sokkal több mindent tehet az alkalmazásproxy:

* [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
* [Alkalmazások közzététele saját tartománynév használatával](application-proxy-configure-custom-domain.md)
* [További tudnivalók az Azure AD-alkalmazásproxy-összekötők](application-proxy-connectors.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md) 
* [Egyéni kezdőlap beállítása](application-proxy-configure-custom-home-page.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).

