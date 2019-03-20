---
title: Az Azure HDInsight architektúrája a vállalati biztonsági csomaggal
description: Útmutató a vállalati biztonsági csomaggal HDInsight biztonságának megtervezése.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7e71f27ab8d577602dd4b02f83d57ff84a92858a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58088094"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>A HDInsight vállalati biztonsági csomag használata

A standard szintű Azure HDInsight-fürtöt, egy egyfelhasználós fürt. Ideális a legtöbb vállalatok számára, hogy kisebb alkalmazásfejlesztő csapatok dolgoznak, nagy mennyiségű adat számítási feladatok létrehozásához. Hozzon létre egy dedikált fürtöt igény szerinti és nincs többé szükség esetén semmisítse meg a minden felhasználónak. 

Sok vállalat olyan modellre, amelyben az informatikai csapatok-fürtök kezelése erőforrásfájljait áthelyezték, és több alkalmazásfejlesztő csapat ugyanazokon a fürtökön. Ezek a nagyobb cégek minden egyes fürt az Azure HDInsight többfelhasználós hozzá kell férniük.

HDInsight a legnépszerűbb identitásszolgáltatóra, az Active Directory – az olyan felügyelt módon támaszkodik. A HDInsight integrálásával [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), a fürtök a tartományi hitelesítő adataival érheti. 

A virtuális gépek (VM) a HDInsight tartományhoz csatlakoztatva, a megadott tartományban. Így a HDInsight (az Apache Ambari, Apache Hive-kiszolgáló, az Apache Ranger, Apache Spark thrift-kiszolgáló és mások) futó összes szolgáltatás zökkenőmentesen működik a hitelesített felhasználó számára. A rendszergazdák majd erős engedélyezési házirendek használatával létrehozhatja az Apache Ranger szerepköralapú hozzáférés-vezérlést biztosít a fürtben lévő erőforrásokat.

## <a name="integrate-hdinsight-with-active-directory"></a>A HDInsight és az Active Directory integrálása

Nyílt forráskódú Apache Hadoop a Kerberos protokoll hitelesítési és biztonsági támaszkodik. HDInsight-fürtcsomópontok vállalati biztonsági csomag (ESP), ezért az Azure Active Directory tartományi szolgáltatások által felügyelt tartományhoz csatlakoznak. A Kerberos biztonsági konfigurálva van a Hadoop-összetevők a fürtön. 

Automatikusan jönnek létre a következő műveleteket:

- egy egyszerű szolgáltatást az egyes Hadoop-összetevők
- az egyes gépek a tartományhoz csatlakozó számítógép rendszerbiztonsági tag
- egy szervezeti egység (OU) minden egyes fürt ezen szolgáltatás és a gépi rendszerbiztonsági tagok tárolásához

Összefoglalva, szüksége a környezet beállításához:

- Active Directory-tartomány (az Azure Active Directory tartományi szolgáltatások által kezelt).
- Biztonságos LDAP (LDAPS) engedélyezve van az Azure Active Directory tartományi szolgáltatásokban.
- Megfelelő hálózati kapcsolat a HDInsight virtuális hálózat és az Azure Active Directory tartományi szolgáltatások virtuális hálózat, ha úgy dönt, különálló virtuális hálózatok az őket. A HDInsight virtuális hálózaton belüli virtuális gépek rendelkeznie kell egy virtuális hálózati társviszony-létesítésen keresztül az Azure Active Directory tartományi szolgáltatásokban üzemel. Ha a HDInsight és az Azure Active Directory tartományi szolgáltatások vannak telepítve az azonos virtuális hálózatba, a rendszer automatikusan létrehozza a kapcsolatot, és további semmit nem kell tennie.

