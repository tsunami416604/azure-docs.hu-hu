---
title: "Az Azure titkosítási áttekintése |} Microsoft Docs"
description: "További tudnivalók az Azure-ban különböző titkosítási beállítások"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Az Azure titkosítási áttekintése

Ez a cikk áttekintése titkosítási használatáról a Microsoft Azure-ban. A titkosítás, a felhőszolgáltató közötti átviteléhez, és a Key Vault kulcskezelés titkosítását, a titkosítási is beleértve főbb területei magában foglalja. Minden szakasz hivatkozásokat részletes információkat tartalmaz.

## <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása

Inaktív adat fizikai adathordozó, bármilyen digitális formában állandó tárolóban lévő adatokat tartalmazza. Ez magában foglalja a fájlok mágneses vagy optikai adathordozón, az archivált adatok és az adatok biztonsági mentése. A Microsoft Azure számos különböző igényeinek, beleértve a fájl, a lemez, a blob és a table storage adatok tárolási megoldásokkal. A Microsoft is biztosít védelme érdekében titkosítási [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md), és az Azure Data Lake.

Inaktív adatok titkosítása érhető el szolgáltatások között az Azure szoftver,--szolgáltatás (SaaS), a felhő modellek Platform,--szolgáltatás (PaaS), és infrastruktúra,--szolgáltatás (IaaS). Ez a dokumentum összefoglalja, és erőforrások Azure titkosítási beállítások használatához nyújtanak segítséget nyújt.

További részletes leírását a hogyan inaktív adatok titkosítása az Azure-ban, tekintse meg a című dokumentum [Azure Data Encryption nyugalmi](azure-security-encryption-atrest.md)

## <a name="azure-encryption-models"></a>Az Azure titkosítási modellek

Azure különböző titkosítási modellek, beleértve a szolgáltatás által kezelt kulcsokkal, felhasználó által felügyelt kulcsok használata az Azure Key Vault vagy kulcsokkal ügyfél által felügyelt ügyfél által felügyelt hardveren kiszolgálóoldali titkosítás támogatja. Ügyféloldali titkosítás segítségével kezelése és a helyszíni kulcsok tárolására, vagy egy másik biztonságos helyen.

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Ügyféloldali titkosítás Azure-on kívüli történik. Ügyféloldali titkosítás tartalmazza:

- Egy olyan alkalmazás használja, hogy az ügyfél adatközpontban fut, vagy egy szolgáltatási alkalmazás által titkosított adatok
- Azure által fogadott már titkosított adatokat.

Az ügyféloldali titkosítással a felhőbeli szolgáltatás szolgáltatója nem fér hozzá a titkosítási kulcsokat, és nem tudja visszafejteni ezeket az adatokat. Teljes körű vezérlést biztosítanak a kulcsok megmaradjanak.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

A három kiszolgálóoldali titkosítás modellt kínál különböző kulcskezelés jellemzőkkel rendelkezik, amely igényeinek / választható ki.

- **Szolgáltatás-felügyelet alatt álló kulcsok** alacsony többletterhelést vezérlő és a felhasználók kényelme érdekében adjon meg

- **Ügyfél által felügyelt kulcsok** segítségével meghatározhatja a kulcsokat, beleértve a kapcsolja a saját kulcs (használatának BYOK), vagy újakat létrehozni.

