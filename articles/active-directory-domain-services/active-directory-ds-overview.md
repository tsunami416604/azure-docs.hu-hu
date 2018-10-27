---
title: Az Azure Active Directory Domain Services áttekintése |} A Microsoft Docs
description: Az Azure Active Directory Domain Services áttekintése
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: ergreenl
ms.openlocfilehash: c424ae1865a710593e509372dc2424c0b8b516a0
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50155167"
---
# <a name="azure-active-directory-ad-domain-services"></a>Az Azure Active Directory (AD) Domain Servicesben
## <a name="overview"></a>Áttekintés
Az Azure infrastruktúra-szolgáltatások lehetővé teszik számos különféle számítástechnikai megoldást helyezhetnek üzembe. Az Azure Virtual Machines, gyors telepíthet, és Ön csak használatalapú díjfizetés. Támogatás Windows, Linux, SQL Server, Oracle, IBM, SAP és a BizTalk használja, telepítheti bármely számítási feladat programnyelvtől függetlenül, szinte bármely operációs rendszerben. Ezeket az előnyöket lehetővé teszi helyszíni üzembe helyezett örökölt alkalmazások áttelepítése az Azure-bA menteni az üzemeltetési költségek.

Kulcsfontosságú az Azure-bA áttelepíteni a helyszíni alkalmazások kezeli ezeket az alkalmazásokat identitás igényeit. Címtáralapú alkalmazásokat előfordulhat, hogy LDAP támaszkodnak az olvasási vagy írási hozzáférés a vállalati címtárban, vagy támaszkodhat a Windows beépített hitelesítés (Kerberos vagy NTLM-hitelesítés) hitelesíti a végfelhasználókat. Windows Server rendszeren futó – üzletági (LOB) alkalmazások tartományhoz csatlakozó gépek, általában telepítve vannak, így biztonságosan a csoportházirend használatával kezelhetők. "Lift-and-shift" a helyszíni alkalmazások a felhőbe a vállalati identitás-infrastruktúrát a függőségeket kell feloldani.

A rendszergazdák gyakran kapcsolja a számukra az Azure-ban üzembe helyezett alkalmazás identitás igényeinek kielégítéséhez a következő megoldások valamelyikét:

* Helyezze üzembe a site-to-site VPN-kapcsolatot az Azure infrastruktúra-szolgáltatások és a vállalati címtár a helyszínen futó számítási feladatok között.
* A vállalati AD-tartomány vagy az erdő infrastruktúra kiterjesztése az Azure virtual machines replika tartományvezérlők beállításával.
* Helyezze üzembe az Azure-ban az Azure virtuális gépként üzembe helyezett tartományvezérlőkhöz önálló tartományhoz.

Ezek a módszerek dochází ke magas költségeket és az adminisztratív terhelést. A rendszergazdák az Azure virtual machines használatával tartományvezérlők üzembe helyezése szükségesek. Emellett szükségük kezelése, biztonságos, patch, figyelése, biztonsági mentés és ezek a virtuális gépek hibaelhárítása. A VPN-kapcsolatok a helyszíni címtár támaszkodás sebezhetők átmeneti hálózati problémák vagy valamilyen okból kimaradás lép az Azure-ban üzembe helyezett számítási feladatok okoz. A hálózati kimaradások viszont alacsonyabb rendelkezésre állását és a korlátozott megbízhatóság ezekhez az alkalmazásokhoz eredményez.

Azure AD tartományi szolgáltatásokat biztosít egyszerűbb alternatív alakítottuk ki.

### <a name="watch-an-introductory-video"></a>Bevezető videó

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Az Azure AD Domain Services bemutatása

Az Azure AD tartományi szolgáltatások például a tartományhoz való csatlakozás, csoport házirend vagy az LDAP, Kerberos/NTLM-hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryval felügyelt tartományi szolgáltatásokat biztosít. A tartományi szolgáltatások anélkül, hogy telepíthet, kezelhet és javítani a tartományvezérlők a felhőben is használhatók. Az Azure AD tartományi szolgáltatásokkal integrálható a meglévő Azure AD-bérlőjében, így lehetővé téve a felhasználók számára a vállalati hitelesítői adataikkal jelentkezhetnek. Emellett használhatja meglévő csoportok és felhasználói fiókok való biztonságos hozzáférés a erőforrásokhoz, ezáltal biztosítható a egyenletesebb "lift-and-shift" az Azure Infrastruktúraszolgáltatások a helyszíni erőforrásokkal.

Függetlenül attól, hogy az Azure AD-bérlő csak felhőalapú, vagy a helyszíni Active Directoryval szinkronizált Azure AD tartományi szolgáltatások funkcióit problémamentesen működik.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>A szervezet kizárólag felhőalapú Azure AD tartományi szolgáltatások

