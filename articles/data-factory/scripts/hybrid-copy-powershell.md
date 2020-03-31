---
title: Adatok másolása a helyszíni adatokról az Azure-ba a PowerShell használatával
description: Ez a PowerShell-parancsfájl adatokat másol egy helyszíni SQL Server-adatbázisból egy másik Azure Blob Storage-ba.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974694"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>A PowerShell használatával hozzon létre egy adat-előállító folyamatot a helyszíni adatok Azure-ba történő másolásához

Ez a minta PowerShell-parancsfájl létrehoz egy folyamatot az Azure Data Factoryban, amely adatokat másol egy helyszíni SQL Server-adatbázisból egy Azure Blob Storage-ba.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- **SQL Server**kiszolgáló . Ebben a mintában egy helyszíni SQL **source** Server-adatbázist használ forrásadattárként.
- **Az Azure Storage-fiók.** Ebben a mintában az Azure blob storage-t **cél-/fogadóadattárként** használja. ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket a létrehozási lépéseket.
- **Saját üzemeltetésű integrációs futásidő**. Töltse le az MSI-fájlt a [letöltőközpontból,](https://www.microsoft.com/download/details.aspx?id=39717) és futtassa le, hogy saját üzemeltetésű integrációs futásidejűt telepítsen a gépére.  

### <a name="create-sample-database-in-sql-server"></a>Mintaadatbázis létrehozása az SQL Server ben
1. A helyszíni SQL Server-adatbázisban hozzon létre egy **emp** nevű táblát a következő SQL-parancsfájl használatával:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Szúrjon be néhány mintaadatot a táblázatba:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Példaszkript

> [!IMPORTANT]
> Ez a parancsfájl JSON-fájlokat hoz létre, amelyek a c:\ Mappa.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A mintaparancsfájl futtatása után a következő paranccsal eltávolíthatja az erőforráscsoportot és a hozzá tartozó összes erőforrást:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Ha el szeretné távolítani az adat-előállítót az erőforráscsoportból, futtassa a következő parancsot:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Adat-előállító létrehozása |
| [Új-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Titkosítja a csatolt szolgáltatás hitelesítő adatait, és új csatolt szolgáltatásdefiníciót hoz létre a titkosított hitelesítő adatokkal.
| [Set-AzDataFactoryV2Linkedservice](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Csatolt szolgáltatást hoz létre az adat-előállítóban. A csatolt szolgáltatás adattort vagy számítást kapcsol össze egy adat-előállítóval. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Adatkészletet hoz létre az adat-előállítóban. Az adatkészlet egy folyamatban lévő tevékenység bemeneti/kimeneti bemenetét/kimenetét jelöli. |
| [Set-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Létrehoz egy folyamatot az adat-előállítóban. A folyamat egy vagy több olyan tevékenységet tartalmaz, amely egy bizonyos műveletet hajt végre. Ebben a folyamatban egy másolási tevékenység adatokat másol az egyik helyről egy másik helyre egy Azure Blob Storage.In this pipeline, a copy activity copys copys copys data from one location in an Azure Blob Storage. |
| [Invoke-AzDataFactoryV2pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Futást hoz létre a folyamathoz. Más szóval fut a folyamat. |
| [Get-AzDataFactoryV2Activityrun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | A folyamatban lévő tevékenység (tevékenységfuttatás) futtatásának részletei.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Data Factory PowerShell-parancsfájlminták találhatók az [Azure Data Factory PowerShell-mintákban.](../samples-powershell.md)
