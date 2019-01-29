---
title: 'Az Azure AD tartományi szolgáltatások: Hasonlítsa össze az Azure AD tartományi szolgáltatások házi KÉSZÍTÉSŰ tartományvezérlőkhöz |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services összehasonlítása házi KÉSZÍTÉSŰ tartományvezérlőn
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: ergreenl
ms.openlocfilehash: 5cfcbba58b4e4416a2f1a56adcbe21eeacb15a85
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152155"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Döntse el, ha az Azure AD Domain Services hogyan ideális a használati eset
Az Azure AD tartományi szolgáltatásokkal telepítheti a számítási feladatokat az Azure infrastruktúra-szolgáltatásokban, ne kelljen foglalkoznia az Azure-ban a személyazonosság-infrastruktúra karbantartása. A felügyelt szolgáltatás üzembe helyezése, és felügyelheti saját tipikus Windows Server Active Directory központi eltér. A szolgáltatás egyszerűen üzembe helyezhetők, és automatizált szolgáltatásállapot-figyelést, a javítási biztosít. Gyakori üzembe helyezési forgatókönyvek támogatása érdekében a szolgáltatás folyamatosan világával.

Az Azure AD tartományi szolgáltatásokat használó kell-e a következő anyagok javasoljuk:

* Listájának megtekintéséhez [az Azure AD Domain Services által kínált funkciók](active-directory-ds-features.md).
* Felülvizsgálat közös [központi telepítési forgatókönyv az Azure AD tartományi szolgáltatásokhoz](active-directory-ds-scenarios.md).
* Végül [egy saját munka AD lehetőséget az Azure AD tartományi szolgáltatások összehasonlítása](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Hasonlítsa össze az Azure AD tartományi szolgáltatások házi KÉSZÍTÉSŰ AD tartományhoz az Azure-ban
Az alábbi táblázat segítségével eldöntheti, hogy Azure AD tartományi szolgáltatások használata és kezelése az Azure-ban a saját AD infrastruktúra között.

| **Funkció** | **Az Azure AD tartományi szolgáltatások** | **"Saját munka" AD az Azure-beli virtuális gépeken** |
| --- |:---:|:---:|
| [**Felügyelt szolgáltatás**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Biztonságos üzembe helyezését**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |A rendszergazdának az üzemelő példány biztonságossá tételéhez. |
| [**DNS-kiszolgáló**](active-directory-ds-comparison.md#dns-server) |**&#x2713;**(felügyelt szolgáltatás) |**&#x2713;** |
| [**Tartományi vagy vállalati rendszergazdai jogosultságokkal**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Csatlakozás tartományhoz**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Tartomány hitelesítése NTLM és Kerberos használatával**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Kerberos által korlátozott delegálás**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|resource-based|erőforrás-alapú & fiók-alapú|
| [**Egyéni Szervezetiegység-struktúrája**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Sémakiterjesztések**](active-directory-ds-comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**AD-tartomány vagy erdő megbízhatónak tekinti**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**LDAP olvasása**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**Biztonságos LDAP (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**LDAP-írás**](active-directory-ds-comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Csoportházirend**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**A földrajzilag elosztott üzembe**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Felügyelt szolgáltatás
A Microsoft által felügyelt Azure AD Domain Services-tartomány. Nem kell aggódnia a javítások, frissítések, figyelés, a biztonsági mentések, és a tartomány rendelkezésre állásának biztosítása. Ezek a felügyeleti feladatok érhetők el szolgáltatásként a Microsoft Azure által a felügyelt tartományra.

#### <a name="secure-deployments"></a>Biztonságos üzembe helyezését
A felügyelt tartomány biztonságosan zárolva megfelelően a Microsoft biztonsági javaslatokat AD központi telepítésekhez. Ezeket a javaslatokat AD termékcsoport évtizedes tapasztalattal, mérnöki és a kiegészítő AD központi telepítések vezethető vissza. Saját munka üzemelő példánya esetében, lépésekkel kell adott üzembe helyezés le/secure zárolni az üzemelő példány.

#### <a name="dns-server"></a>DNS server
Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz felügyelt DNS-szolgáltatást tartalmaz. Az "AAD DC rendszergazdák" csoport tagjai kezelheti a DNS a felügyelt tartományon. A csoport tagjai teljes DNS-felügyeleti jogosultságokat kapnak a felügyelt tartományhoz. DNS-kezelés használatával végezheti el a Távoli kiszolgálófelügyelet eszközei (RSAT) csomagban található a "DNS-felügyeleti konzol".
[További információ](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Tartományi vagy vállalati rendszergazdai jogosultságokkal
Ezek emelt szintű jogosultságokkal a felügyelt AAD-DS-tartomány nem érhető el. A felügyelt tartományok e emelt szintű jogosultságokkal nem állítható rendszerbe, AAD-DS-en végzi igénylő alkalmazásokhoz. Kisebb részhalmazát rendszergazdai jogosultságokkal az "AAD DC rendszergazdák" nevű delegált felügyeleti csoport tagjai számára érhető el. Ezek a jogosultságok például jogosultsága a DNS konfigurálása, a csoportházirend konfigurálásához, a tartományhoz csatlakoztatott gépeket stb rendszergazdai jogosultságokhoz juthat.

#### <a name="domain-join"></a>Csatlakozás tartományhoz
Csatlakozhat a virtuális gépek a felügyelt tartományba, hogy számítógépeket csatlakoztasson AD-tartomány hasonló.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Tartomány hitelesítése NTLM és Kerberos használatával
Az Azure AD tartományi szolgáltatásokkal használhatja a vállalati hitelesítő adatokat a felügyelt tartományon való hitelesítéséhez. Hitelesítő adatok az Azure AD-bérlő szinkronban őrzi meg. A szinkronizált bérlők számára az Azure AD Connect biztosítja, hogy a helyszíni hitelesítő adatokkal végzett módosítások szinkronizálódnak, az Azure ad-hez. A telepítés pedig saját munka tartomány, szükség lehet beállítása az AD-tartomány megbízhatósági kapcsolattal a helyszíni AD-felhasználók számára a vállalati hitelesítő adataikkal hitelesítést. Azt is megteheti szükség lehet annak érdekében, hogy a felhasználói jelszavakat az Azure-tartomány vezérlőhöz tartozó virtuális gépek és szinkronizálása az Active Directory-replikáció beállításához.

#### <a name="kerberos-constrained-delegation"></a>Kerberos által korlátozott delegálás
Nincs "tartományi rendszergazda" jogosultsággal az Active Directory Domain Services által felügyelt tartományokhoz. Fiók-alapú (hagyományos) Kerberos által korlátozott delegálást, ezért nem konfigurálható. A biztonságosabb erőforrás-alapú korlátozott delegálás azonban konfigurálhatja.
[További információ](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Egyéni Szervezetiegység-struktúrája
Az "AAD DC rendszergazdák" csoport tagjai hozhat létre egyéni szervezeti egységek, a felügyelt tartományban. Egyéni szervezeti egységek létrehozására jogosult felhasználók számára a szervezeti egység keresztül kapnak teljes körű rendszergazdai jogosultságokkal.
[További információ](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Sémabővítmények
Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz alap sémája nem terjeszthetők ki. Ezért a bővítmények számára az AD-séma (például. új attribútumok alapján a user objektum) használó alkalmazások vissza, nem tudja, és áttért az AAD-DS-tartománynak.

#### <a name="ad-domain-or-forest-trusts"></a>AD-tartományhoz vagy erdőszintű megbízhatóság
Bizalmi kapcsolatok (bejövő vagy kimenő) más tartományokkal való beállítása nem lehet konfigurálni a felügyelt tartományok. Ezért erőforrás erdő központi telepítési forgatókönyv nem használható az Azure AD Domain Services. Központi telepítések, ahol inkább nem szinkronizálja a jelszavakat az Azure AD hasonlóan az Azure AD Domain Services nem használható.

#### <a name="ldap-read"></a>LDAP-Olvasás
A felügyelt tartomány támogatja az LDAP, olvassa el a számítási feladatok. Ezért a felügyelt tartomány a LDAP olvasási műveleteket végző alkalmazásokat helyezhet üzembe.

#### <a name="secure-ldap"></a>Secure LDAP
Konfigurálhatja az Azure AD tartományi szolgáltatásokat biztosít, többek között az interneten keresztül, a felügyelt tartomány secure LDAP-hozzáférését.
[További információ](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>LDAP Write
A felügyelt tartomány felhasználói objektum írásvédett. Felhasználói objektum attribútumokat LDAP írási műveleteket végző alkalmazások, ezért nem működnek a felügyelt tartományhoz. Ezenkívül a felhasználói jelszavakat nem módosítható a felügyelt tartományban. Egy másik példa lehet csoporttagságok vagy felügyelt tartományon belül, ami nem engedélyezett csoportattribútumok módosítása. Azonban a felhasználói attribútumok vagy a jelszavak (a PowerShell vagy az Azure portal) keresztül az Azure AD-ben végrehajtott módosításokat, vagy a helyszíni AD a rendszer szinkronizálja a felügyelt AAD-DS-tartományhoz.

#### <a name="group-policy"></a>Csoportházirend
Nincs beépített csoportházirend-objektum minden "AADDC számítógépek" és "AADDC felhasználók" tárolók. A beépített csoportházirend-objektumokat a csoportházirend testre szabhatja. Az "AAD DC rendszergazdák" csoport tagja is egyéni csoportházirend-objektumok létrehozása és létező szervezeti egységek (beleértve az egyéni szervezeti egységek) történő kapcsolás céljából.
[További információ](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Földrajzilag elosztott üzemelő példányok
Az Azure AD tartományi szolgáltatásokat a felügyelt tartományok az Azure-ban egyetlen virtuális hálózaton elérhetők. A tartományvezérlők elérhetők több Azure-régiókban világszerte igénylő forgatókönyvek esetén beállítása az Azure IaaS virtuális gépeken futó tartományvezérlők lehet a jobb megoldás.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>"Saját munka" (DIY) AD központi telepítési beállítások
Üzembe helyezési használati esetek esetében van szüksége a Windows Server AD-telepítéssel által kínált funkciók némelyike rendelkezhet. Ezekben az esetekben érdemes lehet az alábbi saját munka (DIY) lehetőségek közül:

* **Önálló cloud tartományhoz:** Beállíthat egy különálló "felhőtartomány" használatával az Azure virtuális gépek, amelyek tartományvezérlőként lett konfigurálva. Ez az infrastruktúra nem integrálható a helyszíni AD-környezetet. Ez a beállítás megköveteli egy külön készletét "felhőalapú hitelesítő adatok" bejelentkezési/felügyeletét a felhőben lévő virtuális gépeket.
* **Erőforrás erdőszintű központi telepítése:** Beállíthat egy tartományhoz a erőforrás erdő topológia az Azure virtual machines konfigurálásra tartományvezérlőként. Ezután konfigurálhat egy AD megbízhatósági kapcsolat a helyszíni AD-környezetet. Tartományhoz csatlakozó számítógépek (az Azure VM-EK) is a felhőben az erőforráserdőben. Felhasználói hitelesítés vagy keresztül történik egy VPN/ExpressRoute-kapcsolat a helyszíni címtár.
* **A helyszíni tartomány kiterjesztése az Azure-bA:** A VPN/ExpressRoute kapcsolattal a helyszíni hálózat Azure-beli virtuális hálózathoz kapcsolódhat. Ez a beállítás lehetővé teszi, hogy az Azure virtuális gépek tartományhoz csatlakoztatni a helyszíni AD. Egy másik lehetőség, hogy az Azure-beli virtuális gépként a helyszíni tartomány replika tartományvezérlők előléptetése. Ezután beállíthat is azt replikálja a helyszíni címtár VPN/ExpressRoute kapcsolaton keresztül. Ebben a telepítési módban hatékonyan kiterjeszti a helyszíni tartomány az Azure-bA.

> [!NOTE]
> Előfordulhat, hogy határozhatja meg, hogy egy házi KÉSZÍTÉSŰ lehetőség van olyan a központi telepítési használati esetek. Fontolja meg [visszajelzés megosztása](active-directory-ds-contact-us.md) segítsen megérteni, milyen funkciókat segít az Azure AD tartományi szolgáltatásokat a jövőben választotta. A visszajelzése segít fejlesztheti tovább a szolgáltatást, hogy jobban illeszkedjen az üzembe helyezésben, és a használati eseteket.
>
>

Microsoft közzétette [központi telepítése Windows Server Active Directory az Azure Virtual Machinesben irányelvek](https://msdn.microsoft.com/library/azure/jj156090.aspx) tanácsokat önálló telepítés megkönnyítése érdekében.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Szolgáltatások – Azure AD tartományi szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyv – Azure AD tartományi szolgáltatások](active-directory-ds-scenarios.md)
* [Útmutató a Windows Server Active Directory-Azure-beli virtuális gépek üzembe helyezése](https://msdn.microsoft.com/library/azure/jj156090.aspx)
