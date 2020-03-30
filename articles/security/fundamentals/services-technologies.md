---
title: Azure biztonsági szolgáltatások és technológiák | Microsoft dokumentumok
description: A cikk az Azure Security-szolgáltatások és -technológiák válogatott listáját tartalmazza.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726565"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Az Azure-ban elérhető biztonsági szolgáltatások és technológiák

A jelenlegi és jövőbeli Azure-ügyfelekkel folytatott beszélgetéseink során gyakran felteszik a kérdést, hogy "rendelkezik-e az Azure által kínált összes biztonsággal kapcsolatos szolgáltatás és technológia listájával?"

A felhőszolgáltató beállításainak kiértékelése során hasznos lehet ezeket az információkat. Ezért a kezdéshez megadtuk ezt a listát.

Idővel ez a lista az Azure-hoz ugyanúgy változik és növekszik, mint az Azure. Győződjön meg róla, hogy rendszeresen ellenőrizze ezt az oldalt, hogy naprakész legyen a biztonsággal kapcsolatos szolgáltatásainkkal és technológiáinkkal kapcsolatban.

## <a name="general-azure-security"></a>Általános Azure-biztonság
|Szolgáltatás|Leírás|
|--------|--------|
|[Az&nbsp;&nbsp;Azure Biztonsági Központ](/azure/security-center/security-center-intro)| Felhőalapú számítási feladatok elleni védelem, amely biztonsági felügyeletet és speciális veszélyforrások elleni védelmet biztosít a hibrid felhőbeli számítási feladatok között.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Biztonságos titkos fájlokat tárol a jelszavak, kapcsolati karakterláncok és egyéb információk, amelyek az alkalmazások működéséhez szükséges. |
|[Az Azure Monitor naplói](/azure/log-analytics/log-analytics-overview)|Egy figyelési szolgáltatás, amely összegyűjti a telemetriai adatokat és egyéb adatokat, és egy lekérdezési nyelvet és elemzési motort biztosít az alkalmazások és erőforrások működési elemzéseinek biztosításához. Használható önmagában vagy más szolgáltatásokkal, például a Security Centerrel. |
|[Azure fejlesztői/tesztelési laborok](/azure/lab-services/devtest-lab-overview)|Olyan szolgáltatás, amely segít a fejlesztőknek és a tesztelőknek gyorsan létrehozni a környezeteket az Azure-ban, miközben minimalizálja a hulladékot és a költségeket.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Tárbiztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;&nbsp;storage-szolgáltatás&nbsp;titkosítása](/azure/storage/common/storage-service-encryption)|Egy biztonsági funkció, amely automatikusan titkosítja az adatokat az Azure storage-ban.   |
|[StorSimple titkosított hibrid tároló](/azure/storsimple/storsimple-ova-overview)| Integrált tárolási megoldás, amely a helyszíni eszközök és az Azure felhőalapú tárolása közötti tárolási feladatokat kezeli.|
|[Azure ügyféloldali titkosítás](/azure/storage/common/storage-client-side-encryption)| Ügyféloldali titkosítási megoldás, amely az Azure Storage-ba való feltöltés előtt titkosítja az ügyfélalkalmazásokon belüli adatokat; is visszafejti az adatokat letöltés közben. |
| [Az Azure Storage megosztott hozzáférési aláírásai](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|A megosztott hozzáférésű aláírás delegált hozzáférést biztosít a tárfiókban lévő erőforrásokhoz.  |
|[Azure storage-fiók kulcsai](/azure/storage/common/storage-create-storage-account)| Az Azure storage hozzáférés-vezérlési módszere, amely a tárfiók elérésekor hitelesítésre szolgál. |
|[Azure file megosztások SMB 3.0 titkosítással](/azure/storage/files/storage-files-introduction)|Hálózati biztonsági technológia, amely lehetővé teszi a Kiszolgálói üzenetblokk (SMB) fájlmegosztási protokoll automatikus hálózati titkosítását. |
|[Azure Storage-statisztika](/rest/api/storageservices/Storage-Analytics)| A naplózási és metrikákat generáló technológia a tárfiókban lévő adatokhoz. |

<!------>

## <a name="database-security"></a>Adatbázis-biztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;&nbsp;SQL tűzfal](/azure/sql-database/sql-database-firewall-configure)|Hálózati hozzáférés-vezérlési szolgáltatás, amely védelmet nyújt az adatbázis hálózati alapú támadásai ellen. |
|[Azure&nbsp;&nbsp;SQL&nbsp;cellaszintű titkosítás](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Adatbázis-biztonsági technológia, amely részletes titkosítást biztosít.  |
| [Azure&nbsp;&nbsp;SQL-kapcsolat titkosítása](/azure/sql-database/sql-database-control-access)|A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. |
| [Azure SQL mindig titkosítás](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Védi az Azure SQL Database vagy AZ SQL Server adatbázisokban tárolt bizalmas adatokat, például hitelkártyaszámokat vagy nemzeti azonosító számokat (például amerikai társadalombiztosítási számokat).  |
| [Azure&nbsp;&nbsp;SQL transzparens adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Adatbázis-biztonsági szolgáltatás, amely titkosítja a teljes adatbázis tárolását. |
| [Azure SQL-adatbázis naplózása](/azure/sql-database/sql-database-auditing)|Adatbázis-naplózási szolgáltatás, amely nyomon követi az adatbázis-eseményeket, és az Azure storage-fiókjában naplóba írja őket.  |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;&nbsp;szerepköralapú hozzáférés-vezérlés&nbsp;](/azure/active-directory/role-based-access-control-configure)|Hozzáférés-vezérlési szolgáltatás, amely lehetővé teszi a felhasználók számára, hogy csak az erőforrásokhoz férjenek hozzá a szervezeten belüli szerepkörük alapján.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Felhőalapú hitelesítési tárház, amely támogatja a több-bérlős, felhőalapú címtár és több identitáskezelési szolgáltatások az Azure-ban.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Identitáskezelési szolgáltatás, amely lehetővé teszi, hogy az Azure-alapú alkalmazások használata esetén szabályozhatja, hogy az ügyfelek hogyan regisztrálnak, jelentkezzenek be és kezeljék a profiljukat.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Az Active Directory tartományi szolgáltatások felhőalapú és felügyelt verziója. |
| [Azure többtényezős hitelesítés](/azure/active-directory/authentication/multi-factor-authentication)| Olyan biztonsági rendelkezés, amely a hitelesítés és ellenőrzés számos különböző formáját alkalmazza, mielőtt lehetővé teszi a biztonságos információkhoz való hozzáférést. |

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;biztonsági mentés](/azure/backup/backup-introduction-to-azure-backup)| Az Azure-alapú szolgáltatás, amely az Azure-felhőben lévő adatok biztonsági és visszaállításához szolgál. |
| [Az&nbsp;&nbsp;Azure webhely helyreállítása](/azure/site-recovery/site-recovery-overview)|Egy online szolgáltatás, amely replikálja a fizikai és virtuális gépeken (VM-eken) futó számítási feladatokat egy elsődleges helyről egy másodlagos helyre, hogy lehetővé tegye a szolgáltatások helyreállítását egy hiba után. |

## <a name="networking"></a>Hálózat
|Szolgáltatás|Leírás|
|------|--------|
| [Hálózati&nbsp;&nbsp;biztonsági csoportok](/azure/virtual-network/virtual-networks-nsg)| Hálózati hozzáférés-vezérlési szolgáltatás, amely 5-t használja a döntések engedélyezéséhez vagy megtagadásához.  |
| [Azure VPN-átjáró](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Vpn-végpontként használt hálózati eszköz, amely lehetővé teszi a létesítmények közötti hozzáférést az Azure virtuális hálózatokhoz.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Egy speciális webalkalmazás-terheléselosztó, amely képes az URL-cím alapján irányítani és SSL-kiszervezést végrehajtani. |
|[Webalkalmazás tűzfala](/azure/frontdoor/waf-overview) (WAF)|Az Application Gateway szolgáltatása, amely központosított védelmet nyújt a webalkalmazásoknak a gyakori biztonsági rések és biztonsági rések elleni védelemben|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|TCP/UDP alkalmazás hálózati terheléselosztója. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Dedikált WAN-kapcsolat a helyszíni hálózatok és az Azure virtuális hálózatok között. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globális DNS-terheléselosztó.|
| [Azure alkalmazásproxy](/azure/active-directory/active-directory-application-proxy-get-started)| A helyszíni üzemeltetett webalkalmazások távelérésének biztosítására használt előtér-hitelesítés. |
|[Azure Firewall](/azure/firewall/overview)|Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely védi az Azure virtuális hálózati erőforrásait.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|Kombinálva alkalmazástervezési ajánlott eljárások, védelmet nyújt a DDoS-támadások ellen.|
|[Virtuális hálózat szolgáltatás végpontjai](/azure/virtual-network/virtual-network-service-endpoints-overview)|Kiterjeszti a virtuális hálózat magáncímterét és a virtuális hálózat identitását az Azure-szolgáltatásokra, közvetlen kapcsolaton keresztül.|