---
title: Az Azure Active Directory tartományi szolgáltatások áttekintése |} Microsoft Docs
description: Az Azure Active Directory tartományi szolgáltatások áttekintése
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 9c22ed1cca49e3e8789c3da5b7bd59d9aeb3ce42
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218455"
---
# <a name="azure-active-directory-ad-domain-services"></a>Az Azure Active Directory (AD) tartományi szolgáltatásokban
## <a name="overview"></a>Áttekintés
Azure infrastruktúra-szolgáltatások lehetővé teszik a számos különböző megoldásokat számítástechnikai gyors módon telepítheti. Azure virtuális gépekkel, szinte azonnal telepíthető, és csak által a percet kell fizetnie. A támogatás a Windows, Linux, SQL Server, Oracle, IBM, SAP, és a BizTalk, alapján telepíthet bármilyen munkaterhelést, ismeretcikkekkel, szinte bármilyen operációs rendszeren. Ilyen előnyt lehetővé teszik a helyszíni örökölt központilag telepített alkalmazások áttelepítése az Azure-ba, kívánja, mentheti a működési költségeket.

Kulcsfontosságú áttelepítése a helyszíni alkalmazások az Azure-bA az ezeket az alkalmazásokat identitás igényeinek megfelelően kezeli. Címtár-kompatibilis alkalmazások LDAP támaszkodnak az olvasási vagy írási hozzáférést a vállalati címtárban vagy integrált Windows-hitelesítés (Kerberos vagy NTLM-hitelesítés) való hitelesíti a végfelhasználókat támaszkodnak. Működő Windows Server-üzletági (LOB) alkalmazások általában a tartományhoz csatlakoztatott gépeken vannak telepítve, ezért ezek kezelhetők biztonságosan a csoportházirend használatával. "Növekedési-és-shift" a helyszíni alkalmazások a felhőbe ezeket a függőségeket, a vállalati identitás-infrastruktúrát kell feloldani.

A rendszergazdák gyakran kapcsolja be az Azure szolgáltatásba telepített alkalmazások identitás igényeinek kielégítéséhez az alábbi megoldások egyikét:

* Telepítse az Azure infrastruktúra-szolgáltatásokat és a vállalati címtár a helyszínen futó feladatok közötti pont-pont VPN-kapcsolat.
* A vállalati AD-tartomány vagy erdő infrastruktúra kiterjesztése az Azure virtuális gépek használatával replika tartományvezérlők beállításával.
* Egy önálló tartományhoz az Azure-ban az Azure virtuális gépként telepített tartományvezérlők telepítése.

Ezek a módszerek érinti a magas költség- és adminisztratív terhelés mellett. A rendszergazdák a tartományvezérlőket az Azure virtuális gépek használata szükséges. Emellett szükséges kezelése, biztonságos, javítás, biztonsági mentését, és hibaelhárítása a virtuális gépek. A VPN-kapcsolatok a helyszíni címtár igényel azt eredményezi, az Azure szolgáltatásban az átmeneti hálózati problémák vagy valamilyen okból kimaradás kitéve üzembe helyezett munkaterhelések tekintetében. A hálózati kimaradások pedig alacsonyabb hasznos üzemidő és az alkalmazás csökkentett megbízhatóságának eredményez.

A könnyebb más Azure AD tartományi szolgáltatások célja azt.

### <a name="watch-an-introductory-video"></a>Egy bevezető videó

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="introducing-azure-ad-domain-services"></a>Azure AD tartományi szolgáltatások bemutatása

Azure AD tartományi szolgáltatások által kezelt tartomány szolgáltatásokat, például a tartományhoz való csatlakozást, csoport házirend, az LDAP, Kerberos vagy NTLM-hitelesítés, amelyek a teljes mértékben kompatibilis a Windows Server Active Directory nyújt. A tartományi szolgáltatások telepítése, kezelése és javítására, de a felhőben tartományvezérlők szükségessége nélkül is felhasználhatnak. Azure AD tartományi szolgáltatások integrálható a meglévő Azure AD-bérlőn, így lehetővé teszi a felhasználók számára, hogy jelentkezzen be a vállalati hitelesítő adatok. Emellett használhatja meglévő csoportok és felhasználói fiókok való biztonságos hozzáférés a erőforrásokhoz, biztosítva ezzel a gördülékenyebb "növekedési-és-shift" az Azure infrastruktúra-szolgáltatásokat a helyszíni erőforrásokat.

Függetlenül attól, hogy az Azure AD-bérlő csak felhőalapú vagy a helyszíni Active Directoryval szinkronizált Azure AD tartományi szolgáltatások funkcióit problémamentesen működik.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>A szervezetek csak felhőalapú Azure AD tartományi szolgáltatások

