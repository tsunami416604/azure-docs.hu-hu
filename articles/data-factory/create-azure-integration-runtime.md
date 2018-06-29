---
title: Azure-integráció futásidejű létrehozása az Azure Data Factory |} Microsoft Docs
description: 'Útmutató: Azure integrációs futásidejű létrehozása az Azure Data Factory, amely másolja az adatokat, és a feladó átalakító tevékenységek.'
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: cb1a263c0a33a291a44e7c60b3c032d7f9dc16a3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054075"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Hozzon létre, és Azure integrációs futásidejű konfigurálása
Az integrációs futásidejű (IR) a számítási infrastruktúrától által használt Azure Data Factory adatok integrációs funkciók különböző hálózati környezetek között. Infravörös kapcsolatos további információkért lásd: [integrációs futásidejű](concepts-integration-runtime.md).

Az Azure IR natív módon tevékenységeket elvégezzen az adatok mozgás és a feladó adatok átalakítása számítja ki a szolgáltatásokat, mint a HDInsight egy teljes körűen felügyelt számítógépes biztosít. Az Azure-alapú környezetben üzemel, és erőforrások nyilvános elérhető végpontok nyilvános hálózati környezetben történő csatlakozást támogatja.

Ez a dokumentum bemutatja, hogyan hozhat létre, és konfigurálja az Azure integrációs futásidejű. 

## <a name="default-azure-ir"></a>Alapértelmezett Azure IR
Alapértelmezés szerint minden adat-előállító rendelkezik egy Azure-IR a háttérben, amely támogatja a felhő adatokat tárolja, és a nyilvános hálózati szolgáltatások számítási műveleteket. Az adott Azure IR helye automatikus feloldása. Ha **connectVia** tulajdonság nincs megadva a kapcsolódószolgáltatás-definícióban az alapértelmezett Azure IR szolgál. Csak létrehozásához szükséges kifejezetten az Azure-IR szeretné definiálhat explicit módon az infravörös helyét, vagy ha szeretné a tevékenység végrehajtások a különböző felügyeleti célú IRs gyakorlatilag csoportban. 

## <a name="create-azure-ir"></a>Az Azure IR létrehozása
Integrációs futásidejű segítségével hozhatók létre a **Set-AzureRmDataFactoryV2IntegrationRuntime** PowerShell-parancsmagot. Hozzon létre egy Azure-IR, meg kell adnia nevét, helyét és típusát. a parancshoz. Íme egy példa a parancsra hozzon létre egy Azure-IR "Nyugat-Európa" értékűre helye:

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Az Azure-IR, a típust kell beállítani **felügyelt**. Nem kell adja meg a számítási adatait, mert a teljes körűen felügyelt rugalmasan a felhőben. Adja meg a számítási csomópont méretének és a csomópont száma, amikor szeretné létrehozni az Azure-SSIS infravörös például Részletek További információkért lásd: [létrehozása és konfigurálása Azure-SSIS-IR](create-azure-ssis-integration-runtime.md).

Egy meglévő Azure-IR szeretné módosítani a Set-AzureRmDataFactoryV2IntegrationRuntime PowerShell-parancsmag használatával konfigurálható. Egy Azure-IR helyére vonatkozó további információkért lásd: [integrációs futásidejű bemutatása](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Az Azure IR használata

Egy Azure-IR létrehozása után hivatkozhasson rá a Kapcsolódószolgáltatás-definícióban. Alább hogyan is hivatkozni lehessen az Azure integrációs futásidejű mintát készült fent egy Azure Storage társított szolgáltatást:  

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
Tekintse meg a következő cikkekben integrációs futtatókörnyezetek más típusú létrehozásával:

- [Saját üzemeltetésű integrációs modul létrehozása](create-self-hosted-integration-runtime.md)
- [Azure-SSIS integrációs futásidejű létrehozása](create-azure-ssis-integration-runtime.md)
 
