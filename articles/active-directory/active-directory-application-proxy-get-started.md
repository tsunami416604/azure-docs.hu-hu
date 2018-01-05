---
title: "Útmutató a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak"
description: "Bemutatja, hogyan adhat a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak az Azure AD alkalmazásproxy segítségével."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: d5450da1-9e06-4d08-8146-011c84922ab5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 24def898f282c4e122ae53932ae86047e815595c
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-provide-secure-remote-access-to-on-premises-applications"></a>Útmutató a helyszíni alkalmazások biztonságos távoli hozzáférést biztosítanak

Az alkalmazottak Ma szeretné, hogy termelékenyebben legyenek a bárhol, bármikor, bármilyen eszközről. Szeretnék a saját eszközeik munkahelyi legyenek azok táblagépek, telefonok vagy hordozható számítógépeken. És várhatóan tudni elérni az alkalmazásaikat, mind a felhőben és a vállalati alkalmazásokhoz a helyszínen SaaS-alkalmazásokhoz. Hozzáférést biztosít a helyszíni alkalmazások hagyományosan van szó, virtuális magánhálózatokhoz (VPN) vagy demilitarizált zóna (DMZ-k). Nem csak ezek a megoldások összetett és biztonsága érdekében rögzített, de azok költséges beállításához és kezeléséhez.

Nincs fejlettebb módszert!

Modern dolgozók számára a mobileszköz-első, a felhő-első globális modern távelérési megoldásra van szükségük. Az Azure AD-alkalmazásproxy egy olyan szolgáltatás, az Azure Active Directory szolgáltatás távoli hozzáférést biztosít. Ez azt jelenti, hogy könnyen központi telepítése, használata és kezelése.

[!INCLUDE [identity](../../includes/azure-ad-licenses.md)]

## <a name="what-is-azure-active-directory-application-proxy"></a>Mi az Azure Active Directory Alkalmazásproxyjával?
Az Azure AD-alkalmazásproxy biztosítja az egyszeri bejelentkezés (SSO) és a webes alkalmazások biztonságos távoli hozzáférést a helyben tárolt. Néhány történő közzétenni kívánt alkalmazások közé tartoznak az SharePoint helyek, az Outlook Web Access vagy bármely más LOB webalkalmazások rendelkezik. Ezek a helyszíni webalkalmazások integrálva vannak az Azure AD, a ugyanazzal az identitással és az Office 365 által használt vezérlő platform. A végfelhasználók hozzáférnek az Office 365 és az egyéb SaaS-alkalmazásokhoz az Azure ad-vel integrált ugyanúgy férhet hozzá a helyszíni alkalmazásokhoz. Nem kell módosítani a hálózati infrastruktúrát vagy VPN ezt a megoldást nyújt a felhasználók számára szükséges.

## <a name="why-is-application-proxy-a-better-solution"></a>Miért érdemes az alkalmazásproxy jobb megoldás?
Az Azure AD-alkalmazásproxy összes az helyszíni alkalmazások távoli elérés egyszerű, biztonságos és költséghatékony megoldást kínál.

Az Azure AD-alkalmazásproxy van:

* **Egyszerű**
   * Nem kell módosítani, vagy frissítse az alkalmazások működéséhez a Proxy. 
   * A felhasználók egy egységes hitelesítési élmény beolvasása. A MyApps portál eléréséhez a felhőben és az alkalmazásokhoz a helyszínen, az egyszeri bejelentkezés mindkét SaaS-alkalmazásokhoz való használhatnak. 
* **Biztonságos**
   * Az Azure AD alkalmazásproxy használó alkalmazások közzétételekor kihasználhatja a gazdag engedélyezési vezérlők és a biztonsági analytics az Azure-ban. A felhőméretű biztonsági és az Azure biztonsági szolgáltatások, mint a feltételes hozzáférés és a kétlépéses ellenőrzés kapják meg.
   * Nem kell megnyitni a bejövő kapcsolatokat a távoli hozzáférést biztosíthat felhasználóinak a tűzfalon keresztül. 
* **Költséghatékony**
   * Alkalmazásproxy működik a felhőben, így időt és pénzt menthető. A helyszíni megoldások általában szükséges beállításához és karbantartásához DMZ-k, a peremhálózati kiszolgálóinak vagy más összetett infrastruktúra.  

## <a name="what-kind-of-applications-work-with-application-proxy"></a>Milyen típusú alkalmazások munkahelyi az alkalmazásproxy?
Az Azure AD alkalmazásproxy különböző típusú belső alkalmazás érhető el:

* Webes használó alkalmazások [integrált Windows-hitelesítés](active-directory-application-proxy-sso-using-kcd.md) hitelesítéshez  
* Webes űrlap alapú használó alkalmazások vagy [fejléc-alapú](application-proxy-ping-access.md) hozzáférés  
* Webes API-t szeretné tenni a különböző eszközökön gazdag alkalmazások  
* Alkalmazások mögött található egy [távoli asztali átjáró](application-proxy-publish-remote-desktop.md)  
* Gazdag ügyfél alkalmazások integrált rendelkező az Active Directory Authentication Library (ADAL)

