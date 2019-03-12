---
title: Azure-beli integrációs modul létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre Azure integrációs modul az Azure Data Factoryben, amely csatolva az átalakítási tevékenységek és az adatok másolása szolgál.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 4b166ded3dcef4a89951eb81f7f1b321f89a0e67
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576038"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Hogyan hozhat létre, és az Azure integrációs modul konfigurálása
Az Integration Runtime (IR) a különböző hálózati környezetekben adatintegrációs képességeket biztosít az Azure Data Factory által használt számítási infrastruktúra áll. Integrációs modul kapcsolatos további információkért lásd: [integrációs modul](concepts-integration-runtime.md).

Azure integrációs modul natív módon végrehajtani az adatok mozgását és küldő adat-átalakítási tevékenységeket számítási szolgáltatások, például a HDInsight egy teljes mértékben felügyelt számítási biztosít. Azure-környezetben üzemeltetett és a támogatja az erőforrás nyilvános hálózati környezetben nyilvánosan hozzáférhető végpontokkal.

Ez a dokumentum bemutatja, hogyan létrehozása és konfigurálása az Azure integrációs modul. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Alapértelmezett Azure integrációs modul
Alapértelmezés szerint minden adat-előállító egy Azure integrációs modul van a háttérrendszerhez, amely támogatja a műveleteket a felhő adatokat tárolja, és a számítási szolgáltatások a nyilvános hálózatban. Automatikus feloldása, hogy az Azure integrációs modul helye. Ha **connectVia** tulajdonság nincs megadva az alapértelmezett Azure integrációs Modult használja a társított szolgáltatás definíciójában. Csak akkor kell explicit módon szeretne explicit módon határozhatja meg az integrációs modul helyét, vagy ha, amelyet szeretne a tevékenység-végrehajtások a különböző IRs felügyeleti célú gyakorlatilag csoport hozzon létre egy Azure integrációs modul. 

## <a name="create-azure-ir"></a>Az Azure integrációs modul létrehozása
Integrációs modul hozható létre a **Set-AzDataFactoryV2IntegrationRuntime** PowerShell-parancsmagot. Hozzon létre egy Azure integrációs Modult, adja meg a nevét, helyét és írja be a következő paranccsal. Egy mintául szolgáló parancs egy Azure integrációs modul létrehozása "Nyugat-európai" állítsa helyre az itt látható:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Az Azure IR, a típus értékre kell állítani **felügyelt**. Nem kell megadnia a számítási részletei, mert azt teljes körűen felügyelt rugalmasan a felhőben. Adja meg a számítási részletesen, a csomópont méretét és a csomópont száma, ha szeretne létrehozni az Azure-SSIS integrációs modult. További információkért lásd: [létrehozása és konfigurálása az Azure-SSIS integrációs modul](create-azure-ssis-integration-runtime.md).

Beállíthatja, hogy egy meglévő Azure integrációs modul helyét a Set-AzDataFactoryV2IntegrationRuntime PowerShell-parancsmag használatával módosíthatja. Az Azure integrációs modul helyének kapcsolatos további információkért lásd: [Bevezetés a saját üzemeltetésű integrációs](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Azure integrációs modul használata

Az Azure IR létrehozása után a társított szolgáltatás definíciójában hivatkozhasson rá. Alább egy mintát hogyan hivatkozhat az Azure integrációs modul jön létre fent egy Azure Storage társított szolgáltatás:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>További lépések
Más típusú integrációs modulok létrehozásáról a következő cikkekben talál:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Az Azure-SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md)
 
