---
title: Az Azure Data Factoryban hitelesítő adatainak titkosítása |} A Microsoft Docs
description: Megtudhatja, hogyan titkosításához, és a helyszíni adattárakban hitelesítő adatainak tárolása a saját üzemeltetésű integrációs modult egy gépen.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8e705a4430f6ccee847dc7d41ef80456a6dc4ea5
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903794"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Az Azure Data Factory a helyszíni adattárak hitelesítő adatok titkosítása
Titkosításához, és tárolja a hitelesítő adatait a helyszíni adattárakban (bizalmas adatokat a társított szolgáltatások) egy gépen a saját üzemeltetésű integrációs modult. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy JSON-definíciós fájl hitelesítő adatokkal adja át a <br/>[**Új AzDataFactoryV2LinkedServiceEncryptedCredential** ](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) parancsmag segítségével előállít egy kimeneti definícióját tartalmazó JSON-fájl a titkosított hitelesítő adatokat. Ezután használja a frissített JSON-definíciót a társított szolgáltatások létrehozása.

## <a name="author-sql-server-linked-service"></a>Fejlesztés az SQL Server-alapú társított szolgáltatás
Hozzon létre egy JSON-fájlt **SqlServerLinkedService.json** bármely mappában az alábbi tartalommal:  

Cserélje le `<servername>`, `<databasename>`, `<username>`, és `<password>` értékekkel, az SQL Server, a fájl mentése előtt. És cserélje le `<integration runtime name>` az integration runtime nevére. 

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
A bizalmas hasznos JSON-a helyszíni saját üzemeltetésű integrációs modul-adatok titkosításához futtassa **New-AzDataFactoryV2LinkedServiceEncryptedCredential**, és adja át a JSON-adattartalmat. Ez a parancsmag biztosítja, hogy a hitelesítő adatok titkosítottak DPAPI-t használ, és a saját üzemeltetésű integrációs modul csomópontján helyileg tárolja. A kimenő hasznos a tartalmazó titkosított a hitelesítő adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben "encryptedlinkedservice.JSON fájlba").

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>A JSON használja a titkosított hitelesítő adataival
Most a kimeneti JSON-fájlt a titkosított hitelesítő adatokat tartalmazó az előző parancs által használatával állítsa be a **SqlServerLinkedService**.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>További lépések
Adatáthelyezés biztonsági szempontjai kapcsolatos információkért lásd: [az adatáthelyezés biztonsági szempontjai](data-movement-security-considerations.md).

