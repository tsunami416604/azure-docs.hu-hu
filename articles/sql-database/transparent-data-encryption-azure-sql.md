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
ms.date: 02/06/2020
ms.openlocfilehash: 982a59f1eb8717e2fe2d86728cdae731c919aaf0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476965"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transzparens adattitkosítás az SQL Database és az Azure Synapse számára

Az átlátszó adattitkosítás (TDE) segít megvédeni az Azure SQL Database- és az Azure SQL felügyelt példánya és a Synapse SQL az Azure Synapse Analytics-ben a rosszindulatú offline tevékenység fenyegetésével szemben az inaktív adatok titkosításával. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE az összes újonnan üzembe helyezett Azure SQL-adatbázis esetében engedélyezve van. A TDE nem használható az SQL Database logikai **főadatbázisának** titkosítására.  A **főadatbázis** olyan objektumokat tartalmaz, amelyek a felhasználói adatbázisokTDE műveleteinek végrehajtásához szükségesek.

A TDE-t manuálisan kell engedélyezni az Azure SQL Database, az Azure SQL felügyelt példány vagy az Azure Synapse régebbi adatbázisaihoz.
A visszaállítással létrehozott felügyelt példányok a forrásadatbázis titkosítási állapotát öröklik.

Az átlátszó adattitkosítás titkosítja a teljes adatbázis tárolását egy szimmetrikus kulcs, az adatbázis titkosítási kulcsa használatával. Ezt az adatbázis-titkosítási kulcsot az átlátszó adattitkosítás-védő védi. A protector vagy egy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által felügyelt transzparens adattitkosítás) vagy egy aszimmetrikus kulcs az Azure Key Vaultban (hozd a saját kulcs). Az átlátszó adattitkosítás-védőt az Azure SQL Database és az Azure Synapse kiszolgálószintjén, valamint az Azure SQL felügyelt példány példányszintjén állíthatja be. A *kiszolgáló* kifejezés a dokumentum ban található kiszolgálóra és példányra is vonatkozik, kivéve, ha másként van megállapítva.

