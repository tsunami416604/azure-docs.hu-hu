---
title: "Azure AD tartományi szolgáltatások: Hasonlítsa össze az Azure AD tartományi szolgáltatások DIY tartományvezérlőn |} Microsoft Docs"
description: "Azure Active Directory tartományi szolgáltatások összehasonlítása DIY tartományvezérlőn"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: 09a68c7f4e7169a6ca02e33e89e0f048155fa88c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>A használati esetnek megfelelő kiválasztásáról, ha az Azure AD tartományi szolgáltatások
Az Azure AD tartományi szolgáltatásokkal telepítheti is az Azure infrastruktúra-szolgáltatásokat, a munkaterhelés nem kell foglalkoznia az identitás-infrastruktúra az Azure-ban karbantartása. A felügyelt szolgáltatás eltér a szokásos Windows Server Active Directory központi telepítéséhez, telepítheti és felügyelheti a saját. A szolgáltatás könnyű üzembe helyezni, és kézbesíti az automatizált állapotfigyelést és javítási. Folyamatosan fejlődik azt a szolgáltatás használatát a gyakori telepítési forgatókönyvekben.

Határozza meg, hogy Azure AD tartományi szolgáltatások a következő anyagok javasoljuk:

* A lista [Azure AD tartományi szolgáltatások által kínált szolgáltatások](active-directory-ds-features.md).
* Felülvizsgálati közös [az Azure AD tartományi szolgáltatásokhoz központi telepítési forgatókönyvek](active-directory-ds-scenarios.md).
* Végezetül [összehasonlítása a saját munka AD lehetőséget az Azure AD tartományi szolgáltatások](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Hasonlítsa össze az Azure AD tartományi szolgáltatások DIY AD tartományhoz az Azure-ban
Az alábbi táblázat segítségével eldöntheti, hogy Azure AD tartományi szolgáltatások és a saját Azure AD-infrastruktúra kezelése között.

| **Funkció** | **Azure AD tartományi szolgáltatások** | **"Saját munka" AD az Azure virtuális gépeken** |
| --- |:---:|:---:|
| [**Felügyelt szolgáltatás**](active-directory-ds-comparison.md#managed-service) |**&#x2713;;** |**& #x 2715;** |
| [**Biztonságos telepítéshez**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;;** |A rendszergazdának a üzembe. |
| [**DNS-kiszolgáló**](active-directory-ds-comparison.md#dns-server) |**& #x 2713;**  (a felügyelt) |**&#x2713;;** |
| [**Tartományi vagy vállalati rendszergazdai jogosultságokkal**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**& #x 2715;** |**&#x2713;;** |
| [**Csatlakozás tartományhoz**](active-directory-ds-comparison.md#domain-join) |**&#x2713;;** |**&#x2713;;** |
| [**Tartomány hitelesítése NTLM és Kerberos használatával**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;;** |**&#x2713;;** |
| [**Kerberos által korlátozott delegálás**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|erőforrás-alapú|erőforrás-alapú & ügyfélalapú|
| [**Egyéni Szervezetiegység-struktúrája**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;;** |**&#x2713;;** |
| [**Sémakiterjesztések**](active-directory-ds-comparison.md#schema-extensions) |**& #x 2715;** |**&#x2713;;** |
| [**AD-tartomány vagy erdő megbízhatónak tekinti**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**& #x 2715;** |**&#x2713;;** |
| [**LDAP olvasása**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;;** |**&#x2713;;** |
| [**Biztonságos LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;;** |**&#x2713;;** |
| [**LDAP írási**](active-directory-ds-comparison.md#ldap-write) |**& #x 2715;** |**&#x2713;;** |
| [**Csoportházirend**](active-directory-ds-comparison.md#group-policy) |**&#x2713;;** |**&#x2713;;** |
| [**Földrajzilag elosztott központi telepítések**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**& #x 2715;** |**&#x2713;;** |

#### <a name="managed-service"></a>Felügyelt szolgáltatás
A Microsoft által felügyelt Azure AD tartományi szolgáltatások-tartományt. Nem kell aggódnia javítását, frissítések, figyelés, a biztonsági mentések, és a tartomány rendelkezésre állását biztosítja. A felügyeleti feladatok kínálják szolgáltatásként a Microsoft Azure által a felügyelt tartományok.

#### <a name="secure-deployments"></a>Biztonságos telepítéshez
A felügyelt tartományra biztonságosan zárolva van a Microsoft biztonsági javaslatok AD telepítések szerint. Ezek a javaslatok az AD termékcsapatának évtizedekben mérnöki és az azt támogató AD központi telepítések felhasználói felület vezethető vissza. Saját munka telepítések esetén kell venni az adott központi telepítési lépéseket le/biztonságos zárolását a központi telepítés.

#### <a name="dns-server"></a>DNS-kiszolgáló
Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz felügyelt DNS-szolgáltatásokat tartalmazza. A "AAD DC rendszergazdák" csoportba kezelheti a felügyelt tartományra DNS. A csoport tagjai teljes DNS-felügyeleti jogosultságokat kap a felügyelt tartomány számára. DNS-kezelés használatával végezheti el a Távoli kiszolgálófelügyelet eszközei (RSAT) csomagban található a "DNS-felügyeleti konzol".
[További információ](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Tartományi vagy vállalati rendszergazdai jogosultságokkal
Ezek a emelt szintű jogosultságok nem érhető el a az AAD-Directory tartományi szolgáltatások által felügyelt tartományokhoz. Ezek a emelt szintű jogosultságok nem telepíthető az AAD-DS-en igénylő alkalmazások felügyelt tartományok. Rendszergazdai jogosultságokkal kisebb részhalmazát az úgynevezett "AAD DC rendszergazdák" delegált felügyeleti csoport tagjai számára érhető el. Ezek a jogosultságok például jogosultsága a DNS konfigurálása, csoportházirend konfigurálásához, hogy rendszergazdai jogokkal a tartományhoz csatlakozó gépek stb.

#### <a name="domain-join"></a>Csatlakozás tartományhoz
Csatlakozhat a virtuális gépek hasonló hogyan számítógépek csatlakoztatása egy Active Directory-tartománynak a felügyelt tartományra.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Tartomány hitelesítése NTLM és Kerberos használatával
Az Azure AD tartományi szolgáltatásokkal használhatja a munkahelyi hitelesítő adatait a felügyelt tartományra szolgáltatással való hitelesítésre. Hitelesítő adatok szinkronban vannak az Azure AD-bérlő tartanak. Szinkronizált bérlők számára az Azure AD Connect biztosítja, hogy helyszíni hitelesítő adatok végrehajtott változásokat az Azure AD szinkronizálja a rendszer. A saját munka Tartománykonfiguráció esetleg állítsa be az AD-tartomány megbízhatósági kapcsolatban áll a helyszíni AD-hez a felhasználók a vállalati hitelesítő adataikkal hitelesítést. Alternatív megoldásként szükség lehet annak érdekében, hogy a felhasználói jelszavakat szinkronizálni az Azure tartományvezérlő virtuális gépeket AD a replikáció beállítása.

#### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás
Nincs "tartományi rendszergazda" jogosultsággal az Active Directory tartományi szolgáltatások által felügyelt tartományokhoz. Fiók-alapú (hagyományos) Kerberos által korlátozott delegálást, ezért nem konfigurálható. Azonban a biztonságosabb erőforrás-alapú korlátozott delegálás konfigurálhatja.
[További információ](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Egyéni Szervezetiegység-struktúrája
A "AAD DC rendszergazdák" csoportba hozhat létre egyéni szervezeti egységében a felügyelt tartományra. Felhasználók, akik egyéni szervezeti egységek létrehozása a szervezeti egység keresztül kapnak teljes körű rendszergazdai jogosultságokkal.
[További információ](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Sémabővítmények
Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz alap sémája nem bővíthető. Ezért a bővítmények számára az AD-séma (például új attribútumot a user objektum) használó alkalmazások nem lehet vissza, és az AAD-DS tartományok vette.

#### <a name="ad-domain-or-forest-trusts"></a>AD-tartomány vagy erdőszintű megbízhatóság
Felügyelt tartományok nem állítható be, a többi tartományhoz (bejövő vagy kimenő) megbízhatósági kapcsolatok beállításához. Ezért erőforrás erdő telepítési forgatókönyvek Azure AD tartományi szolgáltatások nem használhatók. Ehhez hasonlóan központi telepítések, ha nem szeretné szinkronizálni a jelszavakat az Azure AD Azure AD tartományi szolgáltatások nem használhatók.

#### <a name="ldap-read"></a>LDAP olvasása
A felügyelt tartományra támogatja, olvassa el a munkaterhelések LDAP. Ezért telepíthet alkalmazásokat, amelyek a felügyelt tartományra LDAP olvasási műveleteket végrehajtani.

#### <a name="secure-ldap"></a>Biztonságos LDAP
Konfigurálhatja az Azure AD tartományi szolgáltatások biztonságos LDAP hozzáférést biztosít a felügyelt tartományra vonatkozóan, beleértve az interneten keresztül.
[További információ](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP írási
A felügyelt tartományra esetén a felhasználói objektumok írásvédett. Ezért, amelyek a felhasználói objektum attribútumokat LDAP írási műveleteket hajtanak végre nem működnek a felügyelt tartományhoz. Emellett a felhasználói jelszavakat nem módosítható a felügyelt tartományon belül. Egy másik példa lehet módosítása csoporttagságok vagy csoport attribútumok felügyelt tartományon belül, amely nem engedélyezett. Azonban a felhasználói attribútumok vagy a jelszavak (PowerShell vagy az Azure portálon) Azure AD-ben végrehajtott módosításokat, vagy a helyszíni AD az AAD-DS felügyelt tartományra szinkronizálva.

#### <a name="group-policy"></a>Csoportházirend
Nincs beépített csoportházirend-objektum minden egyes a "AADDC Computers" és "AADDC felhasználók" tárolók. Testre szabhatja a beépített csoportházirend-objektumokat a csoportházirend. A "AAD DC" rendszergazdákra is egyéni csoportházirend-objektumok létrehozása, és kapcsolja őket (beleértve az egyéni ou-k) meglévő szervezeti egységekhez.
[További információ](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Földrajzi megvédheti központi telepítések
Az Azure AD tartományi szolgáltatások felügyelt tartományok érhetők el az Azure-ban egy virtuális hálózaton. Tartományvezérlők elérhető legyen a több Azure-régiók világszerte igénylő forgatókönyvek esetén az Azure IaaS virtuális gépeken tartományvezérlők beállításával lehet a jobb megoldás.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>"Saját munka" (DIY) AD üzembe helyezési lehetőségei
Előfordulhat, hogy telepítési használati esetek kell néhány olyan funkciója, a Windows Server AD telepítése által kínált. Ebben az esetben fontolja meg a következő saját munka (DIY) lehetőségek közül:

* **Önálló felhőalapú tartományt:** "felhőalapú tartományt" használatával az Azure virtuális gépek tartományvezérlőként konfigurált önálló állíthat be. Ez az infrastruktúra nem integrálható a helyszíni AD-környezetben. Ez a beállítás eltérő szabályzatkészletet volna a "felhő hitelesítő adatok" használatával bejelentkezés/felügyelik a felhőben.
* **Erőforrások erdő telepítése:** állíthat be egy tartományba az erőforrás szolgának, használja az Azure virtuális gépek konfigurálásra tartományvezérlőként. Ezután konfigurálja a az AD megbízhatósági kapcsolat a helyszíni AD-környezetben. Tartományhoz való csatlakozást számítógépek (Azure virtuális gépeken) az erőforráserdőbe a felhőben is. Felhasználói hitelesítés vagy keresztül történik egy VPN vagy ExpressRoute-kapcsolat a helyszíni címtár.
* **Terjessze ki a helyi tartomány Azure:** egy Azure virtuális hálózatra csatlakozhat a helyszíni hálózat VPN vagy ExpressRoute kapcsolattal. Ez a beállítás lehetővé teszi, hogy az Azure virtuális gépek tartományhoz csatlakoztatni a helyszíni AD. Egy másik lehetőség a helyszíni tartomány, az Azure virtuális gépként replika tartományvezérlők előléptetése. Majd állíthatja a helyszíni címtár VPN vagy ExpressRoute kapcsolattal replikálásához. Ebben a telepítési módban a helyszíni tartományi hatékonyan kibővíti az Azure-bA.

> [!NOTE]
> Előfordulhat, hogy határozhatja meg, hogy a központi telepítés használati esetek jobban megfelelő DIY lehetőséget. Érdemes lehet [visszajelzés megosztása](active-directory-ds-contact-us.md) és segítsen megérteni a szolgáltatások segítene úgy döntött, hogy Azure AD tartományi szolgáltatásokat a jövőben. Ezzel a visszajelzéssel segít nekünk a szolgáltatást, hogy jobban megfeleljenek az igények és a használati esetek fejleszteni.
>
>

A Microsoft közzétett [telepítése Windows Server Active Directory telepítése Azure virtuális gépekre vonatkozó irányelvek](https://msdn.microsoft.com/library/azure/jj156090.aspx) DIY telepítések megkönnyítése érdekében.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Szolgáltatások – Azure AD tartományi szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvek - Azure AD tartományi szolgáltatások](active-directory-ds-scenarios.md)
* [Windows Server Active Directory az Azure virtuális gépeken telepítési útmutatója](https://msdn.microsoft.com/library/azure/jj156090.aspx)
