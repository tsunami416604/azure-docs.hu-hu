---
title: Azure-titkosítás áttekintése |} A Microsoft Docs
description: További tudnivalók az Azure-ban különböző titkosítási beállítások
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 272cc843ab90eade06525f665d3cf2decf74a26f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114654"
---
# <a name="azure-encryption-overview"></a>Azure-titkosítás áttekintése

Ez a cikk áttekintése a Microsoft Azure titkosítási használatáról. Ez fedezi a titkosítás, többek között az inaktív adatok titkosítását, a titkosítás a flight és kulcskezelés az Azure Key Vault szükséges főbb információkat. Minden szakasz részletesebb információkra mutató hivatkozásokat tartalmaz.

## <a name="encryption-of-data-at-rest"></a>A tárolt adatok titkosítása

Inaktív adatok, amelyekhez fizikai adathordozón, digitális nagyságrendű állandó tárolóban található. Az adathordozó mágneses vagy optikai adathordozón, az archivált adatok és a biztonsági mentések fájlok lehetnek. A Microsoft Azure számos adattárolási megoldástípust eltérő igényeinek, beleértve a fájl, a lemez, a blob és a table storage. A Microsoft is biztosít a védelme érdekében titkosítási [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [Azure Cosmos DB](../cosmos-db/introduction.md), és az Azure Data Lake.

Adatok titkosítását rendelkezésre áll a szolgáltatások között a szoftver (SaaS) szolgáltatás, az infrastruktúra és platformszolgáltatás (PaaS) platform egy felhőalapú szolgáltatás (IaaS) modellek. Ez a cikk összefoglalja, és forrásanyagokat biztosít az Azure-titkosítás beállításait használja.

Részletesen tárgyalja, hogyan inaktív adatok titkosítása az Azure-ban, lásd: [Azure adatok titkosítása inaktív](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Az Azure titkosítási modellek

Az Azure támogatja különböző titkosítási modellekhez, beleértve a szolgáltatás által kezelt kulcsokkal, felhasználó által kezelt kulcsok a Key Vaultban vagy ügyfél által felügyelt kulcsokat használó ügyfél által szabályozott hardveren kiszolgálóoldali titkosítás. Ügyféloldali titkosítással is kezelheti és helyszíni kulcsok tárolására, vagy egy másik tegye biztonságossá a helyet.

### <a name="client-side-encryption"></a>Ügyféloldali titkosítás

Ügyféloldali titkosítás az Azure-on kívül történik. Ezek a következők:

- Olyan alkalmazás, amely az ügyfél az adatközpontban fut, vagy egy szolgáltatásalkalmazást titkosított adatokat.
- Már van titkosítva, Azure által fogadott adatok.

Ügyféloldali titkosítás, a felhőbeli szolgáltatók nincs hozzáférése a titkosítási kulcsokat, és nem tudja visszafejteni ezeket az adatokat. A kulcsok teljes körű felügyeletet megmaradjanak.

### <a name="server-side-encryption"></a>Kiszolgálóoldali titkosítás

A három kiszolgálóoldali titkosítás modellek ajánlat az igényeknek megfelelően választhat másik kulcskezelés jellemzők:

- **Szolgáltatás által kezelt kulcsok**: Vezérlés és kényelmi biztosít alacsony többletterhelést okoznak.

- **Felhasználó által kezelt kulcsokkal**: Lehetővé teszi a kulcsok, beleértve a Bring Your Own kulcsokat (BYOK) támogatása, felett ellenőrzést, vagy lehetővé teszi, hogy hozzon létre újakat.

- **Szolgáltatás által kezelt kulcsokat az ügyfél által felügyelt hardveres**: Lehetővé teszi a szellemi tulajdont képező adattárban kívül a Microsoft ellenőrzési kulcsok kezelését. Ez a jellemző gazdagép Your Own Key (HYOK) nevezzük. Azonban konfigurációs összetett, és a legtöbb Azure-szolgáltatások nem támogatják ezt a modellt.

### <a name="azure-disk-encryption"></a>Az Azure disk encryption

Windows és Linux rendszerű virtuális gépek használatával védheti [az Azure disk encryption](azure-security-disk-encryption.md), mely használ [Windows BitLocker](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) technológia és a Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) védelmére egyaránt operációsrendszer-lemezek és az adatlemezek használata teljes kötettitkosítást.

Titkosítási kulcsok és titkos kulcsok számára biztosít védelmet a a [Azure Key Vault-előfizetésük](../key-vault/key-vault-whatis.md). Az Azure Backup szolgáltatás használatával készítsen biztonsági másolatot, és állítsa vissza a titkosított virtuális gépek (VM), Key titkosítási kulcscserekulcs (KEK) konfigurációjának használatára.

### <a name="azure-storage-service-encryption"></a>Azure Storage Service Encryption

Inaktív adatok Azure Blob storage és Azure-fájlmegosztások a kiszolgálóoldali és az ügyféloldali forgatókönyvekben titkosíthatók.

[Az Azure Storage Service Encryption (SSE)](../storage/common/storage-service-encryption.md) automatikusan az adatokat titkosíthatja előtt lesz tárolva, és automatikusan visszafejti az adatokat, lekérésekor. A folyamat az teljesen átlátható a felhasználók számára. A Storage Service Encryption használ 256 bites [Advanced Encryption Standard (AES) titkosítást](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), egyike a rendelkezésre álló legerősebb blokktitkosító. AES átlátható módon kezeli titkosítási, visszafejtési és kulcskezelési.

### <a name="client-side-encryption-of-azure-blobs"></a>Ügyféloldali titkosítás az Azure-blobok

Hajthat végre ügyféloldali titkosítása az Azure-blobok különféle módon.

Az Azure Storage ügyféloldali kódtárat for .NET NuGet csomag segítségével az ügyfél-alkalmazásokban az Azure storage-ba való feltöltés előtt titkosítja az adatokat.

További információk és az Azure Storage ügyféloldali kódtárat for .NET NuGet csomag letöltéséhez lásd: [Windows Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Ügyféloldali titkosítás a Key Vault használatakor az adatok titkosítását a rendszer egy egyszeri szimmetrikus tartalom titkosítási kulcs (CEK) az Azure Storage ügyféloldali SDK által létrehozott. A CEK titkosított használatát egy kulcs titkosítási kulcscserekulcs (KEK), ami az vagy egy szimmetrikus kulcsot, vagy aszimmetrikus kulcspárt alkotnak. Kezelheti, helyileg vagy a Key Vaultban tárolhatók is. A titkosított adatok majd fel van töltve az Azure Storage.

További információ az ügyféloldali titkosítás a Key Vault és az első lépések útmutató utasításait [oktatóanyag: Blobok titkosítása és visszafejtése az Azure Storage Key Vault használatával](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Végül is használhatja az Azure Storage ügyféloldali kódtára a Javához készült ügyféloldali titkosítás végrehajtásához, mielőtt az adatfeltöltés az Azure Storage és az adatok visszafejtéséhez, amikor az ügyfél letöltése. Ebben a könyvtárban is támogatja az integrációt [Key Vault](https://azure.microsoft.com/services/key-vault/) a tárfiókkulcs-kezelés.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Az Azure SQL Database az inaktív adatok titkosítása

[Az Azure SQL Database](../sql-database/sql-database-technical-overview.md) egy általános célú relációs adatbázis-szolgáltatás az Azure-ban, amely támogatja többek között a relációs, JSON-, térbeli és XML. SQL Database támogatja a transzparens adattitkosítás (TDE) funkciójával kiszolgálóoldali titkosítás és az ügyféloldali titkosítás az Always Encrypted funkció segítségével.

#### <a name="transparent-data-encryption"></a>Transzparens adattitkosítás

[TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) titkosításához használt [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md), és [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) adatfájlok valós idejű, egy adatbázis-titkosítási kulcs (Adattitkosítási) használatával , amely a rendelkezésre állási adatbázis rendszerindító rekordját helyreállítás során tárolja.

AES és a Triple Data Encryption Standard (3DES) használatával védi TDE adathoz és naplófájlhoz titkosítási algoritmusokat használhatná. Az adatbázisfájl titkosítás lap szintjén történik. Az oldalak egy titkosított adatbázis titkosításának megkövetelése azok írt lemezre, és ha azok még a memóriába lesznek visszafejtve. TDE alapértelmezés szerint az újonnan létrehozott Azure SQL-adatbázisok már engedélyezve van.

#### <a name="always-encrypted-feature"></a>Always Encrypted funkció

Az a [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkció az Azure SQL-ben belüli előtt, az Azure SQL Database használatával titkosítsa. Is engedélyezze a delegálást, külső felek számára a helyszíni adatbázis-felügyeleti és azok számára, akik a saját és tekintheti meg és azok számára, akik kezelheti, de nem rendelkezhet hozzáféréssel elkülönülését karbantartása.

#### <a name="cell-level-or-column-level-encryption"></a>Cellaszintű vagy oszlopszintű titkosítást

Transact-SQL használatával az Azure SQL Database szimmetrikus titkosítási is alkalmazhat az adatok egy oszlopot. Ezt a módszert nevezik [cellaszintű vagy oszlopszintű titkosítást (CLE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data), mivel használható titkosításához egyes oszlopokat, vagy akár adott cellákat is külön titkosítási kulccsal. Ez lehetővé teszi a részletes titkosítási, mint a TDE, amely titkosítja az adatokat az oldalak.

CLE rendelkezik beépített funkciók, amelyek segítségével a szimmetrikus vagy aszimmetrikus kulcs, a nyilvános kulcsát egy tanúsítványt, vagy egy hozzáférési kódot a 3DES használatával titkosítja az adatokat.

### <a name="cosmos-db-database-encryption"></a>A cosmos DB adatbázis-titkosítás

[Az Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) a Microsoft globálisan elosztott, többmodelles adatbázisa. Felhasználói adatok állandó tárolóba (SSD-meghajtókat) Cosmos DB-ben tárolt titkosított alapértelmezés szerint. Nincsenek kapcsolhatja be és ki. Inaktív adatok titkosítását számos biztonsági technológia, többek között a biztonságos kulcs tárolása rendszerek, a titkosított hálózatokat és a titkosítási API-k segítségével van megvalósítva. Titkosítási kulcsok a Microsoft által felügyelt, és a Microsoft belső irányelveket rögzített száma elforgatott vannak.

### <a name="at-rest-encryption-in-data-lake"></a>A Data Lake-titkosításra inaktív

[Az Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) egy vállalati szintű adattár, bármilyen típusú adat egyetlen helyen bármely követelmények és sémák formális meghatározása előtt gyűjteni. Data Lake Store támogatja az "a alapértelmezés szerint" van beállítva, a fiók létrehozása során inaktív adatok átlátható titkosítást. Alapértelmezés szerint az Azure Data Lake Store kezeli a kulcsok az Ön számára, de lehetősége van saját kezűleg kezelheti őket.

Háromféle kulcsokat használnak az adatok titkosítása és visszafejtése: a titkosítási főkulcs (titkosítási főkulcs esetén), az adatok titkosítási kulcsa (Adattitkosítási) és a blokk titkosítási kulcs (Blokktitkosítási). A titkosítási Főkulcs segítségével titkosíthatja az adattitkosítási kulcsot, amely állandó adathordozón tárolódik, és a blokktitkosítási kulcsot az adattitkosítási Kulcsból és az adatblokkból származtatjuk van származtatva. Ha saját maga kezeli a, hogy a titkosítási Főkulcs rotálása is.

## <a name="encryption-of-data-in-transit"></a>Az átvitt adatok titkosítása

Az Azure számos mechanizmusok adatok megőrzése mellett privát módon egyik helyről a másikra áthelyezi kínál.

### <a name="tlsssl-encryption-in-azure"></a>A TLS/SSL-titkosítást az Azure-ban

A Microsoft használ a [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) protokoll adatok védelme érdekében a cloud services és az ügyfelek közötti útközben. A Microsoft adatközpontjai TLS-kapcsolatot az Azure-szolgáltatásokhoz csatlakozó ügyfél rendszerekkel való egyeztetés. A TLS biztosít erős hitelesítés, üzenetvédelem, és kódintegritási (üzenet illetéktelen hozzáférés és hamisítására észlelésének engedélyezése), együttműködési, az algoritmusok rugalmassága és egyszerű üzembe helyezés és használata.

[Előre sérülés utáni Titkosságvédelmi tökéletes](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) egyedi kulcsok ügyfelei ügyfél és a Microsoft-felhőszolgáltatások közötti kapcsolatok védi. Kapcsolatok is használhatja a kulcshosszúság 2048 bites RSA-alapú titkosítás. Ez a kombináció, így nehéz számára, amely az átvitt adatok intercept és a hozzáférés.

### <a name="azure-storage-transactions"></a>Az Azure Storage-tranzakciók

Ha az Azure Portalon keresztül dolgozhat Azure Storage, az összes tranzakció HTTPS-kapcsolaton keresztül kerül sor. Használhatja a Storage REST API-JÁT HTTPS-kapcsolaton keresztül kommunikáljon az Azure Storage. A HTTPS használata kényszerítheti a REST API-k hozzáférést objektumokhoz a storage-fiókok a biztonságos átvitel szükséges a tárfiók engedélyezésével hívja.

A közös hozzáférésű Jogosultságkódot ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), delegálása az Azure Storage-objektumokhoz való hozzáférést, tartalmaznak, adja meg, hogy csak a HTTPS protokollt használja, közös hozzáférési aláírások használata esetén is használható. Ez a megközelítés biztosítja, hogy bárki, aki küld az SAS-tokeneket hivatkozásokat a megfelelő protokollt használja.

[SMB 3.0-s](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption), amellyel hozzáférhet az Azure Files megosztások, támogatja a titkosítást, és azt a Windows Server 2012 R2, Windows 8, Windows 8.1 és Windows 10-es érhető el. Ez lehetővé teszi a régiók közötti hozzáférést, és elérheti az asztalon.

Ügyféloldali titkosítás titkosítja az adatokat az Azure Storage-példányra, továbbítás előtt, hogy a titkosított a hálózaton keresztül.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Az Azure virtuális hálózatokon keresztül SMB-titkosítás 

Használatával [SMB 3.0-s](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) -beli virtuális gépeken, amely a Windows Server 2012 vagy újabb rendszerű, győződjön meg data transfers gondoskodhat az átvitt adatok titkosítása az Azure virtuális hálózatokon keresztül. Adatok titkosításával segítséget, illetéktelen módosítás, és a támadások lehallgatás elleni védelem érdekében. A rendszergazdák a teljes kiszolgáló, vagy csak adott megosztásokon SMB titkosítást alkalmazhat.

Alapértelmezés szerint Miután SMB-titkosítás be van kapcsolva a megosztás vagy a kiszolgálón, csak az SMB 3.0-ügyfelek engedélyezettek a titkosított megosztások elérésére.

## <a name="in-transit-encryption-in-vms"></a>Az átvitel közbeni titkosítás-beli virtuális gépeken

Számos módon, a kapcsolat jellegétől függően a titkosított, valamint a Windows rendszert futtató virtuális gépek között átvitt adatok.

### <a name="rdp-sessions"></a>RDP-munkamenetekhez

Csatlakozzon, és jelentkezzen be egy virtuális géphez a [Remote Desktop Protocol (RDP)](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) Windows ügyfél-számítógépen, vagy egy Mac számítógéppel egy RDP-ügyfelet. Az RDP-munkameneteket a hálózaton keresztül az átvitt adatok TLS védi.

A távoli asztal használatával csatlakozhat egy Linux rendszerű virtuális gép az Azure-ban.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Biztonságos hozzáférés a Linux rendszerű virtuális gépek az ssh-val

A távoli felügyeleti használhatja [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) Azure-ban futó Linux rendszerű virtuális gépek csatlakozni. Az SSH nem titkosított kapcsolatot protokoll, amely lehetővé teszi a biztonságos bejelentkezés nem biztonságos kapcsolatokon keresztül. Az Azure-ban üzemeltetett Linuxos virtuális gépek az alapértelmezett kapcsolati protokoll. SSH-kulcsokat használnak a hitelesítéshez, megszüntetheti a szükség jelszavakra a bejelentkezéshez. Az SSH egy nyilvános/titkos kulcspárt (az aszimmetrikus titkosítási) használja a hitelesítéshez.

## <a name="azure-vpn-encryption"></a>Az Azure VPN-titkosítás

Az Azure, amely létrehoz egy biztonságos alagutat a a hálózaton keresztül küldött adatok védelmét szolgáló virtuális magánhálózaton keresztül is csatlakozhat.

### <a name="azure-vpn-gateways"></a>Az Azure VPN Gateway átjárók

Használhat egy [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) a virtuális hálózat és a helyszíni hely közötti titkosított adatforgalmat továbbíthat nyilvános kapcsolaton keresztül, vagy a virtuális hálózatok közötti forgalmat.

Site-to-site VPN-ek használata [IPsec](https://en.wikipedia.org/wiki/IPsec) átviteli titkosításhoz. Az Azure VPN-átjárók használatával egy alapértelmezett javaslatokat. Konfigurálhatja az Azure VPN-átjárók egy egyéni IPsec/IKE-házirend használata a titkosítási algoritmusokat és kulcserősségeket ahelyett, hogy beállítja az Azure alapértelmezett szabályzat.

### <a name="point-to-site-vpns"></a>Pont – hely VPN-EK

Pont – hely VPN-ek egyes ügyfél Azure-beli virtuális hálózathoz számítógépek elérését teszi lehetővé. [A Secure Socket Tunneling Protocol (SSTP)](https://technet.microsoft.com/library/2007.06.cableguy.aspx) a VPN-alagút létrehozásához használatos. Azt is bejárása tűzfalak (HTTPS-kapcsolatként jelenik az alagút). A pont – hely kapcsolatot is használhatja a saját belső nyilvános kulcsú infrastruktúra (PKI) legfelső szintű hitelesítésszolgáltató (CA).

Virtuális hálózat pont – hely VPN-kapcsolatot az Azure Portalon a Tanúsítványalapú hitelesítés vagy a PowerShell használatával konfigurálhatja.

Pont – hely VPN-kapcsolatok Azure virtuális hálózatokkal kapcsolatos további információkért lásd:

[Egy virtuális hálózathoz pont – hely kapcsolat konfigurálása a Tanúsítványalapú hitelesítés használatával: Az Azure Portalon](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) 

[Egy virtuális hálózathoz pont – hely kapcsolat konfigurálása a Tanúsítványalapú hitelesítés használatával: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpns"></a>Site-to-site VPN-EK 

Site-to-site VPN gateway-kapcsolat használatával a helyszíni hálózat csatlakoztatása egy Azure virtuális hálózat egy IPsec/IKE (IKEv1 vagy IKEv2) VPN-alagúton keresztül. Ez a kapcsolattípus szükséges egy helyszíni VPN-eszköz, amely rendelkezik egy kívülre irányuló nyilvános IP-cím rendelve.

A site-to-site VPN-kapcsolat egy virtuális hálózatot az Azure portal, PowerShell vagy az Azure CLI használatával konfigurálhatja.

További információkért lásd:

[Helyek közötti kapcsolat létrehozása az Azure Portalon](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Helyek közötti kapcsolat létrehozása a PowerShellben](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal a parancssori felület használatával](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-data-lake"></a>A Data Lake az átvitel közbeni titkosítás

Az átvitt adatok (azaz a mozgásban lévő adatok) titkosítása is mindig a Data Lake Store-ban történik. Mellett, mielőtt állandó adathordozón tárolná, az adatok titkosítására, az adatok is mindig védett átvitel közben HTTPS használatával. A HTTPS az egyetlen olyan protokoll, amely támogatott a Data Lake Store REST-felületeihez.

A Data Lake az átvitt adatok titkosítását kapcsolatos további információkért lásd: [a Data Lake Store az adatok titkosítása az](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-key-vault"></a>Kulcskezelés a Key Vault

Nem megfelelő védelmet és a kulcsok kezelését titkosítási használhatatlan lett megjelenítve. A Key Vault a Microsoft által ajánlott megoldás, kezeléséhez és a cloud services által használt titkosítási kulcsok való hozzáférés szabályozása. Hozzáférési kulcsok engedélyekkel rendelhetők, szolgáltatások vagy felhasználók Azure Active Directory-fiókokon keresztül.

A Key Vault mentesíti a szervezetek kell konfigurálni, javítani és karbantartása a hardveres biztonsági modulokban (HSM) és kulcskezelő szoftvereket. A Key Vault használata esetén, feletti. A Microsoft soha nem látja a kulcsokat, és az alkalmazások nem rendelkeznek közvetlen hozzáférést. Is importálását vagy létrehozását a kulcsokat.

## <a name="next-steps"></a>További lépések

- [Az Azure biztonsági szolgáltatásainak áttekintése](security-get-started-overview.md)
- [Azure-hálózat biztonsági áttekintése](security-network-overview.md)
- [Azure database biztonságának áttekintése](azure-database-security-overview.md)
- [Azure-beli virtuális gépek biztonsági áttekintése](security-virtual-machines-overview.md)
- [Adattitkosítás inaktív állapotban](azure-security-encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](azure-security-data-encryption-best-practices.md)
