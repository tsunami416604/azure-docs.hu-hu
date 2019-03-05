---
title: TDE-tanúsítvány migrálása – Felügyelt Azure SQL Database-példány | Microsoft Docs
description: A tanúsítvány védelme, adatbázis-titkosítási kulcs az adatbázis transzparens adattitkosítás az Azure SQL Database felügyelt példányába történő áttelepítése
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: f27a5b0deb0dd446d4f05b0a6d6e96d67d24d9e9
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315996"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Tanúsítvány TDE védett adatbázis áttelepítése az Azure SQL Database felügyelt példánya

Által védett adatbázis áttelepítését [transzparens adattitkosítás](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) Azure SQL Database felügyelt példányába történő natív visszaállítási lehetőséggel, a megfelelő tanúsítványt a helyszíni vagy IaaS SQL Server kell áttelepíteni Mielőtt az adatbázis-visszaállítás. Ez a cikk a tanúsítványnak a felügyelt Azure SQL Database-példányra történő manuális migrálásának a folyamatát mutatja be lépésről lépésre:

> [!div class="checklist"]
> * A tanúsítvány exportálása egy személyes információcsere (.pfx) fájlba
> * Tanúsítvány kibontása a fájlból base-64 sztringbe
> * Feltöltés PowerShell-parancsmag használatával

Alternatív megoldásként használhat egy teljes körűen felügyelt szolgáltatást a TDE-vel védett adatbázis és a kapcsolódó tanúsítvány zökkenőmentes migrálásához. További információért olvassa el a cikket, amely részletesen ismerteti a [helyszíni adatbázis egy felügyelt példányra való migrálását az Azure Database Migration Service használatával](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Transzparens adattitkosítás az Azure SQL Database felügyelt példánya a szolgáltatás által felügyelt módban működik. A migrált tanúsítvány csak a TDE-vel védett adatbázis visszaállítására használható. A visszaállítást követően a migrált tanúsítvány helyét egy másik, a rendszer által felügyelt tanúsítvány veszi át.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikkben leírt lépések elvégzéséhez a következő előfeltételekre lesz szüksége:

- Telepített [Pvk2Pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) parancssori eszköz egy helyszíni kiszolgálón vagy egy olyan számítógépen, amely hozzáfér a fájlként exportált tanúsítványhoz. A Pvk2Pfx eszköz az [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk) része, amely egy önálló parancssori környezet.
- Telepített [Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell), 5.0-s vagy újabb verzió.
- Azure PowerShell-modul [telepítve van, és a frissített](https://docs.microsoft.com/powershell/azure/install-az-ps).
- [Az.Sql module](https://www.powershellgallery.com/packages/Az.Sql).
  A PowerShell modul telepítéséhez/frissítéséhez a következő parancsokat kell futtatni a PowerShellben:

   ```powershell
   Install-Module -Name Az.Sql
   Update-Module -Name Az.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>A TDE-tanúsítvány exportálása egy személyes információcsere (.pfx) fájlba

A tanúsítvány exportálható közvetlenül az SQL Server-példányról vagy egy tanúsítványtárolóból.

### <a name="export-certificate-from-the-source-sql-server"></a>Tanúsítvány exportálása a forrásként szolgáló SQL Server-példányról

A következő lépéseket követve exportálhat tanúsítványokat az SQL Server Management Studióval, és konvertálhatja azokat pfx formátumba. A lépések során az általános *TDE_Cert* és *full_path* nevet adtuk a tanúsítványnak és a fájlneveknek. Ezeket a gyakorlatban a tényleges nevekre kell cserélni.

1. Az SSMS-ben nyisson meg egy új lekérdezési ablakot, és csatlakozzon a forrásként szolgáló SQL Server-példányhoz.
2. A következő szkripttel megjelenítheti a TDE-vel védett adatbázisok listáját, valamint lekérdezheti a migrálni kívánt adatbázist védő titkosítás tanúsítványának nevét:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![a TDE-tanúsítványok listája](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

3. A következő szkriptet futtatva a tanúsítványt két fájlba (.cer és .pvk) exportálhatja, megőrizve a nyilvános és a titkos kulcs adatait:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![TDE-tanúsítvány biztonsági mentése](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

4. A PowerShell-konzollal másolja az újonnan létrehozott fájlok tanúsítványadatait egy személyes információcsere (.pfx) fájlba a Pvk2Pfx eszköz használatával:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Tanúsítvány exportálása egy tanúsítványtárolóból

Amennyiben a tanúsítványt az SQL Server helyi számítógépének tanúsítványtárolója tartalmazza, a következő módon tudja exportálni onnan:

1. Nyissa meg a PowerShell-konzolt, és futtassa a következő parancsot a Microsoft Management Console Tanúsítványok beépülő moduljának megnyitásához:

   ```powershell
   certlm
   ```

2. A Tanúsítványok beépülő MMC-modulban bontsa ki a Személyes -> Tanúsítványok pontot a tanúsítványok listájának megtekintéséhez.

3. Kattintson a jobb gombbal a tanúsítványra, és válassza az Export… (Exportálás…) parancsot.

4. A varázsló utasításait követve exportálja a tanúsítványt és a titkos kulcsot személyes információcsere formátumba.

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Tanúsítvány feltöltése az Azure SQL Database felügyelt példány az Azure PowerShell-parancsmag segítségével

1. Előkészítő lépések a PowerShellben:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module Az
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzSubscription Guid_Subscription_Id
   ```

2. Ha elvégezte a szükséges előkészítő lépéseket, futtassa a következő parancsokat a base-64 kódolású tanúsítványnak a célként megadott felügyelt példányba való feltöltéséhez:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

A tanúsítvány ezzel elérhetővé válik a megadott felügyelt példányban, és a megfelelő TDE-vel védett adatbázis biztonsági mentése sikeresen visszaállítható.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan telepítheti át a tanúsítvány védelme transzparens adattitkosítás, az adatbázis titkosítási kulcs a helyszíni vagy Azure SQL Database felügyelt példány SQL Server IaaS.

Az adatbázis biztonsági másolatának egy felügyelt Azure SQL Database-példányra történő visszaállításáról itt olvashat: [Adatbázis biztonsági másolatának visszaállítása egy felügyelt Azure SQL Database-példányon](sql-database-managed-instance-get-started-restore.md).
