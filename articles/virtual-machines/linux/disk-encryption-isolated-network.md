---
title: Azure lemeztitkosítás elszigetelt hálózaton
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Microsoft Azure lemeztitkosítás linuxos virtuális gépekhez.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 02/27/2020
ms.custom: seodec18
ms.openlocfilehash: aa0dc204a017e2d40eb3952a9ede0755127f8de2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970657"
---
# <a name="azure-disk-encryption-on-an-isolated-network"></a>Azure lemeztitkosítás elszigetelt hálózaton

Ha a kapcsolatot tűzfal, proxykövetelmény vagy hálózati biztonsági csoport (NSG) beállításai korlátozzák, a bővítmény szükséges feladatok végrehajtására való képessége megszakadhat. Ez a megszakítás állapotüzeneteket eredményezhet, például a "Bővítmény állapota nem érhető el a virtuális gépen."

## <a name="package-management"></a>Csomagkezelés

Az Azure Disk Encryption számos összetevőtől függ, amelyek általában az ADE-engedélyezés részeként vannak telepítve, ha még nincsenek jelen. Ha tűzfal mögött vagy más módon nincs elszigetelve az internettől, ezeket a csomagokat előre telepíteni kell, vagy helyileg kell elérhetőnek lennie.

Itt vannak az egyes disztribúciókhoz szükséges csomagok. A támogatott distististokok és kötettípusok teljes listáját a támogatott virtuális gépek és operációs rendszerek című témakörben [tetszetős ekben lehet.](disk-encryption-overview.md#supported-vms-and-operating-systems)

- **Ubuntu 14.04, 16.04, 18.04**: lsscsi, psmisc, at, cryptsetup-bin, python-parted, python-six, procps
- **CentOS 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, pyparted, procps-ng, util-linux
- **CentOS 6.8**: lsscsi, psmisc, lvm2, uuid, at, cryptsetup-reencrypt, pyparted, python-six
- **RedHat 7.2 - 7.7**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup, cryptsetup-reencrypt, procps-ng, util-linux
- **RedHat 6.8**: lsscsi, psmisc, lvm2, uuid, at, patch, cryptsetup-reencrypt
- **openSUSE 42.3, SLES 12-SP4, 12-SP3**: lsscsi, cryptsetup

A Red Hat, ha egy proxy szükséges, meg kell győződnie arról, hogy az előfizetés-kezelő és yum megfelelően vannak beállítva. További információt az [Előfizetés-kezelő és a yum problémák elhárítása című témakörben talál.](https://access.redhat.com/solutions/189533)  

Ha a csomagokmanuálisan vannak telepítve, az új verziók megjelenésekor manuálisan is frissíteni kell őket.

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Az alkalmazott hálózati biztonsági csoport beállításoknak továbbra is lehetővé kell tenniük, hogy a végpont megfeleljen a lemeztitkosítás dokumentált hálózati konfigurációs előfeltételeinek.  Lásd: [Azure lemeztitkosítás: Hálózati követelmények](disk-encryption-overview.md#networking-requirements)

## <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure lemeztitkosítás az Azure AD-vel (korábbi verzió)

Ha [az Azure Disk Encryption az Azure AD (előző verzió)](disk-encryption-overview-aad.md)használatával, az [Azure Active Directory-könyvtárat](../../active-directory/azuread-dev/active-directory-authentication-libraries.md) manuálisan kell telepíteni az összes distióhoz (a distro-nak megfelelő csomagokon kívül, a [fent felsoroltak szerint).](#package-management)

Ha az Azure [AD-hitelesítő adatokkal](disk-encryption-linux-aad.md)engedélyezve van a titkosítás, a célvirtuális gépnek engedélyeznie kell a kapcsolatot az Azure Active Directory végpontjaihoz és a Key Vault-végpontokhoz. Az Aktuális Azure Active Directory hitelesítési végpontok az [Office 365 URL-címeinek és IP-címtartományainak](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentációjának 56. A Key Vault-útmutató a [tűzfal mögötti Azure Key Vault elérésére](../../key-vault/key-vault-access-behind-firewall.md)vonatkozó dokumentációban található.

### <a name="azure-instance-metadata-service"></a>Azure-példány metaadat-szolgáltatása 

A virtuális gépnek hozzá kell tudnia férni az [Azure Instance metaadatszolgáltatás-végpontjához,](instance-metadata-service.md) amely egy jól ismert, nem irányítható IP-címet (`169.254.169.254`) használ, amely csak a virtuális gépen belül érhető el.  Nem támogatottak azok a proxykonfigurációk, amelyek módosítják a helyi HTTP-forgalmat erre a címre (például x-forwarded-for fejléc hozzáadása).

## <a name="next-steps"></a>További lépések

- További lépések az [Azure lemeztitkosítással kapcsolatos hibaelhárításhoz](disk-encryption-troubleshooting.md)
- [Azure-adattitkosítás inaktív helyen](../../security/fundamentals/encryption-atrest.md)