- **Kulcsok szolgáltatás által kezelt ccustomer-controlledhardware** teszi lehetővé a saját fejlesztésű tárház Microsoft érdekeltségébe kívül esik a kulcsok kezeléséhez. Ennek meghívása az állomás a saját Key (HYOK). Konfigurációs azonban bonyolult, és az Azure szolgáltatások nem támogatják ezt a modellt.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Windows és Linux virtuális gépek védhetők használatával [Azure Disk Encryption](azure-security-disk-encryption.md), mely használja a [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technológia és a Linux [DM-crypt program segítségével](https://en.wikipedia.org/wiki/Dm-crypt) védelmére is operációs rendszer és a teljes kötet titkosítási adatok lemezek.

Titkosítási kulcsok és titkos kulcsokat a védelméről a [Azure Key Vault](../key-vault/key-vault-whatis.md) előfizetés. Készítsen biztonsági másolatot, és állítsa vissza a titkosított virtuális gépeket, a KEK konfigurációjával kapcsolatban az Azure Backup szolgáltatás használatával titkosított.

### <a name="azure-storage-service-encryption"></a>Az Azure Storage szolgáltatás titkosítási

Az Azure storage (Blob és fájl) inaktív adatok titkosíthatók a kiszolgálóoldali és az ügyféloldali forgatókönyvek esetében.

[Az Azure Storage szolgáltatás titkosítási](../storage/storage-service-encryption.md) (SSE) automatikusan az adatokat titkosíthatja előtt tárolja, és automatikusan visszafejti azokat amikor, így a folyamat teljes mértékben transzparens felhasználók. Storage szolgáltatás titkosítási használ, 256 bites [AES titkosítási](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), amely egyik a legerősebb blokk Rejtjelek érhető el, és kezelje a titkosítási, visszafejtési és kulcskezelés átlátható módon.

### <a name="client-side-encryption-of-azure-blobs"></a>Azure-blobokat ügyféloldali titkosítása

Az Azure-blobokat ügyféloldali titkosítás különböző módon hajtható végre.

Használhatja az Azure Storage ügyféloldali kódtára a .NET NuGet-csomagot az ügyfél alkalmazásokban az Azure Storage feltöltés előtt titkosítja az adatokat.

További információ és az Azure Storage ügyféloldali kódtára a .NET NuGet-csomag letöltéséhez lásd: a című dokumentum [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage)

Ügyféloldali titkosítás az Azure Key Vault használatakor az adatok titkosítása a egy egyszeri szimmetrikus tartalom titkosítási kulcs (CEK) az Azure Storage-ügyfél SDK által generált. A CEK titkosítása egy kulcs titkosítási kulcscserekulcs (KEK), amely lehet, vagy a szimmetrikus kulcs, vagy az aszimmetrikus kulcspár. Az adatbázis felügyeletét helyileg, vagy tárolja az Azure Key Vault. A titkosított adatok majd tölt fel az Azure Storage szolgáltatás.

További tudnivalók az Azure Key Vault ügyféloldali titkosítás és első lépések útmutató megtekintéséhez, tekintse meg a című dokumentum [oktatóanyag: titkosításához és visszafejtéséhez az Azure Key Vault használatával a Microsoft Azure Storage blobs](../storage/storage-encrypt-decrypt-blobs-key-vault.md)

Végezetül is használhatja az Azure Storage ügyféloldali kódtára a Java előtt adatok feltöltése az Azure Storage ügyféloldali titkosítás végrehajtásához, és az adatok visszafejtéséhez, akkor az ügyfélnek letöltésekor. A kódtár emellett támogatja való integráció [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Az Azure SQL database inaktív adatok titkosítása

[Az Azure SQL Database](../sql-database/sql-database-technical-overview.md) van egy általános célú relációs adatbázis-szolgáltatás, például a relációs adatok, a JSON térbeli, és az XML-szerkezetek támogató Microsoft Azure-ban. Az Azure SQL keresztül a transzparens Data Encryption (TDE) funkció kiszolgálóoldali titkosítás és a ügyféloldali titkosítás a mindig titkosítja a szolgáltatás használatával is támogatja.

#### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

[TDE átlátható adattitkosítási](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) titkosításához használt [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), és [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) valós idejű adatok fájlok adatbázis-titkosítási kulcs (adattitkosítási kulcs), a rendelkezésre állási adatbázis rendszerindító rekord tárolt helyreállítása során használ.

TDE védi adatainak és naplókönyvtárainak fájlok AES és a 3DES titkosítási algoritmus használatával. Az adatbázisfájl titkosítási történik a lap szintjén; a lapok egy titkosított adatbázis írás előtt titkosítja a lemez- és lesznek visszafejtve, amikor azok még a memóriába. TDE engedélyezve van alapértelmezés szerint az újonnan létrehozott Azure SQL-adatbázisok.

#### <a name="always-encrypted"></a>Always Encrypted

A [mindig titkosítja](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) Azure SQL-szolgáltatás lehetővé teszi az adatok tárolása az Azure SQL Database előtt ügyfélalkalmazásokon belüli titkosítását, és engedélyezheti a helyi adatbázis felügyelet, harmadik felekhez delegálását és a tulajdonosa, és tekintheti meg és kezelheti, de nem rendelkezhet hozzáféréssel rendelkező karbantartása.

#### <a name="cellcolumn-level-encryption"></a>/ Cellaoszlopának a blokkszintű titkosítás

Az Azure SQL Database szimmetrikus titkosítási vonatkozik egy adatoszlopot Transact-SQL használatával teszi lehetővé. Ez a lehetőség [a blokkszintű titkosítás vagy oszlopot a blokkszintű titkosítás cella](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (törlése), mert használhatja adott oszlopok vagy a megadott cellák az adatok titkosítása különböző titkosítási kulcsokkal. Ez lehetővé teszi mint TDE, amely titkosítja az adatokat lapjain részletesebb meghajtótitkosítási képességet.

Törlése rendelkezik beépített függvények, amelyek segítségével adatokat, és szimmetrikus vagy aszimmetrikus kulcsok, egy tanúsítvány nyilvános kulcsa, vagy egy jelszót, 3DES használatával titkosítja.

### <a name="cosmos-db-database-encryption"></a>A cosmos DB az adatbázis-titkosítás

[Az Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) Microsoft globálisan elosztott, több modellre adatbázis. A Cosmos DB nem felejtő (SSD-meghajtó) tárolt felhasználói adatokat titkosított alapértelmezett; Nincsenek nem vezérlők kapcsolhatja be és ki. Titkosítását számos biztonsági technológia, beleértve a biztonságos kulcs tárolása rendszerek, a titkosított hálózatokat és a kriptográfiai API-k segítségével történik. Titkosítási kulcsok a Microsoft által felügyelt, és a Microsoft belső iránymutatásokat / legyenek-e elforgatva.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Azure Data Lake nyugalmi titkosítás

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) egy vállalati szintű tárház minden típusú adatgyűjtés egyetlen helyen előtt minden formális definíciója követelmények vagy séma van. Azure Data Lake Store támogatja az "a alapértelmezés szerint" az adatok aktívan, amelyet a fiók létrehozása közben lehet beállítani az átlátható titkosítási. Alapértelmezés szerint a Data Lake Store kezeli a kulcsokat, de lehetősége van a saját kezűleg kezelheti azokat.

Három típusú kulcsok használt adatok titkosítása és visszafejtése: a titkosítási kulcs (MEK), az adatok titkosítási kulcs-(adattitkosítási kulcs) és a blokk titkosítási kulcs (BEK). A MEK az adattitkosítási kulcs, ami állandó adathordozón, titkosítására szolgál, és a BEK az adattitkosítási kulcs és a adatblokk van származtatva. A saját kulcsok kezelése, a MEK forgathatók.

## <a name="encryption-of-data-in-transit"></a>Az átvitel adatok titkosítása

Azure az adatok személyes tartása átvitel során egyik helyről egy másikra sok mechanizmust kínálja.

### <a name="tlsssl-encryption-in-azure"></a>A TLS/SSL-titkosítást az Azure-ban

A Microsoft a [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokoll a felhőszolgáltatás és az ügyfelek közötti útközben adatok védelmét. A Microsoft azon adatközpontjainak egyezteti az ügyfélszámítógépeken, Azure-szolgáltatásokhoz való csatlakozás TLS-kapcsolatot. A TLS nyújt erős hitelesítés, üzenetvédelem, és integritását (teszi lehetővé az észlelést az üzenet illetéktelen hozzáférés és hamisítására), együttműködés, az algoritmusok rugalmassága, könnyű telepítése és használata.

[Továbbítási titkosítása tökéletes](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) az ügyfelek ügyfél és a Microsoft olyan felhőszolgáltatásai közötti kapcsolatok védi egyedi kulcsok vannak. Kapcsolatok kulcshossz 2048 bites RSA-alapú titkosítást is használhatja. Ez a kombináció megnehezíti mások intercept és a hozzáférési adatok, amely az átvitel.

### <a name="azure-storage-transactions"></a>Az Azure Storage-tranzakció

Azure Storage kommunikálni az Azure portálon keresztül, ha az összes tranzakció HTTPS-KAPCSOLATON keresztül kerül sor. A Storage REST API felülete HTTPS-KAPCSOLATON keresztül kommunikál az Azure Storage is használja. A REST API-k hozzáférést objektumokhoz a tárfiókokban azáltal, hogy a tárfiók szükséges biztonságos átvitele meghívásakor kényszerítheti a HTTPS protokoll használatát.

Közös hozzáférésű Jogosultságkód ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), Azure Storage-objektumokhoz való hozzáférés delegálására, tartalmaznak, adja meg, hogy csak a HTTPS protokollt használja, megosztott hozzáférési aláírásokkal használata esetén használható. Ez biztosítja, hogy birtokában bárki küldi ki az SAS-tokenje hivatkozásokat a megfelelő protokollt használja.

[Az SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) Azure fájlmegosztásokat által támogatott titkosítási, és az elérhető a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10-es kereszt-régió hozzáférést, és az asztalon is elérheti.

Ügyféloldali titkosítás titkosítja az adatokat az Azure Storage elküldés előtt, hogy a titkosított a hálózaton keresztül.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Az Azure virtuális hálózatokon SMB-titkosítás 

[Az SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) az Azure virtuális gépeken futó Windows Server 2012 és újabb lehetővé teszi az adatok átvitel biztonságos adatokat átvitel közben titkosításával Azure virtuális hálózatokon, illetéktelen módosítás és a támadások lehallgatás elleni védelem érdekében. Rendszergazdák is SMB-titkosítás engedélyezéséhez a teljes kiszolgálón vagy csak adott megosztásokon.

Alapértelmezés szerint SMB-titkosítás engedélyezve van a megosztás vagy a kiszolgálón, ha csak az SMB 3-ügyfelek számára engedélyezett hozzáférési a titkosított.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Az átvitel közbeni titkosítás, az Azure virtuális gépeken

Számos módon, a kapcsolat természetétől függően a titkosított adatokat átvitel közben, valamint a Windows rendszert futtató Azure virtuális gépek között.

### <a name="rdp-sessions"></a>RDP-munkamenetekhez

Csatlakozhat, és jelentkezzen be egy Azure virtuális gép használata a [a távoli asztal protokoll](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) Windows rendszerű ügyfélszámítógépek, vagy egy Mac egy RDP-ügyfél telepítve van. Az RDP-munkamenetekhez a hálózaton lévő adatok védhetők TLS.

Távoli asztal használatával csatlakozzon az Azure Linux virtuális gép.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Biztonságos hozzáférés a SSH Linux virtuális gépek

Használhat [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) távoli kezelésére szolgál az Azure-ban futó Linux virtuális gép csatlakozni. Az SSH egy olyan titkosított kapcsolati protokoll, amely lehetővé teszi, hogy a biztonságos bejelentkezés titkosítatlan kapcsolaton keresztül. Az Azure szolgáltatásban üzemeltetett Linux virtuális gépek alapértelmezett csatlakozási protokoll. SSH-kulcsok hitelesítést használ, nincs szükség a bejelentkezési jelszót. SSH, egy nyilvános/titkos kulcspár (aszimmetrikus titkosítási) használ.

## <a name="azure-vpn-encryption"></a>Az Azure VPN-titkosítás

A hálózaton keresztül küldött adatok védelme biztonságos alagutat hoz létre virtuális magánhálózaton keresztül kapcsolódhatnak az Azure-bA.

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

[Az Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) nyilvános kapcsolaton keresztül a virtuális hálózat és a helyszíni hely közötti titkosított forgalom küldéséhez, és virtuális hálózatok közötti adatforgalom küldését is használható.

Telephelyek közötti VPN használ [IPsec](https://en.wikipedia.org/wiki/IPsec) átviteli titkosításhoz. Az Azure VPN gatewayek alapértelmezett javaslatokat készletének használata. Konfigurálhatja az Azure VPN gatewayek használható egyéni IPsec/IKE házirendet titkosítási algoritmusokat és kulcs szintjeiről ahelyett, hogy az Azure alapértelmezett szabályzatot állítja be.

### <a name="point-to-site-vpn"></a>Pont – hely típusú VPN

Pont-pont VPN hozzáférést egyéni ügyfél számítógépek egy Azure virtuális hálózatra. [A Secure Socket Tunneling Protocol](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) a VPN-alagút létrehozásához használt és továbbítható tűzfalak (HTTPS-kapcsolatot, megjelenik az alagutat). A pont – hely kapcsolatot használhatja a saját belső PKI legfelső szintű hitelesítésszolgáltató.

A pont-pont VPN-kapcsolat egy virtuális hálózatot az Azure portál használatával tanúsítványhitelesítés vagy a PowerShell használatával konfigurálható.

Pont-pont VPN-kapcsolatok az Azure Vnetekhez kapcsolatos további információkért lásd: [hitelesítő hitelesítéssel VNet pont – hely kapcsolat konfigurálva: Azure-portálon](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) és

[Egy VNet-alapú hitelesítést használó pont – hely kapcsolat konfigurálva: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>Telephelyek közötti VPN 

A helyek közötti VPN-átjárókapcsolat használatával kapcsolat hozható létre a helyszíni hálózat és egy Azure-beli virtuális hálózat között egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Az ilyen típusú kapcsolatokhoz egy helyszíni VPN-eszközre van szükség, amelyhez hozzá van rendelve egy kifelé irányuló, nyilvános IP-cím.

A pont-pont VPN-kapcsolat egy virtuális hálózatot az Azure-portálon, PowerShell vagy az Azure parancssori felület (CLI) használatával konfigurálható.

Olvassa el az alábbi további információk:

[Pont-pont kapcsolat létrehozása az Azure-portálon](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Pont-pont kapcsolat létrehozása](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Virtuális hálózat létrehozása a parancssori felület használatával pont-pont VPN-kapcsolattal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Azure Data Lake az átvitel közbeni titkosítás

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Amellett, hogy az adatok titkosítása az állandó adathordozón való tárolás előtt történik meg, az átvitt adatok is mindig titkosítva vannak HTTPS segítségével. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

Az Azure Data Lake átvitel adatok titkosításával kapcsolatos további tudnivalókért tekintse meg a című dokumentum [az Azure Data Lake Store adatok titkosítását.](../data-lake-store/data-lake-store-encryption.md)

## <a name="key-management-with-azure-key-vault"></a>Az Azure Key Vault kulcskezelés

Nem megfelelő védelme és kezelése a kulcsok titkosítási használhatatlan lett megjelenítve. Az Azure Key Vault a Microsoft által javasolt megoldás a kezelése és a felhőalapú szolgáltatás által használt titkosítási kulcsok hozzáférés szabályozása. Kulcsok eléréséhez engedélyek rendelhetők, szolgáltatások vagy felhasználók keresztül Azure Active Directory-fiókokat.

Az Azure Key Vault mentesíti szervezetek konfigurálását, a javítás és a hardveres biztonsági modulokkal (HSM) és a kulcskezelést szoftver karbantartása szükségességét. Az Azure Key Vault Microsoft soha nem látja a kulcsokat, és alkalmazások nincs közvetlen hozzáférése irányítást tarthat fenn. Importálja vagy kulcsok létrehozása hardveres biztonsági modulok is.

## <a name="next-steps"></a>Következő lépések

- [Az Azure biztonsági szolgáltatásainak áttekintése](security-get-started-overview.md)
- [Az Azure biztonsági áttekintése](security-network-overview.md)
- [Azure-adatbázis biztonsági áttekintése](azure-database-security-overview.md)
- [Az Azure virtuális gépek biztonsági áttekintése](security-virtual-machines-overview.md)
- [Adattitkosítás inaktív állapotban](azure-security-encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](azure-security-data-encryption-best-practices.md)