Az adatbázis indításakor a titkosított adatbázis-titkosítási kulcsot visszafejti, majd az SQL Server Database Engine folyamatában lévő adatbázisfájlok visszafejtésére és újratitkosítására használja. Az átlátszó adattitkosítás valós idejű I/O-titkosítást és az adatok visszafejtését végzi az oldal szintjén. Az egyes lapok visszafejtése a memóriába történő beolvasáskor történik, a titkosítás pedig a lemezre írás előtt. Az átlátszó adattitkosítás általános leírását az [Átlátszó adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

Az Azure virtuális gépen futó SQL Server is használhatja aszimmetrikus kulcs key vaultból. A konfigurációs lépések eltérnek az SQL Database és az SQL felügyelt példány aszimmetrikus kulcsának használatától. További információ: [Extensible key management by using Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által felügyelt transzparens adattitkosítás

Az Azure-ban az átlátszó adattitkosítás alapértelmezett beállítása az, hogy az adatbázis-titkosítási kulcsot egy beépített kiszolgálói tanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgálóesetében egyedi, a használt titkosítási algoritmus pedig Az AES 256. Ha egy adatbázis georeplikációs kapcsolatban van, az elsődleges és a geoszekunder adatbázist is védi az elsődleges adatbázis szülőkiszolgálókulcsa. Ha két adatbázis ugyanahhoz a kiszolgálóhoz csatlakozik, akkor ugyanaz a beépített tanúsítvány is osztozik.  A Microsoft automatikusan elforgatja ezeket a tanúsítványokat a belső biztonsági házirendnek megfelelően, és a gyökérkulcsot egy Microsoft belső titkos tároló védi.  Az ügyfelek a Microsoft Adatvédelmi központban elérhető független, külső ellenőrzési jelentésekben ellenőrizhetik az SQL Database belső biztonsági szabályzatoknak való [megfelelését.](https://servicetrust.microsoft.com/)

A Microsoft zökkenőmentesen mozgatja és kezeli a kulcsokat, ha szükséges a georeplikációhoz és a visszaállításhoz.

> [!IMPORTANT]
> Az összes újonnan létrehozott SQL-adatbázis és felügyelt példány adatbázis a szolgáltatás által felügyelt transzparens adattitkosítás használatával alapértelmezés szerint titkosítva van. A 2017 májusa előtt létrehozott meglévő SQL-adatbázisok, valamint a visszaállítással, a georeplikációval és az adatbázis-másolattal létrehozott SQL-adatbázisok alapértelmezés szerint nem titkosítva vannak. A 2019 februárja előtt létrehozott meglévő felügyelt példány-adatbázisok alapértelmezés szerint nincsenek titkosítva. A visszaállítással létrehozott felügyelt példányok a forrástól öröklik a titkosítási állapotot.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Ügyfél által felügyelt, átlátható adattitkosítás - Saját kulcs

[Az Azure Key Vaultban az ügyfél által felügyelt kulcsokkal rendelkező TDE](transparent-data-encryption-byok-azure-sql.md) lehetővé teszi az adatbázis-titkosítási kulcs (DEK) titkosítását egy ügyfél által felügyelt aszimmetrikus kulccsal, a TDE Protector-ral.  Ez is általában nevezik Bring Your Own Key (BYOK) támogatja az átlátszó adattitkosítás. A BYOK-forgatókönyvben a TDE Protector egy ügyfél tulajdonában lévő és felügyelt [Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)tárolódik, amely az Azure felhőalapú külső kulcskezelő rendszere. A TDE Protector a key vault által létrehozott, vagy egy helyszíni HSM-eszközről [átvihető a key vaultba.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) A TDE DEK, amely egy adatbázis rendszerindító lapján van tárolva, az Azure Key Vaultban tárolt TDE Protector titkosítja és visszafejti, és soha nem hagyja el a key vaultot.  Az SQL Database-nek engedélyeket kell adni az ügyfél tulajdonában lévő key vaultnak a DEK visszafejtéséhez és titkosításához. Ha a logikai SQL-kiszolgáló engedélyeit visszavonják a key vaultba, az adatbázis nem lesz elérhető, és az összes adat titkosítva lesz. Az Azure SQL Database esetében a TDE-védő a logikai SQL-kiszolgáló szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az [Azure SQL felügyelt példány,](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)a TDE-védő a példány szintjén van beállítva, és az adott példány összes *titkosított* adatbázisa örökli. A *kiszolgáló* kifejezés a dokumentum ban található kiszolgálóra és példányra is vonatkozik, kivéve, ha másként van megállapítva.

Az Azure Key Vault-integrációval rendelkező TDE-integrációval a felhasználók szabályozhatják a kulcskezelési feladatokat, beleértve a kulcsforgatásokat, a kulcstároló engedélyeit, a kulcsbiztonsági mentéseket, és engedélyezhetik az Azure Key Vault funkcióival az összes TDE-védő naplózását/jelentését. A Key Vault központi kulcskezelést biztosít, kihasználja a szigorúan ellenőrzött hardveres biztonsági modulokat (HSM-ek), és lehetővé teszi a kulcsok és az adatok kezelése közötti feladatok elkülönítését a biztonsági házirendeknek való megfelelés érdekében.
Ha többet szeretne megtudni az Azure Key Vault-integrációval (Saját kulcs támogatással) az Azure SQL Database, az SQL felügyelt példány és az Azure Synapse transzparens adattitkosításáról, olvassa el az [Átlátszó adattitkosítás az Azure Key Vault-integrációval](transparent-data-encryption-byok-azure-sql.md)című témakört.

Az Azure Key Vault-integrációval való transzparens adattitkosítás használatának megkezdéséhez (Saját kulcs támogatáshoz való hozzá: [útmutató: Az átlátszó adattitkosítás bekapcsolása a Key Vaultból származó saját kulcs használatával a PowerShell használatával) című](transparent-data-encryption-byok-azure-sql-configure.md)útmutatóban.

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Az Azure-on belüli műveletekhez nem kell visszafejtenie az adatbázisokat. A forrásadatbázis vagy az elsődleges adatbázis transzparens adattitkosítási beállításai transzparens módon öröklődnek a célon. A benne foglalt műveletek a következőket foglalják magukban:

- Georedundáns helyreállítás
- Önkiszolgáló időpont-időpont visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázismásolat létrehozása
- Biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázis manuális copy-only biztonsági mentése nem engedélyezett az Azure SQL felügyelt példányában, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az ilyen típusú adatbázis áthelyezése egy másik felügyelt példányba a pont-az-időben-visszaállítás i-time-restore szolgáltatással.

Átlátszó adattitkosítással védett adatbázis exportálásakor az adatbázis exportált tartalma nem lesz titkosítva. Ez az exportált tartalom nem titkosított BACPAC fájlokban van tárolva. Ügyeljen arra, hogy megvédje a BACPAC fájlokat megfelelően, és lehetővé teszi az átlátható adattitkosítás importálása után az új adatbázis befejeződött.

Ha például a BACPAC-fájlt egy helyszíni SQL Server-példányból exportálja, az új adatbázis importált tartalma nem lesz automatikusan titkosítva. Hasonlóképpen, ha a BACPAC-fájlt egy helyszíni SQL Server-példányba exportálja, az új adatbázis sem lesz automatikusan titkosítva.

Az egyetlen kivétel az SQL-adatbázisba és sql-adatbázisból történő exportálás. Az átlátszó adattitkosítás engedélyezve van az új adatbázisban, de maga a BACPAC fájl még mindig nincs titkosítva.


## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése
# <a name="portal"></a>[Portál](#tab/azure-portal)
Átlátható adattitkosítás kezelése az Azure Portalon.

Az Azure Portalon keresztüli transzparens adattitkosítás konfigurálásához azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell csatlakoznia.

Az átlátszó adattitkosítást az adatbázis szintjén kapcsolja be és ki. Az adatbázis transzparens adattitkosításának engedélyezéséhez nyissa meg az [Azure Portalt,](https://portal.azure.com) és jelentkezzen be az Azure-rendszergazdai vagy közreműködői fiókkal. Keresse meg az átlátszó adattitkosítási beállításokat a felhasználói adatbázisban. Alapértelmezés szerint a szolgáltatás által felügyelt transzparens adattitkosítás t használja. A rendszer automatikusan átlátszó adattitkosítási tanúsítványt hoz létre az adatbázist tartalmazó kiszolgálóhoz. Az Azure SQL felügyelt példány a T-SQL segítségével be-és kikapcsolása átlátszó adattitkosítás egy adatbázisban.

![Szolgáltatás által felügyelt transzparens adattitkosítás](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Az átlátszó adattitkosítási főkulcsot, más néven az átlátszó adattitkosítás-védőt a kiszolgáló szintjén állíthatja be. Ha transzparens adattitkosítást szeretne használni a Saját kulcs támogatása és az adatbázisok kulcssal való védelme key from Key Vault használatával, nyissa meg az átlátszó adattitkosítási beállításokat a kiszolgáló alatt.

![Transzparens adattitkosítás saját kulcstámogatással](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Transzparens adattitkosítás kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Az átlátszó adattitkosítás powershellen keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerként kell csatlakoznia.

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
> Az Azure SQL felügyelt példány, használja a T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) parancsot az átlátszó adattitkosítás be- és kikapcsolása az adatbázis szintjén, és ellenőrizze [a minta PowerShell-parancsfájl](transparent-data-encryption-byok-azure-sql-configure.md) ta-k on egy példány szintjén az adattitkosítás kezelése.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Az átlátható adattitkosítás kezelése a Transact-SQL használatával.

Csatlakozzon az adatbázishoz egy rendszergazdai vagy a **főadatbázisdbmanager-szerepkörének** tagja bejelentkezéssel.

| Parancs | Leírás |
| --- | --- |
| [ALTER ADATBÁZIS (Azure SQL-adatbázis)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS BE/KI BEÁLLÍTÁSA Adatbázis titkosítása vagy visszafejtése |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Információt ad vissza az adatbázis és a hozzá tartozó adatbázis-titkosítási kulcsok titkosítási állapotáról |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Információt ad vissza az egyes adattárház-csomópontok és a hozzájuk tartozó adatbázis-titkosítási kulcsok titkosítási állapotáról. |
|  | |

Az átlátszó adattitkosítás-védő nem válthat kulcsra a Key Vaultból a Transact-SQL használatával. Használja a PowerShellt vagy az Azure Portalt.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Az átlátszó adattitkosítás kezelése a REST API használatával.

Az átlátszó adattitkosítás konfigurálásához a REST API-n keresztül, az Azure-tulajdonos, közreműködő vagy az SQL Security Manager kapcsolatban kell lennie.
Használja az alábbi parancskészletet az Azure SQL Database és az Azure Synapse számára:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Azure Active Directory-identitás hozzáadása egy SQL Server-példányhoz (a Key Vault hoz való hozzáférés engedélyezésére szolgál)|
|[Kiszolgálókulcs létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Key Vault-kulcs hozzáadása SQL Server-példányhoz|
|[Kiszolgálókulcs törlése](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Eltávolítja a Key Vault-kulcsot egy SQL Server-példányból|
|[Kiszolgálókulcsok bekéselése](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Adott Key Vault-kulcs bekéselése EGY SQL Server-példányból|
|[Kiszolgálókulcsok listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Egy SQL Server-példány Key Vault-kulcsainak beszerzése |
|[Titkosítási védő létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Sql Server-példány transzparens adattitkosítás-védőjének beállítja|
|[Titkosítási védő beszerezni](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Lekéri az SQL Server-példány átlátszó adattitkosítás-védőjét|
|[Titkosítási védők listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Lekéri az SQL Server-példány oktatott adattitkosítási védőit |
|[Transzparens adattitkosítási konfiguráció létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Adatbázis transzparens adattitkosításának engedélyezése vagy letiltása|
|[Átlátható adattitkosítási konfiguráció beszereznie](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Az adatbázis transzparens adattitkosítási konfigurációjának beírása|
|[Átlátható adattitkosítási konfigurációs eredmények listázása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Az adatbázis titkosítási eredményének lekéri|

## <a name="next-steps"></a>További lépések

- Az átlátszó adattitkosítás általános leírását az [Átlátszó adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Ha többet szeretne megtudni az átlátható adattitkosításról az Azure SQL Database, az Azure SQL felügyelt példány és az Azure Synapse saját kulcstámogatásával, olvassa el az [Átlátszó adattitkosítás a Saját kulcs támogatással című témakört.](transparent-data-encryption-byok-azure-sql.md)
- Az átlátszó adattitkosítás használatának megkezdéséhez a Saját kulcs hoz támogatásával tekintse meg az [útmutató útmutatót Az átlátszó adattitkosítás bekapcsolása a Key Vaultból származó saját kulcs használatával a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md)című témakörben.
- A Key Vaultról további információt a [Key Vault dokumentációs lapján](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)talál.
