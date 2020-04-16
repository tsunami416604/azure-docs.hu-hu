---
title: Hitelesítő adatok titkosítása az Azure Data Factoryban
description: Megtudhatja, hogyan titkosíthatja és tárolhatja a helyszíni adattárak hitelesítő adatait egy saját üzemeltetésű integrációs futásidejű számítógépen.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8ce26360aca8d8408135cbe89aabff4f923013b4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416367"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Hitelesítő adatok titkosítása a helyszíni adattárakhoz az Azure Data Factoryban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A helyszíni adattárak (bizalmas adatokkal rendelkező kapcsolódó szolgáltatások) hitelesítő adatait titkosíthatja és tárolhatja egy saját üzemeltetésű integrációs futásidejű gépen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Átad egy JSON-definíciós fájlt hitelesítő adatokkal a <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) parancsmag egy kimeneti JSON-definíciós fájl létrehozásához a titkosított hitelesítő adatokkal. Ezután a frissített JSON-definíció segítségével hozza létre a csatolt szolgáltatásokat.

## <a name="author-sql-server-linked-service"></a>SQL Server csatolt szolgáltatás létrehozása
Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt bármely olyan mappában, amelynek tartalma a következő:  

Cserélje `<servername>` `<databasename>`le `<username>`a `<password>` , , , és az SQL Server értékeit a fájl mentése előtt. És cserélje `<integration runtime name>` le az integrációs futásidejű nevét. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
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
A bizalmas adatok titkosításához a JSON-tartalom egy helyszíni önkiszolgáló integrációs futásidejű, futtassa **a New-AzDataFactoryV2LinkedServiceEncryptedCredential**parancsot, és adja át a JSON-tartalom. Ez a parancsmag biztosítja, hogy a hitelesítő adatok dpapi használatával titkosítva legyenek, és az önkiszolgáló integrációs futásidejű csomóponton helyileg tárolhatók legyenek. A hitelesítő adatra vonatkozó titkosított hivatkozást tartalmazó kimeneti tartalom átirányítható egy másik JSON-fájlba (ebben az esetben a "encryptedLinkedService.json").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>A JSON használata titkosított hitelesítő adatokkal
Most használja a kimeneti JSON fájlt az előző parancsból, amely a titkosított hitelesítő adatokat tartalmazza az **SqlServerLinkedService**beállításához.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>További lépések
Az adatok mozgatásával kapcsolatos biztonsági szempontokról az [Adatmozgás biztonsági szempontjai című témakörben talál további információt.](data-movement-security-considerations.md)