Kizárólag felhőalapú Azure AD-bérlő (gyakran nevezik "felügyelt bérlők") nem rendelkezik bármilyen helyszíni identitás erőforrásigényét. Más szóval felhasználói fiókok, a jelszavakat és a csoporttagságok az összes natív módon felhőalapú -, létrehozása és felügyelete az Azure ad-ben. Érdemes lehet egy pillanatra, hogy Contoso kizárólag felhőalapú Azure AD-bérlővel. Amint az alábbi ábrán látható, a Contoso rendszergazda konfigurált egy virtuális hálózatot az Azure infrastruktúra-szolgáltatásokban. Alkalmazások és a server számítási feladatainak Azure-beli virtuális gépeken a virtuális hálózatban lévő vannak telepítve. Mivel a Contoso egy kizárólag felhőalapú bérlők, az összes felhasználói identitások, a hitelesítő adatok és a csoporttagságok létrehozása és felügyelete az Azure ad-ben.

![Az Azure AD Domain Services áttekintése](./media/active-directory-domain-services-overview/aadds-overview.png)

A Contoso rendszergazda engedélyezheti az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő, és tegye elérhetővé a tartományi szolgáltatások, a virtuális hálózatban lévő. Azt követően az Azure AD Domain Services felügyelt tartomány kiépítését, és elérhetővé teszi azokat a virtuális hálózatban. Minden felhasználói fiókok, csoporttagságok és felhasználói hitelesítő adatokat a Contoso Azure AD-bérlő elérhető is elérhetők az újonnan létrehozott tartományban. Ez a funkció lehetővé teszi, hogy a tartományi hitelesítő adataik használatával vállalati – például, amikor távolról csatlakozik a tartományhoz csatlakoztatott gépeket távoli asztalon keresztül bejelentkezni a munkahelyi felhasználók. A rendszergazdák helyezhet üzembe a meglévő csoporttagságok használatával tartományban lévő erőforrásokhoz való hozzáférést. A virtuális hálózaton lévő virtuális gépekre telepített alkalmazások használhatják a szolgáltatások, mint például a tartományhoz való csatlakozás, az LDAP olvasási, LDAP kötés, NTLM és Kerberos-hitelesítéshez és a csoportházirend.

A felügyelt tartományhoz az Azure AD tartományi szolgáltatások által üzembe helyezett néhány következő aspektusait a következők:

* A Contoso informatikai rendszergazda nem kell kezelése, javítása vagy figyelheti ezt a tartományt vagy a felügyelt tartomány minden tartományvezérlője.
* Hiba esetén nem kell a tartomány Active Directory-replikáció kezelésére. Felhasználói fiókok, csoporttagság és hitelesítő adatait a Contoso Azure AD-bérlő automatikusan elérhetők a felügyelt tartományon belül.
* Mert a tartomány kezeli az Azure AD tartományi szolgáltatások, Contoso a rendszergazda nem rendelkezik tartományi rendszergazdaként vagy vállalati rendszergazdai jogosultságokkal ezen a tartományon.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Az Azure AD tartományi szolgáltatások hibrid cégekhez és vállalkozásokhoz
A hibrid informatikai infrastruktúrával rendelkező szervezeteknek felhőbeli erőforrásokat és a helyszíni erőforrások felhasználását. Az ilyen szervezeteknek szinkronizálja a azonosító adatok a helyszíni címtárból az Azure AD-bérlővel. Hibrid szervezetek hely több áttelepíteni, a felhőbe, különösen a régi címtáralapú alkalmazások, a helyszíni alkalmazásaikat az Azure AD tartományi szolgáltatások őket hasznos lehet.

Telepítve van az litware Corporation [az Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md), hogy szinkronizálja a azonosító adatok a helyszíni címtárból az Azure AD-bérlővel. Az azonosító adatokat szinkronizált felhasználói fiókok, a hitelesítési adatok kivonatait hitelesítés (jelszó-szinkronizálás) és a csoporttagságok tartalmazza.

> [!NOTE]
> **A jelszó-szinkronizálás használata kötelező a hibrid Azure AD tartományi szolgáltatásokat használó szervezetek**. Ez a követelmény oka, hogy a felhasználói hitelesítő adatok szükségesek a felügyelt tartomány Azure AD tartományi szolgáltatások által biztosított NTLM vagy Kerberos-hitelesítési módszerek a felhasználók hitelesítéséhez.
>
>

