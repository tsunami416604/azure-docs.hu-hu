---
title: 'Transzparens adattitkosítás a Azure SQL Database és az adattárházban '
description: A SQL Database és az adattárház transzparens adattitkosításának áttekintése. A dokumentum a szolgáltatás által felügyelt transzparens adattitkosítást és Bring Your Own Keyt is magában foglaló előnyökkel és beállításokkal foglalkozik.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: ff712f956278a2a54584c0b0346f8f1147add189
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686791"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transzparens adattitkosítás a SQL Database és az adattárházban

A transzparens adattitkosítás (TDE) segít megvédeni Azure SQL Database, az Azure SQL felügyelt példányát és az Azure-adattárházat a rosszindulatú offline tevékenységek fenyegetésével szemben az inaktív adatok titkosításával. Az adatbázis, a társított biztonsági másolatok és a tranzakciós naplófájlok valós idejű titkosítását és visszafejtését hajtja végre, anélkül, hogy az alkalmazás módosítására lenne szükség. Alapértelmezés szerint a TDE engedélyezve van minden újonnan telepített Azure SQL-adatbázishoz. A TDE nem használható a logikai **Master** adatbázis SQL Database-ben történő titkosítására.  A **főadatbázis olyan** objektumokat tartalmaz, amelyek szükségesek a TDE műveletek végrehajtásához a felhasználói adatbázisokon.

A TDE manuálisan kell engedélyezni a Azure SQL Database, Azure SQL felügyelt példány vagy Azure SQL Data Warehouse régebbi adatbázisaihoz.
A visszaállítással létrehozott felügyelt példány-adatbázisok a forrás-adatbázisból öröklik a titkosítási állapotot.

Az transzparens adattitkosítás titkosítja egy teljes adatbázis tárterületét az adatbázis-titkosítási kulcs nevű szimmetrikus kulcs használatával. Ezt az adatbázis-titkosítási kulcsot a transzparens adattitkosítási védő védi. A védő vagy egy szolgáltatás által felügyelt tanúsítvány (szolgáltatás által kezelt transzparens adattitkosítás) vagy Azure Key Vault (Bring Your Own Key) által tárolt aszimmetrikus kulcs. Az átlátszó adattitkosítási védőt a Azure SQL Database és az adatraktár kiszolgálói szintjén, valamint az Azure SQL felügyelt példányának példányi szintjén állíthatja be. A *kiszolgáló* kifejezés a jelen dokumentumon belül a kiszolgáló és a példányra is vonatkozik, kivéve, ha másként van megadva.

Az adatbázis indításakor a rendszer visszafejti a titkosított adatbázis titkosítási kulcsát, majd felhasználja az adatbázisfájlok visszafejtésére és újratitkosítására a SQL Server adatbázismotor folyamatában. Az transzparens adattitkosítás valós idejű I/O-titkosítást és az adatok visszafejtését végzi az oldal szintjén. A rendszer visszafejti az egyes lapokat, amikor beolvassa a memóriába, majd titkosítja a lemezre írás előtt. Az transzparens adattitkosítás általános ismertetését lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

