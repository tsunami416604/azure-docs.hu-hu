---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179069"
---
## <a name="next-steps"></a>További lépések

Az Azure Key Vault-integráció engedélyezése után engedélyezheti az SQL Server titkosítást az SQL virtuális gépen. Először létre kell hoznia egy aszimmetrikus kulcsot a key vaultban, és egy szimmetrikus kulcsot az SQL Server-en belül a virtuális gépen. Ezután t-SQL-utasítások végrehajtására is képes lesz az adatbázisok és biztonsági mentések titkosításának engedélyezéséhez.

A titkosításnak számos formája van, amelyeket kihasználhat:

* [Transzparens adattitkosítás (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Titkosított biztonsági mentések](https://msdn.microsoft.com/library/dn449489.aspx)
* [Oszlopszintű titkosítás (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

A következő Transact-SQL parancsfájlok példákat szolgáltatnak az egyes területekre.

### <a name="prerequisites-for-examples"></a>A példák előfeltételei

Minden példa a két előfeltételen alapul: egy aszimmetrikus kulcs a kulcstartóból, **amelyet CONTOSO_KEY** és az AKV-integráció szolgáltatás által létrehozott hitelesítő adat **Azure_EKM_TDE_cred.** A következő Transact-SQL parancsok beállítják ezeket az előfeltételeket a példák futtatásához.

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

1. Hozzon létre egy SQL Server bejelentkezési rendszert, amelyet a TDE adatbázis-kezelő motorja használ, majd adja hozzá a hitelesítő adatokat.

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

1. Hozza létre a TDE-hez használt adatbázis-titkosítási kulcsot.

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

### <a name="encrypted-backups"></a>Titkosított biztonsági mentések

1. Hozzon létre egy SQL Server bejelentkezési rendszert, amelyet az adatbázis-kezelő motor a biztonsági mentések titkosításához használ, és adja hozzá a hitelesítő adatokat.

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

1. Biztonsági másolatot készítsen a titkosítást meghatározó adatbázisról a key vaultban tárolt aszimmetrikus kulccsal.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Oszlopszintű titkosítás (CLE)

Ez a parancsfájl létrehoz egy szimmetrikus kulcsot, amelyet a key vault aszimmetrikus kulcsa véd, majd a szimmetrikus kulcsot használja az adatbázisban lévő adatok titkosításához.

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

A titkosítási szolgáltatások használatáról az [EKM használata az SQL Server titkosítási szolgáltatásokkal című témakörben talál](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM)további információt.

Vegye figyelembe, hogy a cikkben leírt lépések feltételezik, hogy már rendelkezik az SQL Server egy Azure virtuális gépen. Ha nem, olvassa [el az SQL Server virtuális gép kiépítése az Azure-ban című témakört.](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) Az SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további útmutatásért tekintse meg [az SQL Server az Azure virtuális gépeken című témakört.](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
