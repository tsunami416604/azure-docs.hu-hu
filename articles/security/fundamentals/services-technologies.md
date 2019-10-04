---
title: Azure biztonsági szolgáltatások és technológiák | Microsoft Docs
description: A cikk az Azure biztonsági szolgáltatásainak és technológiáinak válogatott listáját tartalmazza.
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
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726565"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Az Azure-ban elérhető biztonsági szolgáltatások és technológiák

A jelenlegi és jövőbeli Azure-ügyfelekkel folytatott beszélgetésekben gyakran megkérdezjük, hogy van-e az Azure által kínált biztonsággal kapcsolatos szolgáltatások és technológiák listája?

Ha kiértékeli a Cloud Service Provider beállításait, hasznos lehet ezt az információt. Ezért ezt a listát a kezdéshez biztosítottuk.

Ez a lista idővel megváltozik és növekedni fog, ugyanúgy, mint az Azure. Ügyeljen arra, hogy rendszeresen ellenőrizze ezt a lapot, hogy naprakész maradjon a biztonsággal kapcsolatos szolgáltatásainkról és technológiákról.

## <a name="general-azure-security"></a>Általános Azure-biztonság
|Szolgáltatás|Leírás|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](/azure/security-center/security-center-intro)| Felhőalapú munkaterhelés-védelmi megoldás, amely biztonsági felügyeletet és komplex veszélyforrások elleni védelmet biztosít a hibrid Felhőbeli munkaterhelések között.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Biztonságos titkos kulcsok tárolása a jelszavakhoz, a kapcsolati karakterláncokhoz és az alkalmazások működésének megőrzéséhez szükséges egyéb információkhoz. |
|[Azure Monitor-naplók](/azure/log-analytics/log-analytics-overview)|Egy figyelési szolgáltatás, amely telemetria és más adatokat gyűjt, valamint egy lekérdezési nyelvet és analitikai motort biztosít az alkalmazások és erőforrások működési elemzéséhez. Önállóan vagy más szolgáltatásokkal, például Security Center használható. |
|[Azure dev/test Labs](/azure/lab-services/devtest-lab-overview)|Egy olyan szolgáltatás, amely segít a fejlesztőknek és a tesztelőknek gyorsan létrehozni az Azure-beli környezeteket, miközben minimalizálja a hulladékot és szabályozza a költségeket.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Tárbiztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;Storage&nbsp;szolgáltatás&nbsp;titkosítása](/azure/storage/common/storage-service-encryption)|Egy biztonsági funkció, amely automatikusan titkosítja az adatait az Azure Storage-ban.   |
|[StorSimple titkosított hibrid tárolás](/azure/storsimple/storsimple-ova-overview)| Integrált tárolási megoldás, amely a helyszíni eszközök és az Azure Cloud Storage közötti tárolási feladatokat kezeli.|
|[Azure ügyféloldali titkosítás](/azure/storage/common/storage-client-side-encryption)| Ügyféloldali titkosítási megoldás, amely az Azure Storage-ba való feltöltés előtt titkosítja az ügyfélalkalmazások adatain belüli adattitkosítást. a letöltése közben visszafejti az adatvesztést is. |
| [Azure Storage – közös hozzáférésű aláírások](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Közös hozzáférésű jogosultságkód a tárfiókban található erőforrások delegált hozzáférést biztosít.  |
|[Azure Storage-fiók kulcsainak](/azure/storage/common/storage-create-storage-account)| Hozzáférés-vezérlési módszer az Azure Storage-hoz, amelyet a rendszer a Storage-fiók elérésekor hitelesítésre használ. |
|[Azure-fájlmegosztás SMB 3,0 titkosítással](/azure/storage/files/storage-files-introduction)|Olyan hálózati biztonsági technológia, amely lehetővé teszi az automatikus hálózati titkosítást a Server Message Block (SMB) fájlmegosztás protokollja számára. |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Naplózási és metrikai technológia a Storage-fiókban lévő adatokhoz. |

<!------>

## <a name="database-security"></a>Adatbázis-biztonság
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;SQL&nbsp;tűzfal](/azure/sql-database/sql-database-firewall-configure)|Egy hálózati hozzáférés-vezérlési funkció, amely védelmet biztosít a hálózati alapú támadásokkal szemben az adatbázison. |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;Level Encryption](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Adatbázis-biztonsági technológia, amely részletességi szinten biztosítja a titkosítást.  |
| [Azure&nbsp;SQL&nbsp;Connection Encryption](/azure/sql-database/sql-database-control-access)|A biztonság garantálása érdekében az SQL Database IP-cím alapján történő kapcsolatkorlátozást érvényesítő tűzfalszabályokkal, a felhasználói identitás igazolását megkövetelő hitelesítési mechanizmusokkal, illetve csak bizonyos műveletek és adatok elérését lehetővé tévő engedélyezési mechanizmusokkal vezérli a hozzáférést. |
| [Az Azure SQL Always encryption](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Védi a bizalmas adatokat, például a hitelkártya-számokat vagy a nemzeti azonosító számokat (például az Egyesült államokbeli társadalombiztosítási számokat), Azure SQL Database vagy SQL Server adatbázisokban tárolva.  |
| [Azure&nbsp;SQL&nbsp;Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Adatbázis-biztonsági szolgáltatás, amely egy teljes adatbázis tárterületét titkosítja. |
| [Azure SQL Database naplózás](/azure/sql-database/sql-database-auditing)|Adatbázis-naplózási funkció, amely nyomon követi az adatbázis-eseményeket, és az Azure Storage-fiókban lévő naplóba írja azokat.  |


## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;-&nbsp;beli&nbsp;szerepköralapú Access Control](/azure/active-directory/role-based-access-control-configure)|Hozzáférés-vezérlési funkció, amely lehetővé teszi, hogy a felhasználók csak azokat az erőforrásokat férhessenek hozzá, amelyekre a szervezeten belüli szerepköreik alapján van szükségük.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Egy felhőalapú hitelesítési tárház, amely több-bérlős, felhőalapú címtárat és több Identitáskezelés-kezelő szolgáltatást támogat az Azure-on belül.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Identitáskezelés-kezelő szolgáltatás, amely lehetővé teszi, hogy az ügyfelek regisztráljanak, jelentkezzenek be és kezeljenek profilokat Azure-alapú alkalmazások használatakor.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| A Active Directory tartományi szolgáltatások felhőalapú és felügyelt verziója. |
| [Az Azure multi-factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| A biztonságos információkhoz való hozzáférés engedélyezése előtt a hitelesítés és ellenőrzés számos különböző formáját alkalmazó biztonsági rendelkezés. |

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vészhelyreállítás
|Szolgáltatás|Leírás|
|------|--------|
| [Azure&nbsp;Backup](/azure/backup/backup-introduction-to-azure-backup)| Azure-alapú szolgáltatás, amely az Azure-felhőben tárolt adatbiztonsági mentésére és helyreállítására szolgál. |
| [Azure&nbsp;site&nbsp;Recovery](/azure/site-recovery/site-recovery-overview)|Egy online szolgáltatás, amely a fizikai és virtuális gépeken futó munkaterheléseket replikálja egy elsődleges helyről egy másodlagos helyre, hogy a meghibásodás után engedélyezze a szolgáltatások helyreállítását. |

## <a name="networking"></a>Hálózat
|Szolgáltatás|Leírás|
|------|--------|
| [Hálózati&nbsp;biztonsági&nbsp;csoportok](/azure/virtual-network/virtual-networks-nsg)| Egy 5 rekordos hálózati hozzáférés-vezérlési funkció, amely lehetővé teszi vagy megtagadja a döntéseket.  |
| [Azure-VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| VPN-végpontként használt hálózati eszköz, amely lehetővé teszi a létesítmények közötti hozzáférést az Azure-beli virtuális hálózatokhoz.  |
| [Azure-Application Gateway](/azure/application-gateway/application-gateway-introduction)|Egy speciális webalkalmazás-terheléselosztó, amely URL-cím alapján irányítható át, és SSL-kiürítést hajt végre. |
|[Webalkalmazási tűzfal](/azure/frontdoor/waf-overview) WAF|A Application Gateway szolgáltatása, amely a webalkalmazások központosított védelmét nyújtja a gyakori biztonsági rések és sebezhetőségek ellen|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|TCP/UDP-alkalmazás hálózati terheléselosztó. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Dedikált WAN-kapcsolat a helyszíni hálózatok és az Azure-beli virtuális hálózatok között. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globális DNS-terheléselosztó.|
| [Azure Application Proxy](/azure/active-directory/active-directory-application-proxy-get-started)| A helyszíni környezetben üzemeltetett webalkalmazások távoli elérésének biztonságossá tételéhez használt hitelesítő kezelőfelület. |
|[Azure Firewall](/azure/firewall/overview)|Felügyelt, felhőalapú hálózati biztonsági szolgáltatás, amely megvédi az Azure Virtual Network-erőforrásait.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|Az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben.|
|[Virtuális hálózati szolgáltatásvégpontok](/azure/virtual-network/virtual-network-service-endpoints-overview)|Közvetlen kapcsolaton keresztül kiterjesztheti a virtuális hálózat privát címterület-területét és a VNet identitását az Azure-szolgáltatásokra.|