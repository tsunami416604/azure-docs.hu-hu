---
title: Gyakori kérdések – Azure Active Directory Domain Servicesben |} A Microsoft Docs
description: Az Azure Active Directory Domain Services kapcsolatos gyakori kérdések
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: c2f10b1bf3b61577a2b0616be6fa50f7639559ba
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158914"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Az Azure Active Directory tartományi szolgáltatások: Gyakori kérdések (GYIK)
Ezen a lapon az Azure Active Directory Domain Services kapcsolatos gyakori kérdésekre ad választ. Tartsa vissza a frissítések keresése.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Tekintse meg a [hibaelhárítási útmutatója](active-directory-ds-troubleshooting.md) konfigurálása és felügyelete az Azure AD Domain Services gyakori hibáinak megoldásokhoz.

## <a name="configuration"></a>Konfiguráció
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Létrehozható több felügyelt tartományok egyetlen Azure AD-címtár?
Nem. Csak egyetlen Azure AD tartományi szolgáltatások által kiszolgált egyetlen felügyelt tartományt hozhat létre az Azure AD-címtárban.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Egy Azure Resource Managerbeli virtuális hálózat az Azure AD tartományi szolgáltatások engedélyezése
Igen. Az Azure AD Domain Services engedélyezhető egy Azure Resource Manager virtuális hálózaton. A klasszikus Azure virtuális hálózatok új felügyelt tartományok létrehozása már nem támogatottak.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Át tudom telepíteni a meglévő felügyelt tartományt egy klasszikus virtuális hálózat a Resource Manager virtuális hálózatot?
Jelenleg nem. A Microsoft egy olyan mechanizmust át a meglévő felügyelt tartományt egy klasszikus virtuális hálózat a Resource Manager virtuális hálózatot a későbbiekben fog nyújtani.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Az Azure CSP (Cloud Solution Provider) előfizetés az Azure AD tartományi szolgáltatások engedélyezése
Igen. Tekintse meg, hogyan engedélyezheti [Azure AD tartományi szolgáltatásokat az Azure CSP-előfizetésekben](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezhető az Azure AD tartományi szolgáltatásokat az összevont Azure AD-címtár? A jelszókivonatok Azure ad szinkronizálása nem. Engedélyezheti az Azure AD Domain Servicest a címtárhoz?
Nem. Az Azure AD tartományi szolgáltatások a jelszókivonatokat az NTLM vagy Kerberos felhasználók hitelesítéséhez a felhasználói fiókok hozzá kell férnie. Egy összevont könyvtárban jelszókivonatokat nem kerülnek be az Azure AD-címtárban. Azure AD tartományi szolgáltatásokat, ezért az ilyen az Azure AD-címtár nem működik.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>I elérhetővé teheti az Azure AD tartományi szolgáltatások több virtuális hálózat az előfizetésem?
A szolgáltatás nem támogatja közvetlenül ebben a forgatókönyvben. A felügyelt tartomány egyszerre csak egy virtuális hálózat érhető el. Azonban konfigurálhatja az Azure AD tartományi szolgáltatások más virtuális hálózatokhoz több virtuális hálózatok közötti kapcsolatot. Bemutatja, hogyan [az Azure-beli virtuális hálózatok összekapcsolása](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell-lel az Azure AD Domain Services engedélyezése
Igen. Lásd: [engedélyezése az Azure AD Domain Services a PowerShell-lel](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager-sablon használatával az Azure AD tartományi szolgáltatások engedélyezése
Nem, akkor még nem jelenleg lehet engedélyezni az Azure AD tartományi szolgáltatások sablon használatával. Helyette használhatja az PowerShell, [engedélyezése az Azure AD Domain Services a PowerShell-lel](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz való is hozzáadhatok tartományvezérlőket?
Nem. Az Azure AD tartományi szolgáltatások által biztosított tartomány egy felügyelt tartományon. Nem kell kiépíteni, konfigurálhatja vagy egyéb módon kezelni a tartományvezérlők a tartomány - e felügyeleti tevékenységek szolgáltatás Microsoft által biztosított. Ezért nem adhat hozzá további tartományvezérlőket (olvasási és írási vagy csak olvasható) a felügyelt tartományhoz.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Saját címtárbeli meghívott vendégfelhasználók használhatja az Azure AD Domain Services?
Nem. Az Azure AD directory a meghívott vendégfelhasználók a [az Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) meghívó folyamat szinkronizált be az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ezek a felhasználók jelszavainak azonban nem tárolódnak az Azure AD-címtárhoz. Ezért az Azure AD Domain Services olyan semmilyen módon nem lehet szinkronizálni az NTLM és Kerberos kivonatolja ezeknek a felhasználóknak a felügyelt tartományba. Ennek eredményeképpen ezek a felhasználók nem jelentkezzen be a felügyelt tartományhoz, vagy számítógépek csatlakoztatása a felügyelt tartományhoz.

## <a name="administration-and-operations"></a>Felügyelet és műveletek
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Képes kapcsolódni a tartományvezérlő a távoli asztal használatával felügyelt tartományhoz tartozó?
Nem. Nincs engedélye csatlakozni a távoli asztalon keresztül a felügyelt tartományhoz tartozó tartományvezérlőket. Az "AAD DC rendszergazdák" csoport tagjai felügyelhetik a felügyelt tartomány AD felügyeleti eszközök, például az Active Directory felügyeleti központban (ADAC) vagy AD PowerShell segítségével. Ezek az eszközök a felügyelt tartományhoz csatlakoztatott Windows-kiszolgálón a "Távoli kiszolgálófelügyelet eszközei" funkció használatával telepíthetők.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>E engedélyezte az Azure AD tartományi szolgáltatásokat. Milyen felhasználói fiók használható a tartományhoz való csatlakozás gépek a tartományhoz?
A felügyeleti csoport "AAD DC rendszergazdák" tartományhoz csatlakozó gépek is. Ezenkívül a csoport tagjai, amelyekhez hozzáférést távoli asztali gépek, amelyek a tartományhoz csatlakozott.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Az Azure AD Domain Services által kínált felügyelt tartományhoz tartozó tartományi rendszergazdai jogosultságokkal kell?
Nem. Rendszergazdai jogosultságokkal azon a felügyelt tartomány nem kapnak. "Tartományi rendszergazda" és a "vállalati rendszergazdaként" jogosultságokkal kell használni a tartományon belül nem érhetők el. A tartományi rendszergazda vagy a helyszíni Active Directory vállalati rendszergazdák csoportok tagjai nem is kapnak a felügyelt tartományon tartományi vagy vállalati rendszergazdai jogosultságokkal.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosíthatja a csoporttagságot a felügyelt tartományok LDAP vagy egyéb AD felügyeleti eszközöket használ?
Nem. Az Azure AD tartományi szolgáltatások által kiszolgált tartományok nem lehet módosítani a csoporttagságok. Ugyanez vonatkozik a felhasználói attribútumok. Az Azure ad-ben vagy a helyszíni tartomány csoporttagságok vagy felhasználói attribútumokat azonban módosítható. A módosítások automatikusan szinkronizálódnak, az Azure AD tartományi szolgáltatásokra.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi ideig tart a módosítások I győződjön meg arról, a saját Azure AD-címtár lesznek láthatók a felügyelt tartomány?
Az Azure AD-címtárát az Azure AD felhasználói felületén vagy a PowerShell használatával végzett módosítások szinkronizálódnak a felügyelt tartományra. Ez a szinkronizálási folyamat fut a háttérben. Miután befejeződött a kezdeti szinkronizálás, általában megjelennek a felügyelt tartományhoz az Azure ad-ben végzett módosítások körülbelül 20 percet vesz igénybe.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kiterjesztheti a felügyelt tartományhoz az Azure AD Domain Services által kínált sémája?
Nem. A séma a Microsoft felügyeli a felügyelt tartományhoz. Az Azure AD tartományi szolgáltatások a sémakiterjesztések nem támogatottak.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosítása vagy a felügyelt tartomány DNS-rekordok hozzáadásához?
Igen. Az "AAD DC rendszergazdák" csoport tagjai megkapják DNS-rendszergazda jogosultságokkal a felügyelt tartomány DNS-rekordok módosítása. A felügyelt tartományhoz csatlakoztatott Windows Server rendszert futtató gépen a DNS-kezelő konzolt is használata kezelheti DNS. A DNS-kezelő konzol használatával telepítse a "DNS-kiszolgálói eszközök", amely része a "Távoli kiszolgálófelügyelet eszközei" választható szolgáltatás a kiszolgálón. További információ a [segédprogramok, figyelés és hibaelhárítás DNS](https://technet.microsoft.com/library/cc753579.aspx) érhető el a TechNet webhelyén.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi az a jelszóházirend élettartama egy felügyelt tartományon?
Az alapértelmezett jelszó élettartamát az Azure AD-tartomány szolgáltatásokkal felügyelt tartományban 90 nap. A jelszó élettartama nincs szinkronizálva az Azure ad-ben konfigurált jelszó élettartamát. Ezért előfordulhat, hogy rendelkezik egy olyan helyzetet, ahol felhasználói jelszavak jár le, a felügyelt tartományhoz, de továbbra is érvényesek az Azure ad-ben. Ilyen esetekben a felhasználóknak kell módosítani a jelszavát, az Azure ad-ben, és az új jelszó szinkronizálja a felügyelt tartományra. Emellett a "jelszó-biztosítja-not-jár le" és "user-must-change-password-at-next-logon" attribútumok felhasználói fiókok esetében nem szinkronizálódnak a felügyelt tartományra.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Az Azure AD Domain Services biztosítja a AD fiókzárolási?
Igen. Öt érvénytelen bejelentkezési kísérletek 2 percen belül a felügyelt tartományon miatt 30 percig zárolható lesz egy felhasználói fiókot. Várjon néhány percig a felhasználói fiók automatikusan zárolva. Érvénytelen jelszómegadási kísérletek a felügyelt tartományon nem a felhasználói fiók zárolása az Azure ad-ben. A felhasználói fiók ki van zárva, csak az Azure AD tartományi szolgáltatások által felügyelt tartományon belül.

## <a name="billing-and-availability"></a>Számlázás és a rendelkezésre állás
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Az Azure AD tartományi szolgáltatások egy fizetős szolgáltatás?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>Van-e a szolgáltatás egy ingyenes próbaverzióra?
Ez a szolgáltatás tartalmazza az Azure-ban az ingyenes próbaverzióra. Regisztrálhat egy [az Azure egy hónapos ingyenes](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz akár szüneteltetheti? 
Nem. Miután engedélyezte az Azure AD tartományi szolgáltatásokkal felügyelt tartományban, a szolgáltatás érhető el a kiválasztott virtuális hálózaton belül csak akkor letiltása/törlése a felügyelt tartományhoz. Nincs semmilyen módon nem lehet a szolgáltatás szüneteltetése. A számlázás továbbra is óránként, amíg nem törli a felügyelt tartományhoz.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Azure AD tartományi szolgáltatások egy másik régióba DR esemény feladatátvételi lehetőségeket?
Nem.  Az Azure AD tartományi szolgáltatások jelenleg nem biztosít georedundáns telepítési modell. Azure-régióban egyetlen virtuális hálózaton korlátozva. Ha szeretné használják a több Azure-régióban, az Active Directory-tartományvezérlőket az Azure IaaS virtuális gépekhez való futtatásához szükséges.  Architektúra-útmutató található [Itt](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kaphatok Azure AD tartományi szolgáltatásokat a nagyvállalati mobilitási csomag (EMS) keretében? Prémium szintű Azure AD használata az Azure AD tartományi szolgáltatások van szükségem?
Nem. Az Azure AD tartományi szolgáltatásokat az Azure-szolgáltatás utólagos elszámolású csomagokra, és nem EMS-hez. Az Azure AD tartományi szolgáltatások összes kiadása esetén az Azure AD használható (ingyenes, alapszintű és, prémium). Attól függően, használati óradíjat számítjuk fel.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régióban érhető el a szolgáltatást?
Tekintse meg a [Azure-szolgáltatások régió szerint](https://azure.microsoft.com/regions/#services/) oldalt, hogy hol érhető el az Azure AD tartományi szolgáltatások Azure-régiók listáját.