Egy kizárólag felhőalapú Azure AD-bérlő (más néven "felügyelt bérlők") nem rendelkezik a helyszíni identitás erőforrásigényét. Ez azt jelenti felhasználói fiókok, a jelszavakat és a csoporttagságokat az összes natív módon felhőalapú – Ez azt jelenti, hogy létre és kezelhető az Azure ad-ben. Érdemes lehet egy kis ideig, hogy a Contoso egy kizárólag felhőalapú Azure AD-bérlő. A következő ábrán látható módon Contoso-rendszergazda konfigurált virtuális hálózatot az Azure infrastruktúra-szolgáltatásokat. Alkalmazások és a kiszolgáló-munkaterhelések vannak telepítve a virtuális hálózat az Azure virtuális gépeken. Mivel a Contoso egy kizárólag felhőalapú bérlői, minden felhasználói identitások, a hitelesítő adatokat és a csoporttagságokat létrehozása és kezelése az Azure ad-ben.

![Az Azure AD tartományi szolgáltatások áttekintése](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso-rendszergazda engedélyezi az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő, és tegye elérhetővé a tartományi szolgáltatások, a virtuális hálózaton található. Ezt követően az Azure AD tartományi szolgáltatások egy felügyelt tartomány kiépítését, és lehetővé teszi a virtuális hálózat. Összes felhasználói fiókok, a csoporttagságot és a felhasználó hitelesítési adat lesz elérhető, a Contoso Azure AD-bérlő is rendelkezésre áll az újonnan létrehozott tartományban. Ez a funkció lehetővé teszi, hogy a szervezetben lévő felhasználók jelentkezhetnek be a tartományhoz a vállalati hitelesítő adatok – például amikor távolról kapcsolódik a távoli asztalon keresztül a tartományhoz csatlakoztatott számítógépeken. A rendszergazdák építhető ki a tartományban meglévő csoporttagságok használatával erőforrásokhoz való hozzáférést. A virtuális hálózat virtuális gépekre telepített alkalmazások használhatják a szolgáltatások, mint tartományhoz való csatlakozást, LDAP olvasható, LDAP bind, NTLM és Kerberos hitelesítéshez és a csoportházirend.

A felügyelt tartományra Azure AD tartományi szolgáltatások által ellátott néhány jellegzetességei a következők:

* Contoso-rendszergazda nem kell kezelése, javítása vagy ebben a tartományban vagy bármely olyan tartományvezérlőn, a felügyelt tartomány figyeléséhez.
* Nincs szükség erre a tartományra vonatkozó Active Directory-replikáció kezelésére. Felhasználói fiókok, csoporttagságot és hitelesítő adatait a Contoso Azure AD-bérlő érhetők el automatikusan a kezelt tartományon belül.
* Mivel a tartomány kezeli az Azure AD tartományi szolgáltatások, a Contoso a rendszergazda nem rendelkezik tartománygazdai vagy vállalati rendszergazdai jogosultságokkal ezen a tartományon.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>A hibrid szervezetek számára az Azure AD tartományi szolgáltatások
Egy hibrid informatikai infrastruktúrával rendelkező szervezeteknek felhőalapú erőforrásokat és a helyszíni erőforrások felhasználását. Olyan szervezetek identitásinformációi a helyszíni címtárból az Azure AD-bérlő szinkronizálni. Hibrid szervezetek hely több áttelepíteni a felhőbe, különösen a régebbi címtár-kompatibilis alkalmazások, a helyszíni alkalmazások az Azure AD tartományi szolgáltatások hasznosak lehetnek a őket.

Telepítve van a Litware Corporation [az Azure AD Connect](../active-directory/active-directory-aadconnect.md), identitásinformációi a helyszíni címtárból az Azure AD-bérlő szinkronizálni. Az azonosító adatok szinkronizált felhasználói fiókok, a hitelesítő kivonatokat a hitelesítés (jelszó-szinkronizálás) és a csoporttagságokat tartalmazza.

> [!NOTE]
> **Jelszó-szinkronizálás használata az Azure AD tartományi szolgáltatásokat használó hibrid szervezetek kötelező**. Ez a követelmény az oka, hogy a felhasználói hitelesítő adatok szükségesek az Azure AD tartományi szolgáltatások által biztosított a felügyelt tartományra, NTLM vagy Kerberos hitelesítési módszerek segítségével a felhasználók hitelesítésére.
>
>

![Az Azure AD tartományi szolgáltatások a Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

Az előző ábrán egy hibrid informatikai infrastruktúrát, például a Litware Corporation rendelkező szervezetek használatát Azure AD tartományi szolgáltatásokat. Egy virtuális hálózatot az Azure infrastruktúra-szolgáltatásokat litware's alkalmazások és kiszolgálói szolgáltatások, amelyek tartományi szolgáltatások vannak telepítve. Litware's rendszergazda engedélyezi az Azure AD tartományi szolgáltatásokat az Azure AD-bérlő, és tegye elérhetővé a felügyelt tartományhoz, a virtuális hálózaton található. Mivel Litware egy hibrid informatikai infrastruktúra rendelkező szervezeteknél, felhasználói fiókok, csoportok és hitelesítő adatokat az Azure AD-bérlő a helyszíni címtárból szinkronizálja meg. Ez a funkció lehetővé teszi, hogy a felhasználók jelentkezhetnek be a tartományhoz a vállalati hitelesítő adatok – például amikor gépek távolról csatlakozni a tartományhoz csatlakoztatott távoli asztalon keresztül. A rendszergazdák építhető ki a tartományban meglévő csoporttagságok használatával erőforrásokhoz való hozzáférést. A virtuális hálózat virtuális gépekre telepített alkalmazások használhatják a szolgáltatások, mint tartományhoz való csatlakozást, LDAP olvasható, LDAP bind, NTLM és Kerberos hitelesítéshez és a csoportházirend.

A felügyelt tartományra Azure AD tartományi szolgáltatások által ellátott néhány jellegzetességei a következők:

* A felügyelt tartományra egy különálló tartományban. Egy bővítmény Litware meg a helyi tartomány nincs.
* Litware's rendszergazda nem kell kezelni, javítást, vagy a felügyelt tartomány tartományvezérlők figyeléséhez.
* Nincs szükség az ebben a tartományban az Active Directory-replikáció kezelésére. Felhasználói fiókok, a csoporttagságot és a hitelesítő adatok Litware meg a helyszíni címtárból az Azure AD az Azure AD Connecten keresztül szinkronizálódnak. A felhasználói fiókok, a csoporttagságot és a hitelesítő adatok érhetők el automatikusan a kezelt tartományban.
* Mert az Azure AD tartományi szolgáltatások, Litware's kezeli a tartományi rendszergazda nem rendelkezik tartománygazdai vagy vállalati rendszergazdai jogosultságokkal ezen a tartományon.

## <a name="benefits"></a>Előnyök
Az Azure AD tartományi szolgáltatásokkal a következő előnyöket élvezheti:

* **Egyszerű** – tud eleget tenni a Azure infrastruktúra-szolgáltatásokat, néhány kattintással telepített virtuális gépek identitás igényeinek. Nem kell telepíteni, és identitás-infrastruktúra kezelése az Azure vagy a telepítő kapcsolattal a helyszíni identitás-infrastruktúra.
* **Integrált** – Azure AD tartományi szolgáltatások mélyen integrált a Azure AD-bérlő. Most már használhatja az Azure AD a modern alkalmazások és a hagyományos címtár-kompatibilis alkalmazások igényeihez caters integrált, felhőalapú vállalati könyvtárként.
* **Kompatibilis** – Azure AD tartományi szolgáltatások a Windows Server Active Directory megbízható enterprise osztályú infrastruktúrája épül. Ezért az alkalmazások kompatibilisek a Windows Server Active Directory-szolgáltatások nagyobb fokú támaszkodhat. Nem minden szolgáltatások a Windows Server AD jelenleg érhetők el az Azure AD tartományi szolgáltatásokban. Azonban elérhető szolgáltatások kompatibilisek-e a megfelelő Windows Server AD-funkciót, a helyszíni-infrastruktúrájában használ. LDAP, a Kerberos, az NTLM, a csoportházirend és a tartomány illesztési képességeit egy összetett ajánlat, amely tesztelése és Windows Server különböző kiadásai keresztül is jelent.
* **Költséghatékony** – az Azure AD tartományi szolgáltatásokkal, elkerülheti a az infrastruktúra és a felügyeleti terheket társított identitás-infrastruktúra támogatja a hagyományos címtár-kompatibilis alkalmazásokat kezeléséhez. Helyezze át való Azure Infrastruktúraszolgáltatásai ezeket az alkalmazásokat, és jelentősebb megtakarítást tesznek lehetővé a működési költségeket kihasználhassa.


## <a name="next-steps"></a>További lépések
### <a name="learn-more-about-azure-ad-domain-services"></a>További tudnivalók az Azure AD tartományi szolgáltatások
* [Szolgáltatások](active-directory-ds-features.md)
* [Központi telepítési forgatókönyvek](active-directory-ds-scenarios.md)
* [Annak megállapítása, ha Azure AD tartományi szolgáltatások megfelel-e a használati esetek](active-directory-ds-comparison.md)
* [Megérteni, hogyan szinkronizálja az Azure AD tartományi szolgáltatásokat az Azure AD-címtár](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Ismerkedés az Azure AD tartományi szolgáltatásokkal
* [Az Azure portál használatával Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started.md)
