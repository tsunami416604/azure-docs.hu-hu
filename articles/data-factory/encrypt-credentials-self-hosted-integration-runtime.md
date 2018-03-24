---
title: Az Azure Data Factory hitelesítő adatok titkosításához |} Microsoft Docs
description: Ismerje meg, titkosítás és a helyszíni adattárolókhoz hitelesítő adatok tárolását önálló üzemeltetett integrációs futásidejű virtuális gép.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 570ebe99066ecdaf2531751abb2e3fb821f29f64
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>A helyszíni adattárolókhoz az Azure Data Factory a hitelesítő adatok titkosításához
Titkosításához, és a helyszíni adattárolókhoz (bizalmas információkat tartalmazó összekapcsolt szolgáltatások) a virtuális gép önálló üzemeltetett integrációs futásidejű tartozó hitelesítő adatok tárolására. 

Adja meg a JSON-definíciós fájl hitelesítő adatokkal a <br/>[**Új AzureRmDataFactoryV2LinkedServiceEncryptedCredential** ](https://docs.microsoft.com/powershell/module/azurerm.datafactoryv2/New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential?view=azurermps-4.4.0) parancsmag egy kimeneti JSON csomagdefiníciós fájlt, a titkosított hitelesítő adatok létrehozásához. Ezután a frissített JSON-definícióból segítségével a társított szolgáltatások létrehozásához.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának dokumentációja](v1/data-factory-introduction.md).

## <a name="author-sql-server-linked-service"></a>Szerző kapcsolódó SQL Server szolgáltatás
Hozzon létre egy JSON fájlt **SqlServerLinkedService.json** bármely mappában, amelynek a következőket:  

Cserélje le `<servername>`, `<databasename>`, `<username>`, és `<password>` a fájl mentése előtt az SQL Server értékekkel. És csere `<integration runtime name>` a integrációs futásidejű nevével. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
            }
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Hitelesítő adatok titkosítása
A JSON-adattartalmat egy helyszíni önálló üzemeltetett integrációs Runtime a bizalmas adatok titkosítására, futtassa **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**, és a JSON-adattartalmat továbbítására. Ez a parancsmag biztosítja a azonosító adatok titkosítása a DPAPI-t használ, és a csomóponton önálló üzemeltetett integrációs futásidejű helyileg tárolja. A kimeneti forgalma átirányíthatók egy másik JSON-fájl (ebben az esetben "encryptedLinkedService.json"), amely tartalmazza a titkosított hitelesítő adatokat.

```powershell
New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>A JSON titkosított hitelesítő adatok használata
Mostantól az előző parancs, a titkosított hitelesítő adatokat tartalmazó a kimeneti JSON-fájl használatával állítsa be a **SqlServerLinkedService**.

```powershell
Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>További lépések
Biztonsági szempontok az adatmozgás kapcsolatos információkért lásd: [adatok mozgása biztonsági szempontok](data-movement-security-considerations.md).

