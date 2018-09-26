---
title: Az Azure biztonsági szolgáltatások és technológiák |} A Microsoft Docs
description: A cikk az Azure biztonsági szolgáltatások és technológiák rendszerezett listáját.
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
ms.openlocfilehash: 0addad1db4d9f897736a7793c5489897bca2cd38
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093426"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Biztonsági szolgáltatások és technológiák elérhető az Azure-ban

A jelenlegi és jövőbeli Azure-ügyfelek párbeszédet hogy milyen gyakran arra kéri, "rendelkezik a biztonsággal kapcsolatos szolgáltatások és technológiák, amelyek az Azure által biztosított lehetőségeket listáját?"

Cloud service provider beállítások értékelésekor hasznos rendelkezik ezekkel az információkkal. Tehát ebben a listában az első lépésekhez adtunk meg.

Ez a lista idővel módosítja, és növekszik, ugyanúgy, mint az Azure elvégzi. Ellenőrizze, hogy naprakész információkat a biztonsággal kapcsolatos szolgáltatások és technológiák rendszeres időközönként ezen az oldalon.

## <a name="general-azure-security"></a>Általános Azure biztonság
|Szolgáltatás|Leírás|
|--------|--------|
|[Azure&nbsp;biztonsági&nbsp;Center](../security-center/security-center-intro.md)| A számítási feladatok védelmét felhőmegoldás, amely biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| A jelszavak, kapcsolati karakterláncok és egyéb információkat, az alkalmazások futtatása egy biztonságos titkoskulcs üzlet. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|A figyelési szolgáltatás telemetriai és egyéb adatokat gyűjt, és a egy lekérdezési nyelvet és elemzési motort, hogy az operational insights, az alkalmazásokat és erőforrásokat biztosít. Használható önállóan, vagy más szolgáltatások például a Security Center. |
|[Az Azure Dev/Test Lab](../devtest-lab/devtest-lab-overview.md)|Egy szolgáltatás, amellyel gyorsan a fejlesztőknek és tesztelőknek környezetek létrehozása az Azure-ban hatékonysággal és a költségek ellenőrzés alatt tartásával.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Tárbiztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;tárolási&nbsp;szolgáltatás&nbsp;titkosítás](../storage/common/storage-service-encryption.md)|Egy biztonsági szolgáltatás, amely automatikusan titkosítja az adatokat az Azure storage-ban.   |
|[A StorSimple hibrid tárolás titkosítása](../storsimple/storsimple-ova-overview.md)| Egy integrált tárolási megoldás, amely a helyszíni eszközök és az Azure felhőalapú tárolás közötti tárolási feladatokat kezeli.|
|[Az Azure ügyféloldali titkosítás](../storage/common/storage-client-side-encryption.md)| Ügyféloldali titkosítás megoldás, amely az adatok ügyfélalkalmazásokon belüli titkosítását az Azure Storage-; feltöltése előtt is visszafejti az adatok letöltése során. |
| [Az Azure Storage közös hozzáférésű Jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít.  |
|[Az Azure Storage Account Keys](../storage/common/storage-create-storage-account.md)| Az Azure storage, a tárfiók elérésekor használt hitelesítés hozzáférési vezérlés módját. |
|[Azure-fájlmegosztások az SMB 3.0-titkosítás](../storage/files/storage-files-introduction.md)|Egy hálózati biztonsági technológia lehetővé teszi az automatikus hálózati titkosítás a Server Message Block (SMB) fájlmegosztási protokollt. |
|[Az Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Naplózás és mérőszámok generálásához technológia a tárfiókban lévő adatokat. |

<!------>

## <a name="database-security"></a>Adatbázis-biztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;SQL&nbsp;tűzfal](../sql-database/sql-database-firewall-configure.md)|Egy hálózati hozzáférést vezérlő szolgáltatás, amely az adatbázis hálózatalapú támadások ellen védi. |
|[Azure&nbsp;SQL&nbsp;cella&nbsp;titkosítási szint](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Adatbázis biztonsági technológia, amely részletes titkosítását biztosítja.  |
| [Azure&nbsp;SQL&nbsp;kapcsolat titkosítása](../sql-database/sql-database-control-access.md)|A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. |
| [Az Azure SQL mindig a titkosítási](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Bizalmas adatok, például hitelkártya-számokat vagy különféle nemzeti azonosítószámokat (például Egyesült államokbeli társadalombiztosítási szám), Azure SQL Database vagy SQL Server-adatbázisokban tárolt védi.  |
| [Azure&nbsp;SQL&nbsp;transzparens adattitkosítás](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Egy adatbázis biztonsági funkció, amely titkosítja a tárolót a teljes adatbázisra vonatkozóan. |
| [Az Azure SQL Database naplózás](../sql-database/sql-database-auditing.md)|Egy adatbázis-naplózás funkció, amely nyomon követi az adatbázisok eseményeit és felvezeti ezeket egy naplófájlba, az Azure storage-fiókban.  |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;szerepkör&nbsp;alapú&nbsp;hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md)|Egy hozzáférés-vezérlési funkciója tervezték, hogy a felhasználók csak a hozzáférést a szervezeten belül szerepkörökhöz alapján kell az erőforrások eléréséhez.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|A felhőalapú hitelesítés adattár, amely támogatja a több-bérlős, felhőalapú címtár, és több identity management szolgáltatás Azure-ban.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Egy identitáskezelő szolgáltatás, amely lehetővé teszi a módjának regisztrációs, bejelentkezési, ügyfelek és a profiljuk kezelését az Azure-alapú alkalmazások használatakor.   |
| [Az Azure Active Directory Domain Servicesben](../active-directory-domain-services/active-directory-ds-overview.md)| A felhőalapú és a felügyelt verziója Active Directory Domain Servicesben. |
| [Az Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Egy biztonsági rendelkezni, amely számos különböző űrlapos hitelesítés és az ellenőrzés, mielőtt hozzáférést alkalmaz a védett adatok. |

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;biztonsági mentés](../backup/backup-introduction-to-azure-backup.md)| Egy Azure-alapú szolgáltatás biztonsági mentése és visszaállítása az Azure-felhőben lévő adatok segítségével. |
| [Azure&nbsp;hely&nbsp;helyreállítási](../site-recovery/site-recovery-overview.md)|Egy online szolgáltatás, amely replikálja a futó számítási feladatok fizikai és virtuális gépek (VM) egy elsődleges helyről egy másodlagos helyre engedélyezése a szolgáltatások helyreállítása meghibásodás után. |

## <a name="networking"></a>Hálózat
|Szolgáltatás|Leírás|
|------|--------|
| [Hálózati&nbsp;biztonsági&nbsp;csoportok](../virtual-network/virtual-networks-nsg.md)| A hálózati hozzáférés-vezérlési funkciója 5 rekordos használatával, hogy engedélyezi vagy megtagadja a döntéseket hozhat.  |
| [Az Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Egy VPN-végpontnak, lehetővé teszi a létesítmények közötti hozzáférés az Azure Virtual Networks használt hálózati eszköz.  |
| [Az Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|Speciális webes alkalmazás egyes terheléselosztó, amely irányítása URL-cím alapján, és hajtsa végre az SSL-tehermentesítést. |
| [Azure Load Balancer](../load-balancer/load-balancer-overview.md)|TCP/UDP-alkalmazás a hálózati terheléselosztás. |
| [Az Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Egy dedikált WAN-hivatkozás a helyszíni hálózatok és az Azure-beli virtuális hálózatok között. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Egy globális DNS load balancert.|
| [Az Azure Application Proxy](../active-directory/active-directory-application-proxy-get-started.md)| Egy hitelesítése előtér segítségével biztonságos távoli hozzáférés a webes alkalmazásokhoz a helyszínen üzemeltetett. |