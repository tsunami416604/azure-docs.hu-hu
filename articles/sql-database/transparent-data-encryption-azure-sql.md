---
title: Transzparens adattitkosítás
description: Az Azure szinapszis Analytics szolgáltatásban az SQL Database és a szinapszis SQL transzparens adattitkosításának áttekintése. A dokumentum a szolgáltatás által felügyelt transzparens adattitkosítást és Bring Your Own Keyt is magában foglaló előnyökkel és beállításokkal foglalkozik.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113550"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transzparens adattitkosítás a SQL Database és az Azure szinapszis számára

A [transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) segít megvédeni Azure SQL Database, az Azure SQL felügyelt példányát és a szinapszis sqlot az Azure szinapszis Analytics szolgáltatásban a rosszindulatú offline tevékenységek fenyegetésével szemben, ha titkosítja az inaktív adatok mennyiségét. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE engedélyezve van minden újonnan telepített Azure SQL-adatbázishoz, és manuálisan kell engedélyezni a Azure SQL Database, Azure SQL felügyelt példány vagy Azure szinapszis régebbi adatbázisaihoz.

A TDE az adatok valós idejű I/O-titkosítását és visszafejtését hajtja végre az oldal szintjén. Az egyes lapok visszafejtése a memóriába történő beolvasáskor történik, a titkosítás pedig a lemezre írás előtt. A TDE titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs (ADATTITKOSÍTÁSI kulcsot) nevű szimmetrikus kulcs használatával. Az adatbázis indításakor a rendszer visszafejti a titkosított ADATTITKOSÍTÁSI kulcsot, majd felhasználja az adatbázisfájlok visszafejtésére és újratitkosítására a SQL Server adatbázismotor folyamatában. A ADATTITKOSÍTÁSI kulcsot a TDE Protector védi. A TDE-védő vagy egy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által felügyelt transzparens adattitkosítás) vagy [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) tárolt aszimmetrikus kulcs (ügyfél által felügyelt transzparens adattitkosítás). 

A Azure SQL Database és az Azure szinapszis esetében a TDE Protector a logikai SQL Server szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az Azure SQL felügyelt példányainál (BYOK funkció előzetes verzióban) a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes titkosított adatbázis örökli. A *kiszolgáló* kifejezés a jelen dokumentumon belül a kiszolgáló és a példányra is vonatkozik, kivéve, ha másként van megadva.

> [!IMPORTANT]
> Alapértelmezés szerint minden újonnan létrehozott Azure SQL-adatbázis titkosítva van a szolgáltatás által felügyelt transzparens adattitkosítás használatával. A 2017-as és a Restore, Geo-replikáció és adatbázis-másolat használatával létrehozott meglévő SQL Database-adatbázisok nincsenek titkosítva alapértelmezés szerint. A 2019 februárjában létrehozott meglévő felügyelt példány-adatbázisok alapértelmezés szerint nincsenek titkosítva. A visszaállítással létrehozott felügyelt példány-adatbázisok a forrástól öröklik a titkosítási állapotot.

> [!NOTE]
> A TDE nem használható a logikai **Master** adatbázis SQL Database-ben történő titkosítására.  A **főadatbázis olyan** objektumokat tartalmaz, amelyek szükségesek a TDE műveletek végrehajtásához a felhasználói adatbázisokon.


## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által kezelt transzparens adattitkosítás

