---
title: Az Azure titkosítási áttekintése |} Microsoft Docs
description: További tudnivalók az Azure-ban különböző titkosítási beállítások
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 00c8b30b5351b7a6e4388b186fab70e3a3357ef4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366307"
---
# <a name="azure-encryption-overview"></a>Az Azure titkosítási áttekintése

Ez a cikk áttekintése titkosítási használatáról a Microsoft Azure-ban. A titkosítás, többek között a titkosítását, a titkosítás a felhőszolgáltató közötti átviteléhez, és az Azure Key Vault kulcskezelés főbb területei magában foglalja. Az egyes szakaszokon részletesebb információkra mutató hivatkozásokat tartalmaz.

## <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása

Inaktív adat fizikai adathordozó, bármilyen digitális formában állandó tárolóban lévő adatokat tartalmazza. Az adathordozó tartalmazhatnak fájlok mágneses vagy optikai adathordozón, az archivált adatok és az adatok biztonsági mentése. A Microsoft Azure számos különböző igényeinek, beleértve a fájl, a lemez, a blob és a table storage adatok tárolási megoldásokkal. A Microsoft is biztosít védelme érdekében titkosítási [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../cosmos-db/introduction.md), és az Azure Data Lake.

Inaktív adatok titkosítása rendelkezésre áll a szolgáltatások keresztül a szoftver a szolgáltatott szoftverként (SaaS), a platform (PaaS), vagy infrastruktúra egy szolgáltatási (IaaS) felhő modellt. Ez a cikk foglalja össze, és biztosít erőforrásokat az Azure titkosítási beállítások használatához nyújtanak segítséget.

