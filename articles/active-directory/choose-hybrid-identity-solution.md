---
title: Válassza ki az Azure hibrid identitáskezelési megoldás |} A Microsoft Docs
description: Ismerje meg a rendelkezésre álló hibrid identitáskezelési megoldások és javaslatok is, hogy a legjobb identitás cégirányítási döntés a szervezete számára.
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
ms.openlocfilehash: 15a28cd5937be103aac888a5fc5485e39854a1b4
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319948"
---
# <a name="microsoft-hybrid-identity-solutions"></a>A Microsoft hibrid identitáskezelési megoldások

[A Microsoft Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) hibrid identitáskezelési megoldások engedélyezéséhez való szinkronizálását a helyszíni címtárobjektumok az Azure AD, miközben továbbra is a felhasználók a helyszíni. Az első döntés, ha tervezi, hogy a helyszíni Windows Server Active Directory szinkronizálása az Azure AD-e használni kívánt identitás szinkronizált vagy összevont identitás. Szinkronizált identitások, és szükség esetén jelszókivonatokat engedélyezése a felhasználók ugyanazt a jelszót használja mind a helyszíni és felhőalapú szervezeti erőforrások eléréséhez. Speciális forgatókönyvek követelményeinek meghatározása, például az egyszeri bejelentkezéses (SSO) vagy a helyszínen az MFA, az is telepíteni kell az Active Directory összevonási szolgáltatások (AD FS) identitásokat vonhat össze. 

Nincsenek elérhető konfigurálásához hibrid identitás több lehetőség. Ez a cikk nyújt segítséget, válassza ki a legmegfelelőbb a szervezete alapján az egyszerű telepítés és az adott identitás- és hozzáférés-kezelési igényeken. Ajánlott identitáskezelési modellt válassza ki a szervezet igényeinek megfelelő meghatározásához vegye figyelembe, szükség idő, a meglévő infrastruktúra, az összetettséget és a költség gondolja. Ezek a tényezők különböző minden szervezet számára, és idővel változhatnak. Azonban a követelmények változásakor, akkor is egy másik identitás modellre rugalmasságot.

