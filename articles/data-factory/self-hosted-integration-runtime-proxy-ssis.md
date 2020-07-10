---
title: Saját üzemeltetésű integrációs modul konfigurálása SSIS proxyként
description: Megtudhatja, hogyan konfigurálhat egy saját üzemeltetésű integrációs modult proxyként egy Azure-SSIS Integration Runtime számára.
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
ms.date: 07/09/2020
ms.openlocfilehash: 1eac86e856840d5cb78313fb4d61751066d6886b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184004"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Saját üzemeltetésű IR konfigurálása proxyként egy Azure-SSIS IRhoz Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ez a cikk azt ismerteti, hogyan futtathat SQL Server Integration Services (SSIS) csomagokat egy Azure-SSIS Integration Runtime (Azure-SSIS IR) Azure Data Factory egy saját üzemeltetésű integrációs modul (helyi IR) használatával, amely proxyként van konfigurálva. 

Ezzel a szolgáltatással a helyszíni adataihoz anélkül férhet hozzá, hogy a [virtuális hálózathoz kellene csatlakoztatnia a Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). A funkció akkor hasznos, ha a vállalati hálózat konfigurációja túl összetett, vagy a házirend túlságosan korlátozó ahhoz, hogy beinjektálja a Azure-SSIS IR.

Ez a szolgáltatás minden olyan SSIS-adatfolyam-feladatot feldarabol, amely egy helyszíni adatforrással rendelkezik két előkészítési feladatban: 
* Az első feladat, amely a saját üzemeltetésű integrációs modulján fut, először a helyszíni adatforrásból helyez át egy átmeneti területre az Azure Blob Storage-ban.
* A második feladat, amely a Azure-SSIS IR fut, majd áthelyezi az adatok az átmeneti területről a kívánt adat célhelyére.

A szolgáltatás egyéb előnyei és képességei lehetővé teszik például a saját üzemeltetésű integrációs modul beállítását olyan régiókban, amelyeket egy Azure-SSIS IR még nem támogat, és lehetővé teszi a saját üzemeltetésű IR nyilvános statikus IP-címének használatát az adatforrások tűzfalán.

## <a name="prepare-the-self-hosted-ir"></a>A saját üzemeltetésű integrációs modul előkészítése

A szolgáltatás használatához először hozzon létre egy adatelőállítót, és állítson be egy Azure-SSIS IR. Ha még nem tette meg, kövesse az [Azure-SSIS IR beállítása](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)című témakör utasításait.

Ezután állítsa be a saját üzemeltetésű integrációs modult ugyanabban az adatgyárban, ahol a Azure-SSIS IR be van állítva. Ehhez tekintse meg [a saját üzemeltetésű IR létrehozása](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)című témakört.

