---
title: Azure titkosítás áttekintése | Microsoft dokumentumok
description: További információ az Azure különböző titkosítási lehetőségeiről
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 5d8fd578c5539c83e37a232d8425ad8bdf22129b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125050"
---
# <a name="azure-encryption-overview"></a>Azure-titkosítás – áttekintés

Ez a cikk áttekintést nyújt arról, hogyan használják a titkosítást a Microsoft Azure-ban. Ez magában foglalja a főbb titkosítási területeket, beleértve az inaktív titkosítást, a repülés közbeni titkosítást és a kulcskezelést az Azure Key Vault segítségével. Minden szakasz részletesebb információkra mutató hivatkozásokat tartalmaz.

## <a name="encryption-of-data-at-rest"></a>Az inaktív adatok titkosítása

A nyugalmi állapotban lévő adatok olyan információkat tartalmaznak, amelyek a fizikai adathordozón, bármilyen digitális formátumban állandó tárolóban találhatók. Az adathordozó tartalmazhat mágneses vagy optikai adathordozón lévő fájlokat, archivált adatokat és adatbiztonsági mentéseket. A Microsoft Azure számos adattárolási megoldást kínál a különböző igényeknek megfelelően, beleértve a fájlokat, a lemezt, a blobot és a táblatárolást. A Microsoft titkosítást is biztosít az [Azure SQL Database](../../sql-database/sql-database-technical-overview.md), az Azure [Cosmos DB](../../data-factory/introduction.md)és az Azure Data Lake védelme érdekében.

Az inaktív adatok titkosítása elérhető a szolgáltatások szolgáltatások szolgáltatásként (SaaS), platform szolgáltatásként (PaaS) és infrastruktúra szolgáltatásként (IaaS) felhőmodellek szolgáltatások hoz érhető el. Ez a cikk összegzi és erőforrásokat biztosít az Azure titkosítási beállításainak használatához.

Az inaktív adatok Azure-beli titkosításának részletesebb, az [Azure Data Encryption-at-In](encryption-atrest.md)című témakörében.

## <a name="azure-encryption-models"></a>Azure titkosítási modellek

Az Azure támogatja a különböző titkosítási modellek, beleértve a kiszolgálóoldali titkosítást használó szolgáltatás által felügyelt kulcsok, ügyfél által felügyelt kulcsok key vault, vagy az ügyfél által felügyelt kulcsok az ügyfél által vezérelt hardver. Az ügyféloldali titkosítással kezelheti és tárolhatja a kulcsokat a helyszínen vagy egy másik biztonságos helyen.

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Az ügyféloldali titkosítás az Azure-on kívül történik. A következőket tartalmazza:

- Az ügyfél adatközpontjában futó alkalmazás vagy egy szolgáltatásalkalmazás által titkosított adatok.
- Az Azure által immár titkosított adatok.

Az ügyféloldali titkosítással a felhőszolgáltatók nem férnek hozzá a titkosítási kulcsokhoz, és nem tudják visszafejteni ezeket az adatokat. Ön tartja fenn a kulcsok teljes irányítását.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

A három kiszolgálóoldali titkosítási modell különböző kulcskezelési jellemzőket kínál, amelyek et az igényeinek megfelelően választhat:

- **Szolgáltatás által felügyelt kulcsok**: A vezérlés és a kényelem kombinációját biztosítja az alacsony terheléssel.

- **Ügyfél által felügyelt kulcsok**: Ön ellenőrzi a kulcsokat, beleértve a Bring Your Own Keys (BYOK) támogatást, vagy lehetővé teszi, hogy újakat hozzon létre.

