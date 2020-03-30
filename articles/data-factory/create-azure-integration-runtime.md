---
title: Azure-integrációs futásidő létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre Azure-integrációs futásidejűt az Azure Data Factoryban, amely az adatok másolására és a feladási átalakítási tevékenységekre szolgál.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: 87633abaaae1f6034709c6e552be6647533115ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260760"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Az Azure-integrációs futásidő létrehozása és konfigurálása
Az integrációs futásidejű (IR) az Azure Data Factory által a különböző hálózati környezetekben az adatintegrációs képességek biztosításához használt számítási infrastruktúra. Az infravörös kapcsolatról további információt az [Integrációs futásidő](concepts-integration-runtime.md)című témakörben talál.

Az Azure IR teljes körűen felügyelt számítási lehetőséget biztosít az adatáthelyezési és adatátalakítási tevékenységek natív végrehajtásához az olyan számítási szolgáltatásokhoz, mint a HDInsight. Az Azure-környezetben üzemelteti, és támogatja a nyilvános hálózati környezetben lévő erőforrásokhoz való csatlakozást nyilvános elérhető végpontokkal.

Ez a dokumentum bemutatja, hogyan hozhat létre és konfigurálhat Azure-integrációs futástime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Alapértelmezett Azure-ir
Alapértelmezés szerint minden adat-előállító rendelkezik egy Azure IR a háttérrendszer, amely támogatja a műveleteket a felhőbeli adattárak és számítási szolgáltatások nyilvános hálózatban. Az Azure IR helye automatikus feloldás. Ha **a connectVia** tulajdonság nincs megadva a csatolt szolgáltatásdefinícióban, a rendszer az alapértelmezett Azure IR-t használja. Csak akkor kell explicit módon létrehozni egy Azure IR- t, ha explicit módon meg szeretné határozni az infravörös rendszer helyét, vagy ha gyakorlatilag szeretné csoportosítani a tevékenység-végrehajtásokat a különböző IRs-eken felügyeleti célokra. 

## <a name="create-azure-ir"></a>Azure IR létrehozása
Integrációs futásidejű a **Set-AzDataFactoryV2IntegrationRuntime** PowerShell-parancsmag használatával hozható létre. Azure-kapcsolati rt. létrehozásához adja meg a nevét, helyét és típusát a parancshoz. Az alábbiakban egy mintaparancs található, amely "Nyugat-Európa" helyre állított helyet foglal el:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Az Azure IR esetében a típust **Felügyelt**beállításra kell állítani. Nem kell megadnia a számítási részleteket, mert azok teljes körűen felügyelt a felhőben. Adja meg a számítási részleteket, például a csomópont méretét és a csomópontok számát, amikor létre szeretné hozni az Azure-SSIS IR-t. További információ: [Azure-SSIS IR létrehozása és konfigurálása.](create-azure-ssis-integration-runtime.md)

Konfigurálhatja a meglévő Azure IR a set-azdatafactoryv2Integrationruntime PowerShell-parancsmag használatával módosíthatja a helyét. Az Azure IR helyéről további információt a Bevezetés az [integrációs futásidejű bemutatkozás című témakörben talál.](concepts-integration-runtime.md)

## <a name="use-azure-ir"></a>Az Azure IR használata

Az Azure IR létrehozása után hivatkozhat rá a csatolt szolgáltatás definíciójában. Az alábbiakban egy példa látható arról, hogyan hivatkozhat az Azure Storage-alapú szolgáltatásból fent létrehozott Azure-integrációs futásórára:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>További lépések
Az integrációs futtatások más típusainak létrehozásáról az alábbi cikkekben a következőképpen hozhat létre:

- [Saját üzemeltetésű integrációs futásidejű létrehozása](create-self-hosted-integration-runtime.md)
- [Azure SSIS integrációs modul létrehozása](create-azure-ssis-integration-runtime.md)
 
