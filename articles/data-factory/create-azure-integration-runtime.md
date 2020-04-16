---
title: Azure-integrációs futásidő létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre Azure-integrációs futásidejűt az Azure Data Factoryban, amely az adatok másolására és a feladási átalakítási tevékenységekre szolgál.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/13/2020
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.openlocfilehash: e32530ece3626807b199850a2b4af5461ff51cde
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414067"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Az Azure-integrációs futásidő létrehozása és konfigurálása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az integrációs futásidejű (IR) az Azure Data Factory által a különböző hálózati környezetekben az adatintegrációs képességek biztosításához használt számítási infrastruktúra. Az infravörös kapcsolatról további információt az [Integrációs futásidő](concepts-integration-runtime.md)című témakörben talál.

Az Azure IR teljes körűen felügyelt számítási lehetőséget biztosít az adatáthelyezési és adatátalakítási tevékenységek natív végrehajtásához az olyan számítási szolgáltatásokhoz, mint a HDInsight. Az Azure-környezetben üzemelteti, és támogatja a nyilvános hálózati környezetben lévő erőforrásokhoz való csatlakozást nyilvános elérhető végpontokkal.

Ez a dokumentum bemutatja, hogyan hozhat létre és konfigurálhat Azure-integrációs futástime. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Alapértelmezett Azure-ir
Alapértelmezés szerint minden adat-előállító rendelkezik egy Azure IR a háttérrendszer, amely támogatja a műveleteket a felhőbeli adattárak és számítási szolgáltatások nyilvános hálózatban. Az Azure IR helye automatikus feloldás. Ha **a connectVia** tulajdonság nincs megadva a csatolt szolgáltatásdefinícióban, a rendszer az alapértelmezett Azure IR-t használja. Csak akkor kell explicit módon létrehozni egy Azure IR- t, ha explicit módon meg szeretné határozni az infravörös rendszer helyét, vagy ha gyakorlatilag szeretné csoportosítani a tevékenység-végrehajtásokat a különböző IRs-eken felügyeleti célokra. 

## <a name="create-azure-ir"></a>Azure IR létrehozása

Azure-kapcsolati kapcsolat létrehozásához és beállításához használhatja az alábbi eljárásokat.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Azure IR létrehozása az Azure PowerShell használatával
Integrációs futásidejű a **Set-AzDataFactoryV2IntegrationRuntime** PowerShell-parancsmag használatával hozható létre. Azure-kapcsolati kapcsolat létrehozásához adja meg a nevet, a helyet, és írja be a parancsot. Az alábbiakban egy mintaparancs található, amely "Nyugat-Európa" helyre állított helyet foglal el:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Az Azure IR esetében a típust **Felügyelt**beállításra kell állítani. Nem kell megadnia a számítási részleteket, mert azok teljes körűen felügyelt a felhőben. Adja meg a számítási részleteket, például a csomópont méretét és a csomópontok számát, amikor létre szeretné hozni az Azure-SSIS IR-t. További információ: [Azure-SSIS IR létrehozása és konfigurálása.](create-azure-ssis-integration-runtime.md)

Konfigurálhatja a meglévő Azure IR a set-azdatafactoryv2Integrationruntime PowerShell-parancsmag használatával módosíthatja a helyét. Az Azure IR helyéről további információt a Bevezetés az [integrációs futásidejű bemutatkozás című témakörben talál.](concepts-integration-runtime.md)

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Azure-kapcsolati kapcsolat tal kapcsolatban az Azure Data Factory felhasználói felületén keresztül
Az alábbi lépésekkel hozzon létre egy Azure IR az Azure Data Factory felhasználói felülethasználatával.

1. Az Azure Data Factory felhasználói felületének **Get's get s get started (Lépések)** lapján válassza a **Szerző** lapot a bal oldali ablaktáblán.

   ![A kezdőlap Szerző gombja](media/doc-common-process/get-started-page-author-button.png)

1. Válassza a **Kapcsolatok** lehetőséget a bal oldali ablaktábla alján, majd a **Kapcsolatok** ablakban válassza az **Integráció safuválasztót.** Válassza **a +Új**lehetőséget.

   ![Integrációs modul létrehozása](media/create-azure-integration-runtime/new-integration-runtime.png)

1. Az **Integrációs futásidejű beállítási** lapon válassza az **Azure, Self-Hosted**( Integrációs futásidejű beállítási lap) lehetőséget, majd a **Continue (Folytatás)** lehetőséget. 

1. A következő lapon válassza az **Azure-t** az **Continue**Azure-kapcsolat irt.
   ![Integrációs modul létrehozása](media/create-azure-integration-runtime/new-azure-ir.png)

1. Adja meg az Azure IR nevét, és válassza a **Létrehozás gombot.**
   ![Azure-kapcsolati kapcsolat létrehozása](media/create-azure-integration-runtime/create-azure-ir.png)

1. A létrehozás befejezésekor előugró értesítés jelenik meg. Az **integrációs futásidők** lapon győződjön meg arról, hogy az újonnan létrehozott infravörös a listában.

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
 
