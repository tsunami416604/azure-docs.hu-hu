---
title: Saját üzemeltetésű integrációs modul konfigurálása SSIS proxyként
description: Megtudhatja, hogyan konfigurálhat saját üzemeltetésű Integration Runtime proxyként Azure-SSIS Integration Runtimehoz.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 02/06/2020
ms.openlocfilehash: b20a615691d95c04574e2909f69b5a83a97f9d14
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048953"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Saját üzemeltetésű IR konfigurálása az ADF-Azure-SSIS IR proxyként

Ez a cikk azt ismerteti, hogyan futtathatók a SQL Server Integration Services (SSIS) csomagok Azure-SSIS Integration Runtime (IR)-ben Azure Data Factory (ADF)-ben, a saját üzemeltetésű IR proxyként konfigurálva.  Ez a funkció lehetővé teszi a helyszíni adatelérést anélkül, hogy a [virtuális hálózathoz csatlakozna a Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  Akkor hasznos, ha a vállalati hálózat túlságosan összetett konfigurációval/korlátozó házirenddel rendelkezik, hogy beinjektálja a Azure-SSIS IR.

Ez a szolgáltatás a helyszíni adatforrással rendelkező adatáramlási feladatot tartalmazó csomagot két előkészítési feladatba osztja szét: a saját üzemeltetésű integrációs modul első futtatásakor először a helyszíni adatforrásból származó adatok kerülnek át az Azure-Blob Storage egy átmeneti területére, miközben a Azure-SSIS IRon futó második egy, az átmeneti területről a kívánt adatok célhelyére helyezi át az adatátvitelt.

Ez a funkció más előnyöket és képességeket is biztosít, mivel lehetővé teszi a saját üzemeltetésű integrációs modul kiépítését olyan régiókban, amelyeket a Azure-SSIS IR még nem támogat, lehetővé teszi a saját üzemeltetésű IR nyilvános statikus IP-címének használatát az adatforrások tűzfalán, stb.

## <a name="prepare-self-hosted-ir"></a>Saját üzemeltetésű IR előkészítése

Ahhoz, hogy használhassa ezt a szolgáltatást, először létre kell hoznia egy ADF-t, és ki kell állítania a Azure-SSIS IR, ha még nem tette volna meg, kövesse a Azure-SSIS IR cikk kiépítésének [módját](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Ezután létre kell hoznia a saját üzemeltetésű integrációs modult ugyanabban az ADF-ben, ahol a Azure-SSIS IR a saját üzemeltetésű integrációs modul [létrehozása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) című cikkben leírtak szerint kell kiépíteni.

Végezetül le kell töltenie és telepítenie kell a saját üzemeltetésű integrációs modul legújabb verzióját, valamint a további illesztőprogramokat és futtatókörnyezetet a helyszíni gépen/Azure-beli virtuális gépen (VM) a következőképpen:
- Töltse le és [telepítse a saját](https://www.microsoft.com/download/details.aspx?id=39717)üzemeltetésű IR legújabb verzióját innen.
- Ha OLEDB-összekötőket használ a csomagokban, töltse le és telepítse a megfelelő OLEDB-illesztőprogramokat ugyanarra a gépre, ahol a saját üzemeltetésű IR telepítve van, ha még nem tette meg.  Ha az OLEDB-illesztőprogram korábbi verzióját használja a SQL Serverhoz (SQLNCLI), [innen](https://www.microsoft.com/download/details.aspx?id=50402)töltheti le a 64 bites verziót.  Ha az OLEDB-illesztőprogram legújabb verzióját használja a SQL Serverhoz (MSOLEDBSQL), [innen](https://www.microsoft.com/download/details.aspx?id=56730)töltheti le a 64 bites verziót.  Ha az OLEDB-illesztőprogramokat más adatbázis-rendszerekhez, például a PostgreSQL-hez, a MySQL-hez, az Oracle-hoz stb. használja, letöltheti a 64 bites verziót a saját webhelyeiről.
- Töltse le és telepítse C++ a Visual (VC) futtatókörnyezetet ugyanarra a gépre, ahol a saját üzemeltetésű IR telepítve van, ha még nem tette meg.  Az 64 bites [verziót innen töltheti le.](https://www.microsoft.com/download/details.aspx?id=40784)

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Az Azure Blob Storage társított szolgáltatás előkészítése átmeneti használatra

Hozzon létre egy Azure Blob Storage társított szolgáltatást ugyanabban az ADF-ben, ahol a Azure-SSIS IR kiépítve, ha még nem tette meg, kövesse az [ADF-hez társított szolgáltatás létrehozásával kapcsolatos](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) cikket.  Ellenőrizze a következőket:
- Az **Azure Blob Storage** van kiválasztva az **adattárhoz**
- Az **integrációs modulon keresztüli csatlakozás** **AutoResolveIntegrationRuntime** van kiválasztva
- Vagy a **fiók kulcsa**/**sas URI**/**egyszerű szolgáltatásnév** van kiválasztva a **hitelesítési módszerhez**

>[!TIP]
>Ha a **szolgáltatás egyszerű** van kiválasztva, adjon meg legalább **tárolási blob adatközreműködői szerepkört**. További információkért tekintse meg az [Azure Blob Storage-összekötőt](connector-azure-blob-storage.md#linked-service-properties).

![Az Azure Blob Storage társított szolgáltatás előkészítése átmeneti használatra](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Azure-SSIS IR konfigurálása saját üzemeltetésű IR proxyként

A saját üzemeltetésű IR-és Azure Blob Storage társított szolgáltatás előkészítésének előkészítésével mostantól konfigurálhatja az új/meglévő Azure-SSIS IRt a saját üzemeltetésű integrációs modulként az ADF-portálon/alkalmazáson keresztül.  Ha a meglévő Azure-SSIS IR fut, állítsa le, mielőtt ezt megtenné, majd indítsa újra.

1. Az integrációs modul telepítése panelen az **általános beállítások** és az SQL- **Beállítások** szakaszban válassza a **tovább** gombot. 

1. A **Speciális beállítások** szakaszban:

   1. Jelölje be a saját üzemeltetésű **Integration Runtime beállítása Azure-SSIS Integration Runtime proxyként** jelölőnégyzetet. 

   1. Saját üzemeltetésű **Integration Runtime**esetén válassza ki a meglévő, saját üzemeltetésű IR-t Azure-SSIS IR-proxyként.

   1. Az **átmeneti tároláshoz társított szolgáltatás**esetében válassza ki a meglévő Azure Blob Storage-beli társított szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

   1. Az **előkészítési útvonal**mezőben adja meg a BLOB-tárolót a kiválasztott Azure Blob Storage-fiókban, vagy hagyja üresen, hogy az alapértelmezett beállítást használja az átmeneti tároláshoz.

   1. Válassza a **Folytatás** elemet.

   ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Az új/meglévő Azure-SSIS IR saját üzemeltetésű IR-ként is konfigurálhatja a PowerShell használatával.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS-csomagok engedélyezése proxy alapján való csatlakozásra

A SSIS projects bővítménnyel a Visual studióhoz készült legújabb SSDT használatával letölthető [innen vagy önálló](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) telepítőként, amely innen tölthető le, és [itt](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)talál egy új **ConnectByProxy** -tulajdonságot, amely az OLEDB/Flat file ügyfélkapcsolat-kezelőben lett hozzáadva.  

Amikor az OLEDB/Flat file sources használatával olyan új csomagokat tervez, amelyekben az adatbázisok/fájlok elérhetők a helyszíni adatbázisokhoz/fájlokhoz, ezt a tulajdonságot a megfelelő Csatlakozáskezelő tulajdonságok paneljének **true (igaz** ) értékre állításával engedélyezheti.

![ConnectByProxy tulajdonság engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Ezt a tulajdonságot akkor is engedélyezheti, ha már meglévő csomagokat futtat, és nem kell manuálisan módosítania őket.  Két lehetőség érhető el:
- A csomagokat tartalmazó projekt megnyitása, újraépítése és újbóli üzembe helyezése a legújabb SSDT a Azure-SSIS IRon való futtatáshoz: ezt követően a tulajdonság értéke **true (igaz** ) értékre állítható be, ha a SSMS-ból csomagokat futtat, és a **Csatlakozáskezelő** lapon megjelenő kapcsolódó ügyfélkapcsolat-kezelők.

  ![ConnectByProxy property2 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A tulajdonságot engedélyezheti úgy is, hogy **igaz** értékre állítja a [SSIS-csomag végrehajtásakor](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) a **kapcsolatkezelő** lapon megjelenő kapcsolódó ügyfélkapcsolat-kezelők számára a csomagok az ADF-folyamatokban való futtatásakor.
  
  ![ConnectByProxy property3 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- A csomagokat tartalmazó projekt újbóli üzembe helyezése az SSIS IR-ben: a tulajdonság ezután engedélyezhető a tulajdonság elérési útjának megadásával, a `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`és az **igaz** értékre állításával a csomag előugró ablakának **speciális** LAPJÁN, a csomagok SSMS való futtatásakor.

  ![ConnectByProxy property4 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A tulajdonságot engedélyezheti a tulajdonság elérési útjának megadásával, a `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`ával és az **igaz** értékre való beállításával, **Ha a csomagok** az ADF-folyamatokban való futtatásakor a tulajdonságok felülbírálása lapon a [SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) .
  
  ![ConnectByProxy property5 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat hibakeresése

A saját üzemeltetésű integrációs modulban megtalálhatja a futásidejű naplókat `C:\ProgramData\SSISTelemetry` mappában, valamint az első előkészítési feladatok végrehajtási naplóit `C:\ProgramData\SSISTelemetry\ExecutionLog` mappában.  A második előkészítési feladatok végrehajtási naplói a SSISDB vagy a megadott naplózási elérési utakon találhatók, attól függően, hogy a csomagokat a SSISDB, illetve a fájlrendszer/fájlmegosztás/Azure Filesban tárolja-e a rendszer.  Az első előkészítési feladatok egyedi azonosítói is megtalálhatók a második előkészítési feladatok végrehajtási naplóiban, például: 

![Az első előkészítési feladat egyedi azonosítója](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Windows-hitelesítés használata átmeneti feladatokban

Ha a saját üzemeltetésű integrációs modul előkészítési feladatainak Windows-hitelesítésre van szükségük, [konfigurálnia kell a SSIS-csomagokat, hogy ugyanazt a Windows-hitelesítést használják](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Az előkészítési feladatok a saját üzemeltetésű IR-szolgáltatásfiók (`NT SERVICE\DIAHostService` alapértelmezés szerint) lesznek meghívva, és az adattárak a Windows-hitelesítési fiókkal lesznek elérhetők. Mindkét fiókhoz szükség van bizonyos biztonsági házirendek hozzárendelésére. Ezért a saját üzemeltetésű IR-gépen nyissa meg `Local Security Policy` -> `Local Policies` -> `User Rights Assignment`, és végezze el a következő lépéseket.
- Rendeljen hozzá **egy folyamathoz tartozó memória-kvótát** , és **cserélje le a folyamat szintű jogkivonat** -szabályzatokat a saját üzemeltetésű IR-szolgáltatásfiók. Ezt automatikusan el kell végezni a saját üzemeltetésű integrációs szolgáltatás alapértelmezett szolgáltatásfiók telepítésekor. Ha más szolgáltatásfiókot használ, ugyanazt a szabályzatot rendeli hozzá.
- Rendelje hozzá a **bejelentkezést szolgáltatási** házirendként a Windows-hitelesítési fiókhoz.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat számlázása

A saját üzemeltetésű integrációs modulon futó első előkészítési feladatok számlázása ugyanúgy történik, mint a saját üzemeltetésű integrációs modulon futó adatáthelyezési tevékenységek számlázása az [ADF-adatcsatorna díjszabási](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) cikkében megadott módon.

A Azure-SSIS IR futó második előkészítési feladatok külön nem lesznek felszámítva, de a futó Azure-SSIS IR a [Azure-SSIS IR árképzési](https://azure.microsoft.com/pricing/details/data-factory/ssis/) cikkben megadott módon lesznek számlázva.

## <a name="current-limitations"></a>Aktuális korlátozások

- Jelenleg csak az ODBC/OLEDB/Flat file ügyfélkapcsolat-kezelővel és az ODBC/OLEDB/Flat file sources vagy OLEDB-célhoz tartozó adatáramlási feladatok támogatottak. 
- Jelenleg csak az Azure Blob Storage társított szolgáltatások vannak konfigurálva a **fiók kulcsa**/**sas URI**/**egyszerű szolgáltatásnév** -hitelesítés.

## <a name="next-steps"></a>Következő lépések

Ha a saját üzemeltetésű integrációs modult proxyként konfigurálta a Azure-SSIS IR számára, a csomagokat üzembe helyezheti és futtathatja, hogy az ADF-folyamatokban hajtsa végre a helyszíni adatSSIS-csomag tevékenységeit, lásd: [SSIS-csomagok futtatása az ADF-folyamatok végrehajtási SSIS](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
