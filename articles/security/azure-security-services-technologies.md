---
title: Az Azure biztonsági szolgáltatásokat és technológiákat |} Microsoft Docs
description: A cikk az Azure biztonsági szolgáltatásokat és technológiákat válogatott listáját tartalmazza.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641458"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Biztonsági szolgáltatásokat és technológiákat érhető el az Azure-on

A jelenlegi és jövőbeli Azure-ügyfél folytatott beszélgetéseket a rendszer gyakran megkéri "rendelkezik a biztonsági szolgáltatásokat és technológiákat, amelyre az Azure ajánlat listáját?"

Amikor a felhőbeli szolgáltatás szolgáltatója lehetőségek kiértékelése, akkor célszerű ezeket az adatokat. Ezért adtunk meg ebben a listában, az első lépésekhez.

Az idő múlásával a lista módosítja, és nő, ahogy Azure teszi. Ellenőrizze, hogy ezen a lapon, rendszeresen figyelje meg a biztonsági szolgáltatásokat és technológiákat.

## <a name="general-azure-security"></a>Általános Azure biztonsági
|Szolgáltatás|Leírás|
|--------|--------|
|[Azure&nbsp;biztonsági&nbsp;Center](../security-center/security-center-intro.md)| A felhőalapú alkalmazások és szolgáltatások adatvédelmi megoldást hibrid felhő-munkaterhelések biztonsági kezelése és az advanced threat protection biztosító.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| A biztonságos kulcsok tároló a jelszavak, a kapcsolati karakterláncok és más információkat kell tartani az alkalmazások használata. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|Telemetriai adatok és egyéb adatokat gyűjt, és egy lekérdezési nyelv és az elemzések motor képes biztosítani a alkalmazások és erőforrások az operational insights figyelési szolgáltatás. Használható önmagában, vagy más szolgáltatásokkal például a Security Center. |
|[Az Azure fejlesztési és tesztelési célú Labs](../devtest-lab/devtest-lab-overview.md)|Egy szolgáltatás, amellyel a fejlesztők és a tesztelést gyorsan hozzon létre, ugyanakkor minimalizálja a pazarlás és költségek szabályozása környezeteket az Azure-ban.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Tárbiztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;tárolási&nbsp;szolgáltatás&nbsp;titkosítás](../storage/common/storage-service-encryption.md)|Egy biztonsági funkció, amely automatikusan titkosítja az adatokat az Azure-tárfiókba.   |
|[A StorSimple hibrid tárolási titkosított](../storsimple/storsimple-ova-overview.md)| Egy integrált tárolási megoldás, amely kezeli a tárolási feladatok elvégzéséről a helyszíni eszközök és az Azure felhőalapú tárolást között.|
|[Az Azure ügyféloldali titkosítás](../storage/common/storage-client-side-encryption.md)| Egy ügyféloldali titkosítás megoldás, amely ügyfélalkalmazások található adatok Azure Storage; feltöltés előtt titkosítja is visszafejti az adatok letöltése során. |
| [Az Azure Storage közös hozzáférésű Jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|A közös hozzáférésű jogosultságkód a tárfiókban lévő erőforrások delegált hozzáférést biztosít.  |
|[Az Azure Storage-fiók kulcsok](../storage/common/storage-create-storage-account.md)| Egy hozzáférés-szabályozási módszer az Azure-tárolás a tárfiók elérésekor használt hitelesítés. |
|[Azure fájlmegosztásokat az SMB 3.0-titkosítás](../storage/files/storage-files-introduction.md)|A hálózati biztonsági technológia, amely lehetővé teszi az automatikus hálózati titkosítás a Server Message Block (SMB) fájlmegosztási protokoll. |
|[Az Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| A naplózás és a metrikák létrehozásakor technológia a tárfiókban lévő adatokat. |

<!------>

## <a name="database-security"></a>Adatbázis-biztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;SQL&nbsp;tűzfal](../sql-database/sql-database-firewall-configure.md)|A hálózati hozzáférés-vezérlési funkciója, amely a hálózati támadásoktól adatbázis véd. |
|[Azure&nbsp;SQL&nbsp;cella&nbsp;titkosítási szint](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Adatbázis biztonsági technológia, amely alapszinten titkosítását biztosítja.  |
| [Azure&nbsp;SQL&nbsp;kapcsolati titkosítása](../sql-database/sql-database-control-access.md)|A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. |
| [Az Azure SQL mindig titkosítás](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Bizalmas adatok védelmét, hitelkártyaszámok vagy a nemzeti azonosító szám (például Egyesült államokbeli társadalombiztosítási számot), például az Azure SQL Database vagy az SQL Server adatbázis tárolja.  |
| [Azure&nbsp;SQL&nbsp;átlátható adattitkosítás](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Egy adatbázis biztonsági funkció, a tárhely teljes adatbázis titkosítja. |
| [Az Azure SQL Database naplózási](../sql-database/sql-database-auditing.md)|Adatbázis naplózási szolgáltatás, amely nyomon követi az adatbázis-események, mind az írás őket naplózási jelentkezzen be az Azure storage-fiók.  |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;szerepkör&nbsp;alapú&nbsp;hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md)|Egy hozzáférés-vezérlési funkciója úgy tervezték, hogy a felhasználók csak a hozzáférést a szervezeten belüli szerepük alapján kötelező erőforrások eléréséhez.  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|A felhőalapú hitelesítés tárház, amely támogatja az egy több-bérlős, a felhőalapú és több identity management szolgáltatás Azure-ban.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Az identity management szolgáltatás, amely lehetővé teszi, hogy hogyan vezérelheti az ügyfelek regisztráció, bejelentkezés, profilok és kezelése az Azure-alapú alkalmazások használatakor.   |
| [Az Azure Active Directory tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md)| Egy felhőalapú, felügyelt verziója Active Directory tartományi szolgáltatásokban. |
| [Az Azure többtényezős hitelesítés](../active-directory/authentication/multi-factor-authentication.md)| A biztonsági kiépítése számos különböző űrlapos hitelesítési és ellenőrzési, mielőtt hozzáférést által védett adatok. |

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és katasztrófa utáni helyreállítás
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;biztonsági mentése](../backup/backup-introduction-to-azure-backup.md)| Egy Azure-alapú szolgáltatás biztonsági mentése és visszaállítása az Azure felhőben tárolt adatainak használatával. |
| [Azure&nbsp;hely&nbsp;helyreállítási](../site-recovery/site-recovery-overview.md)|Egy olyan online szolgáltatás, amelyek replikációja munkaterheléseinek fizikai és virtuális gépek (VM) egy elsődleges helyről egy másodlagos helyre, egy meghibásodás után helyreállítási szolgáltatások engedélyezése. |

## <a name="networking"></a>Hálózat
|Szolgáltatás|Leírás|
|------|--------|
| [Hálózati&nbsp;biztonsági&nbsp;csoportok](../virtual-network/virtual-networks-nsg.md)| A hálózati hozzáférés-vezérlési funkciója az 5-rekordot végre a engedélyezi vagy megtagadja a döntések.  |
| [Az Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| A hálózati eszköz egy VPN-végponttal, hogy a létesítmények közötti hozzáférés az Azure virtuális hálózataihoz használatos.  |
| [Az Azure alkalmazás átjáró](../application-gateway/application-gateway-introduction.md)|Speciális webes alkalmazás egyes terheléselosztó, amely továbbításához URL-címe alapján, és hajtsa végre az SSL-feladatkiszervezést. |
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|A TCP/UDP-alkalmazás hálózati terheléselosztó. |
| [Az Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Egy dedikált WAN-hivatkozás a helyszíni hálózatokhoz és az Azure virtuális hálózatok között. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| A globális DNS terheléselosztó.|
| [Az Azure Application Proxy](../active-directory/active-directory-application-proxy-get-started.md)| Egy hitelesítése előtér segítségével biztonságos távoli hozzáférés a webes alkalmazásokhoz, a helyben tárolt. |