Hogyan inaktív adatok titkosítása az Azure-ban a részletes tárgyalását lásd: [Azure Data Encryption nyugalmi](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Az Azure titkosítási modellek

Azure különböző titkosítási modellek, beleértve a szolgáltatás által kezelt kulcsoknak, az ügyfél által felügyelt kulcsok a Key Vault vagy az ügyfél által felügyelt kulcsok által az ügyfél által felügyelt hardver kiszolgálóoldali titkosítás támogatja. Ügyféloldali titkosítás választhat kezelése és a helyszíni kulcsok tárolására, vagy egy másik biztonságos helyen.

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Ügyféloldali titkosítás Azure-on kívüli történik. Ezek a következők:

- Egy olyan alkalmazás használja, hogy az ügyfél az adatközpontban fut, vagy egy szolgáltatási alkalmazás által titkosított adatokat.
- Azure által fogadott már titkosított adatokat.

Az ügyféloldali titkosítással felhőszolgáltatók nem férnek hozzá a titkosítási kulcsokat, és nem tudja visszafejteni ezeket az adatokat. Teljes körű vezérlést biztosítanak a kulcsok megmaradjanak.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

A három kiszolgálóoldali titkosítás modellt kínál különböző kulcskezelés jellemzőkkel rendelkezik, amelyen kiválaszthatja a követelményeknek megfelelően:

- **Szolgáltatás-felügyelet alatt álló kulcsok**: vezérlő és a kényelem biztosít alacsony többletterhelést.

- **Ügyfél által felügyelt kulcsok**: tesz lehetővé a kulcsokat, kapcsolja a saját kulcs használatának (BYOK) támogatják, szabályozható vagy lehetővé teszi, hogy hozzon létre újakat.

- **Az ügyfél által felügyelt hardver szolgáltatás által kezelt kulcsok**: lehetővé teszi a saját fejlesztésű tárházban Microsoft vezérlőn kívül kulcsok kezeléséhez. Ez a jellemző állomás a saját Key (HYOK) neve. Konfigurációs azonban bonyolult, és az Azure szolgáltatások nem támogatják ezt a modellt.

### <a name="azure-disk-encryption"></a>Az Azure lemeztitkosítás

Windows és Linux rendszerű virtuális gépek segítségével megvédheti [Azure disk encryption](azure-security-disk-encryption.md), használó [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technológia és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) védelmére egyaránt operációs rendszer és a teljes kötet titkosítási adatok lemezek.

Titkosítási kulcsok és titkos kulcsokat a védelméről a [Azure Key Vault előfizetés](../key-vault/key-vault-whatis.md). Az Azure Backup szolgáltatás használatával készítsen biztonsági másolatot, és állítsa vissza a titkosított használó virtuális gépek (VM) kulcs titkosítási kulcscserekulcs (KEK)-konfiguráció.

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

Inaktív adatok Azure Blob storage és az Azure fájlmegosztások a kiszolgálóoldali és az ügyféloldali forgatókönyvek esetében a titkosíthatók.

[Az Azure Storage szolgáltatás titkosítási (SSE)](../storage/common/storage-service-encryption.md) automatikusan az adatokat titkosíthatja előtt tárolja, és automatikusan visszafejti az adatok amikor azt. A folyamat az teljes mértékben transzparens a felhasználók számára. Storage szolgáltatás titkosítási használ, 256 bites [Advanced Encryption Standard (AES) titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), vagyis a legerősebb blokk Rejtjelek közül. AES transzparens módon kezelje titkosítási, visszafejtési és kulcskezelést.

### <a name="client-side-encryption-of-azure-blobs"></a>Azure-blobokat ügyféloldali titkosítása

Hajthat végre az ügyféloldali titkosítás az Azure-blobok különböző módokon.

Az Azure Storage ügyféloldali kódtára a .NET NuGet-csomag segítségével belül az ügyfélalkalmazások, azt az Azure Storage feltöltés előtt titkosítja az adatokat.

További információ, és töltse le az Azure Storage ügyféloldali kódtára a .NET NuGet-csomagot, [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Key Vault ügyféloldali titkosítást használ, amikor az adatok titkosítása a egy egyszeri szimmetrikus tartalom titkosítási kulcs (CEK) az Azure Storage-ügyfél SDK által generált. A CEK titkosítása egy kulcs titkosítási kulcscserekulcs (KEK), amely lehet, vagy a szimmetrikus kulcs, vagy az aszimmetrikus kulcspár. Helyileg a kezeléshez, vagy a Key Vault tárolja. A titkosított adatok majd tölt fel az Azure Storage.

További tudnivalók az Key Vault ügyféloldali titkosítás és az első lépések útmutató megtekintéséhez [oktatóanyag: titkosításához és visszafejtéséhez az Azure Storage blobs Key Vault használatával](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Végezetül is használhatja az Azure Storage ügyféloldali kódtára a Java ügyféloldali titkosítás végrehajtásához, mielőtt feltölti adatok Azure Storage, és az adatok visszafejtéséhez, az ügyfél letöltésekor. A kódtár emellett támogatja való integráció [Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Az Azure SQL Database inaktív adatok titkosítása

[Az Azure SQL Database](../sql-database/sql-database-technical-overview.md) egy általános célú relációs adatbázis-szolgáltatás például a relációs adatok, a JSON térbeli, és az XML-szerkezetek támogató Azure-ban. SQL-adatbázis keresztül a transzparens Data Encryption (TDE) funkció kiszolgálóoldali titkosítás és a ügyféloldali titkosítás a mindig titkosítja a szolgáltatás használatával is támogatja.

#### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) titkosításához használt [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), és [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adatfájlok valós idejű használ egy adatbázis titkosítási kulcsa (adattitkosítási kulcs tartománynév-rendszer) , amely a rendelkezésre állási adatbázis rendszerindító rekord helyreállítás során tárolja.

AES és Triple Data Encryption Standard (3DES) használatával védi TDE adatainak és naplókönyvtárainak fájlok titkosítási algoritmusokat. Az adatbázisfájl titkosítási a lap szintjén történik. A lapok egy titkosított adatbázis írás előtt titkosítja a lemez- és lesznek visszafejtve, amikor azok még a memóriába. TDE engedélyezve van alapértelmezés szerint az újonnan létrehozott Azure SQL-adatbázisok.

#### <a name="always-encrypted-feature"></a>Mindig titkosított szolgáltatás

Az a [mindig titkosítja](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkció az Azure SQL belül ügyfélalkalmazások előtt az Azure SQL-adatbázis tárolja az adatokat titkosíthatja. Harmadik felek részére a helyi adatbázis felügyelet delegálását engedélyezheti és karbantartása a tulajdonosa, és tekintheti meg és kezelheti, de nem rendelkezhet hozzáféréssel rendelkező is.

#### <a name="cell-level-or-column-level-encryption"></a>Cellaszintű vagy oszlopszintű titkosítás

Az Azure SQL Database a Transact-SQL használatával alkalmazhat szimmetrikus titkosítási adatok oszlopra. Ezt a módszert nevezik [cellaszintű vagy oszlopszintű titkosítást (törlése)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), mert az adott oszlopok vagy a megadott cellák az adatok titkosítása különböző titkosítási kulcsokkal használható. Így lehetővé teszi az mint TDE, amely titkosítja az adatokat lapjain részletesebb meghajtótitkosítási képességet.

Törlése rendelkezik beépített függvények, amelyek segítségével a szimmetrikus vagy aszimmetrikus kulcsok, a nyilvános kulcsát egy tanúsítványt, vagy egy jelszót, 3DES használatával titkosítja az adatokat.

### <a name="cosmos-db-database-encryption"></a>A cosmos DB az adatbázis-titkosítás

[Az Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) Microsoft globálisan elosztott, több modellre adatbázis. A Cosmos DB nem felejtő (SSD-meghajtó) tárolt felhasználói adatok alapértelmezett beállításként titkosítva. Nincsenek nem vezérlők kapcsolhatja be és ki. Titkosítását számos biztonsági technológia, beleértve a biztonságos kulcs tárolása rendszerek, a titkosított hálózatokat és a kriptográfiai API-k segítségével történik. Titkosítási kulcsok a Microsoft által felügyelt, és a Microsoft belső iránymutatásokat / legyenek-e elforgatva.

### <a name="at-rest-encryption-in-data-lake"></a>A Data Lake nyugalmi titkosítás

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) egy vállalati szintű tárház minden típusú adatgyűjtés egyetlen helyen előtt minden formális definíciója követelmények vagy séma van. Data Lake Store támogatja az "a alapértelmezés szerint" az adatok aktívan, amelyet a fiók létrehozása közben lehet beállítani az átlátható titkosítási. Alapértelmezés szerint az Azure Data Lake Store kezeli a kulcsokat meg, de lehetősége van a saját kezűleg kezelheti azokat.

Három típusú kulcsok használt adatok titkosítása és visszafejtése: a titkosítási kulcs (MEK), az adatok titkosítási kulcs-(adattitkosítási kulcs) és a blokk titkosítási kulcs (BEK). A MEK az adattitkosítási kulcs, ami állandó adathordozón, titkosítására szolgál, és a BEK az adattitkosítási kulcs és a adatblokk van származtatva. A saját kulcsok kezelése, a MEK forgathatók.

## <a name="encryption-of-data-in-transit"></a>Az átvitel adatok titkosítása

Azure az adatok személyes tartása átvitel során egyik helyről egy másikra sok mechanizmust kínálja.

### <a name="tlsssl-encryption-in-azure"></a>A TLS/SSL-titkosítást az Azure-ban

A Microsoft a [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokoll a felhőszolgáltatás és az ügyfelek közötti útközben adatok védelmét. A Microsoft adatközpontjaiban egyezteti a TLS-kapcsolatot az ügyfélszámítógépeken, az Azure-szolgáltatásokhoz. A TLS nyújt erős hitelesítés, üzenetvédelem, és integritását (teszi lehetővé az észlelést az üzenet illetéktelen hozzáférés és hamisítására), együttműködés, az algoritmusok rugalmassága és könnyű telepítése és használata.

[Továbbítási titkosítása tökéletes](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) ügyfelek ügyfél és a Microsoft közötti kapcsolatok védi egyedi kulcsok vannak. Kapcsolatok kulcshossz 2048 bites RSA-alapú titkosítást is használhatja. Ez a kombináció megnehezíti mások intercept és a hozzáférési adatok, amely az átvitel során.

### <a name="azure-storage-transactions"></a>Az Azure Storage-tranzakció

Azure Storage kommunikálni az Azure portálon keresztül, ha az összes tranzakció HTTPS-KAPCSOLATON keresztül kerül sor. A Storage REST API felülete HTTPS-KAPCSOLATON keresztül kommunikál az Azure Storage is használja. A HTTPS protokoll használatát kényszeríthetik hívásakor a REST API-k access objektumok tárfiókokban azáltal, hogy a biztonságos átvitele, amely a tárfiók szükséges.

Közös hozzáférésű Jogosultságkód ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), Azure Storage-objektumokhoz való hozzáférés delegálására, adja meg, hogy csak a HTTPS protokollt használja, megosztott hozzáférési aláírásokkal használatakor lehetőséget is használható. Ez a megközelítés biztosítja, hogy birtokában bárki elküldi az SAS-tokenje hivatkozásokat a megfelelő protokollt használja.

[Az SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), amely Azure fájlok megosztások, támogatja a titkosítást, és azt eléréséhez használt érhető el a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10-es. Lehetővé teszi a kereszt-régió hozzáférést, és az asztalon is elérheti.

Ügyféloldali titkosítás titkosítja az adatokat az Azure Storage-példányra, elküldés előtt, hogy a titkosított a hálózaton keresztül.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Az Azure virtuális hálózatokon SMB-titkosítás 

A [SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) virtuális gépeken, amelyek a Windows Server 2012 vagy újabb rendszerű, biztosíthatja az adatok átvitel való megfelelést az adatokat átvitel közben Azure virtuális hálózaton keresztül történő biztonságos. Adatok titkosításával segítséget, illetéktelen módosítás és a támadások lehallgatás elleni védelem érdekében. Rendszergazdák engedélyezhetik a teljes kiszolgálóhoz, vagy csak adott megosztásokon SMB-titkosítás.

Alapértelmezés szerint a megosztás vagy a kiszolgálón, be van kapcsolva a SMB-titkosítás után csak az SMB 3.0-ügyfelek számára engedélyezett hozzáférési a titkosított.

## <a name="in-transit-encryption-in-vms"></a>Az átvitel közbeni titkosítás virtuális gépeken

Számos módon, a kapcsolat természetétől függően a titkosított adatokat átvitel közben, valamint a Windows rendszert futtató virtuális gépek között.

### <a name="rdp-sessions"></a>RDP-munkamenetekhez

Csatlakozhat, és jelentkezzen be egy virtuális géphez a [Remote Desktop Protocol (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) Windows rendszerű ügyfélszámítógépek, vagy egy Mac egy RDP-ügyfél telepítve van. Az RDP-munkamenetekhez a hálózaton lévő adatok védhetők TLS.

Távoli asztal használatával csatlakozzon az Azure Linux virtuális gép.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Biztonságos hozzáférés a SSH Linux virtuális gépek

A távfelügyelethez, használhatja a [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) Azure-ban futó Linux virtuális gépek csatlakozni. Az SSH egy olyan titkosított kapcsolati protokoll, amely lehetővé teszi, hogy a biztonságos bejelentkezések nem biztonságos kapcsolaton keresztül. Az Azure szolgáltatásban üzemeltetett Linux virtuális gépek alapértelmezett csatlakozási protokoll. SSH-kulcsok hitelesítést használ, nincs szükség a jelszót a bejelentkezéshez. SSH, egy nyilvános/titkos kulcspár (aszimmetrikus titkosítási) használ.

## <a name="azure-vpn-encryption"></a>Az Azure VPN-titkosítás

A hálózaton keresztül küldött adatok védelme biztonságos alagutat hoz létre virtuális magánhálózaton keresztül kapcsolódhatnak az Azure-bA.

### <a name="azure-vpn-gateways"></a>Az Azure VPN gatewayek

Használhat egy [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) küldhet a titkosított forgalmat a virtuális hálózat és a helyszíni hely közötti nyilvános kapcsolaton keresztül, vagy virtuális hálózatok között forgalmat küldeni.

Pont-pont virtuális magánhálózatok használata [IPsec](https://en.wikipedia.org/wiki/IPsec) átviteli titkosításhoz. Az Azure VPN gatewayek alapértelmezett javaslatokat készletének használata. Konfigurálhatja az Azure VPN gatewayek használható egyéni IPsec/IKE házirendet titkosítási algoritmusokat és kulcs szintjeiről ahelyett, hogy az Azure alapértelmezett szabályzatot állítja be.

### <a name="point-to-site-vpns"></a>Pont-pont VPN

Pont-pont VPN egyéni ügyfél egy Azure virtuális hálózatra számítógépek elérését teszi lehetővé. [A Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) a VPN-alagút létrehozásához használt. Azt is haladnak át a tűzfalak (HTTPS-kapcsolatot, megjelenik az alagutat). A pont – hely kapcsolatot saját belső nyilvános kulcsú infrastruktúra (PKI) legfelső szintű hitelesítésszolgáltatót (CA) is használhatja.

A pont-pont VPN-kapcsolat a virtuális hálózati konfigurálhatja az Azure portál használatával tanúsítványhitelesítés vagy a PowerShell használatával.

Pont-pont VPN-kapcsolatok az Azure virtuális hálózatokhoz kapcsolatos további információkért lásd:

[Hitelesítés használatával egy virtuális hálózati pont – hely kapcsolat konfigurálva: Azure-portálon](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Tanúsítványalapú hitelesítés használatával egy virtuális hálózati pont – hely kapcsolat konfigurálva: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Pont-pont VPN 

A pont-pont VPN gateway-kapcsolatot használhatja a helyszíni hálózat csatlakozni egy Azure virtuális hálózatra (IKEv1 vagy IKEv2) IPsec/IKE VPN-alagúton keresztül. Ilyen típusú kapcsolat szükséges egy helyszíni VPN-eszköz egy kívülre irányuló nyilvános IP-cím hozzárendelve.

Az Azure portálon, a PowerShell vagy az Azure parancssori felület használatával konfigurálhatja a virtuális hálózati helyek a VPN-kapcsolatot.

További információkért lásd:

[Pont-pont kapcsolat létrehozása az Azure-portálon](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Pont-pont kapcsolat létrehozása a PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Virtuális hálózat pont-pont VPN-kapcsolat létrehozása a parancssori felület használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>A Data Lake az átvitel közbeni titkosítás

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Mellett a titkosított adatok állandó media tárolásukról előtt, az adatok is minden esetben védett átvitel közben HTTPS használatával. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

Az adatok átvitele történik a Data Lake titkosításával kapcsolatos további tudnivalókért lásd: [a Data Lake Store adatok titkosítását](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>A Key Vault kulcskezelés

Nem megfelelő védelme és kezelése a kulcsok titkosítási használhatatlan lett megjelenítve. Key Vault egy a Microsoft által ajánlott megoldás kezelése és a felhőalapú szolgáltatás által használt titkosítási kulcsok hozzáférés szabályozása. Kulcsok eléréséhez engedélyek rendelhetők, szolgáltatások vagy felhasználók keresztül Azure Active Directory-fiókokat.

Key Vault mentesíti szervezetek konfigurálását, a javítás és a hardveres biztonsági modulokkal (HSM) és a kulcskezelést szoftver karbantartása szükségességét. Key Vault használatakor irányítást tarthat fenn. Microsoft soha nem látja a kulcsokat, és alkalmazások nincs közvetlen hozzáférése őket. Importálja vagy kulcsok létrehozása hardveres biztonsági modulok is.

## <a name="next-steps"></a>További lépések

- [Az Azure biztonsági szolgáltatásainak áttekintése](security-get-started-overview.md)
- [Az Azure biztonsági áttekintése](security-network-overview.md)
- [Azure-adatbázis biztonsági áttekintése](azure-database-security-overview.md)
- [Az Azure virtuális gépek biztonsági áttekintése](security-virtual-machines-overview.md)
- [Adattitkosítás inaktív állapotban](azure-security-encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](azure-security-data-encryption-best-practices.md)
