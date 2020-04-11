---
title: Transzparens adattitkosítás
description: Az Azure Synapse Analytics átlátható adattitkosításának áttekintése az SQL Database és a Synapse SQL számára. A dokumentum ismerteti annak előnyeit és a konfigurációs lehetőségeket, amely magában foglalja a szolgáltatás által felügyelt transzparens adattitkosítást és a Saját kulcs hozást.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113550"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transzparens adattitkosítás az SQL Database és az Azure Synapse számára

[Az átlátszó adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) segít megvédeni az Azure SQL Database- és az Azure SQL felügyelt példánya és a Synapse SQL az Azure Synapse Analytics-ben a rosszindulatú offline tevékenység fenyegetésével szemben az inaktív adatok titkosításával. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van az összes újonnan telepített Azure SQL-adatbázisok és manuálisan engedélyezni kell a régebbi adatbázisok az Azure SQL Database, az Azure SQL felügyelt példány, vagy az Azure Synapse.

A TDE valós idejű I/O-titkosítást és az adatok visszafejtését végzi az oldal szintjén. Az egyes lapok visszafejtése a memóriába történő beolvasáskor történik, a titkosítás pedig a lemezre írás előtt. A TDE egy teljes adatbázis tárolását egy adatbázis-titkosítási kulcsnak (DEK) nevezett szimmetrikus kulccsal titkosítja. Az adatbázis indításakor a titkosított DEK-t visszafejti, majd az SQL Server Database Engine folyamatában lévő adatbázisfájlok visszafejtésére és újratitkosítására használja. A DEK-t a TDE-védő védi. A TDE-védő vagy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által felügyelt transzparens adattitkosítás) vagy az [Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) tárolt aszimmetrikus kulcs (ügyfél által felügyelt transzparens adattitkosítás). 

Az Azure SQL Database és az Azure Synapse esetében a TDE-védő a logikai SQL-kiszolgáló szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az Azure SQL felügyelt példány (BYOK szolgáltatás előzetes verzióban) esetében a TDE-védő a példány szintjén van beállítva, és az adott példány összes titkosított adatbázisa örökli. A *kiszolgáló* kifejezés a dokumentum ban található kiszolgálóra és példányra is vonatkozik, kivéve, ha másként van megállapítva.

> [!IMPORTANT]
> Az összes újonnan létrehozott Azure SQL-adatbázis alapértelmezés szerint titkosítva van a szolgáltatás által felügyelt transzparens adattitkosítás használatával. A 2017 májusa előtt létrehozott meglévő SQL-adatbázisok, valamint a visszaállítással, a georeplikációval és az adatbázis-másolattal létrehozott SQL-adatbázisok alapértelmezés szerint nem titkosítva vannak. A 2019 februárja előtt létrehozott meglévő felügyelt példány-adatbázisok alapértelmezés szerint nincsenek titkosítva. A visszaállítással létrehozott felügyelt példányok a forrástól öröklik a titkosítási állapotot.

> [!NOTE]
> A TDE nem használható az SQL Database logikai **főadatbázisának** titkosítására.  A **főadatbázis** olyan objektumokat tartalmaz, amelyek a felhasználói adatbázisokTDE műveleteinek végrehajtásához szükségesek.


## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által felügyelt transzparens adattitkosítás

