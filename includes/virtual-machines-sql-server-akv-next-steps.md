---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227362"
---
## <a name="next-steps"></a>További lépések

Miután engedélyezte az Azure Key Vault-integráció, az SQL virtuális gép az SQL Server titkosítási engedélyezheti. Először is szüksége lesz a virtuális Gépen belül a key vault aszimmetrikus kulccsal és a egy szimmetrikus kulcsot, az SQL-kiszolgálón létre. Ezt követően lesz a T-SQL-utasítások használatával engedélyezze a titkosítást az adatbázisok és a biztonsági mentések végrehajtásához.

Nincsenek igénybe veheti a titkosítási több űrlapok:

* [Transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Titkosított biztonsági mentés](https://msdn.microsoft.com/library/dn449489.aspx)
* [Oszlop a blokkszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

A következő Transact-SQL-parancsfájlok a következő területeken példákat biztosítanak.

### <a name="prerequisites-for-examples"></a>Példák előfeltételei

Valamennyi példa alapján a két Előfeltételek: a key vaultból aszimmetrikus kulccsal nevű **CONTOSO_KEY** és a egy hitelesítő adatot az AKV-integráció a szolgáltatás által létrehozott **Azure_EKM_TDE_cred**. A következő Transact-SQL-parancsok futtatásához a példák az Előfeltételek beállítása.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transzparens adattitkosítás (TDE)

1. TDE az adatbázismotor által használandó SQL Server-bejelentkezés létrehozásával, majd a hitelesítő adatok hozzá.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. A TDE használandó adatbázis-titkosítási kulcs létrehozása.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Titkosított biztonsági mentés

1. Biztonsági mentések titkosításához az adatbázismotor által használandó SQL Server-bejelentkezés létrehozásával, és hozzá tud adni a hitelesítő adatokat.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. A megadása az adatbázis-titkosítás, az aszimmetrikus kulccsal a key vaultban tárolt biztonsági másolat.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Oszlop a blokkszintű titkosítás (CLE)

Ez a szkript létrehoz egy szimmetrikus kulcsot a key vaultban az aszimmetrikus kulcs által védett, és a szimmetrikus kulcs használatával titkosítja az adatokat az adatbázisban.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>További források

Ezek titkosítási szolgáltatások használatáról további információkért lásd: [EKM használatával az SQL Server titkosítási funkciók](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Vegye figyelembe, hogy ez a cikk lépései azt feltételezik, hogy már rendelkezik egy Azure virtuális gépen futó SQL Server. Ha nincs engedélyezve, [az Azure-beli SQL Server virtuális gép kiépítése](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Az Azure virtuális gépeken futó SQL Server más útmutatóért lásd: [SQL Server on Azure Virtual Machines – áttekintés](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
