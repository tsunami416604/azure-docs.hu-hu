---
title: Egyéni tartományok az Azure AD alkalmazásproxyban | Microsoft dokumentumok
description: Egyéni tartományok konfigurálása és kezelése az Azure AD alkalmazásproxyban.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481381"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Egyéni tartományok konfigurálása az Azure AD alkalmazásproxyval

Amikor közzétesz egy alkalmazást az Azure Active Directory alkalmazásproxyn keresztül, külső URL-címet hoz létre a felhasználók számára. Ez az URL-cím kapja az alapértelmezett *tartományt yourtenant.msappproxy.net.* Ha például egy *Költségek* nevű alkalmazást tesz közzé a *Contoso*nevű bérlőben, a külső URL-cím *https:\//expenses-contoso.msappproxy.net.* Ha *msappproxy.net*helyett saját tartománynevet szeretne használni, egyéni tartományt is beállíthat az alkalmazáshoz. 

## <a name="benefits-of-custom-domains"></a>Az egyéni tartományok előnyei

Célszerű egyéni tartományokat beállítani az alkalmazásokhoz, amikor csak lehetséges. Az egyéni tartományok használatának néhány oka a következő:

- Az alkalmazások közötti kapcsolatok a vállalati hálózaton kívül is működnek. Egyéni tartomány nélkül, ha az alkalmazás az alkalmazásproxyn kívüli célokhoz kódolt belső hivatkozásokkal rendelkezik, és a hivatkozások külsőleg nem feloldhatók, akkor megszakadnak. Ha a belső és külső URL-ek azonosak, elkerülheti ezt a problémát. Ha nem tudja használni az egyéni tartományokat, olvassa el a probléma megoldásának egyéb módjait az [Azure AD alkalmazásproxyval közzétett alkalmazások hoz létre a kódkódolását](../application-proxy-link-translation.md) című témakörben. 
  
- A felhasználók könnyebb enélkedhetnek, mivel ugyanazzal az URL-címmel érhetik el az alkalmazást a hálózaton belülről vagy kívülről. Nem kell különböző belső és külső URL-eket megtanulniuk, és nem kell nyomon követniük az aktuális helyüket. 

- Szabályozhatja a márkajelzést, és létrehozhatja a kívánt URL-címeket. Az egyéni tartomány segíthet a felhasználók bizalmának kiépítésében, mivel a felhasználók *a msappproxy.net*helyett ismerős nevet látnak és használnak.

- Egyes konfigurációk csak egyéni tartományokkal működnek. Például egyéni tartományokra van szükség a biztonsági helyességi feltétel jelölési nyelvét (SAML) használó alkalmazásokhoz, például active directory összevonási szolgáltatások (AD FS) használataesetén, de nem tudja használni a WS-Federation szolgáltatást. További információt a [Jogcímbarát alkalmazások használata az Alkalmazásproxyban című témakörben talál.](application-proxy-configure-for-claims-aware-applications.md) 

Ha nem tudja, hogy a belső és külső URL-címek egyeznek, nem olyan fontos, hogy az egyéni tartományok, de továbbra is kihasználhatja a többi előnye. 

## <a name="dns-configuration-options"></a>DNS-konfigurációs beállítások

A DNS-konfiguráció beállítására számos lehetőség van, a követelményektől függően:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Ugyanaz a belső és külső URL, különböző belső és külső viselkedés 

Ha nem szeretné, hogy a belső felhasználók az alkalmazásproxyn keresztül irányíthatók legyenek, beállíthat egy *osztott agyú DNS-t.* A megosztott DNS-infrastruktúra névfeloldáshoz a belső állomásokat egy belső tartománynév-kiszolgálóra, a külső állomásokat pedig egy külső tartománynév-kiszolgálóra irányítja. 