Az Azure-ban a TDE alapértelmezett beállítása az, hogy a DEK-t egy beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgálóesetében egyedi, a használt titkosítási algoritmus pedig Az AES 256. Ha egy adatbázis georeplikációs kapcsolatban van, az elsődleges és a geoszekunder adatbázisokat is védi az elsődleges adatbázis szülőkiszolgálókulcsa. Ha két adatbázis ugyanahhoz a kiszolgálóhoz csatlakozik, akkor ugyanaz a beépített tanúsítvány is osztozik.  A Microsoft automatikusan elforgatja ezeket a tanúsítványokat a belső biztonsági házirendnek megfelelően, és a gyökérkulcsot egy Microsoft belső titkos tároló védi.  Az ügyfelek a Microsoft Adatvédelmi központban elérhető független, külső ellenőrzési jelentésekben ellenőrizhetik az SQL Database belső biztonsági szabályzatoknak való [megfelelését.](https://servicetrust.microsoft.com/)

A Microsoft zökkenőmentesen mozgatja és kezeli a kulcsokat, ha szükséges a georeplikációhoz és a visszaállításhoz.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Ügyfél által felügyelt, átlátható adattitkosítás - Saját kulcs

Az ügyfél által felügyelt TDE-t a TDE saját kulcsának (BYOK) támogatásaként is emlegetik. Ebben a forgatókönyvben a DEK-t titkosító TDE Protector egy ügyfél által felügyelt aszimmetrikus kulcs, amely egy ügyfél tulajdonában lévő és felügyelt Azure Key Vaultban (az Azure felhőalapú külső kulcskezelő rendszerében) tárolódik, és soha nem hagyja el a key vaultot. A TDE Protector a key vault által létrehozható, vagy egy helyszíni hardveres biztonsági modul (HSM) eszközről [átvihető a key vaultba.](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) Az SQL Database-nek engedélyeket kell adni az ügyfél tulajdonában lévő key vaultnak a DEK visszafejtéséhez és titkosításához. Ha a logikai SQL-kiszolgáló nak a key vaultra vonatkozó engedélyeit visszavonják, az adatbázis nem lesz elérhető, és az összes adat titkosítva van

Az Azure Key Vault-integrációval rendelkező TDE-integrációval a felhasználók szabályozhatják a kulcskezelési feladatokat, beleértve a kulcsforgatásokat, a kulcstároló engedélyeit, a kulcsbiztonsági mentéseket, és engedélyezhetik az Azure Key Vault funkcióival az összes TDE-védő naplózását/jelentését. A Key Vault központi kulcskezelést biztosít, kihasználja a szigorúan figyelt HSM-eket, és lehetővé teszi a feladatok elkülönítését a kulcsok és az adatok kezelése között, hogy megfeleljen a biztonsági házirendeknek való megfelelésnek.
Ha többet szeretne megtudni a BYOK for Azure SQL Database és az Azure Synapse szolgáltatásról, olvassa el [az Átlátszó adattitkosítás az Azure Key Vault-integrációval című témakört.](transparent-data-encryption-byok-azure-sql.md)

A TDE azure-beli Key Vault-integrációval való használatának megkezdéséhez olvassa el az útmutató útmutatót az [átlátható adattitkosítás bekapcsolása a Key Vaultból származó saját kulcs használatával című témakörben.](transparent-data-encryption-byok-azure-sql-configure.md)

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Az Azure-on belüli műveletekhez nem kell visszafejtenie az adatbázisokat. A forrásadatbázis vagy az elsődleges adatbázis TDE-beállításai transzparens módon öröklődnek a célon. A benne foglalt műveletek a következőket foglalják magukban:

- Georedundáns helyreállítás
- Önkiszolgáló időpont-időpont visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázismásolat létrehozása
- Biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázis manuális copy-only biztonsági mentése nem engedélyezett az Azure SQL felügyelt példányában, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az ilyen típusú adatbázis áthelyezése egy másik felügyelt példányba a pont-az-időben-visszaállítás i-time-restore szolgáltatással.

TDE-védelemmel ellátott adatbázis exportálásakor az adatbázis exportált tartalma nem lesz titkosítva. Ez az exportált tartalom titkosítatlan BACPAC-fájlokban tárolódik. Ügyeljen arra, hogy megfelelően védje a BACPAC-fájlokat, és engedélyezze a TDE-t az új adatbázis importálása után.

Ha például a BACPAC-fájlt egy helyszíni SQL Server-példányból exportálja, az új adatbázis importált tartalma nem lesz automatikusan titkosítva. Hasonlóképpen, ha a BACPAC-fájlt egy helyszíni SQL Server-példányba exportálja, az új adatbázis sem lesz automatikusan titkosítva.

Az egyetlen kivétel az SQL-adatbázisba és sql-adatbázisból történő exportálás. A TDE engedélyezve van az új adatbázisban, de maga a BACPAC fájl még mindig nincs titkosítva.


## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése
# <a name="portal"></a>[Portál](#tab/azure-portal)
A TDE kezelése az Azure Portalon.

A TDE konfigurálásához az Azure Portalon keresztül, az Azure-tulajdonos, közreműködő vagy az SQL Security Manager kapcsolatban kell lennie.

A TDE-t az adatbázis szintjén kapcsolja be és ki. A TDE engedélyezése egy adatbázisban, nyissa meg az [Azure Portalon,](https://portal.azure.com) és jelentkezzen be az Azure-rendszergazdai vagy közreműködői fiókkal. Keresse meg a TDE-beállításokat a felhasználói adatbázisban. Alapértelmezés szerint a szolgáltatás által felügyelt transzparens adattitkosítás t használja. A rendszer automatikusan TDE-tanúsítványt hoz létre az adatbázist tartalmazó kiszolgálóhoz. Az Azure SQL felügyelt példány a T-SQL segítségével kapcsolja be és ki a TDE egy adatbázisban.

![Szolgáltatás által felügyelt transzparens adattitkosítás](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

A TDE főkulcsot, más néven a TDE-védőt a kiszolgáló szintjén állíthatja be. Ha a TDE-t BYOK-támogatással szeretné használni, és az adatbázisokat a Key Vault ból származó kulccsal szeretné megvédeni, nyissa meg a TDE-beállításokat a kiszolgáló alatt.

![Transzparens adattitkosítás saját kulcstámogatással](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
TDE kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

A TDE PowerShellen keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell csatlakoznia.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Parancsmagok az Azure SQL Database és az Azure Synapse számára

Használja a következő parancsmagokat az Azure SQL Database és az Azure Synapse számára:

| Parancsmag | Leírás |
| --- | --- |
| [Set-AzsqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Adatbázis transzparens adattitkosításának engedélyezése vagy letiltása|
| [Get-AzSQLDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Az adatbázis transzparens adattitkosítási állapotának beírása |
| [Get-AzSqlDatabaseTransparentDataEncryptionactivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Adatbázis titkosítási folyamatának ellenőrzése |
| [Add-AzSqlserverKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Key Vault-kulcs hozzáadása SQL Server-példányhoz |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Beszerzi egy Azure SQL Database-kiszolgáló Key Vault-kulcsait  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Sql Server-példány transzparens adattitkosítás-védőjének beállítja |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Beszerzi az átlátszó adattitkosítás-védőt |
| [Eltávolítás-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Eltávolítja a Key Vault-kulcsot egy SQL Server-példányból |
|  | |

> [!IMPORTANT]
> Az Azure SQL felügyelt példány, használja a T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) parancsot a TDE be- és kikapcsolása adatbázis szinten, és ellenőrizze [a minta PowerShell-parancsfájl](transparent-data-encryption-byok-azure-sql-configure.md) tde egy példány szinten.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
A TDE kezelése a Transact-SQL használatával.

Csatlakozzon az adatbázishoz egy rendszergazdai vagy a **főadatbázisdbmanager-szerepkörének** tagja bejelentkezéssel.

| Parancs | Leírás |
| --- | --- |
| [ALTER ADATBÁZIS (Azure SQL-adatbázis)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS BE/KI BEÁLLÍTÁSA Adatbázis titkosítása vagy visszafejtése |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Információt ad vissza az adatbázis és a hozzá tartozó adatbázis-titkosítási kulcsok titkosítási állapotáról |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Információt ad vissza az egyes Azure Synapse-csomópontok és a hozzájuk társított adatbázis-titkosítási kulcsok titkosítási állapotáról |
|  | |

A TDE-védő nem kapcsolható kulcsra a Key Vaultból a Transact-SQL használatával. Használja a PowerShellt vagy az Azure Portalt.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
A TDE kezelése a REST API használatával.

A TDE konfigurálásához a REST API-n keresztül, az Azure-tulajdonos, közreműködő vagy AZ SQL Security Manager csatlakoztatva kell lennie.
Használja az alábbi parancskészletet az Azure SQL Database és az Azure Synapse számára:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Azure Active Directory-identitás hozzáadása egy SQL Server-példányhoz (a Key Vault hoz való hozzáférés engedélyezésére szolgál)|
|[Kiszolgálókulcs létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Key Vault-kulcs hozzáadása SQL Server-példányhoz|
|[Kiszolgálókulcs törlése](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Eltávolítja a Key Vault-kulcsot egy SQL Server-példányból|
|[Kiszolgálókulcsok bekéselése](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Adott Key Vault-kulcs bekéselése EGY SQL Server-példányból|
|[Kiszolgálókulcsok listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Egy SQL Server-példány Key Vault-kulcsainak beszerzése |
|[Titkosítási védő létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Sql Server-példány TDE-védőjének beállítja|
|[Titkosítási védő beszerezni](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Egy SQL Server-példány TDE-védőjének lekéri|
|[Titkosítási védők listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Beszerzi egy SQL Server-példány TDE-védőit |
|[Transzparens adattitkosítási konfiguráció létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Adatbázis TDE-jének engedélyezése vagy letiltása|
|[Átlátható adattitkosítási konfiguráció beszereznie](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Adatbázis TDE-konfigurációjának beírása|
|[Átlátható adattitkosítási konfigurációs eredmények listázása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Az adatbázis titkosítási eredményének lekéri|

## <a name="see-also"></a>Lásd még:
- Az Azure virtuális gépen futó SQL Server is használhatja aszimmetrikus kulcs key vaultból. A konfigurációs lépések eltérnek az SQL Database és az SQL felügyelt példány aszimmetrikus kulcsának használatától. További információ: [Extensible key management by using Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- A TDE általános leírását az [Átlátszó adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Ha többet szeretne megtudni a TDE with BYOK támogatásaz Azure SQL Database, az Azure SQL felügyelt példány és az Azure Synapse, olvassa el [átlátszó adattitkosítás a saját kulcsa támogatással.](transparent-data-encryption-byok-azure-sql.md)
- A TDE használatának a Saját kulcs hoz támogatásával való használatának megkezdéséhez olvassa el az [útmutatóútmutatót: Az átlátszó adattitkosítás bekapcsolása saját kulcs használatával a Key Vaultból.](transparent-data-encryption-byok-azure-sql-configure.md)
- A Key Vaultról további információt a [Kulcstartóhoz való hozzáférés biztonságossá tétele](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)című témakörben talál.