## <a name="how-does-application-proxy-work"></a>Hogyan működik az alkalmazásproxy?
Konfigurálja a munkahelyi alkalmazás proxybeállítások két összetevőből: összekötő és a külső végpont. 

Az összekötő egy egyszerűsített ügynök, amely a Windows Server a hálózaton belül helyezkedik el. Az összekötő lehetővé teszi a forgalom áramlását a felhőben az alkalmazásproxy az alkalmazás a helyszínen. Kimenő kapcsolatok csak így nem kell arra, hogy semmit a Szegélyhálózaton lévő vagy nyissa meg a bejövő portra használ. Az összekötők állapot nélküli és információkat le, szükség esetén a felhőből. Összekötők, például hogy miként kapcsolatos további információk azok terheléselosztásához és elvégezni a hitelesítést, lásd: [megértéséhez Azure AD-alkalmazásproxy összekötők](application-proxy-understand-connectors.md). 

A külső végpont nem hogyan a felhasználók elérését a hálózaton kívül az alkalmazások. Azok vagy folytathatja közvetlenül egy külső URL-CÍMÉT, amely annak meghatározását, vagy azokat a MyApps portálon keresztül érhetik el az alkalmazást. Amikor a felhasználók nyissa meg ezeket a végpontokat egyikére, az Azure AD hitelesíti, és majd irányított az összekötő a helyszíni alkalmazások.

 ![Alkalmazásproxy AzureAD diagramja](./media/active-directory-application-proxy-get-started/azureappproxxy.png)

1. A felhasználó az alkalmazásproxy keresztül fér hozzá az alkalmazást, és az Azure AD bejelentkezési oldalára hitelesítésre kéri.
2. Egy sikeres bejelentkezés, után jogkivonat előállítja, és elküldi az ügyféleszközre.
3. Az ügyfél elküldi az alkalmazásproxy-szolgáltatás lekéri az egyszerű felhasználónév (UPN) és a biztonsági egyszerű szolgáltatásnév (SPN) a jogkivonatot, majd arra utasítja az alkalmazásproxy-összekötő a kérést.
4. Ha egyszeri bejelentkezésre van beállítva, az összekötő a felhasználó nevében szükséges további hitelesítést hajt végre.
5. Az összekötő a helyszíni alkalmazások elküldi a kérelmet.  
6. A válasz alkalmazásproxy-szolgáltatás és -összekötő keresztül küld el a felhasználó számára.

### <a name="single-sign-on"></a>Egyszeri bejelentkezés
Az Azure AD-alkalmazásproxy biztosít az egyszeri bejelentkezés (SSO) integrált Windows-hitelesítéssel (IWA) használó alkalmazásokhoz, vagy a jogcímbarát alkalmazásokhoz. Ha az alkalmazás integrált Windows-Hitelesítést, az alkalmazásproxy megszemélyesít a Kerberos által korlátozott delegálás használata a egyszeri Bejelentkezéses felhasználó. Ha egy megjelölt jogcímbarát alkalmazáshoz, amely megbízik az Azure Active Directory, egyszeri Bejelentkezéses működik, mivel a Azure AD által már hitelesíteni a felhasználót.

Kerberos kapcsolatos további információkért lásd: [összes meg szeretné ismerni a kapcsolatos a Kerberos által korlátozott delegálás (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

### <a name="managing-apps"></a>Alkalmazások felügyelete
Miután közzétette az alkalmazást az alkalmazásproxy, mint bármely más vállalati alkalmazás az Azure-portálon kezelheti. Használja az Azure Active Directory biztonsági funkciók, például a feltételes hozzáférés és a kétlépéses ellenőrzést, szabályozhatja a felhasználói engedélyek, és testre szabhatja az alkalmazás branding. 

## <a name="get-started"></a>Bevezetés

Alkalmazásproxy konfigurálása előtt ellenőrizze, hogy a támogatott [Azure Active Directory edition](https://azure.microsoft.com/pricing/details/active-directory/) és az Azure AD-címtár, amelynek globális rendszergazdája.

Ismerkedés az alkalmazásproxy két lépésben:

1. [Alkalmazásproxy engedélyezése és konfigurálása az összekötő](active-directory-application-proxy-enable.md).    
2. [Alkalmazások közzététele](active-directory-application-proxy-publish.md) -a helyszíni alkalmazások beszerzéséhez közzétett és elérhető távolról a gyors és egyszerű varázsló segítségével.

## <a name="whats-next"></a>A következő lépések
Miután az első alkalmazás közzétételéhez nincs sokkal több alkalmazásproxy teheti:

* [Egyszeri bejelentkezés engedélyezése](active-directory-application-proxy-sso-using-kcd.md)
* [Alkalmazások közzététele saját tartománynév használatával](active-directory-application-proxy-custom-domains.md)
* [További tudnivalók az Azure AD-alkalmazásproxy-összekötő](application-proxy-understand-connectors.md)
* [Meglévő helyszíni Proxy kiszolgálók használata](application-proxy-working-with-proxy-servers.md) 
* [Állítsa be egy egyéni kezdőlap](application-proxy-office365-app-launcher.md)

A legújabb híreket és frissítéseket itt találja: [Alkalmazásproxy blog](http://blogs.technet.com/b/applicationproxyblog/).