Az Azure-ban a TDE alapértelmezett beállítása az, hogy a ADATTITKOSÍTÁSI kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgáló esetében egyedi, és a használt titkosítási algoritmus az AES 256. Ha egy adatbázis földrajzi replikálási kapcsolatban van, az elsődleges és a Geo-másodlagos adatbázist is az elsődleges adatbázis szülő kiszolgáló kulcsa védi. Ha két adatbázis csatlakozik ugyanahhoz a kiszolgálóhoz, akkor ugyanazt a beépített tanúsítványt is megosztják.  A Microsoft automatikusan elforgatja ezeket a tanúsítványokat a belső biztonsági házirendnek megfelelően, és a legfelső szintű kulcsot egy Microsoft belső titkos tárolója védi.  Az ügyfelek ellenőrizhetik a belső biztonsági házirendekkel való SQL Database megfelelőségét a [Microsoft adatvédelmi központban](https://servicetrust.microsoft.com/)elérhető független külső naplózási jelentésekben.

A Microsoft emellett zökkenőmentesen helyezi át és felügyeli a kulcsokat a földrajzi replikálás és a visszaállítások igény szerint.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Az ügyfél által felügyelt transzparens adattitkosítás – Bring Your Own Key

Az ügyfél által felügyelt TDE más néven Bring Your Own Key (BYOK) támogatás a TDE-hez. Ebben az esetben a ADATTITKOSÍTÁSI kulcsot titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és a felügyelt Azure Key Vault (az Azure felhőalapú külső kulcs-felügyeleti rendszere) tárol, és soha nem hagyja el a kulcstartót. A TDE-védőt [a Key Vault generálhatja, vagy a Key vaultba helyezheti át](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) a helyszíni hardveres biztonsági modul (HSM) eszközéről. SQL Database engedélyeket kell adni az ügyfél által birtokolt kulcstartó számára a ADATTITKOSÍTÁSI kulcsot visszafejtéséhez és titkosításához. Ha a rendszer visszavonja a logikai SQL Server-kiszolgáló és a kulcstartó engedélyeit, az adatbázis nem érhető el, és az összes adatátvitel titkosítva lesz.

A Azure Key Vault-integrációval rendelkező TDE segítségével a felhasználók vezérelhetik a kulcsfontosságú felügyeleti feladatokat, beleértve a kulcsok elforgatását, a Key Vault engedélyeit és a biztonsági mentéseket, valamint lehetővé teszik a naplózást/jelentéskészítést az összes TDE-védelemmel Azure Key Vault funkcióval. Key Vault biztosít a központi kulcskezelő szolgáltatáshoz, a kihasználja a szigorúan figyelt HSM, és lehetővé teszi a feladatok elkülönítését a kulcsok és az adatok felügyelete között a biztonsági szabályzatoknak való megfelelés elősegítése érdekében.
Ha többet szeretne megtudni a Azure SQL Database és az Azure szinapszis BYOK, tekintse meg az [átlátható adattitkosítás Azure Key Vault integrációs](transparent-data-encryption-byok-azure-sql.md)szolgáltatással című témakört.

A TDE Azure Key Vault-integrációval való használatának megkezdéséhez tekintse meg a következő témakört: útmutató az [transzparens adattitkosítás bekapcsolásához a saját kulcsát használva Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Nem kell visszafejtenie az adatbázisokat az Azure-ban végzett műveletekhez. A forrás-vagy az elsődleges adatbázis TDE-beállításai transzparens módon öröklik a célt. A benne foglalt műveletek a következők:

- Georedundáns helyreállítás
- Önkiszolgáló időpontra történő visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázis-másolat létrehozása
- Biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázisok manuális MÁSOLÁSi biztonsági mentése nem engedélyezett az Azure SQL felügyelt példányain, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az időponthoz hasonló visszaállítási funkció használatával áthelyezheti az ilyen típusú adatbázist egy másik felügyelt példányra.

TDE-védelemmel ellátott adatbázis exportálásakor az adatbázis exportált tartalma nincs titkosítva. Az exportált tartalmat titkosítatlan BACPAC-fájlok tárolják. Ügyeljen arra, hogy a megfelelő módon megvédje a BACPAC-fájlokat, és engedélyezze a TDE az új adatbázis importálása után.

Ha például a BACPAC-fájlt egy helyszíni SQL Server-példányból exportálja, a rendszer nem titkosítja automatikusan az új adatbázis importált tartalmát. Hasonlóképpen, ha a BACPAC-fájlt egy helyszíni SQL Server-példányba exportálja, az új adatbázis szintén nem lesz automatikusan titkosítva.

Az egyetlen kivétel az, amikor egy SQL-adatbázisból exportál, és onnan exportálja. A TDE engedélyezve van az új adatbázisban, de maga a BACPAC-fájl még nem titkosított.


## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése
# <a name="portal"></a>[Portál](#tab/azure-portal)
A Azure Portal TDE kezelése.

A TDE a Azure Portalon keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerrel kell csatlakoznia.

Az adatbázis szintjén be-és kikapcsolhatja a TDE. Ha engedélyezni szeretné a TDE egy adatbázison, lépjen a [Azure Portal](https://portal.azure.com) , és jelentkezzen be az Azure-beli rendszergazdai vagy közreműködői fiókjával. Keresse meg a felhasználói adatbázis TDE beállításait. Alapértelmezés szerint a rendszer a szolgáltatás által kezelt transzparens adattitkosítást használja. A rendszer automatikusan létrehoz egy TDE-tanúsítványt az adatbázist tartalmazó kiszolgálóhoz. Az Azure SQL felügyelt példánya a T-SQL használatával kapcsolja be és ki a TDE egy adatbázison.

![Szolgáltatás által kezelt transzparens adattitkosítás](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Beállíthatja a TDE főkulcsát, azaz a TDE-védőt a kiszolgálói szinten. Ha a TDE-t a BYOK-támogatással szeretné használni, és az adatbázisait Key Vault-kulccsal szeretné védelemmel ellátni, nyissa meg a TDE beállításait a kiszolgálón.

![Transzparens adattitkosítás Bring Your Own Key-támogatással](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
TDE kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A TDE PowerShellen keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security managerként kell csatlakoznia.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>A Azure SQL Database és az Azure szinapszis-parancsmagjai

A következő parancsmagokat használja a Azure SQL Database és az Azure Szinapszishoz:

| Parancsmag | Leírás |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Egy adatbázis transzparens adattitkosításának engedélyezése vagy letiltása|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Egy adatbázis transzparens adattitkosítási állapotának lekérése |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Egy adatbázis titkosítási folyamatának ellenőrzése |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Key Vault kulcs feladása egy SQL Server példányhoz |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Lekéri egy Azure SQL Database-kiszolgáló Key Vault kulcsait  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Beállítja egy SQL Server példány transzparens adattitkosítási védelmezőjét |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Az átlátszó adattitkosítási védő beolvasása |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Key Vault kulcs eltávolítása egy SQL Server példányból |
|  | |

> [!IMPORTANT]
> Az Azure SQL felügyelt példányai esetében használja a T-SQL [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) parancsot a TDE be-és kikapcsolásához az adatbázis szintjén, valamint a [minta PowerShell-parancsfájlt](transparent-data-encryption-byok-azure-sql-configure.md) a TDE kezeléséhez a példány szintjén.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
TDE kezelése a Transact-SQL használatával.

Kapcsolódjon az adatbázishoz egy olyan bejelentkezéssel, amely a Master adatbázisban a **DBManager** szerepkör rendszergazdája vagy tagja.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS be-és kikapcsolása titkosítja vagy visszafejti az adatbázist |
| [sys. dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Az adatbázis és a hozzá tartozó adatbázis titkosítási kulcsainak titkosítási állapotával kapcsolatos információkat ad vissza. |
| [sys. dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Az egyes Azure szinapszis-csomópontok és a hozzájuk társított adatbázis-titkosítási kulcsok titkosítási állapotával kapcsolatos információkat adja vissza. |
|  | |

A TDE-védőt nem lehet a kulcsra váltani Key Vault a Transact-SQL használatával. Használja a PowerShellt vagy a Azure Portal.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
A TDE kezelése a REST API használatával.

A TDE a REST APIon keresztüli konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security Managerrel kell csatlakoznia.
A következő parancsokat használja a Azure SQL Database és az Azure szinapszis-hoz:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Azure Active Directory identitást ad hozzá egy SQL Server-példányhoz (amelyet a rendszer a Key Vaulthoz való hozzáférés biztosítására használ)|
|[Kiszolgáló kulcsának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Key Vault kulcs feladása egy SQL Server példányhoz|
|[Kiszolgáló kulcsának törlése](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Key Vault kulcs eltávolítása egy SQL Server példányból|
|[Kiszolgálói kulcsok beolvasása](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Egy adott Key Vault kulcs beolvasása egy SQL Server példányból|
|[Kiszolgálói kulcsok listázása kiszolgáló alapján](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Lekéri egy SQL Server példány Key Vault kulcsait |
|[Titkosítási védő létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Egy SQL Server példány TDE-oltalmazójának beállítása|
|[Titkosítási védő beolvasása](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Egy SQL Server példány TDE-oltalmazójának beolvasása|
|[Titkosítási védők listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Lekéri egy SQL Server példány TDE-védelmi rendszerét |
|[transzparens adattitkosítás konfiguráció létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Engedélyezheti vagy letilthatja a TDE az adatbázishoz|
|[transzparens adattitkosítás konfiguráció beolvasása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Egy adatbázis TDE-konfigurációjának beolvasása|
|[A transzparens adattitkosítás konfigurációs eredményeinek listázása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Egy adatbázis titkosítási eredményének beolvasása|

## <a name="see-also"></a>Lásd még:
- Az Azure-beli virtuális gépeken futó SQL Server a Key Vault aszimmetrikus kulcsát is használhatja. A konfigurációs lépések eltérnek a SQL Database és az SQL felügyelt példányának aszimmetrikus kulcsának használatával. További információ: [bővíthető kulcskezelő Azure Key Vault (SQL Server) használatával](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- A TDE általános ismertetését lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Ha többet szeretne megtudni a Azure SQL Database, az Azure SQL felügyelt példányának és az Azure szinapszisnak a BYOK-támogatásával kapcsolatos TDE, tekintse meg [a bring your own Key-támogatással rendelkező transzparens adattitkosítás](transparent-data-encryption-byok-azure-sql.md)
- A TDE Bring Your Own Key-támogatással való használatának megkezdéséhez tekintse meg a útmutató című témakört, és [kapcsolja be a transzparens adattitkosítást a saját kulcsának használatával Key Vaultból](transparent-data-encryption-byok-azure-sql-configure.md).
- További információ a Key Vaultről: [biztonságos hozzáférés a kulcstartóhoz](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
