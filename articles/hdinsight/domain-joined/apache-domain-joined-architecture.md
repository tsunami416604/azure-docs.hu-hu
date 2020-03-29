---
title: Azure HDInsight architektúra vállalati biztonsági csomaggal
description: Ismerje meg, hogyan tervezheti meg az Azure HDInsight biztonságát az Enterprise Security Package csomaggal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/11/2020
ms.openlocfilehash: 452a3b04637126b40aca907178bebd6f74ec4481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365779"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Enterprise Security Package használata a HDInsightban

A szabványos Azure HDInsight-fürt egyfelhasználós fürt. A legtöbb olyan vállalat számára alkalmas, amelyek kisebb alkalmazáscsoportokkal rendelkeznek, amelyek nagy adatszámítási feladatokat hoznak. Minden felhasználó igény szerint létrehozhat egy dedikált fürtöt, és megsemmisítheti azt, ha már nincs rá szükség.

Számos vállalat egy olyan modell felé mozdult el, amelyben az informatikai csapatok kezelik a fürtöket, és több alkalmazáscsoport osztozik a fürtökön. Ezeknek a nagyobb vállalatoknak többfelhasználós hozzáférésre van szükségük az Azure HDInsight minden fürtjéhez.

A HDInsight egy népszerű identitásszolgáltatóra (Active Directory) támaszkodik felügyelt módon. A HDInsight integrálásával az [Azure Active Directory tartományi szolgáltatásokkal (Azure AD DS)](../../active-directory-domain-services/overview.md)a tartományi hitelesítő adatok használatával érheti el a fürtöket.

A HDInsight virtuális gépei tartománycsatlakozik a megadott tartományhoz. Így a HDInsight-on futó összes szolgáltatás (Apache Ambari, Apache Hive szerver, Apache Ranger, Apache Spark takarékossági kiszolgáló és mások) zökkenőmentesen működik a hitelesített felhasználó számára. A rendszergazdák ezután erős engedélyezési szabályzatokat hozhatnak létre az Apache Ranger használatával a fürt erőforrásainak szerepköralapú hozzáférés-vezérléséhez.

## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

A nyílt forráskódú Apache Hadoop a Kerberos protokollra támaszkodik a hitelesítés és a biztonság érdekében. Ezért a HDInsight fürtcsomópontok enterprise security package (ESP) csatlakozik egy, az Azure AD DS által felügyelt tartományhoz. A Kerberos-biztonság a fürt Hadoop-összetevőihez van konfigurálva.

A következő dolgok jönnek létre automatikusan:

- Minden Hadoop-összetevő szolgáltatásnév
- A tartományhoz csatlakozott gépek hez egy gépnév-
- Szervezeti egység (OU) minden egyes fürthöz a szolgáltatás- és gépnévbiztonsági tagok tárolásához

Összefoglalva, be kell állítania egy környezetet a következőkkel:

- Active Directory-tartomány (az Azure AD DS által kezelt). **Az Azure HDInsight működéséhez a tartománynévnek legkisebb nek kell lennie.**
- Biztonságos LDAP (LDAPS) engedélyezve van az Azure AD DS-ben.
- Megfelelő hálózati kapcsolat a HDInsight virtuális hálózatról az Azure AD DS virtuális hálózatra, ha külön virtuális hálózatokat választ számukra. A HDInsight virtuális hálózaton belüli virtuális gépnek a virtuális hálózati társviszony-létesítésen keresztül az Azure AD DS-re kell rálátással rendelkeznie. Ha a HDInsight és az Azure AD DS ugyanazon a virtuális hálózaton van telepítve, a kapcsolat automatikusan biztosított lesz, és nincs szükség további műveletekre.

## <a name="set-up-different-domain-controllers"></a>Különböző tartományvezérlők beállítása

A HDInsight jelenleg csak az Azure AD DS szolgáltatást támogatja a fürt által a Kerberos-kommunikációhoz használt fő tartományvezérlőként. De más összetett Active Directory-beállítások is lehetségesek, feltéve, hogy egy ilyen beállítás az Azure AD DS hdinsight-hozzáféréshez való engedélyezéséhez vezet.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások

[Az Azure AD DS](../../active-directory-domain-services/overview.md) olyan felügyelt tartományt biztosít, amely teljes mértékben kompatibilis a Windows Server Active Directoryval. A Microsoft gondoskodik a tartomány magas rendelkezésre állású (HA) telepítésével történő kezeléséről, javításáról és figyeléséről. A fürt anélkül is üzembe helyezheti a tartományvezérlők karbantartását.

A felhasználók, csoportok és jelszavak szinkronizálása az Azure AD-ből. Az Egyirányú szinkronizálás az Azure AD-példány és az Azure AD DS lehetővé teszi a felhasználók számára, hogy jelentkezzen be a fürtbe ugyanazt a vállalati hitelesítő adatokat.

További információ: [HDInsight-fürtök konfigurálása ESP használatával az Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md)használatával című témakörben.

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Helyszíni Active Directory vagy Active Directory IaaS virtuális gépeken

Ha rendelkezik egy helyszíni Active Directory-példányvagy összetettebb Active Directory-beállítások a tartományhoz, szinkronizálhatja ezeket az identitásokat az Azure AD az Azure AD Connect használatával. Ezután engedélyezheti az Azure AD DS-t az Active Directory-bérlőn.

Mivel a Kerberos jelszókiírásokra támaszkodik, engedélyeznie kell [a jelszókivonat-szinkronizálást az Azure AD DS szolgáltatásban.](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)

Ha összevonást használ az Active Directory összevonási szolgáltatásokkal (AD FS), engedélyeznie kell a jelszókivonat-szinkronizálást. (Az ajánlott beállításról ezt a videót a [.)](https://youtu.be/qQruArbu2Ew) A jelszókivonat-szinkronizálás segít a vészhelyreállításban abban az esetben, ha az AD FS-infrastruktúra meghibásodik, és segít a kiszivárgott hitelesítő adatok védelmében is. További információt a [Jelszókivonat-szinkronizálás engedélyezése az Azure AD Connect-szinkronizálással](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)című témakörben talál.

A helyszíni Active Directory vagy az Active Directory használata csak az IaaS-alapú virtuális gépeken, az Azure AD és az Azure AD DS nélkül, nem támogatott konfiguráció az ESP-vel rendelkező HDInsight-fürtök számára.

Ha az összevonás t használ, és a jelszókitagok szinkronizálása megfelelően van szinkronizálva, de hitelesítési hibákat kap, ellenőrizze, hogy a felhőbeli jelszó-hitelesítés engedélyezve van-e a PowerShell egyszerű szolgáltatásához. Ha nem, be kell állítania egy [otthoni birodalom felderítése (HRD) szabályzataz](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) Azure AD-bérlő. A hrd-házirend ellenőrzése és beállítása:

1. Telepítse az előzetes [Verziójú Azure AD PowerShell-modult.](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)

   ```powershell
   Install-Module AzureAD
   ```

2. Csatlakozás globális rendszergazdai (bérlői rendszergazdai) hitelesítő adatokkal.

   ```powershell
   Connect-AzureAD
   ```

3. Ellenőrizze, hogy a Microsoft Azure PowerShell egyszerű szolgáltatás már létrejött.Check if the Microsoft Azure PowerShell service principal has already created.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Ha nem létezik, majd hozza létre a szolgáltatásnév.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Hozza létre és csatolja a szabályzatot ehhez a szolgáltatásnévhez.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId

    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>További lépések

- [HDInsight-fürtök konfigurálása ESP-vel](apache-domain-joined-configure-using-azure-adds.md)
- [Apache Hive-házirendek konfigurálása HDInsight-fürtökhöz ESP-vel](apache-domain-joined-run-hive.md)
- [HDInsight-fürtök kezelése ESP-vel](apache-domain-joined-manage.md)