> [!TIP]
> Ezek a megoldások összes csoportokban [az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="synchronized-identity"></a>Szinkronizált identitás 

Szinkronizált identitás az Azure AD szinkronizálása a helyszíni címtárobjektumok (felhasználókat és csoportokat) legegyszerűbb módja. 

![Szinkronizált hibrid identitás](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Bár a szinkronizált identitás a legkönnyebb és leggyorsabb mód, a felhasználók továbbra is biztosítani kell egy felhőbeli erőforrásokat külön jelszót. Ennek elkerülése érdekében is (opcionális) [kivonatát, felhasználói jelszavakat szinkronizálja](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) az Azure AD-címtárhoz. Jelszókivonatok lehetővé teszi a felhasználók számára a szervezeti erőforrásokhoz az azonos felhasználónevet és jelszót használják-e a helyszíni és felhőalapú jelentkezzen be. Az Azure AD Connect rendszeresen ellenőrzi a helyszíni címtár és az Azure AD-címtár szinkronizálva tartja változásaira. Ha egy felhasználói attribútum vagy a jelszó megváltozott a helyszíni Active Directory, automatikusan frissül az Azure ad-ben. 

A legtöbb szervezet számára, akiknek csak szükség van a bejelentkezni az Office 365-höz, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások számára engedélyezzék az alapértelmezett jelszó-szinkronizálás beállítást javasoljuk. Ha ez nem működik az Ön számára, szüksége lesz a dönthet arról, hogy átmenő hitelesítést és az AD FS közötti.

> [!TIP]
> A felhasználói jelszavakat a helyszíni Windows Server Active Directory egy kivonatolt érték, amely a tényleges felhasználói jelszó formájában vannak tárolva. A kivonat értéke (a kivonatoló algoritmus) egyirányú matematikai függvény eredménye. Nincs semmilyen metódus vissza a jelszót egyszerű szöveges verziójának egy egyirányú függvény eredménye. Jelentkezzen be a helyszíni hálózat Jelszókivonat nem használható. Ha úgy dönt, jelszavak szinkronizálása, az Azure AD Connect jelszókivonatokat kinyeri a helyszíni Active Directoryból, és alkalmazza azt az Azure AD szinkronizálása előtt a Jelszókivonat feldolgozás további biztonsági. A jelszó-szinkronizálás is használható együtt a jelszóvisszaírás engedélyezése az önkiszolgáló jelszó-visszaállítás, az Azure ad-ben. Emellett engedélyezheti egyszeri bejelentkezés (SSO) a felhasználók számára a vállalati hálózathoz csatlakozó tartományhoz csatlakoztatott számítógépeken. Az egyszeri bejelentkezést az engedélyezett felhasználók csak meg kell adnia a felhőbeli erőforrások biztonságosan érhetik el a felhasználónevet. 
>

## <a name="pass-through-authentication"></a>Átmenő hitelesítés

[Az Azure AD átmenő hitelesítésének](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) egyszerű jelszó érvényesítése megoldást kínál a helyszíni Active Directory használatával az Azure AD-alapú szolgáltatások. Ha a szervezete biztonsági és megfelelőségi szabályzatok nem engedélyezik a küldése a felhasználói jelszavakat, akár egy kivonatolt formában, és csak kell asztali egyszeri bejelentkezés támogatása, tartományhoz csatlakoztatott eszközökön, ajánlott átmenő hitelesítéssel kiértékeléséhez. Az átmenő hitelesítés nem szükséges a DMZ-t, amely az üzembehelyezési infrastruktúra és az AD FS képest egyszerűsíti a környezethez. Amikor a felhasználók bejelentkeznek az Azure AD-vel, ez a hitelesítési módszer érvényesíti közvetlenül a helyi Active Directorybeli felhasználói jelszavakat.

![Átmenő hitelesítés](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Az átmenő hitelesítést nem kell egy összetett hálózati infrastruktúrát, és nem kell a helyszíni jelszavak tárolása a felhőben. Egyszeri bejelentkezés, kombinálva átmenő hitelesítést a valóban integrált élményt nyújt az Azure ad-ben való bejelentkezéskor vagy egyéb felhőszolgáltatásban.

Az átmenő hitelesítés van konfigurálva az Azure AD Connect, amely egy egyszerű helyi ügynök, amely figyeli a jelszó érvényesítése kéréseket használja. Az ügynök a magas rendelkezésre állás és a terheléselosztás több gép könnyen telepíthetők. Mivel minden kimenő csak, esetében nem követelmény az összekötő egy DMZ-ben kell telepíteni. Az összekötő kiszolgáló számítógéphez követelményei a következők:

- A Windows Server 2012 R2 vagy újabb
- Az erdő, amelyen keresztül a rendszer érvényesíti a felhasználók tartományhoz

## <a name="federated-identity-ad-fs"></a>Összevont identitás (AD FS)

Hogyan felhasználók férnek hozzá az Office 365-höz és más felhőszolgáltatásokat pontosabban állíthat be a címtár-szinkronizálás használata egyszeri bejelentkezéshez (SSO) [Active Directory összevonási szolgáltatások (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server). A felhasználói bejelentkezéseket a az AD FS Összevonás delegálja a hitelesítést egy helyszíni kiszolgálón, amely ellenőrzi a felhasználói hitelesítő adatokat. Ebben a modellben a helyi Active Directorybeli hitelesítő adatokat soha nem továbbítódnak az Azure ad-hez.

![Összevont identitás](./media/choose-hybrid-identity-solution/federated-identity.png)

Más néven identitás-összevonást, a bejelentkezési módszer biztosítja, hogy a felhasználói hitelesítést ellenőrzött helyszíni, és lehetővé teszi a rendszergazdák szigorúbb szintű hozzáférés-vezérlés megvalósításához. Identitás-összevonást az AD FS-sel a legtöbb bonyolult megoldás, és telepíteni kell a helyszíni környezetben további kiszolgálókat. Identitás-összevonást is véglegesítések, 24 x 7 támogatás biztosítása az Active Directory és az AD FS-infrastruktúra. A magas szintű támogatást szükség, mert ha a helyszíni eszközök internetes eléréséhez, tartományvezérlőn, vagy az AD FS-kiszolgálók sem érhető el, felhasználók nem jelentkezhetnek be a felhőalapú szolgáltatások.

> [!TIP]
> Ha úgy dönt, hogy összevonási használja az Active Directory összevonási szolgáltatások (AD FS), igény szerint állíthat be a jelszó-szinkronizálás biztonsági abban az esetben az AD FS-infrastruktúra sikertelen lesz.
>

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok

Az alábbiakban néhány hibrid identitás- és hozzáférés kezelése az általános forgatókönyveinek feltárhatja, hogy melyik hibrid identitás lehetőséget (vagy beállítások) megfelelő lehet az egyes javaslatok.

|Kell:|PWS és egyszeri bejelentkezés<sup>1</sup>| ESP és egyszeri bejelentkezés<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Szinkronizálja az új felhasználó, névjegy és csoportfiókok automatikusan létrehozza a saját helyszíni Active Directory, a felhőbe.|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Állítsa be a hibrid forgatókönyvek Office 365-bérlőhöz|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|A felhasználók bejelentkezhet és elérheti a cloud services használatával a helyszíni jelszó engedélyezése|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Egyszeri bejelentkezés használatával vállalati hitelesítő adatok megvalósítása|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)| ![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png) |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Győződjön meg arról, nincs jelszókivonatokat a felhőben vannak tárolva.| |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|A helyszíni többtényezős hitelesítési megoldások engedélyezése| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Intelligens kártyás hitelesítés támogatása a felhasználók számára<sup>4</sup>| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|
|Jelszó lejárati értesítés megjelenítése, az Office portálon és a Windows 10 asztali verzió| | |![Ajánlott](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> az egyszeri bejelentkezés jelszó-szinkronizálás.
>
> <sup>2</sup> átmenő hitelesítést és egyszeri bejelentkezést. 
>
> <sup>3</sup> összevont egyszeri bejelentkezés használata az AD FS szolgáltatással.
>
> <sup>4</sup> a vállalat nyilvános kulcsokra épülő infrastruktúra, hogy integrálható az AD FS-bejelentkezés tanúsítványokkal. Ezek a tanúsítványok helyreállítható-tanúsítványoknak megbízható kiépítési csatornákról, például a mobileszköz-kezelési vagy a csoportházirend-objektum vagy intelligens kártyás tanúsítványok (beleértve a PIV/CAC-kártyák) vagy a Hello for Business (tanúsítvány-megbízhatósági) keresztül is lehet. Intelligens kártyás hitelesítés támogatásával kapcsolatos további információkért lásd: [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).
>

## <a name="next-steps"></a>További lépések

[További tudnivalók az Azure a koncepció igazolása környezet](https://aka.ms/aad-poc)
[telepítse az Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)
[hibrid identitás szinkronizálhasson figyelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)
