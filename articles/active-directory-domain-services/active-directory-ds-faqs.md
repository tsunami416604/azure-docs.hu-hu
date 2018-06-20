---
title: Gyakori kérdések – Azure Active Directory tartományi szolgáltatások |} Microsoft Docs
description: Azure Active Directory tartományi szolgáltatások kapcsolatos gyakori kérdések
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 41e5d515dcea4a4bc2ebfec8493d44de877a263f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36214729"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Az Azure Active Directory tartományi szolgáltatások: Gyakran ismételt kérdések (GYIK)
Ezen a lapon az Azure Active Directory tartományi szolgáltatások kapcsolatos gyakori kérdésekre. Tartsa biztonsági frissítések keresése.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
Tekintse meg a [hibaelhárítási útmutatója](active-directory-ds-troubleshooting.md) konfigurálása és felügyelete az Azure AD tartományi szolgáltatások termékkel kapcsolatos gyakori hibák megoldások keresése.

## <a name="configuration"></a>Konfiguráció
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Hozható létre több felügyelt tartományok egyetlen Azure AD-címtár?
Nem. Csak egyetlen Azure AD tartományi szolgáltatások által kiszolgált egy felügyelt tartomány hozhat létre Azure AD-címtárban.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Egy Azure Resource Manager virtuális hálózatot az Azure AD tartományi szolgáltatások engedélyezése
Igen. Azure AD tartományi szolgáltatások az Azure Resource Manager virtuális hálózat engedélyezhető. Új felügyelt tartományok létrehozásához a klasszikus Azure virtuális hálózatok használata már nem támogatott.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Telepíthetek át a meglévő felügyelt tartomány klasszikus virtuális hálózaton keresztül egy erőforrás-kezelő virtuális hálózathoz?
Jelenleg nem. A Microsoft egy mechanizmus, amellyel áttelepítheti a meglévő felügyelt tartomány a klasszikus virtuális hálózatot egy erőforrás-kezelő virtuális hálózatot a jövőben fog továbbítani.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Az Azure kriptográfiai Szolgáltató (Cloud Solution Provider) előfizetéssel az Azure AD tartományi szolgáltatások engedélyezése
Igen. Tekintse meg, hogyan engedélyezheti [Azure CSP-előfizetések az Azure AD tartományi szolgáltatások](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezhető az Azure AD tartományi szolgáltatások egy összevont Azure AD-címtár? Az Azure AD-jelszó-kivonatok nem szinkronizálni. Ez a könyvtár az Azure AD tartományi szolgáltatások engedélyezése
Nem. Azure AD tartományi szolgáltatások hozzá kell férnie a jelszókivonatait a felhasználói fiókok, NTLM vagy Kerberos használatával a felhasználók hitelesítéséhez. Egy összevont könyvtárban a jelszó-kivonatok nem szerepelnek az Azure AD-címtár. Azure AD tartományi szolgáltatásokat, ezért az ilyen Azure AD-címtártól nem működik.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Tehetek Azure AD tartományi szolgáltatások több virtuális hálózat érhető el az előfizetésen belül?
A szolgáltatás nem támogatja közvetlenül ebben a forgatókönyvben. A felügyelt tartományok egyszerre csak egy virtuális hálózaton rendelkezésre álló lehet. Azonban előfordulhat, hogy konfigurálnia több virtuális hálózat teszi közzé az Azure AD tartományi szolgáltatásokat másik virtuális hálózatok közötti kapcsolatot. Tekintse meg, hogyan zajlik [csatlakozzon az Azure virtuális hálózatairól](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell-lel Azure AD tartományi szolgáltatások engedélyezése
Igen. Lásd: [engedélyezése az Azure AD tartományi szolgáltatások a PowerShell használatával hogyan](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>A Resource Manager sablonnal Azure AD tartományi szolgáltatások engedélyezése
Igen. Lásd: [engedélyezése az Azure AD tartományi szolgáltatások a PowerShell használatával hogyan](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Hozzáadható-e tartományvezérlők az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz?
Nem. Az Azure AD tartományi szolgáltatások által biztosított tartománya felügyelt tartományhoz. Nem kell kiépíteni, konfigurálása, vagy egyéb módon kezelje a tartományvezérlők a tartomány - e felügyeleti tevékenységek szolgáltatásként Microsoft által biztosított. További tartományvezérlők (olvasási és írási vagy olvasási) a felügyelt tartomány számára, ezért nem adható hozzá.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Használhatja a vendégfelhasználók meghívót, hogy a címtár Azure AD tartományi szolgáltatásokat?
Nem. A vendégfelhasználók meghívót, hogy az Azure Active directory használata a [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) a meghívás folyamat szinkronizálva az Azure AD tartományi szolgáltatások által kezelt tartomány be. Ezek a felhasználók jelszavainak azonban nem tárolódnak az Azure AD-címtár. Ezért az Azure AD tartományi szolgáltatások rendelkezik semmilyen módon nem lehet szinkronizálni az NTLM, és a Kerberos csak ezek a felhasználók a felügyelt tartományba. Ennek eredményeképpen ezek a felhasználók nem jelentkezzen be a felügyelt tartományra vagy számítógépek csatlakoztatása a felügyelt tartományra.

## <a name="administration-and-operations"></a>Felügyelet és műveletek
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Képes kapcsolódni a tartományvezérlő a távoli asztal használata felügyelt tartomány?
Nem. Nincs engedélye csatlakozni a távoli asztalon keresztül a felügyelt tartományra tartományvezérlőkhöz. A "AAD DC rendszergazdák" csoport tagjai felügyelhetik a felügyelt tartományra AD felügyeleti eszközök, például az Active Directory felügyeleti központban (ADAC) vagy AD PowerShell segítségével. Ezek az eszközök vannak telepítve, a "Távoli kiszolgálófelügyelet eszközei" funkció használata a felügyelt tartományhoz csatlakoztatott Windows Server.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD tartományi szolgáltatások engedélyezése I. Milyen felhasználói fiók használható a tartomány illesztési gépek a tartományhoz?
A felügyeleti csoport "AAD DC rendszergazdák" Csatlakozás tartományhoz gép is. Emellett a csoport tagjai távoli asztali gépek, a tartományhoz csatlakozott hozzáférési engedéllyel.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatások által biztosított felügyelt tartományhoz tartozó tartományi rendszergazdai jogosultságokkal kell?
Nem. Akkor kapnak rendszergazdai jogosultságokkal azon a felügyelt tartományra. "Tartományi rendszergazda" és a "vállalati rendszergazda" jogosultságokkal lesz szükség a tartományon belül nem érhetők el. A tartományi rendszergazda vagy a vállalati rendszergazdai csoportok a helyszíni Active Directoryban is kapnak nem tartományi vagy vállalati rendszergazdai jogosultságokkal a felügyelt tartományra.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosíthatja a csoporttagságot a felügyelt tartományok LDAP és egyéb AD felügyeleti eszközök használatával?
Nem. Csoporttagságok Azure AD tartományi szolgáltatások által kiszolgált tartományok nem módosítható. Ugyanez vonatkozik, a felhasználói attribútumokat. Csoporttagságok vagy a felhasználói attribútumokat azonban változtassa meg az Azure ad-ben vagy a helyszíni tartományban. Változások a rendszer automatikusan szinkronizálja az Azure AD tartományi szolgáltatásokra.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi időt vesz igénybe a változásokat I lesz látható a saját felügyelt tartomány számára saját Azure AD-címtár tenni?
Az Azure AD-címtár az Azure AD felhasználói felületén vagy a PowerShell használatával végzett változásokat szinkronizálja a rendszer a felügyelt tartományra. Ez a szinkronizálási folyamat fut a háttérben. Kezdeti szinkronizálás befejezése után, általában a felügyelt tartományok megjelennek az Azure ad-ben végzett módosítások körülbelül 20 percet vesz igénybe.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Ki lehet terjeszteni a felügyelt tartományra Azure AD tartományi szolgáltatások által biztosított sémája?
Nem. A séma a Microsoft felügyeli a felügyelt tartomány számára. Sémakiterjesztések nem támogatottak az Azure AD tartományi szolgáltatásokat.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosítsa vagy a felügyelt tartomány DNS-rekordok hozzáadásához?
Igen. A "AAD DC rendszergazdák" csoportba kapnak "DNS-rendszergazda" jogosultsággal, a felügyelt tartomány DNS-rekordjainak módosítására. Ezek segítségével a DNS-kezelő konzolt egy tartományhoz a felügyelt, Windows Server rendszert futtató számítógép DNS kezelése. A DNS-kezelő konzol használatával telepítse "DNS-kiszolgálói eszközök", amely része a "Távoli kiszolgáló felügyeleti eszközei" választható szolgáltatás a kiszolgálón. További információ a [segédprogramok felügyelete, figyelés és hibaelhárítás DNS](https://technet.microsoft.com/library/cc753579.aspx) a TechNet webhelyen érhető el.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi az a jelszóházirend élettartama egy felügyelt tartomány része?
Az Azure AD-tartomány az alapértelmezett jelszó élettartamának szolgáltatások által kezelt tartomány 90 nap. A jelszó élettartama nincs szinkronizálva az Azure ad-ben beállított jelszót élettartamát. Emiatt előfordulhat, hogy olyan helyzet, amikor a felhasználói jelszavak jár le, a felügyelt tartományok, de továbbra is érvényesek az Azure ad-ben. Ilyen esetekben a felhasználóknak kell módosítani a jelszavát, az Azure ad-ben, és az új jelszót fogja szinkronizálni a felügyelt tartományra. Emellett a "jelszó-biztosítja – nem-jár le" és "user-must-change-password-at-next-logon" attribútumok a felhasználói fiókok nincsenek szinkronizálva a felügyelt tartományra.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD tartományi szolgáltatásokat nyújt AD fiókzárolási Fiókvédelem?
Igen. Öt érvénytelen bejelentkezési kísérletek 2 percen belül a felügyelt tartományra a felhasználói fiókot az 30 percig zárolása miatt. 30 másodperc után automatikusan fel nem a felhasználói fiók. Érvénytelen bejelentkezési kísérletek a felügyelt tartomány nem a felhasználói fiók zárolása az Azure ad-ben. A felhasználói fiók ki van zárva, csak az Azure AD tartományi szolgáltatások által felügyelt tartományon belül.

## <a name="billing-and-availability"></a>Számlázási és rendelkezésre állás
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Az Azure AD tartományi szolgáltatások egy fizetős szolgáltatás?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>A szolgáltatás egy ingyenes próbaverzióra van?
Ez a szolgáltatás az Azure ingyenes próbaverzió tartalmazza. Regisztrálhatnak az egy [ingyenes egy hónapos próbaverzió Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz is szüneteltetése? 
Nem. Miután engedélyezte az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, a szolgáltatás nem érhető el a kiválasztott virtuális hálózaton belül, addig, amíg Ön letiltása/törlése a felügyelt tartományra. Nincs mód a szolgáltatás szüneteltetésére. Számlázási óránként folytatódik, amíg nem törlik a felügyelt tartományra.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kaphatok Azure AD tartományi szolgáltatások részét képező nagyvállalati mobilitási csomag (EMS)? Azure AD prémium szintű Azure AD tartományi szolgáltatások kell?
Nem. Azure AD tartományi szolgáltatások egy Azure-szolgáltatás – használatalapú fizetés, és nincs EMS-hez. Azure AD tartományi szolgáltatások összes kiadása esetén az Azure AD is használható (szabad, Basic, és, Premium). Óránként, attól függően, hogy a használati kell fizetni.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régiók érhető el a szolgáltatást?
Tekintse meg a [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/) lapot, melyen megtekintheti, amennyiben rendelkezésre áll-e az Azure AD tartományi szolgáltatások az Azure-régiók listáját.