Végül letölti és telepíti a saját üzemeltetésű integrációs modul legújabb verzióját, valamint a további illesztőprogramokat és futtatókörnyezetet a helyszíni gépen vagy az Azure-beli virtuális gépen (VM), a következőképpen:
- Töltse le és telepítse a [saját](https://www.microsoft.com/download/details.aspx?id=39717)üzemeltetésű integrációs modul legújabb verzióját.
- Ha objektum-csatolási és beágyazási adatbázis (OLEDB) összekötőket használ a csomagokban, töltse le és telepítse a megfelelő OLEDB-illesztőprogramokat ugyanarra a gépre, amelyen a saját üzemeltetésű integrációs modul telepítve van, ha még nem tette volna meg.  

  Ha az OLEDB-illesztőprogram korábbi verzióját használja SQL Server (SQL Server Native Client [SQLNCLI]), [töltse le a 64 bites verziót](https://www.microsoft.com/download/details.aspx?id=50402).  

  Ha az OLEDB-illesztőprogram legújabb verzióját használja a SQL Serverhoz (MSOLEDBSQL), [töltse le a 64 bites verziót](https://www.microsoft.com/download/details.aspx?id=56730).  
  
  Ha az OLEDB-illesztőprogramokat más adatbázis-rendszerekhez (például PostgreSQL, MySQL, Oracle stb.) használja, letöltheti a 64 bites verzióit a webhelyükről.
- Ha még nem tette meg, [töltse le és telepítse a Visual C++ (VC) futtatókörnyezet 64 bites verzióját](https://www.microsoft.com/download/details.aspx?id=40784) ugyanarra a gépre, amelyen a saját üzemeltetésű IR telepítve van.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Az Azure Blob Storage-hoz társított szolgáltatás előkészítése előkészítéshez

Ha még nem tette meg, hozzon létre egy Azure Blob Storage-társított szolgáltatást ugyanabban az adatgyárban, ahol a Azure-SSIS IR be van állítva. Ehhez tekintse meg [Az Azure-beli adatfeldolgozó-társított szolgáltatás létrehozása](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)című témakört. Ügyeljen arra, hogy tegye a következőket:
- Az **adattár**lapon válassza az **Azure Blob Storage**lehetőséget.  
- Az **integrációs modulon keresztüli csatlakozáshoz**válassza a **AutoResolveIntegrationRuntime** (nem a Azure-SSIS IR, sem a saját üzemeltetésű IR) beállítást, mert az alapértelmezett Azure IR használatával beolvassa az Azure-Blob Storage hozzáférési hitelesítő adatait.
- A **hitelesítési módszer**beállításnál válassza a **fiók kulcsa**, **sas URI**vagy **egyszerű szolgáltatásnév**lehetőséget.  

    >[!TIP]
    >Ha az **egyszerű szolgáltatásnév** módszert választja, adja meg az egyszerű szolgáltatásnév számára a *tárolási blob adatközreműködői*   szerepkört. További információkért tekintse meg az [Azure Blob Storage-összekötőt](connector-azure-blob-storage.md#linked-service-properties).

![Az Azure Blob Storage-hoz társított szolgáltatás előkészítése előkészítéshez](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Azure-SSIS IR konfigurálása saját üzemeltetésű IR proxyként

A saját üzemeltetésű IR-és Azure Blob Storage-hez társított szolgáltatás előkészítésének előkészítéséhez mostantól konfigurálhatja az új vagy meglévő Azure-SSIS IR a saját üzemeltetésű integrációs modult a saját adatfeldolgozó portál vagy alkalmazás használatával. Mielőtt ezt megtenné, azonban ha a meglévő Azure-SSIS IR már fut, állítsa le, majd indítsa újra.

1. Az **integrációs modul telepítése** ablaktáblán a **Tovább gombra**kattintva ugorjon az **általános beállítások** és az **SQL-beállítások** szakaszban. 

1. A **Speciális beállítások** szakaszban tegye a következőket:

   1. Jelölje be a saját üzemeltetésű **Integration Runtime beállítása Azure-SSIS Integration Runtime proxyként** jelölőnégyzetet. 

   1. A saját üzemeltetésű **Integration Runtime** legördülő listában válassza ki a meglévő, saját üzemeltetésű IR-t a Azure-SSIS IRhoz.

   1. Az **átmeneti tárolóhoz társított szolgáltatás** legördülő listában válassza ki a meglévő Azure Blob Storage-társított szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

   1. Az **átmeneti útvonal** mezőben adja meg a BLOB-tárolót a kiválasztott Azure Blob Storage-fiókban, vagy hagyja üresen, hogy az alapértelmezett beállítást használja az átmeneti tároláshoz.

   1. Válassza a **Folytatás** elemet.

   ![Speciális beállítások önkiszolgáló IR-vel](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Az új vagy meglévő Azure-SSIS IR a PowerShell használatával is konfigurálhatja proxyként a saját üzemeltetésű IR-ként.

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

A legújabb SSDT a Visual studióhoz készült SSIS projects bővítménnyel vagy egy önálló telepítővel olyan új tulajdonságot talál, `ConnectByProxy` amely az OLEDB vagy a Flat file kapcsolatkezelő szolgáltatásban lett hozzáadva.
* [A SSDT letöltése a SSIS projects bővítménnyel a Visual studióhoz](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Az önálló telepítő letöltése](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Ha olyan új csomagokat tervez meg, amelyek az OLEDB vagy a Flat file sources adatáramlási feladatait tartalmazzák, lehetővé teszik a helyszíni adatbázisok vagy fájlok elérését, ha a megfelelő Csatlakozáskezelő **Tulajdonságok** ablaktábláján az *igaz* értékre állítja a tulajdonságot.

![ConnectByProxy tulajdonság engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Ezt a tulajdonságot akkor is engedélyezheti, ha meglévő csomagokat futtat, anélkül, hogy ezeket manuálisan módosítania kellene.  Két lehetőség érhető el:
- **A. lehetőség**: Nyissa meg, építse újra, majd telepítse újra a csomagokat tartalmazó projektet a legújabb SSDT, hogy az a Azure-SSIS IR fusson. Ezt követően engedélyezheti a tulajdonságot úgy, hogy az *igaz* értéket adja meg a megfelelő ügyfélkapcsolat-kezelők számára. Amikor csomagokat futtatnak a SSMS-ból, ezek a kapcsolatok a **csomag** előugró ablakának **Csatlakozáskezelő** lapján jelennek meg.

  ![ConnectByProxy property2 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, hogy az *igaz* értékre van ÁLLÍTVA a [SSIS-csomag végrehajtásához](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) tartozó **kapcsolatkezelő** lapon megjelenő kapcsolódó kapcsolatkezelő esetén, amikor Data Factory folyamatokban csomagokat futtatnak.
  
  ![ConnectByProxy property3 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **B. lehetőség:** Telepítse újra a csomagokat tartalmazó projektet a SSIS IR-ben való futtatáshoz. Ezután engedélyezheti a tulajdonságot a tulajdonság elérési útjának megadásával, és beállíthatja, hogy a tulajdonság `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` felülbírálja a **csomag** előugró ablakának **speciális** lapján a csomagok SSMS *való* futtatásakor.

  ![ConnectByProxy property4 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, ha a tulajdonság elérési útját `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` adja meg, és az *igaz* értéket adja meg **Property Overrides** a tulajdonság felülbírálásához a [SSIS-csomag végrehajtása tevékenységben](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) , amikor csomagokat futtat Data Factory folyamatokban.
  
  ![ConnectByProxy property5 engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat hibakeresése

A saját üzemeltetésű integrációs modulban megtalálhatja a futásidejű naplókat a *C:\ProgramData\SSISTelemetry* mappában, valamint az első előkészítési feladatok végrehajtási naplóit a *C:\ProgramData\SSISTelemetry\ExecutionLog* mappában.  A SSISDB vagy a megadott naplózási elérési utakon megtalálhatja a második előkészítési feladat végrehajtási naplóit attól függően, hogy a csomagokat SSISDB vagy fájlrendszerben, fájlmegosztást vagy Azure Files tárolja-e. Az első előkészítési feladatok egyedi azonosítóit is megtalálhatja a második előkészítési feladatok végrehajtási naplóiban. 

![Az első előkészítési feladat egyedi azonosítója](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Windows-hitelesítés használata átmeneti feladatokban

Ha a saját üzemeltetésű integrációs modul előkészítési feladatai Windows-hitelesítést igényelnek, [konfigurálja a SSIS-csomagokat, hogy ugyanazt a Windows-hitelesítést használják](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). 

Az előkészítési feladatok a saját üzemeltetésű IR-szolgáltatásfiók (alapértelmezés szerint*NT SERVICE\DIAHostService*) szerint lesznek megnyitva, és az adattárak a Windows-hitelesítési fiókkal lesznek elérhetők. Mindkét fiókhoz szükség van bizonyos biztonsági házirendek hozzárendelésére. A saját üzemeltetésű IR-gépen lépjen a **helyi biztonsági házirend**  >  **helyi házirendek**  >  **felhasználói jogok kiosztása**elemre, majd tegye a következőket:

1. Rendeljen hozzá *egy folyamathoz tartozó memória-kvótát* , és *cserélje le a folyamat szintű jogkivonat* -szabályzatokat a saját üzemeltetésű IR-szolgáltatásfiók. Ez automatikusan megtörténik, amikor telepíti a saját üzemeltetésű integrációs modult az alapértelmezett szolgáltatásfiók-fiókkal. Ha nem, akkor manuálisan rendelje hozzá ezeket a házirendeket. Ha más szolgáltatásfiókot használ, ugyanazt a szabályzatot rendeli hozzá.

1. Rendelje hozzá a *bejelentkezést szolgáltatási* házirendként a Windows-hitelesítési fiókhoz.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Az első és a második előkészítési feladat számlázása

A saját üzemeltetésű IR-ben futtatott első előkészítési feladatok számlázása külön történik, ugyanúgy, mint a saját üzemeltetésű integrációs modulon futó adatáthelyezési tevékenységek számlázása. Ez a [Azure Data Factory adatcsatorna-díjszabási](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) cikkben van megadva.

A Azure-SSIS IR futó második előkészítési feladatot nem külön számlázjuk, de a futó Azure-SSIS IR a [Azure-SSIS IR árképzési](https://azure.microsoft.com/pricing/details/data-factory/ssis/) cikkben megadott módon kell fizetni.

## <a name="enabling-tls-12"></a>A TLS 1.2 engedélyezése

Ha erős titkosítást/biztonságosabb hálózati protokollt (TLS 1,2) kell használnia, és le kell tiltania a régebbi SSL/TLS-verziókat a saját üzemeltetésű integrációs modulban, letöltheti és futtathatja a nyilvános előzetes tároló *CustomSetupScript/UserScenarios/TLS 1,2* mappájában található *Main. cmd* parancsfájlt.  A [Azure Storage Explorer](https://storageexplorer.com/)használatával a következő sas URI azonosító megadásával csatlakozhat a nyilvános előzetes tárolóhoz:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuális korlátozások

- Jelenleg csak az Open Database Connectivity (ODBC)/OLEDB/Flat vagy az OLEDB céljával rendelkező adatáramlási feladatok támogatottak. 
- Jelenleg csak az Azure Blob Storage-hoz társított, a *fiók kulcsával*, a *közös hozzáférésű aláírás (SAS) URI azonosítóval*vagy a *szolgáltatás egyszerű* hitelesítésével konfigurált szolgáltatások támogatottak.
- Az OLEDB-forrás *ParameterMapping* még nem támogatott. Áthidaló megoldásként használja az *SQL parancsot a változóból* *AccessMode* , és használja a *kifejezést* a változók/paraméterek egy SQL-parancsba való beszúrásához. Példaként tekintse meg a *ParameterMappingSample. dtsx* csomagot, amely a nyilvános előzetes verziójú tároló *SelfHostedIRProxy/korlátozások* mappájában található. A Azure Storage Explorer használatával a fenti SAS URI beírásával csatlakozhat a nyilvános előzetes verziójú tárolóhoz.

## <a name="next-steps"></a>További lépések

Miután konfigurálta a saját üzemeltetésű integrációs modult proxyként a Azure-SSIS IR számára, a csomagokat üzembe helyezheti és futtathatja a helyszíni adateléréshez a SSIS-csomag tevékenységének végrehajtása Data Factory folyamatokban. Ebből a cikkből megtudhatja, hogyan [FUTTATHAT SSIS-CSOMAGOKAT SSIS-csomagok végrehajtásaként Data Factory-folyamatokban](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).
