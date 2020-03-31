---
title: Gyakori kérdések az Azure AD tartományi szolgáltatásokkal kapcsolatban | Microsoft dokumentumok
description: Az Azure Active Directory tartományi szolgáltatások konfigurációjával, felügyeletével és elérhetőségével kapcsolatos gyakori kérdések elolvasása és megismerése
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: a57826c79babded6e616548879a5ec0c223307d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78946438"
---
# <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYAKORI KÉRDÉSEK)

Ez a lap választ ad az Azure Active Directory tartományi szolgáltatásokkal kapcsolatos gyakori kérdésekre.

## <a name="configuration"></a>Konfiguráció

* [Létrehozhatok több felügyelt tartományt egyetlen Azure AD-címtárhoz?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy klasszikus virtuális hálózatban?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy Azure Resource Manager virtuális hálózatban?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Áttelepíthetem a meglévő felügyelt tartományomat egy klasszikus virtuális hálózatról egy Erőforrás-kezelő virtuális hálózatra?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy Azure CSP(Cloud Solution Provider) előfizetésben?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszókitagokat az Azure AD-vel. Engedélyezhetem az Azure AD tartományi szolgáltatásokat ehhez a címtárhoz?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Elérhetővé tehetem az Azure AD tartományi szolgáltatásokat több virtuális hálózatban az előfizetésemen belül?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat a PowerShell használatával?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Engedélyezhetem az Azure AD tartományi szolgáltatásokat erőforráskezelő sablon használatával?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Hozzáadhatok tartományvezérlőket egy Azure AD tartományi szolgáltatások által kezelt tartományhoz?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [A címtárba meghívott vendégfelhasználók használhatják az Azure AD tartományi szolgáltatásokat?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Áthelyezhetek egy meglévő Azure AD tartományi szolgáltatások által felügyelt tartományt egy másik előfizetésbe, erőforráscsoportba, régióba vagy virtuális hálózatba?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Az Azure AD tartományi szolgáltatások magas rendelkezésre állási lehetőségeket tartalmaznak?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Létrehozhatok több felügyelt tartományt egyetlen Azure AD-címtárhoz?
Nem. Csak egyetlen felügyelt tartományt hozhat létre az Azure AD tartományi szolgáltatások egyetlen Azure AD-címtárhoz.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy klasszikus virtuális hálózatban?
Klasszikus virtuális hálózatok nem támogatottak az új központi telepítések. A klasszikus virtuális hálózatokban telepített meglévő felügyelt tartományok továbbra is támogatottak, amíg 2023. Meglévő telepítések esetén [áttelepítheti az Azure AD tartományi szolgáltatásokat a klasszikus virtuális hálózati modellről az Erőforrás-kezelőbe.](migrate-from-classic-vnet.md)

További információkért lásd a [hivatalos elemesztési értesítést](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy Azure Resource Manager virtuális hálózatban?
Igen. Az Azure AD tartományi szolgáltatások engedélyezhetők az Azure Resource Manager virtuális hálózatban. A klasszikus Azure virtuális hálózatok már nem érhetők el felügyelt tartomány létrehozásakor.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Áttelepíthetem a meglévő felügyelt tartományomat klasszikus virtuális hálózatról erőforrás-kezelői virtuális hálózatra?
Igen. További információ: [Az Azure AD tartományi szolgáltatások áttelepítése a klasszikus virtuális hálózati modellről az Erőforrás-kezelőbe.](migrate-from-classic-vnet.md)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy Azure CSP(Cloud Solution Provider) előfizetésben?
Igen. További információt az [Azure AD tartományi szolgáltatások azure-beli CSP-előfizetésekben való engedélyezéséről](csp.md)talál.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszókitagokat az Azure AD-vel. Engedélyezhetem az Azure AD tartományi szolgáltatásokat ehhez a címtárhoz?
Nem. A felhasználók ntlm-en vagy Kerberos-on keresztül történő hitelesítéséhez az Azure AD tartományi szolgáltatásoknak hozzá kell férniük a felhasználói fiókok jelszó-kivéihez. Az összevont címtárban a jelszókikeresések nem tárolódnak az Azure AD-címtárban. Ezért az Azure AD tartományi szolgáltatások nem működik az ilyen Azure AD-könyvtárak.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Elérhetővé tehetem az Azure AD tartományi szolgáltatásokat több virtuális hálózatban az előfizetésemen belül?
Maga a szolgáltatás közvetlenül nem támogatja ezt a forgatókönyvet. A felügyelt tartomány egyszerre csak egy virtuális hálózatban érhető el. Azonban konfigurálhatja a kapcsolatot több virtuális hálózatok azure AD tartományi szolgáltatások más virtuális hálózatok számára elérhetővé. További információt [avirtuális babérokból megtudhatja, hogyan kapcsolhat hozzá virtuális hálózatokat az Azure-ban VPN-átjárók](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) vagy [virtuális hálózati társviszony-létesítés](../virtual-network/virtual-network-peering-overview.md)használatával.

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat a PowerShell használatával?
Igen. További információt az [Azure AD tartományi szolgáltatások PowerShell használatával történő engedélyezéséről talál.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Engedélyezhetem az Azure AD tartományi szolgáltatásokat erőforráskezelő sablon használatával?
Igen, létrehozhat egy Azure AD tartományi szolgáltatások által felügyelt tartományt egy Erőforrás-kezelő sablon használatával. Egy egyszerű szolgáltatás és az Azure AD-csoport felügyelethez létre kell hozni az Azure Portalon vagy az Azure PowerShell a sablon üzembe helyezése előtt. További információ: [Azure AD DS felügyelt tartomány létrehozása Azure Resource Manager-sablon használatával](template-create-instance.md)című témakörben olvashat. Amikor létrehoz egy Azure AD tartományi szolgáltatások felügyelt tartományt az Azure Portalon, van egy is lehetőség a sablon exportálására további központi telepítések.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Hozzáadhatok tartományvezérlőket egy Azure AD tartományi szolgáltatások által kezelt tartományhoz?
Nem. Az Azure AD tartományi szolgáltatások által biztosított tartomány egy felügyelt tartomány. Ehhez a tartományhoz nem kell tartományvezérlőket létesíteni, konfigurálni a tartományhoz, illetve más módon kezelnie. Ezeket a felügyeleti tevékenységeket a Microsoft szolgáltatásként biztosítja. Ezért a felügyelt tartományhoz nem adhat hozzá további tartományvezérlőket (írás-olvasás vagy írásvédett).

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>A címtárba meghívott vendégfelhasználók használhatják az Azure AD tartományi szolgáltatásokat?
Nem. Az [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) meghívási folyamattal az Azure AD-címtárba meghívott vendégfelhasználók szinkronizálódnak az Azure AD tartományi szolgáltatások felügyelt tartományával. Ezek a felhasználók jelszavait azonban nem tárolja az Azure AD-címtárban. Ezért az Azure AD tartományi szolgáltatások nem tudja szinkronizálni az NTLM és a Kerberos kivéket ezeka felhasználók számára a felügyelt tartományba. Ezek a felhasználók nem tudnak bejelentkezni, és nem csatlakozhatnak számítógépekhez a felügyelt tartományhoz.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Áthelyezhetek egy meglévő Azure AD tartományi szolgáltatások által felügyelt tartományt egy másik előfizetésbe, erőforráscsoportba, régióba vagy virtuális hálózatba?
Nem. Miután létrehozott egy Azure AD tartományi szolgáltatások felügyelt tartományt, nem helyezheti át a példányt egy másik erőforráscsoportba, virtuális hálózatba, előfizetésbe stb. Az Azure AD DS-példány üzembe helyezésekor ügyeljen a legmegfelelőbb előfizetés, erőforráscsoport, régió és virtuális hálózat kiválasztására.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Az Azure AD tartományi szolgáltatások magas rendelkezésre állási lehetőségeket tartalmaznak?

Igen. Minden Azure AD tartományi szolgáltatások által kezelt tartomány két tartományvezérlőt tartalmaz. Nem kezeli ezeket a tartományvezérlőket, és nem csatlakozik ezekhez a tartományvezérlőkhöz, hanem a felügyelt szolgáltatás részét képezik. Ha az Azure AD tartományi szolgáltatásokat olyan régióban telepíti, amely támogatja a rendelkezésre állási zónákat, a tartományvezérlők zónák között oszlanak meg. Azokban a régiókban, amelyek nem támogatják a rendelkezésre állási zónák, a tartományvezérlők között vannak elosztva rendelkezésre állási csoportok között. Nincs enek konfigurációs beállításai vagy felügyeleti vezérlése a terjesztés felett. További információt az [Azure-beli virtuális gépek elérhetőségi lehetőségei című témakörben talál.](../virtual-machines/windows/availability.md)

## <a name="administration-and-operations"></a>Adminisztráció és működés

* [Csatlakozhatok a felügyelt tartomány tartományvezérlőjéhez a Távoli asztal használatával?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Engedélyeztem az Azure AD tartományi szolgáltatásokat. Milyen felhasználói fiókkal csatlakoztassam a tartományhoz a gépeket?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Rendelkezem tartományi rendszergazdai jogosultságokkal az Azure AD tartományi szolgáltatások által biztosított felügyelt tartományhoz?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosíthatom a csoporttagságokat LDAP vagy más AD felügyeleti eszközök használatával felügyelt tartományokon?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Mennyi ideig tart, amíg az Azure AD-címtárban végzett módosítások láthatóvá válnak a felügyelt tartományomban?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kiterjeszthetem az Azure AD tartományi szolgáltatások által biztosított felügyelt tartomány sémáját?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosíthatom vagy hozzáadhatom a DNS-rekordokat a felügyelt tartományomban?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Mi a jelszó élettartamra vonatkozó házirendje egy felügyelt tartományban?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Az Azure AD tartományi szolgáltatások biztosít AD-fiók zárolási védelmet?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Csatlakozhatok a felügyelt tartomány tartományvezérlőjéhez a Távoli asztal használatával?
Nem. Nincs engedélye a felügyelt tartomány tartományvezérlőihez való csatlakozáshoz a Távoli asztal használatával. Az *AAD tartományvezérlő-rendszergazdák* csoport tagjai felügyelhetik a felügyelt tartományt az AD felügyeleti eszközök, például az Active Directory felügyeleti központ (ADAC) vagy az AD PowerShell használatával. Ezeket az eszközöket a felügyelt tartományhoz csatlakozó Windows-kiszolgáló *Távoli kiszolgálófelügyeleti eszközök* szolgáltatásával telepíti. További információ: [Felügyeleti virtuális gép létrehozása az Azure AD tartományi szolgáltatások felügyelt tartományának konfigurálásához és felügyeletéhez.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Engedélyeztem az Azure AD tartományi szolgáltatásokat. Milyen felhasználói fiókkal csatlakoztassam a tartományhoz a gépeket?
Bármely felhasználói fiók, amely az Azure AD DS felügyelt tartomány része, csatlakozhat egy virtuális géphez. Az *AAD tartományvezérlő-rendszergazdák* csoport tagjai távoli asztali hozzáférést kapnak a felügyelt tartományhoz csatlakozott gépekhez.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Rendelkezem tartományi rendszergazdai jogosultságokkal az Azure AD tartományi szolgáltatások által biztosított felügyelt tartományhoz?
Nem. A felügyelt tartományban nincs rendszergazdai jogosultsága. *A tartományrendszergazda* és *a vállalati rendszergazdai* jogosultságok nem használhatók a tartományon belül. A helyszíni Active Directory tartományi rendszergazdájának vagy vállalati rendszergazdai csoportjának tagjai szintén nem kapnak tartományi/ vállalati rendszergazdai jogosultságokat a felügyelt tartományban.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosíthatom a csoporttagságokat LDAP vagy más AD felügyeleti eszközök használatával felügyelt tartományokon?
Az Azure Active Directoryés az Azure AD tartományi szolgáltatások között szinkronizált felhasználók és csoportok nem módosíthatók, mert a származási forrásuk az Azure Active Directory. A felügyelt tartományból származó bármely felhasználó vagy csoport módosítható.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi ideig tart, amíg az Azure AD-címtárban végzett módosítások láthatóvá válnak a felügyelt tartományomban?
Az Azure AD felhasználói felületével vagy a PowerShell használatával az Azure AD-címtárban végrehajtott módosítások automatikusan szinkronizálódnak a felügyelt tartománnyal. Ez a szinkronizálási folyamat a háttérben fut. Nincs meghatározott időszak a szinkronizáláshoz az objektum összes módosításához.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kiterjeszthetem az Azure AD tartományi szolgáltatások által biztosított felügyelt tartomány sémáját?
Nem. A sémát a Microsoft feligazja a felügyelt tartományban. Sémabővítmények et az Azure AD tartományi szolgáltatások nem támogatnak.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosíthatom vagy hozzáadhatom a DNS-rekordokat a felügyelt tartományomban?
Igen. Az *AAD TARTOMÁNYVEZÉRLŐ-rendszergazdák* csoport tagjai *DNS-rendszergazdai* jogosultsággal rendelkeznek a felügyelt tartomány DNS-rekordjainak módosításához. Ezek a felhasználók használhatják a DNS Manager konzolt egy olyan gépen, amelyen a felügyelt tartományhoz csatlakozott Windows Server csatlakozik a DNS kezeléséhez. A DNS Manager konzol használatához telepítse a *DNS-kiszolgálóeszközök*eszközt , amely a *kiszolgáló távoli kiszolgálófelügyeleti eszközeinek* választható szolgáltatása. További információt a [DNS felügyelete egy Azure AD tartományi szolgáltatások által felügyelt tartományban](manage-dns.md)című témakörben talál.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi a jelszó élettartamra vonatkozó házirendje egy felügyelt tartományban?
Az Azure AD tartományi szolgáltatások által kezelt tartomány alapértelmezett jelszóélettartama 90 nap. Ez a jelszó élettartama nincs szinkronizálva az Azure AD-ben konfigurált jelszó élettartamával. Ezért előfordulhat, hogy a felhasználók jelszavai lejárnak a felügyelt tartományban, de továbbra is érvényesek az Azure AD-ben. Ilyen esetekben a felhasználóknak módosítaniuk kell a jelszavukat az Azure AD-ben, és az új jelszó szinkronizálódik a felügyelt tartománnyal. Emellett a *jelszó-nem-le járni,* és a felhasználói fiókok *felhasználó-must-change-password-at-next-logon* attribútumok nincsenek szinkronizálva a felügyelt tartománnyal.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Az Azure AD tartományi szolgáltatások biztosít AD-fiók zárolási védelmet?
Igen. Öt érvénytelen jelszókísérlet 2 percen belül a felügyelt tartományban 30 percre zárolt felhasználói fiókot eredményez. 30 perc elteltével a felhasználói fiók zárolása automatikusan feloldódik. A felügyelt tartományérvénytelen jelszókísérletei nem zárják ki a felhasználói fiókot az Azure AD-ben. A felhasználói fiók csak az Azure AD tartományi szolgáltatások által felügyelt tartományon belül van zárolva. További információt a [felügyelt tartományok jelszó- és fiókzárolási házirendjei című témakörben talál.](password-policy.md)

## <a name="billing-and-availability"></a>Számlázás és elérhetőség

* [Az Azure AD tartományi szolgáltatások fizetős szolgáltatás?](#is-azure-ad-domain-services-a-paid-service)
* [Van egy ingyenes próba a szolgáltatás?](#is-there-a-free-trial-for-the-service)
* [Szüneteltethetek egy Azure AD tartományi szolgáltatások által kezelt tartományt?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Feladatátvételt kaphatok az Azure AD tartományi szolgáltatásokkal egy másik régióban egy VÉSZ-eseményhez?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Beszerezhetem az Azure AD tartományi szolgáltatásokat az Enterprise Mobility Suite (EMS) részeként? Szükségem van az Azure AD Premium használatára az Azure AD tartományi szolgáltatások használatához?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Milyen Azure-régiókban érhető el a szolgáltatás?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Az Azure AD tartományi szolgáltatások fizetős szolgáltatás?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>Van egy ingyenes próba a szolgáltatás?
Az Azure AD tartományi szolgáltatások az Azure ingyenes próbaverziója részét képezik. Regisztrálhat az [Azure egy hónapos ingyenes próbaverziójára.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Szüneteltethetek egy Azure AD tartományi szolgáltatások által kezelt tartományt?
Nem. Miután engedélyezte az Azure AD tartományi szolgáltatások felügyelt tartományát, a szolgáltatás elérhető a kiválasztott virtuális hálózaton belül, amíg nem törli a felügyelt tartományt. Nem lehet szüneteltetni a szolgáltatást. A számlázás óránként folytatódik, amíg nem törli a felügyelt tartományt.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Feladatátvételt kaphatok az Azure AD tartományi szolgáltatásokkal egy másik régióban egy VÉSZ-eseményhez?
Nem. Az Azure AD tartományi szolgáltatások jelenleg nem biztosít georedundáns telepítési modellt. Az Azure-régióegyetlen virtuális hálózatra korlátozódik. Ha több Azure-régiót szeretne használni, futtatnia kell az Active Directory tartományvezérlőit az Azure IaaS virtuális gépeken. Az architektúra-útmutatásért [lásd: A helyszíni Active Directory-tartomány kiterjesztése az Azure-ra.](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Beszerezhetem az Azure AD tartományi szolgáltatásokat az Enterprise Mobility Suite (EMS) részeként? Szükségem van az Azure AD Premium használatára az Azure AD tartományi szolgáltatások használatához?
Nem. Az Azure AD tartományi szolgáltatások egy felosztó-kirovó Azure-szolgáltatás, és nem része az EMS-nek. Az Azure AD tartományi szolgáltatások az Azure AD (Ingyenes és prémium) összes kiadásával használhatók. A számlázás a használattól függően óránként történik.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régiókban érhető el a szolgáltatás?
Tekintse meg az [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/) lapon az Azure-régiók listáját, ahol az Azure AD tartományi szolgáltatások érhető el.

## <a name="troubleshooting"></a>Hibaelhárítás

Tekintse meg a [hibaelhárítási útmutatóaz](troubleshoot.md) Azure AD tartományi szolgáltatások konfigurálásával vagy felügyeletével kapcsolatos gyakori problémák megoldásait.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure AD tartományi szolgáltatásokról, olvassa el [a Mi az Azure Active Directory tartományi szolgáltatások?](overview.md)

Első lépésekért olvassa el Az [Azure Active Directory tartományi szolgáltatások példányának létrehozása és konfigurálása című témakört.](tutorial-create-instance.md)