- **Szolgáltatás által felügyelt kulcsok az ügyfél által vezérelt hardverben:** Lehetővé teszi a kulcsok kezelését a saját tárházban, a Microsoft ellenőrzésén kívül. Ezt a jellemzőt a Host Your Own Key (HYOK) (HOST Your Own Key) (HOST Your Own Key) (HOST Your Own Key) (HOST Your Own Key) ( A konfiguráció azonban összetett, és a legtöbb Azure-szolgáltatás nem támogatja ezt a modellt.

### <a name="azure-disk-encryption"></a>Azure lemeztitkosítás

A Windows és Linux virtuális gépek az [Azure lemeztitkosítással](/azure/security/fundamentals/azure-disk-encryption-vms-vmss)védhetik meg a [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technológiát és a Linux [DM-Crypt technológiát](https://en.wikipedia.org/wiki/Dm-crypt) az operációs rendszer lemezei és az adatlemezek teljes kötettitkosítással történő védelmére.

A titkosítási kulcsokat és a titkos kulcsokat az [Azure Key Vault-előfizetés](../../key-vault/key-vault-overview.md)védi. Az Azure Backup szolgáltatás használatával biztonsági másolatot készíthet és visszaállíthat a kulcstitkosítási kulcs (KEK) konfigurációját használó titkosított virtuális gépekről (VM-ek).

### <a name="azure-storage-service-encryption"></a>Azure storage-szolgáltatás titkosítása

Az Azure Blob storage és az Azure fájlmegosztások in-in tárolt adatok kiszolgálóoldali és ügyféloldali forgatókönyvekben is titkosíthatók.

[Az Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) automatikusan titkosítja az adatokat, mielőtt azok tárolása, és automatikusan visszafejti az adatokat, amikor beolvassa. A folyamat teljesen átlátható a felhasználók számára. A Storage Service Encryption 256 bites [Advanced Encryption Standard (AES) titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)használ, amely az egyik legerősebb blokktitkosítás. Az AES transzparens módon kezeli a titkosítást, a visszafejtést és a kulcskezelést.

### <a name="client-side-encryption-of-azure-blobs"></a>Az Azure-blobok ügyféloldali titkosítása

Az Azure-blobok ügyféloldali titkosítását különböző módokon hajthathatja végre.

Az Azure Storage Client Library for .NET NuGet csomag használatával titkosíthatja az adatokat az ügyfélalkalmazásokon belül, mielőtt feltöltené azokat az Azure storage-ba.

Ha többet szeretne megtudni az Azure Storage Client Library for .NET NuGet csomagról, és letöltheti azAzure Storage Client Library for .NET NuGet csomagot, olvassa el a [Windows Azure Storage 8.3.0 című témakört.](https://www.nuget.org/packages/WindowsAzure.Storage)

Ha ügyféloldali titkosítást használ a Key Vault használatával, az adatok egy egyszeri szimmetrikus tartalomtitkosítási kulcs (CEK) használatával titkosítva lesz, amelyet az Azure Storage-ügyfél SDK generál. A CEK kulcstitkosítási kulcs (KEK) használatával van titkosítva, amely lehet szimmetrikus kulcs vagy aszimmetrikus kulcspár. Kezelheti helyileg, vagy tárolhatja a Key Vaultban. A titkosított adatok at majd feltölti az Azure Storage.The encrypted data is then uploaded to Azure Storage.

Ha többet szeretne tudni az ügyféloldali titkosításról a Key Vault használatával, és ismerkedjen meg az útmutatóval, olvassa el [az Oktatóanyag: Blobok titkosítása és visszafejtése az Azure Storage-ban a Key Vault használatával.](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)

Végül az Azure Storage Client Library for Java használatával is végrehajthatja az ügyféloldali titkosítást, mielőtt adatokat töltene fel az Azure Storage-ba, és visszafejtheti az adatokat, amikor letölti az ügyfélre. Ez a könyvtár is támogatja a [Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiók kulcskezelése integráció.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Inaktív adatok titkosítása az Azure SQL Database segítségével

[Az Azure SQL Database](../../sql-database/sql-database-technical-overview.md) egy általános célú relációs adatbázis-szolgáltatás az Azure-ban, amely támogatja a struktúrák, például a relációs adatok, JSON, térbeli és XML. Az SQL Database támogatja mind a kiszolgálóoldali titkosítást az Átlátszó adattitkosítás (TDE) szolgáltatáson keresztül, mind az ügyféloldali titkosítást a Mindig titkosított szolgáltatáson keresztül.

#### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

[A TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) az [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [az Azure SQL Database](../../sql-database/sql-database-technical-overview.md)és az Azure SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adatfájlok valós idejű titkosítására szolgál egy adatbázis-titkosítási kulcs (DEK) használatával, amely az adatbázis rendszerindítási rekordjában tárolódik a helyreállítás során rendelkezésre állásszempontjából.

A TDE az AES és a Triple Data Encryption Standard (3DES) titkosítási algoritmusokkal védi az adatokat és a naplófájlokat. Az adatbázisfájl titkosítása oldalszinten történik. A titkosított adatbázis lapjai a lemezre írás előtt titkosítva vannak, és visszafejtésükkor visszafejtik őket a memóriába. A TDE alapértelmezés szerint engedélyezve van az újonnan létrehozott Azure SQL-adatbázisokban.

#### <a name="always-encrypted-feature"></a>Mindig titkosított funkció

Az Azure SQL [Mindig titkosított](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkciójával az azure-beli SQL-adatbázisban való tárolás előtt titkosíthatja az adatokat az ügyfélalkalmazásokon belül. Engedélyezheti a helyszíni adatbázis-felügyelet harmadik felekre delegálását is, és fenntarthatja az adatokat birtokló és megtekintő személyek és azok elkülönítését, akik kezelik azokat, de nem férhetnek hozzá.

#### <a name="cell-level-or-column-level-encryption"></a>Cella- vagy oszlopszintű titkosítás

Az Azure SQL Database segítségével szimmetrikus titkosítást alkalmazhat egy adatoszlopra a Transact-SQL használatával. Ezt a módszert [cellszintű titkosításnak vagy oszlopszintű titkosításnak (CLE)](/sql/relational-databases/security/encryption/encrypt-a-column-of-data)nevezik, mivel segítségével bizonyos oszlopokat vagy akár különböző titkosítási kulcsokkal rendelkező adatok at is titkosíthat. Ezzel részletesebb titkosítási képességet biztosít, mint a TDE, amely titkosítja az oldalakon lévő adatokat.

A CLE beépített funkciókkal rendelkezik, amelyekkel szimmetrikus vagy aszimmetrikus kulcsokkal, egy tanúsítvány nyilvános kulcsával vagy 3DES-sel kapcsolatos jelszóval titkosíthatja az adatokat.

### <a name="cosmos-db-database-encryption"></a>Cosmos DB adatbázis titkosítása

[Az Azure Cosmos DB](../../cosmos-db/database-encryption-at-rest.md) a Microsoft globálisan elosztott, többmodelles adatbázisa. A Cosmos DB nem felejtő tárolóban (ssd-meghajtókon) tárolt felhasználói adatok alapértelmezés szerint titkosítva vannak. Nincsenek be- és kikapcsolandó vezérlők. Az inaktív titkosítás számos biztonsági technológiával valósul meg, beleértve a biztonságos kulcstároló rendszereket, a titkosított hálózatokat és a kriptográfiai API-kat. A titkosítási kulcsokat a Microsoft kezeli, és a Microsoft belső irányelvei szerint elforgatják őket.

### <a name="at-rest-encryption-in-data-lake"></a>Inaktív titkosítás a Data Lake-ben

[Az Azure Data Lake](../../data-lake-store/data-lake-store-encryption.md) a követelmények vagy séma formális meghatározása előtt egy helyen gyűjtött összes adattípus vállalati szintű tárháza. A Data Lake Store támogatja az "alapértelmezés szerint" az inaktív adatok transzparens titkosítását, amely a fiók létrehozása során van beállítva. Alapértelmezés szerint az Azure Data Lake Store kezeli a kulcsokat az Ön számára, de lehetősége van saját maga által kezelni őket.

Az adatok titkosításához és visszafejtéséhez három féle kulcs használatos: a fő titkosítási kulcs (MEK), az adattitkosítási kulcs (DEK) és a blokktitkosítási kulcs (BEK). A MEK az állandó adathordozón tárolt DEK titkosítására szolgál, és a BEK a DEK-ből és az adatblokkból származik. Ha ön kezelő saját kulcsok, tudod forog a MEK.

## <a name="encryption-of-data-in-transit"></a>Az átvitel alatt álló adatok titkosítása

Az Azure számos mechanizmust kínál az adatok bizalmas anamforitására, mivel azok egyik helyről a másikra mozognak.

### <a name="tlsssl-encryption-in-azure"></a>TLS/SSL titkosítás az Azure-ban

A Microsoft a [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokollt használja az adatok védelmére, amikor azok a felhőszolgáltatások és az ügyfelek között haladnak. A Microsoft adatközpontjai egyeztetik a TLS-kapcsolatot az Azure-szolgáltatásokhoz kapcsolódó ügyfélrendszerekkel. A TLS erős hitelesítést, üzenet-adatvédelmet és integritást biztosít (lehetővé téve az üzenetek illetéktelen ítésének, lehallgatásának és hamisításának észlelését), az interoperabilitást, az algoritmusok rugalmasságát, valamint a könnyű telepítést és használatot.

[A tökéletes továbbítási titkosság](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) egyedi kulcsokkal védi az ügyfelek ügyfélrendszerei és a Microsoft felhőszolgáltatásai közötti kapcsolatokat. A kapcsolatok RSA-alapú 2048 bites titkosítási kulcshosszokat is használnak. Ez a kombináció megnehezíti, hogy valaki elfogja és hozzáférjen az átmenő adatokhoz.

### <a name="azure-storage-transactions"></a>Azure Storage-tranzakciók

Amikor az Azure Portalon keresztül kommunikál az Azure Storage-szal, minden tranzakció HTTPS-en keresztül történik. A Storage REST API HTTPS-en keresztül is használhatja az Azure Storage-szal való interakciót. A HTTPS használatával kényszerítheti, amikor meghívja a REST API-k at a tárfiókok ban lévő objektumok eléréséhez, ha engedélyezi a tárfiókhoz szükséges biztonságos átvitelt.

Megosztott hozzáférésű aláírások ([SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)), amely használható az Azure Storage-objektumokhoz való hozzáférés delegálásához, tartalmaz egy lehetőséget, amellyel megadhatja, hogy csak a HTTPS protokoll használható megosztott hozzáférésű aláírások használataesetén. Ez a megközelítés biztosítja, hogy bárki, aki sas-jogkivonatokkal küld kapcsolatokat, a megfelelő protokollt használja.

[Az SMB 3.0,](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption)amely az Azure Files-megosztások eléréséhez használt, támogatja a titkosítást, és elérhető a Windows Server 2012 R2, A Windows 8, a Windows 8.1 és a Windows 10 rendszerben. Lehetővé teszi a régiók közötti hozzáférést, és még az asztalon is.

Az ügyféloldali titkosítás titkosítja az adatokat, mielőtt az Okat az Azure Storage-példányba küldené, így titkosítva van, miközben a hálózaton keresztül utazik.

### <a name="smb-encryption-over-azure-virtual-networks"></a>SMB-titkosítás az Azure virtuális hálózatain keresztül 

Az [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) használatával windows Server 2012 vagy újabb rendszert futtató virtuális gépeken biztonságossá teheti az adatátvitelt az Azure virtuális hálózatokon keresztül történő átvitel során használt adatok titkosításával. Az adatok titkosításával védelmet nyújt a hamisítás és a lehallgatás imitálása ellen. A rendszergazdák engedélyezhetik az SMB-titkosítást a teljes kiszolgálón, vagy csak bizonyos megosztásokat.

Alapértelmezés szerint, miután az SMB-titkosítás be van kapcsolva egy megosztáson vagy kiszolgálón, csak az SMB 3.0-s ügyfelek férhetnek hozzá a titkosított megosztásokhoz.

## <a name="in-transit-encryption-in-vms"></a>Átvitel közbeni titkosítás a virtuális gépeken

A Windows rendszert futtató virtuális gépekre, azokból és gépek között átmenő adatok a kapcsolat jellegétől függően számos módon titkosítva vannak.

### <a name="rdp-sessions"></a>RDP-munkamenetek

A virtuális gépekhez a Távoli asztali [protokoll (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) segítségével csatlakozhat és bejelentkezhet egy Windows ügyfélszámítógépről, vagy egy telepített RDP-ügyféllel rendelkező Mac ről. Az RDP-munkamenetekben a hálózaton keresztül átadott adatokat a TLS védheti.

A Távoli asztal használatával is csatlakozhat egy Linux virtuális géphez az Azure-ban.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Biztonságos hozzáférés a Linux virtuális gépekhez SSH-val

A távoli felügyelet hez a [Secure Shell](../../virtual-machines/linux/ssh-from-windows.md) (SSH) segítségével csatlakozhat az Azure-ban futó Linux-alapú virtuális gépekhez. Az SSH egy titkosított kapcsolatprotokoll, amely biztonságos bejelentkezést tesz lehetővé a nem biztonságos kapcsolatokon keresztül. Ez az Azure-ban üzemeltetett Linux-virtuális gépek alapértelmezett kapcsolatprotokollja. SSH-kulcsok használatával a hitelesítés, nincs szükség a jelszavakat, hogy jelentkezzen be. Az SSH nyilvános/titkos kulcspárt (aszimmetrikus titkosítást) használ a hitelesítéshez.

## <a name="azure-vpn-encryption"></a>Azure VPN-titkosítás

Az Azure-hoz egy virtuális magánhálózaton keresztül csatlakozhat, amely biztonságos bújtategy biztonságos bújtatása a hálózaton keresztül küldött adatok adatvédelmének védelme érdekében.

### <a name="azure-vpn-gateways"></a>Azure VPN-átjárók

Az Azure [VPN-átjáró](../../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) használatával titkosított forgalmat küldhet a virtuális hálózat és a helyszíni hely között nyilvános kapcsolaton keresztül, vagy küldhet forgalmat a virtuális hálózatok között.

A helyek közötti VPN-ek [IPsec-et](https://en.wikipedia.org/wiki/IPsec) használnak az átvitel titkosításához. Az Azure VPN-átjárók alapértelmezett javaslatokat használnak. Az Azure VPN-átjárók konfigurálhatja, hogy egy egyéni IPsec/IKE-szabályzat ot használjon adott kriptográfiai algoritmusokkal és kulcserősségekkel, nem pedig az Azure alapértelmezett házirend-készleteit.

### <a name="point-to-site-vpns"></a>Pont–hely VPN-ek

A pont-hely VPN-ek lehetővé teszik az egyes ügyfélszámítógépek számára az Azure virtuális hálózathoz való hozzáférést. [A Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) a VPN-alagút létrehozásához használatos. Áttud haladni a tűzfalakon (az alagút HTTPS-kapcsolatként jelenik meg). A saját nyilvános kulcsú infrastruktúra (PKI) legfelső szintű hitelesítésszolgáltatóját használhatja a helyek közötti kapcsolathoz.

A virtuális hálózathoz az Azure Portal tanúsítványhitelesítéssel vagy a PowerShell használatával konfigurálhat egy pont-hely VPN-kapcsolatot.

Ha többet szeretne tudni az Azure virtuális hálózatokkal való pont-hely VPN-kapcsolatokról, olvassa el a következő témakört:

[Pont-hely kapcsolat konfigurálása virtuális hálózattal hitelesítési hitelesítés használatával: Azure Portal](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[A virtuális hálózattal való pont-hely kapcsolat konfigurálása tanúsítványhitelesítés használatával: PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Helyek közötti VPN-ek 

A helyek közötti VPN-átjárókapcsolat segítségével csatlakoztathatja a helyszíni hálózatot egy Azure virtuális hálózathoz egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolathoz olyan helyszíni VPN-eszközre van szükség, amelyhez külső nyilvános IP-cím van rendelve.

Az Azure Portal, a PowerShell vagy az Azure CLI használatával konfigurálhatja a helyek közötti VPN-kapcsolatot egy virtuális hálózathoz.

További információkért lásd:

[Helyek közötti kapcsolat létrehozása az Azure Portalon](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Helyek közötti kapcsolat létrehozása a PowerShellben](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a CLI használatával](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>Átvitel közbeni titkosítás a Data Lake-ben

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Az adatok állandó adathordozón való tárolása előtti titkosítása mellett az adatok is mindig biztonságosak a HTTPS használatával történő átvitel során. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

Ha többet szeretne tudni a Data Lake-ben áthaladó adatok titkosításáról, olvassa el [az Adatok titkosítása a Data Lake Store áruházban.](../../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-key-vault"></a>Kulcskezelés a Key Vault segítségével

A kulcsok megfelelő védelme és kezelése nélkül a titkosítás használhatatlanná válik. A Key Vault a Microsoft által ajánlott megoldás a felhőszolgáltatások által használt titkosítási kulcsokhoz való hozzáférés kezelésére és szabályozására. A hozzáférési kulcsokhoz való hozzáférési engedélyek hozzárendelhetők a szolgáltatásokhoz vagy a felhasználókhoz az Azure Active Directory-fiókokon keresztül.

A Key Vault mentesíti a szervezeteket a hardveres biztonsági modulok (HSM- ek) és a kulcskezelő szoftverek konfigurálásának, javításának és karbantartásának szükségességétől. A Key Vault használatakor megtarthatja az irányítást. A Microsoft soha nem látja a kulcsokat, és az alkalmazások nem rendelkeznek közvetlen hozzáféréssel azokhoz. Kulcsokat is importálhat vagy létrehozhat a HSM-ekben.

## <a name="next-steps"></a>További lépések

- [Az Azure biztonsági szolgáltatásainak áttekintése](get-started-overview.md)
- [Az Azure hálózati biztonságának áttekintése](network-overview.md)
- [Az Azure-adatbázis biztonságának áttekintése](database-security-overview.md)
- [Az Azure virtuális gépek biztonságának áttekintése](virtual-machines-overview.md)
- [Adattitkosítás inaktív](encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](data-encryption-best-practices.md)
