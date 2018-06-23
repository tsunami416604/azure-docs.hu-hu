---
title: Egy Azure hibrid identitáskezelési megoldás kiválasztása |} Microsoft Docs
description: Ismerkedjen meg a rendelkezésre álló hibrid identitáskezelési megoldások és javaslatok csak akkor lehet a legjobb identitás irányítás döntés a szervezete.
keywords: ''
author: jeffgilb
manager: mtillman
ms.reviewer: jsnow
ms.author: billmath
ms.date: 03/02/2018
ms.topic: article
ms.prod: ''
ms.service: azure
ms.technology: ''
ms.assetid: ''
ms.custom: it-pro
ms.openlocfilehash: 905cedd6ee781ca163731a6f71b083acfc11b5eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332371"
---
# <a name="microsoft-hybrid-identity-solutions"></a>Microsoft hibrid identitáskezelési megoldások
[A Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hibrid identitáskezelési megoldások lehetővé teszik a helyszíni címtár objektumainak szinkronizálása az Azure ad szolgáltatással, miközben továbbra is a felhasználók a helyszínen. Az első azt kell eldöntenie, ha a helyszíni Windows Server Active Directory szinkronizálása és az Azure AD tervezési szeretné használni identitás szinkronizált vagy összevont identitás. Szinkronizált identitások, és szükség esetén jelszókivonatait, lehetővé teszi a felhasználók, mind a helyszíni és felhőalapú szervezeti erőforrások eléréséhez használható ugyanazt a jelszót. A speciális forgatókönyv-követelményeinek, például egyszeri bejelentkezéses (SSO) vagy a helyi multi-factor Authentication szolgáltatás használatakor is telepíteni kell az Active Directory összevonási szolgáltatások (AD FS) összevont identitások. 

Nincsenek elérhető hibrid identitásnak számos lehetőség közül választhat. Ez a cikk nyújt segítséget az egyszerű telepítés alapján szervezete számára legjobb közül választhat, és az adott identitások és hozzáférések felügyeletéhez szükséges. Ajánlott identitás modellt a szervezet igényeinek megfelelő meghatározásához vegye figyelembe, szükség gondolja át idő, a meglévő infrastruktúra, az összetettséget és a költségeket. Ezek a tényezők esetében minden egyes szervezet különböző, és idővel változhatnak. Azonban ha a követelmények változnak, akkor is szeretne váltani egy másik identitás modell rugalmasságot.

> [!TIP]
> Ezek a megoldások összes érkeznek által [az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Szinkronizált identitás 
Szinkronizált identitás legegyszerűbben a helyszíni címtár objektumainak (felhasználókat és csoportokat) való szinkronizálásához az Azure ad-val. 

![Szinkronizált hibrid identitás](./media/choose-hybrid-identity-solution/synchronized-identity.png)

A legegyszerűbben és leggyorsabban metódus szinkronizált identitás pedig a felhasználók továbbra is kell egy külön jelszót felhőalapú erőforrások kezelése. Ennek elkerülése érdekében is (opcionális) [kivonatát a felhasználói jelszavakat szinkronizálja](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) az Azure AD-címtárral. Jelszókivonatait szinkronizálása lehetővé teszi a felhasználók számára az azonos felhasználói nevét és jelszavát, hogy használhatják-e a helyszíni szervezeti erőforrások felhőalapú bejelentkezni. Az Azure AD Connect rendszeresen ellenőrzi a módosításokat, és az Azure AD-címtár szinkronizálva tartja a helyszíni címtárba. Amikor egy felhasználói attribútum, vagy a jelszó megváltozott a helyszíni Active Directory, automatikusan frissül az Azure ad-ben. 

A legtöbb szervezet számára, akik csak a felhasználók jelentkezhetnek be az Office 365, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások engedélyezni kell az alapértelmezett jelszó-szinkronizálási beállítás ajánlott. Ha a probléma nem szűnik meg, a lesz szüksége, döntse el, hogy átmenő hitelesítést és az AD FS között.

> [!TIP]
> Felhasználói jelszavak a helyi Windows Server Active Directory a tényleges felhasználói jelszó jelölő kivonatot formájában vannak tárolva. A kivonat értéke (a kivonatoló algoritmus) egyirányú matematikai függvény eredménye. Nincs módszer eredménye egy egyirányú működnek, mint az egyszerű szövegként a jelszavak visszaállítását. Jelentkezzen be a helyszíni hálózat Jelszókivonat nem használható. Ha úgy dönthet, hogy a jelszavak szinkronizálása, az Azure AD Connect jelszókivonatait kibontja a helyszíni Active Directoryból, és alkalmazza azok a jelszókivonatok feldolgozás az Azure AD szinkronizálása előtt további biztonsági. A jelszó-szinkronizálás a jelszóvisszaírás együtt is használható a ahhoz, hogy az önkiszolgáló jelszó-változtatási Azure AD-ben. Emellett engedélyezheti az egyszeri bejelentkezés (SSO) a felhasználók számára a vállalati hálózathoz csatlakoznak a tartományhoz csatlakoztatott számítógépeken. Az engedélyezett felhasználók az egyszeri bejelentkezést, csak kell adjon meg egy felhasználónevet biztonságosan hozzáférhessenek a felhőben található erőforrásokat. 

## <a name="pass-through-authentication"></a>Átmenő hitelesítés
[Az Azure AD átmenő hitelesítés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) egyszerű jelszó érvényesítése megoldást kínál az Azure AD-alapú szolgáltatásokhoz a helyszíni Active Directory használatával. Ha a szervezet biztonsági és megfelelőségi házirendek nem teszik lehetővé a felhasználói jelszavak küldése még kivonatolt formában, és csak asztali SSO tartományhoz csatlakoztatott eszközök támogatásához szükséges, ajánlott átmenő hitelesítést használó kiértékelése. Áteresztő hitelesítés nem szükséges a szegélyhálózaton, amely a telepítési infrastruktúrának, az AD FS-hez képest egyszerűsíti a telepítést. Amikor a felhasználók bejelentkeznek az Azure AD, ezt a hitelesítési módszert érvényesíti közvetlenül szemben a helyszíni Active Directory felhasználók jelszavát.

![Átmenő hitelesítés](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Átmenő hitelesítéssel nincs szükség speciális hálózati infrastruktúra, és nem kell a helyszíni jelszavak tárolása a felhőben. Egyszeri bejelentkezés, kombinálva átmenő hitelesítés valóban integrált élményt biztosít, ha az Azure AD bejelentkezés vagy más felhőalapú szolgáltatásokat.

Áteresztő hitelesítés van beállítva, az Azure AD Connect, amely használ egy egyszerű helyszíni ügynök, amely figyeli a jelszó érvényesítése kéréseket. Az ügynök több gép magas rendelkezésre állás biztosításához és a terheléselosztás könnyen telepíthetők. Mivel az összes kommunikáció kimenő csak, esetében nem követelmény az összekötő telepíteni kell a szegélyhálózaton. A kiszolgáló számítógép az összekötőre vonatkozó követelmények a következők:

- Windows Server 2012 R2 vagy újabb
- Az erdő, amelyen keresztül a felhasználók érvényesítése tartományhoz

## <a name="federated-identity-ad-fs"></a>Összevont identitás (AD FS)
Jobban kézben tartani, hogy a felhasználók miként férhetnek hozzá a Office 365 és más felhőalapú szolgáltatásokat, állíthatja be a címtár-szinkronizálás egyszeri bejelentkezés (SSO) használatával [Active Directory összevonási szolgáltatások (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). A felhasználói bejelentkezéseket a az AD FS összevonását biztosít hitelesítést, amely ellenőrzi az felhasználói hitelesítő adatok a helyi kiszolgálóhoz. Ebben a modellben a helyszíni Active Directory hitelesítő adatok soha nem továbbítódnak az Azure AD.

![Összevont identitás](./media/choose-hybrid-identity-solution/federated-identity.png)

Más néven identitás-összevonást, a bejelentkezési módszer biztosítja, hogy a felhasználói hitelesítést ellenőrzött a helyszínen, és lehetővé teszi a rendszergazdák hozzáférés-vezérlés szigorúbb szintű megvalósítását. Az AD FS identitás-összevonási a legtöbb bonyolult beállítás, és megköveteli, hogy a helyszíni környezetben további kiszolgáló üzembe helyezése. Identitás-összevonást is megmutatják a véglegesítések számát, az Active Directory és az AD FS infrastruktúra 24 x 7 támogatás biztosítása. A magas szintű támogatási szükség, mert ha a helyszíni Internet-hozzáférést, tartományvezérlő, vagy az AD FS-kiszolgálók nem érhetők el, felhasználók nem jelentkezhetnek be a felhőszolgáltatásokhoz.

> [!TIP]
> Ha úgy dönt, hogy összevonási az Active Directory összevonási szolgáltatások (AD FS) használni, opcionálisan állíthatja be a jelszó-szinkronizálás biztonsági abban az esetben, ha az AD FS infrastruktúra nem sikerül.


## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok
Az alábbiakban néhány használt hibrid identitás- és hozzáférés-kezelési forgatókönyveket, mely hibrid identitás beállítás (vagy beállítások) megfelelő lehet az egyes ajánlásokkal.

|Kell:|PWS és az egyszeri bejelentkezés<sup>1</sup>| ESP és az egyszeri bejelentkezés<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Új felhasználói, lépjen kapcsolatba, és a helyszíni Active Directoryban a felhőbe automatikusan létrehozott szinkronizálása.|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Állítsa be a bérlő Office 365 hibrid forgatókönyvek esetén|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|A felhasználók bejelentkezhetnek és elérhetik a helyszíni jelszavakat felhőszolgáltatások|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Egyszeri bejelentkezés vállalati hitelesítő adataival megvalósítása|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Győződjön meg arról, nincs jelszókivonatait a felhőben vannak tárolva.| |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|A helyi multi-factor Authentication hitelesítés megoldások| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Intelligens kártyás hitelesítés támogatása a felhasználók számára<sup>4</sup>| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Jelszó lejárati értesítéseinek megjelenítése az Office-portál és a Windows 10 asztali verzió| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> jelszó-szinkronizálás az egyszeri bejelentkezést. 

> <sup>2</sup> áteresztő hitelesítés és egyszeri bejelentkezést. 

> <sup>3</sup> összevont egyszeri bejelentkezés az AD FS.

> <sup>4</sup> AD FS integrálható a vállalati nyilvános kulcsokra épülő infrastruktúra, hogy a bejelentkezés tanúsítványokkal. Ezek a tanúsítványok lehet például az MDM-mel vagy csoportházirend-objektum vagy intelligens kártyás tanúsítványok (beleértve a PIV/CAC kártyák) vagy Hello for Business (tanúsítvány-megbízhatósági) megbízható létesítési csatornákon keresztül telepített soft-tanúsítványok. Intelligens kártyás hitelesítés támogatásával kapcsolatos további információkért lásd: [ebben a blogban](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>További lépések
[További információ a koncepció igazolása Azure környezetben:](https://aka.ms/aad-poc)

[Az Azure AD Connect telepítése](http://go.microsoft.com/fwlink/?LinkId=615771)

[Hibrid identitásszinkronizálási figyelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

