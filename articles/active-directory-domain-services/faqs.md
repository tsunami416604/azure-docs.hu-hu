---
title: Gyakori kérdések – Azure Active Directory Domain Services | Microsoft Docs
description: Gyakori kérdések a Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 509bd472fe5f930ac830b8b4016129f8acb67324
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612882"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Gyakori kérdések (GYIK)
Ez az oldal válaszol a Azure Active Directory Domain Servicesával kapcsolatos gyakori kérdésekre. A frissítések ellenőrzésének megtartása.

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutató
A Azure AD Domain Services konfigurálásával és felügyeletével kapcsolatos gyakori problémák megoldásához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md) .

## <a name="configuration"></a>Konfiguráció
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Több felügyelt tartományt is Létrehozhatok egyetlen Azure AD-címtárhoz?
Nem. Egyetlen Azure AD-címtárhoz Azure AD Domain Services egyetlen felügyelt tartományt hozhat létre.  

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Engedélyezhető a Azure AD Domain Services egy klasszikus virtuális hálózaton?
A klasszikus virtuális hálózatok nem támogatottak új központi telepítések esetén. A klasszikus virtuális hálózatokban telepített meglévő felügyelt tartományok továbbra is támogatottak.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Engedélyezhető a Azure AD Domain Services egy Azure Resource Manager virtuális hálózaton?
Igen. Azure AD Domain Services engedélyezhető Azure Resource Manager virtuális hálózaton. A klasszikus Azure-beli virtuális hálózatok már nem támogatottak új felügyelt tartományok létrehozásához.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Áttelepíthetem a meglévő felügyelt tartományt egy klasszikus virtuális hálózatról egy Resource Manager-alapú virtuális hálózatra?
Jelenleg nem. A Microsoft egy mechanizmust biztosít a meglévő felügyelt tartomány klasszikus virtuális hálózatról egy Resource Manager-alapú virtuális hálózatra való áttelepítéséhez a jövőben.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Engedélyezhető a Azure AD Domain Services egy Azure CSP-előfizetésben?
Igen. Megtudhatja, hogyan engedélyezheti [Azure ad Domain Services az Azure CSP-](csp.md)előfizetésekben.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezhető Azure AD Domain Services egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszó-kivonatokat az Azure AD-vel. Engedélyezhető Azure AD Domain Services ehhez a címtárhoz?
Nem. Azure AD Domain Servicesnak hozzá kell férniük a felhasználói fiókok jelszavas kivonatához, hogy az NTLM vagy a Kerberos használatával hitelesítse a felhasználókat. Összevont könyvtárban a rendszer nem tárolja a jelszavak kivonatait az Azure AD-címtárban. Ezért az Azure AD Domain Services nem működik ilyen Azure AD-címtárakkal.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Elérhetővé tehetek Azure AD Domain Services az előfizetésen belüli több virtuális hálózatban?
Maga a szolgáltatás nem támogatja közvetlenül ezt a forgatókönyvet. A felügyelt tartomány egyszerre csak egy virtuális hálózatban érhető el. A több virtuális hálózat közötti kapcsolat azonban konfigurálható úgy, hogy a Azure AD Domain Servicest más virtuális hálózatokhoz tegye elérhetővé. Ismerje meg, hogyan [csatlakoztathatók a virtuális hálózatok az Azure-ban](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Engedélyezhető Azure AD Domain Services a PowerShell használatával?
Igen. Lásd: [Azure ad Domain Services engedélyezése a PowerShell használatával](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Engedélyezhető a Azure AD Domain Services Resource Manager-sablon használatával?
Nem, jelenleg nem lehet engedélyezni Azure AD Domain Services sablon használatával. Ehelyett használja a PowerShell használatát: [Azure ad Domain Services engedélyezése a PowerShell használatával](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Adhatok tartományvezérlőket Azure AD Domain Services felügyelt tartományhoz?
Nem. A Azure AD Domain Services által megadott tartomány felügyelt tartomány. Ehhez a tartományhoz nem szükséges a tartományvezérlők üzembe helyezése, konfigurálása vagy más módon történő kezelése – ezeket a felügyeleti tevékenységeket a Microsoft nyújtja. Ezért a felügyelt tartományhoz nem adhat hozzá további tartományvezérlőket (írható-olvasható vagy csak olvasható).

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>A vendégnek a címtárat használó felhasználók meghívókat Azure AD Domain Services?
Nem. Az Azure ad [B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) Meghívási folyamatát használó vendég felhasználók a Azure ad Domain Services felügyelt tartományba vannak szinkronizálva. A felhasználók jelszavait azonban nem tárolja az Azure AD-címtárban. Ezért a Azure AD Domain Services nem képes szinkronizálni az NTLM-és Kerberos-kivonatokat a felügyelt tartományba tartozó felhasználók számára. Ennek eredményeképpen az ilyen felhasználók nem jelentkezhetnek be a felügyelt tartományba, vagy számítógépeket csatlakoztatnak a felügyelt tartományhoz.

## <a name="administration-and-operations"></a>Felügyelet és műveletek
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Csatlakozhatok a felügyelt tartományhoz tartozó tartományvezérlőhöz a Távoli asztal használatával?
Nem. Nincs engedélye a felügyelt tartományhoz tartozó tartományvezérlőkön való kapcsolódásra Távoli asztalon keresztül. Az "HRE DC rendszergazdák" csoport tagjai felügyelhetik a felügyelt tartományt az AD felügyeleti eszközök, például a Active Directory felügyeleti központ (ADAC) vagy az AD PowerShell használatával. Ezek az eszközök a felügyelt tartományhoz csatlakoztatott Windows Server "Távoli kiszolgálófelügyelet eszközei" funkciójával telepíthetők.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Engedélyeztem Azure AD Domain Services. Milyen felhasználói fiókot használok a tartományhoz való csatlakozáshoz ehhez a tartományhoz?
A "HRE DC Administrators" felügyeleti csoport tagjai csatlakozhatnak a tartományhoz csatlakozó gépekhez. Emellett a csoport tagjai távoli asztali hozzáférést kapnak a tartományhoz csatlakoztatott gépekhez.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Vannak tartományi rendszergazdai jogosultságok a Azure AD Domain Services által biztosított felügyelt tartományhoz?
Nem. Nem kap rendszergazdai jogosultságot a felügyelt tartományon. A "tartományi rendszergazda" és a "vállalati rendszergazda" jogosultságok nem használhatók a tartományon belül. A helyszíni Active Directory tartományi rendszergazda vagy vállalati rendszergazdai csoportok tagjai nem kapnak tartományi/vállalati rendszergazdai jogosultságokat a felügyelt tartományhoz.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosíthatom a csoporttagság a felügyelt tartományokban található LDAP vagy más AD felügyeleti eszközök használatával?
Nem. A csoporttagságok nem módosíthatók Azure AD Domain Services által kiszolgált tartományokon. Ugyanez vonatkozik a felhasználói attribútumokra is. Az Azure AD-ben vagy a helyszíni tartományban azonban a csoporttagság vagy a felhasználói attribútumok is megváltoztathatók. Ezeket a módosításokat a rendszer automatikusan szinkronizálja Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi időt vesz igénybe, hogy az Azure AD-címtárban végrehajtott módosítások megjelenjenek a felügyelt tartományban?
Az Azure AD-címtárban az Azure AD felhasználói felület vagy a PowerShell használatával végzett módosítások a felügyelt tartományba vannak szinkronizálva. Ez a szinkronizálási folyamat a háttérben fut. A kezdeti szinkronizálás befejezése után általában körülbelül 20 percet vesz igénybe, hogy az Azure AD-ben végzett módosítások tükrözzék a felügyelt tartományban.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Bővíthető a Azure AD Domain Services által biztosított felügyelt tartomány sémája?
Nem. A sémát a Microsoft felügyeli a felügyelt tartományhoz. A Azure AD Domain Services nem támogatja a séma-bővítményeket.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosíthatom vagy Hozzáadhatok DNS-rekordokat a saját felügyelt tartományában?
Igen. A "HRE DC rendszergazdák" csoport tagjai "DNS-rendszergazda" jogosultságokat kapnak a felügyelt tartomány DNS-rekordjainak módosításához. Használhatják a DNS-kezelő konzolt a felügyelt tartományhoz csatlakoztatott Windows Servert futtató gépen a DNS kezeléséhez. A DNS-kezelő konzol használatához telepítse a "DNS-kiszolgáló eszközöket", amely a kiszolgáló "Távoli kiszolgálófelügyelet eszközei" választható funkciójának részét képezi. További információ a [DNS felügyeletére, figyelésére és hibaelhárítására szolgáló segédprogramokról](https://technet.microsoft.com/library/cc753579.aspx) a TechNet webhelyen érhető el.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi a jelszó élettartama házirend egy felügyelt tartományon?
Egy Azure AD Domain Services felügyelt tartomány alapértelmezett jelszójának élettartama 90 nap. A jelszó élettartama nincs szinkronizálva az Azure AD-ben konfigurált jelszó-élettartammal. Ezért előfordulhat, hogy a felhasználók jelszavai lejárnak a felügyelt tartományban, de továbbra is érvényesek az Azure AD-ben. Ilyen esetekben a felhasználóknak módosítaniuk kell a jelszavukat az Azure AD-ben, és az új jelszó szinkronizálva lesz a felügyelt tartományba. Emellett a "password-do-not-lejárat" és a "user-do-Change-Password-on-Next-Logon" attribútumokat a rendszer nem szinkronizálja a felügyelt tartományával.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biztosít Azure AD Domain Services AD-fiók zárolásának védelmét?
Igen. A felügyelt tartomány 2 percen belül öt érvénytelen jelszóval próbálkozik, mert a felhasználói fiók 30 percig kizárja a felhasználót. 30 perc elteltével a felhasználói fiók automatikusan fel lesz oldva. A felügyelt tartomány jelszavas kísérleteinek érvénytelenek, ne zárja ki a felhasználói fiókot az Azure AD-ben. A felhasználói fiók csak a Azure AD Domain Services felügyelt tartományon belül van zárolva.

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Díjköteles szolgáltatás Azure AD Domain Services?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>Van ingyenes próbaverzió a szolgáltatáshoz?
Ezt a szolgáltatást az Azure ingyenes próbaverziója tartalmazza. Regisztrálhat az [Azure ingyenes, egy hónapos próbaidőszakára](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Szüneteltethető Azure AD Domain Services felügyelt tartomány? 
Nem. Miután engedélyezte a Azure AD Domain Services felügyelt tartományt, a szolgáltatás a kiválasztott virtuális hálózaton belül elérhető, amíg le nem tiltja/törli a felügyelt tartományt. A szolgáltatás szüneteltetése nincs mód. A számlázás óránként folytatódik, amíg nem törli a felügyelt tartományt.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Használhatok feladatátvételi Azure AD Domain Services egy másik régióba egy DR eseményhez?
Nem.  Azure AD Domain Services jelenleg nem biztosít földrajzilag redundáns telepítési modellt. Egy Azure-régióban egyetlen virtuális hálózatra korlátozódik. Ha több Azure-régiót szeretne használni, futtatnia kell a Active Directory-tartomány-vezérlőket az Azure IaaS virtuális gépeken.  Az architektúra-útmutatás [itt](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)található.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Lekérhetek Azure AD Domain Services a nagyvállalati mobilitási csomag (EMS) részeként? Szükség van-e a Azure AD Domain Services használatára prémium szintű Azure AD?
Nem. Azure AD Domain Services az utólagos elszámolású Azure-szolgáltatás, amely nem része az EMS-nek. Azure AD Domain Services az Azure AD összes kiadásával használható (ingyenes és prémium szintű). A használattól függően óradíjat számolunk fel.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régiók érhetők el a szolgáltatásban?
Az Azure- [szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/) lapon megtekintheti az Azure-régiók listáját, ahol a Azure ad Domain Services elérhető.