Az Azure-beli virtuális gépeken futó SQL Server a Key Vault aszimmetrikus kulcsát is használhatja. A konfigurációs lépések eltérnek a SQL Database és az SQL felügyelt példányának aszimmetrikus kulcsának használatával. További információ: [bővíthető kulcskezelő Azure Key Vault (SQL Server) használatával](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Szolgáltatás által kezelt transzparens adattitkosítás

Az Azure-ban az transzparens adattitkosítás alapértelmezett beállítása az, hogy az adatbázis-titkosítási kulcsot egy beépített kiszolgálótanúsítvány védi. A beépített kiszolgálói tanúsítvány minden kiszolgáló esetében egyedi, és a használt titkosítási algoritmus az AES 256. Ha egy adatbázis földrajzi replikálási kapcsolatban van, az elsődleges és a Geo-másodlagos adatbázist is az elsődleges adatbázis szülő kiszolgáló kulcsa védi. Ha két adatbázis csatlakozik ugyanahhoz a kiszolgálóhoz, akkor ugyanazt a beépített tanúsítványt is megosztják.  A Microsoft automatikusan elforgatja ezeket a tanúsítványokat a belső biztonsági házirendnek megfelelően, és a legfelső szintű kulcsot egy Microsoft belső titkos tárolója védi.  Az ügyfelek ellenőrizhetik a belső biztonsági házirendekkel való SQL Database megfelelőségét a [Microsoft adatvédelmi központban](https://servicetrust.microsoft.com/)elérhető független külső naplózási jelentésekben.

A Microsoft emellett zökkenőmentesen helyezi át és felügyeli a kulcsokat a földrajzi replikálás és a visszaállítások igény szerint.

> [!IMPORTANT]
> Alapértelmezés szerint minden újonnan létrehozott SQL-adatbázis és felügyelt példány adatbázisa titkosítva van a szolgáltatás által felügyelt transzparens adattitkosítás használatával. A 2017-as és a Restore, Geo-replikáció és adatbázis-másolat használatával létrehozott meglévő SQL Database-adatbázisok nincsenek titkosítva alapértelmezés szerint. A 2019 februárjában létrehozott meglévő felügyelt példány-adatbázisok alapértelmezés szerint nincsenek titkosítva. A visszaállítással létrehozott felügyelt példány-adatbázisok a forrástól öröklik a titkosítási állapotot.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Az ügyfél által felügyelt transzparens adattitkosítás – Bring Your Own Key

A [Azure Key Vault ügyfél által felügyelt kulcsokkal történő TDE](transparent-data-encryption-byok-azure-sql.md) lehetővé teszi az adatbázis-titkosítási kulcs (adattitkosítási kulcsot) titkosítását a TDE Protector nevű ügyfél által felügyelt aszimmetrikus kulccsal.  Ezt általában a transzparens adattitkosítás Bring Your Own Key (BYOK) támogatása is említi. A BYOK-forgatókönyvben a TDE-védőt az Azure felhőalapú külső kulcs-felügyeleti rendszere tárolja az ügyfél által birtokolt és felügyelt [Azure Key Vaultban](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault). A TDE-védőt [a Key Vault generálhatja, vagy áthelyezheti a Key vaultba](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) egy helyszíni HSM-eszközről. Az adatbázis rendszerindító oldalán tárolt TDE-ADATTITKOSÍTÁSI kulcsot a TDE Protector titkosítja és visszafejti, amelyet Azure Key Vault tárol, és soha nem hagyja a kulcstartót.  SQL Database engedélyeket kell adni az ügyfél által birtokolt kulcstartó számára a ADATTITKOSÍTÁSI kulcsot visszafejtéséhez és titkosításához. Ha a rendszer visszavonja a logikai SQL Server-kiszolgáló és a kulcstartó engedélyeit, az adatbázisok nem lesznek elérhetők, és az összes adatátvitel titkosítva lesz. Azure SQL Database esetében a TDE-védő a logikai SQL Server szintjén van beállítva, és az adott kiszolgálóhoz társított összes adatbázis örökli. Az [Azure SQL felügyelt példányai](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)esetében a TDE-védő a példány szintjén van beállítva, és az adott példányon található összes *titkosított* adatbázis örökli. A *kiszolgáló* kifejezés a jelen dokumentumon belül a kiszolgáló és a példányra is vonatkozik, kivéve, ha másként van megadva.

A Azure Key Vault-integrációval rendelkező TDE segítségével a felhasználók vezérelhetik a kulcsfontosságú felügyeleti feladatokat, beleértve a kulcsok elforgatását, a Key Vault engedélyeit és a biztonsági mentéseket, valamint lehetővé teszik a naplózást/jelentéskészítést az összes TDE-védelemmel Azure Key Vault funkcióval. Key Vault biztosítja a központi kulcskezelő szolgáltatásokat, kihasználja a jól felügyelt hardveres biztonsági modulok (HSM-EK) használatát, és lehetővé teszi a feladatok elkülönítését a kulcsok és az adatok felügyelete között a biztonsági szabályzatoknak való megfelelés elősegítése érdekében.
Ha többet szeretne megtudni a Azure SQL Database, az SQL felügyelt példányához és az adatraktárhoz Azure Key Vault integrációval (Bring Your Own Key támogatással) kapcsolatos transzparens adattitkosításról, tekintse meg az [átlátható adattitkosítás Azure Key Vault-integrációval](transparent-data-encryption-byok-azure-sql.md)című témakört.

Az Azure Key Vault Integration (Bring Your Own Key-támogatás) használatával történő transzparens adattitkosítás használatának megkezdéséhez tekintse meg a következő témakört: útmutató az [transzparens adattitkosítás bekapcsolásához a saját kulcsával Key Vault a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Transzparens adattitkosítással védett adatbázis áthelyezése

Nem kell visszafejtenie az adatbázisokat az Azure-ban végzett műveletekhez. A forrás-adatbázis vagy az elsődleges adatbázis transzparens adattitkosítási beállításai transzparens módon öröklik a célt. A benne foglalt műveletek a következők:

- Georedundáns helyreállítás
- Önkiszolgáló időpontra történő visszaállítás
- Törölt adatbázis visszaállítása
- Aktív georeplikáció
- Adatbázis-másolat létrehozása
- Biztonságimásolat-fájl visszaállítása az Azure SQL felügyelt példányára

> [!IMPORTANT]
> A szolgáltatás által felügyelt TDE által titkosított adatbázisok manuális MÁSOLÁSi biztonsági mentése nem engedélyezett az Azure SQL felügyelt példányain, mivel a titkosításhoz használt tanúsítvány nem érhető el. Az időponthoz hasonló visszaállítási funkció használatával áthelyezheti az ilyen típusú adatbázist egy másik felügyelt példányra.

Ha transzparens adattitkosítással védett adatbázist exportál, az adatbázis exportált tartalma nincs titkosítva. Ezt az exportált tartalmat a rendszer nem titkosított BACPAC-fájlokban tárolja. Ügyeljen arra, hogy az új adatbázis importálása után a megfelelő módon megvédje a BACPAC-fájlokat, és engedélyezze az transzparens adattitkosítást.

Ha például a BACPAC-fájlt egy helyszíni SQL Server-példányból exportálja, a rendszer nem titkosítja automatikusan az új adatbázis importált tartalmát. Hasonlóképpen, ha a BACPAC-fájlt egy helyszíni SQL Server-példányba exportálja, az új adatbázis szintén nem lesz automatikusan titkosítva.

Az egyetlen kivétel az, amikor egy SQL-adatbázisból exportál, és onnan exportálja. Az transzparens adattitkosítás engedélyezve van az új adatbázisban, de maga a BACPAC-fájl még nem titkosított.


## <a name="manage-transparent-data-encryption"></a>Transzparens adattitkosítás kezelése
# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)
A Azure Portal transzparens adattitkosítás kezelése.

A Azure Portal keresztüli transzparens adattitkosítás konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security managerként kell csatlakoznia.

Az transzparens adattitkosítás be-és kikapcsolása az adatbázis szintjén. Egy adatbázis transzparens adattitkosításának engedélyezéséhez lépjen a [Azure Portalra](https://portal.azure.com) , és jelentkezzen be az Azure-beli rendszergazdai vagy közreműködői fiókjával. Keresse meg a felhasználói adatbázis transzparens adattitkosítási beállításait. Alapértelmezés szerint a rendszer a szolgáltatás által kezelt transzparens adattitkosítást használja. Az adatbázist tartalmazó kiszolgáló számára automatikusan létrejön egy transzparens adattitkosítási tanúsítvány. Az Azure SQL felügyelt példánya esetében a T-SQL használatával be-és kikapcsolhatja a transzparens adattitkosítást egy adatbázison.

![Szolgáltatás által kezelt transzparens adattitkosítás](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Az átlátszó adattitkosítási főkulcsot (más néven transzparens adattitkosítási védőt) a kiszolgálói szinten állíthatja be. Ha transzparens adattitkosítást szeretne használni Bring Your Own Key támogatással, és az adatbázisait Key Vault-kulccsal védi, nyissa meg a kiszolgáló transzparens adattitkosítási beállításait.

![Transzparens adattitkosítás Bring Your Own Key-támogatással](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Az transzparens adattitkosítás kezelése a PowerShell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A PowerShellen keresztüli transzparens adattitkosítás konfigurálásához Azure tulajdonosként, közreműködőként vagy SQL Security Managerrel kell csatlakoznia.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>A Azure SQL Database és az adatraktár parancsmagjai

Használja a következő parancsmagokat a Azure SQL Database és az adatraktárhoz:

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
> Az Azure SQL felügyelt példányai esetében használja a T-SQL [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) parancsot az átlátható adattitkosítás be-és kikapcsolásához az adatbázis szintjén, valamint a [minta PowerShell-parancsfájlt](transparent-data-encryption-byok-azure-sql-configure.md) egy példány szintjén lévő transzparens adattitkosítás kezelésére.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Az transzparens adattitkosítás kezelése a Transact-SQL használatával.

Kapcsolódjon az adatbázishoz egy olyan bejelentkezéssel, amely a Master adatbázisban a **DBManager** szerepkör rendszergazdája vagy tagja.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | A TITKOSÍTÁS be-és kikapcsolása titkosítja vagy visszafejti az adatbázist |
| [sys. DM _database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Az adatbázis és a hozzá tartozó adatbázis titkosítási kulcsainak titkosítási állapotával kapcsolatos információkat ad vissza. |
| [sys. DM _pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Az egyes adatraktár-csomópontok titkosítási állapotára és a hozzá tartozó adatbázis-titkosítási kulcsokra vonatkozó információkat adja vissza. |
|  | |

A Transact-SQL használatával nem lehet átváltani az átlátszó adattitkosítási védőt Key Vault kulcsra. Használja a PowerShellt vagy a Azure Portal.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
Az transzparens adattitkosítás kezelése a REST API használatával.

A REST API keresztüli transzparens adattitkosítás konfigurálásához Azure-tulajdonosként, közreműködőként vagy SQL Security managerként kell csatlakoznia.
Használja az alábbi parancsokat a Azure SQL Database és az adatraktárhoz:

| Parancs | Leírás |
| --- | --- |
|[Kiszolgáló létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Azure Active Directory identitást ad hozzá egy SQL Server-példányhoz (amelyet a rendszer a Key Vaulthoz való hozzáférés biztosítására használ)|
|[Kiszolgáló kulcsának létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Key Vault kulcs feladása egy SQL Server példányhoz|
|[Kiszolgáló kulcsának törlése](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Key Vault kulcs eltávolítása egy SQL Server példányból|
|[Kiszolgálói kulcsok beolvasása](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Egy adott Key Vault kulcs beolvasása egy SQL Server példányból|
|[Kiszolgálói kulcsok listázása kiszolgáló alapján](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Lekéri egy SQL Server példány Key Vault kulcsait |
|[Titkosítási védő létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Beállítja egy SQL Server példány transzparens adattitkosítási védelmezőjét|
|[Titkosítási védő beolvasása](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Beolvassa a SQL Server példány transzparens adattitkosítási védelmezőjét|
|[Titkosítási védők listázása kiszolgáló szerint](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Beolvassa a SQL Server példány transzparens adattitkosítási védelmi szolgáltatásait |
|[transzparens adattitkosítás konfiguráció létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Egy adatbázis transzparens adattitkosításának engedélyezése vagy letiltása|
|[transzparens adattitkosítás konfiguráció beolvasása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Az adatbázis transzparens adattitkosítási konfigurációjának lekérése|
|[A transzparens adattitkosítás konfigurációs eredményeinek listázása](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Egy adatbázis titkosítási eredményének beolvasása|

## <a name="next-steps"></a>További lépések

- Az transzparens adattitkosítás általános ismertetését lásd: [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Ha többet szeretne megtudni a Azure SQL Database, az Azure SQL felügyelt példányának és az adattárház Bring Your Own Key támogatásával kapcsolatos transzparens adattitkosításról, tekintse meg a következőt: [transzparens adattitkosítás bring your own Key-támogatással](transparent-data-encryption-byok-azure-sql.md).
- Az Bring Your Own Key-támogatással rendelkező transzparens adattitkosítás használatának megkezdéséhez tekintse meg a következő témakört: útmutató az [átlátszó adattitkosítás bekapcsolásához a saját kulcs használatával Key Vault a PowerShell használatával](transparent-data-encryption-byok-azure-sql-configure.md).
- További információ a Key Vaultről: [Key Vault dokumentációs oldal](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