![Az Azure AD Domain Services for Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A fenti ábrán látható, hogyan teszi a szervezetek számára a hibrid informatikai infrastruktúrát, például Litware Corporation, az Azure AD tartományi szolgáltatásokat. Litware's tartományszolgáltatások igényel server számítási feladatok és alkalmazások helyezik üzembe egy virtuális hálózatot az Azure infrastruktúra-szolgáltatásokban. Litware's rendszergazda engedélyezése az Azure AD tartományi szolgáltatásokat az Azure AD-bérlővel, és válassza ki a virtuális hálózatban lévő elérhetővé egy felügyelt tartományon. Mivel Litware egy szervezet a hibrid informatikai infrastruktúrát, felhasználói fiókok, csoportok és hitelesítő adatokat az Azure AD-bérlő a helyszíni címtárból szinkronizált meg. Ez a funkció lehetővé teszi, hogy a felhasználók számára a tartományi hitelesítő adataik használatával vállalati – például, ha távolról csatlakozik a gépek a tartományhoz csatlakoztatott távoli asztalon keresztül bejelentkezni. A rendszergazdák helyezhet üzembe a meglévő csoporttagságok használatával tartományban lévő erőforrásokhoz való hozzáférést. A virtuális hálózaton lévő virtuális gépekre telepített alkalmazások használhatják a szolgáltatások, mint például a tartományhoz való csatlakozás, az LDAP olvasási, LDAP kötés, NTLM és Kerberos-hitelesítéshez és a csoportházirend.

A felügyelt tartományhoz az Azure AD tartományi szolgáltatások által üzembe helyezett néhány következő aspektusait a következők:

* A felügyelt tartomány egy különálló tartományban. Akkor sem bővítményeként Litware's helyszíni tartománnyal.
* Litware's rendszergazdának nem kell kezelni, patch, vagy figyelheti a felügyelt tartományhoz tartozó tartományvezérlőket.
* Hiba esetén nem kell, ez a tartomány Active Directory-replikáció kezelésére. Felhasználói fiókok, csoporttagság és hitelesítő adatok Litware's a helyszíni címtárból vannak szinkronizálva az Azure AD az Azure AD Connect használatával. Ezen felhasználói fiókok, csoporttagság és hitelesítő adatok automatikusan elérhetők a felügyelt tartományban.
* Mert az Azure AD tartományi szolgáltatások, Litware's kezeli a tartományi rendszergazda nem rendelkezik tartományi rendszergazdaként vagy vállalati rendszergazdai jogosultságokkal ezen a tartományon.

## <a name="benefits"></a>Előnyök
Az Azure AD tartományi szolgáltatásokkal a következő előnyöket élvezheti:

* **Egyszerű** – az Azure infrastruktúraszolgáltatások néhány egyszerű kattintással üzembe helyezett virtuális gépek identitás igényeit is teljesíti. Nem kell üzembe helyezése és kezelése a személyazonosság-infrastruktúra az Azure-ban vagy a beállítás kapcsolattal a helyszíni identitás-infrastruktúrát.
* **Integrált** – Azure AD tartományi szolgáltatások szorosan integrálódik az Azure AD-bérlőben. Mostantól használhatja az Azure AD a modern alkalmazások és a hagyományos címtáralapú alkalmazásokat igényeihez caters integrált, felhőalapú és nagyvállalati könyvtárként.
* **Kompatibilis** – Azure AD tartományi szolgáltatások a Windows Server Active Directory bevált vállalati szintű infrastruktúrája épül. Ezért az alkalmazások a kompatibilitás a Windows Server Active Directory szolgáltatásokkal nagyobb mértékű is támaszkodik. Nem minden funkciója elérhető a Windows Server AD el jelenleg az Azure AD tartományi szolgáltatásokat. Elérhető funkciók azonban a megfelelő Windows Server AD funkciókat, amelyeket összeköthet a helyszíni-infrastruktúrájában kompatibilisek lesznek. Az LDAP, Kerberos, NTLM, a csoportházirend és tartományi csatlakozás képességeket egy érett ajánlat, amely a tesztelt és a Windows Server különböző kiadásai keresztül is jelent.
* **Költséghatékony** – az Azure AD tartományi szolgáltatások, elkerülheti a társított kezelése a személyazonosság-infrastruktúra támogatja a hagyományos címtáralapú alkalmazásokat infrastruktúra és a felügyeleti terheket. Helyezze át ezeket az alkalmazásokat az Azure infrastruktúra-szolgáltatásokat, és kihasználhatják a jelentősebb megtakarítást tesznek, a üzemeltetési költségek.


## <a name="next-steps"></a>További lépések
### <a name="learn-more-about-azure-ad-domain-services"></a>További információ az Azure AD tartományi szolgáltatások
* [Szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvei](active-directory-ds-scenarios.md)
* [Ismerje meg, ha az Azure AD Domain Services megfelelő-e a használati esetek](active-directory-ds-comparison.md)
* [Megismerheti, hogyan szinkronizálja az Azure AD tartományi szolgáltatásokat az Azure AD-címtár](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Ismerkedés az Azure AD tartományi szolgáltatásokkal
* [Az Azure Portalon az Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started.md)