![Maszkolt DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Különböző belső és külső URL-ek 

Ha a belső és a külső URL-címek eltérőek, nem kell konfigurálni a megosztott agy viselkedését, mert a felhasználói útválasztást az URL határozza meg. Ebben az esetben csak a külső DNS-t módosítja, és a külső URL-címet az alkalmazásproxy-végpontra irányítja. 

Amikor egy külső URL-címhez egyéni tartományt választ ki, egy információs sáv ontja a külső DNS-szolgáltatóhoz hozzáadni kívánt CNAME bejegyzést. Ezt az információt mindig megtekintheti az alkalmazás **Application proxy** proxylapján.

## <a name="set-up-and-use-custom-domains"></a>Egyéni tartományok beállítása és használata

A helyszíni alkalmazás egyéni tartomány használatára történő konfigurálásához egy ellenőrzött Azure Active Directory egyéni tartományra, az egyéni tartomány PFX-tanúsítványára és egy helyszíni alkalmazásra van szükség a konfiguráláshoz. 

### <a name="create-and-verify-a-custom-domain"></a>Egyéni tartomány létrehozása és ellenőrzése

Egyéni tartomány létrehozása és ellenőrzése:

1. Az Azure Active Directoryban válassza az **Egyéni tartománynevek lehetőséget** a bal oldali navigációs sávon, majd válassza **az Egyéni tartomány hozzáadása**lehetőséget. 
1. Adja meg az egyéni tartománynevet, és válassza **a Tartomány hozzáadása**lehetőséget. 
1. A tartomány lapon másolja a tartomány TXT rekordadatait. 
1. Nyissa meg a tartományregisztrálót, és hozzon létre egy új TXT rekordot a tartományhoz a másolt DNS-adatok alapján.
1. A tartomány regisztrálása után a tartomány Azure Active Directoryban a tartomány lapján válassza az **Ellenőrzés lehetőséget.** Miután a tartomány állapota **ellenőrzött, használhatja**a tartományt az összes Azure AD-konfigurációk, beleértve az alkalmazásproxy. 

További részletes útmutatásért olvassa [el Az egyéni tartománynév hozzáadása az Azure Active Directory portálon című témakört.](../fundamentals/add-custom-domain.md)

### <a name="configure-an-app-to-use-a-custom-domain"></a>Alkalmazás konfigurálása egyéni tartomány használatára

Az alkalmazás közzététele egyéni tartománnyal rendelkező alkalmazásproxyn keresztül:

1. Egy új alkalmazás, az Azure Active Directoryban válassza **a vállalati alkalmazások** a bal oldali navigációs. Válassza az **Új alkalmazás** lehetőséget. A **helyszíni alkalmazások** szakaszban válassza **a Helyszíni alkalmazás hozzáadása**lehetőséget. 
   
   Ha egy alkalmazás már **a vállalati alkalmazásokban**van, jelölje ki a listából, majd válassza az **Alkalmazásproxy** lehetőséget a bal oldali navigációs sávban. 

2. Az Alkalmazásproxy beállításai lapon adjon meg egy **nevet,** ha saját helyszíni alkalmazást ad hozzá.

3.  A **Belső URL-cím** mezőbe írja be az alkalmazás belső URL-címét.
   
4. A **Külső URL mezőben** jelölje le a listát, és jelölje ki a használni kívánt egyéni tartományt.
   
5. Válassza a **Hozzáadás** lehetőséget.
   
   ![Egyéni tartomány kiválasztása](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Ha a tartomány már rendelkezik tanúsítvánnyal, a **Tanúsítvány** mező megjeleníti a tanúsítvány adatait. Ellenkező esetben jelölje be a **Tanúsítvány** mezőt. 
   
   ![Kattintson ide a tanúsítvány feltöltéséhez](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Az **SSL-tanúsítvány** lapon keresse meg és jelölje ki a PFX tanúsítványfájlt. Adja meg a tanúsítvány jelszavát, és válassza a **Tanúsítvány feltöltése**lehetőséget. A tanúsítványokról további információt az [Egyéni tartományok tanúsítványok szakaszában](#certificates-for-custom-domains) talál.
   
   ![Tanúsítvány feltöltése](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Az egyéni tartománycsak egyszer tölti fel a tanúsítványt. Ezt követően a feltöltött tanúsítvány automatikusan érvénybe lép, amikor más alkalmazásokhoz használja az egyéni tartományt.
   
8. Ha tanúsítványt adott hozzá, az **Alkalmazás proxylapján** válassza a **Mentés lehetőséget.** 
   
9. Az **Alkalmazás proxylapjának** információs sávján jegyezze fel a DNS-zónához hozzáadni kívánt CNAME bejegyzést. 
   
   ![CNAME DNS-bejegyzés hozzáadása](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Kövesse a [DNS-rekordok és rekordkészletek kezelése](../../dns/dns-operations-recordsets-portal.md) az Azure Portal segítségével található utasításokat egy OLYAN DNS-rekord hozzáadásához, amely átirányítja az új külső URL-t a *msappproxy.net* tartományba.
   
11. A DNS-rekord megfelelő beállításának ellenőrzéséhez használja az [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) parancsot annak ellenőrzéséhez, hogy a külső URL-cím elérhető-e, és a *msapproxy.net* tartomány aliasként jelenjen meg.

Az alkalmazás most már be van állítva az egyéni tartomány használatára. A tesztelés vagy a kiadás előtt rendeljen felhasználókat az alkalmazáshoz. 

Egy alkalmazás tartományának módosításához válasszon egy másik tartományt az alkalmazás **proxylapján,** a Külső **URL-cím** legördülő listájából. Szükség esetén töltsön fel egy tanúsítványt a frissített tartományhoz, és frissítse a DNS-rekordot. Ha nem látja a kívánt egyéni tartományt a **külső URL-cím**legördülő listájában, előfordulhat, hogy nem lesz ellenőrizve.

Az alkalmazásproxyval kapcsolatos részletesebb útmutatásért olvassa el [az Oktatóanyag: Helyszíni alkalmazás hozzáadása távoli elérést az Azure Active Directory alkalmazásproxyn keresztül című témakörben.](application-proxy-add-on-premises-application.md)

## <a name="certificates-for-custom-domains"></a>Egyéni tartományok tanúsítványai

A tanúsítvány létrehozza a biztonságos TLS-kapcsolatot az egyéni tartományhoz. 

### <a name="certificate-formats"></a>Tanúsítványformátumok

PFX-tanúsítványt kell használnia, hogy minden szükséges köztes tanúsítvány szerepeljen. A tanúsítványnak tartalmaznia kell a személyes kulcsot.

A tanúsítvány aláírási módszerei nincsenek korlátozva. Az elliptikus görbe kriptográfia (ECC), a tulajdonos alternatív neve (SAN) és más gyakori tanúsítványtípusok támogatottak. 

Helyettesítő karakteres tanúsítványokat is használhat, ha a helyettesítő karakter megegyezik a külső URL-címmel. Helyettesítő karakteres tanúsítványokat kell használnia a [helyettesítő karakteres alkalmazásokhoz.](application-proxy-wildcard.md) Ha a tanúsítványt altartományok elérésére is használni szeretné, az altartomány helyettesítő karaktereit tulajdonosként alternatív névként kell hozzáadnia ugyanabban a tanúsítványban. A * \*.adventure-works.com* tanúsítvány a * \*.apps.adventure-works.com* esetében nem fog működni, hacsak nem ad meg * \*.apps.adventure-works.com-t* tulajdonosi alternatív névként. 

A saját nyilvános kulcsú infrastruktúra (PKI) által kiállított tanúsítványokat is használhatja, ha a tanúsítványlánc telepítve van az ügyféleszközökön. Az Intune telepítheti ezeket a tanúsítványokat felügyelt eszközökre. Nem felügyelt eszközök esetén manuálisan kell telepítenie ezeket a tanúsítványokat. 

Nem javasoljuk a privát legfelső szintű hitelesítés-hitelesítési tanúsítvány használatát, mivel a privát legfelső szintű hitelesítés- és hitelesítésállomást is az ügyfélgépekre kell tolni, ami számos kihívást jelenthet.

### <a name="certificate-management"></a>Tanúsítványkezelés

Minden tanúsítványkezelés az egyes alkalmazásoldalakon keresztül történik. Lépjen az alkalmazás **proxylapjára** a **Tanúsítvány** mező eléréséhez.

Ugyanazt a tanúsítványt több alkalmazáshoz is használhatja. Ha egy feltöltött tanúsítvány egy másik alkalmazással működik, a rendszer automatikusan alkalmazza azt. Az alkalmazás hozzáadásakor vagy konfigurálásakor a rendszer nem kéri, hogy újra töltse fel. 

Amikor egy tanúsítvány lejár, figyelmeztetést kap, amely egy másik tanúsítvány feltöltésére figyelmeztet. Ha a tanúsítványt visszavonják, a felhasználók biztonsági figyelmeztetést kaphatnak az alkalmazás elérésekor. Egy alkalmazás tanúsítványának frissítéséhez keresse meg az alkalmazás **proxylapját,** válassza a **Tanúsítvány**lehetőséget, és töltsön fel egy új tanúsítványt. Ha a régi tanúsítványt más alkalmazások nem használják, a rendszer automatikusan törli. 

## <a name="next-steps"></a>További lépések
* [Engedélyezze az egyszeri bejelentkezést a](application-proxy-configure-single-sign-on-with-kcd.md) közzétett alkalmazásokba az Azure AD-hitelesítéssel.
* [Feltételes hozzáférés engedélyezése](../conditional-access/overview.md) a közzétett alkalmazásokhoz.