## <a name="set-up-different-domain-controllers"></a>Állítsa be a különféle tartományvezérlők
HDInsight jelenleg csak az Azure AD DS támogatja a fő tartományvezérlő, a fürt által használt a Kerberos-kommunikációhoz. Azonban más összetett az Active Directory-beállításokat is lehetséges, mindaddig, amíg egy ilyen beállítás vezet, a HDInsight hozzáférést az Azure Active Directory tartományi szolgáltatások engedélyezése.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások
[Az Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md) biztosít, amely teljesen kompatibilis a Windows Server Active Directoryval felügyelt tartományhoz. A Microsoft gondoskodik kezelése, javításokat és a tartomány egy magas rendelkezésre állású (HA) beállítás figyelése. Telepítheti a fürt nem kell bajlódnunk a tartományvezérlők karbantartásához. 

Felhasználók, csoportok és jelszavak szinkronizálódnak, az Azure ad-ből. Az Azure Active Directory tartományi szolgáltatások az Azure AD-példányt a egyirányú szinkronizálás lehetővé teszi, hogy a felhasználók által a vállalati hitelesítő adatokkal jelentkezhetnek be a fürt. 

További információkért lásd: [konfigurálása HDInsight-fürtök az Azure AD DS segítségével ESP](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>A helyszíni Active Directory vagy az Active Directory IaaS virtuális gépeken

Ha egy helyszíni Active Directory-példányból vagy összetettebb Active Directory-beállításokat a tartomány, ezeket az Azure AD-identitások szinkronizálhatók az Azure AD Connect használatával. Engedélyezheti, hogy az Active Directory-bérlőre az Azure Active Directory tartományi Szolgáltatásokban. 

Mivel a Kerberos jelszókivonatokat alapul, meg kell [Jelszókivonat-szinkronizálás az Azure Active Directory tartományi szolgáltatások engedélyezése](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Ha az összevonási használja az Active Directory összevonási szolgáltatások (AD FS), engedélyeznie kell a Jelszókivonat-szinkronizálás. (Javasolt beállítás esetén lásd: [ebben a videóban](https://youtu.be/qQruArbu2Ew).) Jelszókivonat-szinkronizálás segít a vész-helyreállítási, abban az esetben az AD FS-infrastruktúra meghiúsul, és azt is védelmet nyújt a kiszivárgott hitelesítő adatok. További információkért lásd: [Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás engedélyezése](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Használatával a helyszíni Active Directory vagy az IaaS virtuális gépek önmagában, az Active Directory nélkül az Azure AD és az Azure Active Directory tartományi Szolgáltatásokban, nem egy támogatott konfigurációkra vonatkozó ESP HDInsight-fürtöket.

Ha összevonási használatban van, és a jelszókivonatokat megfelelően szinkronizálva lesznek, de hitelesítési hibák azért kapta, ellenőrizze, ha felhőalapú jelszavas hitelesítés engedélyezve van a PowerShell szolgáltatás egyszerű. Ha nem, be kell állítani egy [kezdőlap Kezdőtartomány felderítése (HRD) házirend](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) az Azure AD-bérlője számára. Ellenőrizze és állítsa be a HRD-szabályzattal:

1. Az Azure AD PowerShell modul telepítése.

   ```
   Install-Module AzureAD
   ```

2. Adja meg `Connect-AzureAD` globális rendszergazdai (bérlői rendszergazdai) hitelesítő adatok használatával.

3. Ellenőrizze, hogy ha a Microsoft Azure PowerShell szolgáltatásnév már létrejött.

   ```
   $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Ha még nem létezik (azaz ha `($powershellSPN -eq $null)`), majd hozza létre a szolgáltatásnevet.

   ```
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. Hozzon létre, és csatolja a szabályzatot a szolgáltatásnévnek.

   ```
   $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

   Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>További lépések

* [ESP HDInsight-fürtök konfigurálása](apache-domain-joined-configure-using-azure-adds.md)
* [ESP az Apache Hive-házirendek a HDInsight-fürtök konfigurálása](apache-domain-joined-run-hive.md)
* [ESP a HDInsight-fürtök kezelése](apache-domain-joined-manage.md) 
