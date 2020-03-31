---
title: Saját üzemeltetésű integrációs futásidő konfigurálása az SSIS proxyjaként
description: Ismerje meg, hogyan konfigurálhat egy saját üzemeltetésű integrációs futásidejűt az Azure-SSIS-integrációs futásidejű proxyként.
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
ms.date: 03/27/2020
ms.openlocfilehash: 9a1923057bc318869f491791520aacb4d0d17591
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346622"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Saját üzemeltetésű infravörös kapcsolat konfigurálása az Azure-SSIS ir proxyjaként az Azure Data Factoryban

Ez a cikk ismerteti, hogyan futtatható az SQL Server Integration Services (SSIS) csomagok egy Azure-SSIS-integrációs runtime (Azure-SSIS IR) az Azure Data Factory egy saját üzemeltetésű integrációs futásidejű (saját üzemeltetésű IR) proxyként konfigurált. 

Ezzel a funkcióval anélkül érheti el a helyszíni adatokat, hogy [az Azure-SSIS ir-t egy virtuális hálózathoz kellene csatlakoztatnia.](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) A szolgáltatás akkor hasznos, ha a vállalati hálózat konfigurációja túl összetett, vagy egy olyan házirend, amely túl korlátozó ahhoz, hogy az Azure-SSIS IR-t adja bele.

Ez a szolgáltatás felosztja a helyszíni adatforrással rendelkező SSIS-adatfolyam-feladatokat két átmeneti feladatra: 
* Az első feladat, amely a saját üzemeltetésű infravörös kapcsolaton fut, először áthelyezi az adatokat a helyszíni adatforrásból az Azure Blob-tároló egy átmeneti területre.
* A második feladat, amely az Azure-SSIS IR-en fut, majd áthelyezi az adatokat az átmeneti területről a kívánt adatcélba.

A szolgáltatás egyéb előnyei és képességei lehetővé teszik például a saját üzemeltetésű infravörös szolgáltatás beállítását olyan régiókban, amelyeket még nem támogat az Azure-SSIS ir, és lehetővé teszi a saját üzemeltetésű infravörös kapcsolat nyilvános IP-címét az adatforrások tűzfalán.

## <a name="prepare-the-self-hosted-ir"></a>A saját üzemeltetésű infravörös szolgáltatás előkészítése

A funkció használatához először hozzon létre egy adat-előállító, és hozzon létre egy Azure-SSIS IR benne. Ha még nem tette meg, kövesse az [Azure-SSIS ir beállítása](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)című részben található utasításokat.

Ezután állítsa be a saját üzemeltetésű infravörös ugyanabban az adat-előállítóban, ahol az Azure-SSIS IR van beállítva. Ehhez olvassa el [A saját üzemeltetésű infravörös kapcsolat létrehozása című témakört.](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

Végül töltse le és telepítse a saját üzemeltetésű infravörös szolgáltatás legújabb verzióját, valamint a további illesztőprogramokat és futásidejűeket a helyszíni gépre vagy az Azure virtuális gépre (VM), az alábbiak szerint:
- Töltse le és telepítse a [saját üzemeltetésű infravörös fájl](https://www.microsoft.com/download/details.aspx?id=39717)legújabb verzióját.
- Ha objektumcsatolási és -beágyazási adatbázis (OLEDB) összekötőket használ a csomagokban, töltse le és telepítse a megfelelő OLEDB-illesztőprogramokat ugyanarra a számítógépre, ahol a saját üzemeltetésű infravörös kapcsolat telepítve van, ha még nem tette meg.  

  Ha az OLEDB illesztőprogram korábbi verzióját használja az SQL Server (SQL Server Native Client [SQLNCLI] rendszerhez), [töltse le a 64 bites verziót.](https://www.microsoft.com/download/details.aspx?id=50402)  

  Ha az OLEDB illesztőprogram legújabb verzióját használja az SQL Server (MSOLEDBSQL) rendszerhez, [töltse le a 64 bites verziót.](https://www.microsoft.com/download/details.aspx?id=56730)  
  
  Ha OLEDB illesztőprogramokat használ más adatbázis-rendszerekhez, például a PostgreSQL, a MySQL, az Oracle és így tovább, letöltheti a 64 bites verziókat a webhelyükről.
- Ha még nem tette meg, [töltse le és telepítse a Visual C++ (VC) 64 bites verzióját](https://www.microsoft.com/download/details.aspx?id=40784) ugyanarra a számítógépre, ahol a saját üzemeltetésű infravörös kapcsolat telepítve van.

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Az Azure Blob storage-hoz csatolt szolgáltatás előkészítése az előkészítéshez

Ha még nem tette meg, hozzon létre egy Azure Blob-storage-kapcsolt szolgáltatást ugyanabban az adat-előszobában, ahol az Azure-SSIS IR van beállítva. Ehhez olvassa el [Az Azure-adatokhoz kapcsolt szolgáltatás létrehozása](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)című témakört. Ügyeljen arra, hogy tegye a következőket:
- Az **Adattárban**válassza az **Azure Blob Storage lehetőséget.**  
- Az **integrációs futásidejű csatlakozáshoz**válassza az **AutoResolveIntegrationRuntime** lehetőséget (nem az Azure-SSIS IR-t vagy a saját üzemeltetésű infravörös modult), mert az alapértelmezett Azure IR-t használjuk az Azure Blob Storage hozzáférési hitelesítő adatainak lehívásához.  
- A **Hitelesítési módszer**hez válassza **a Fiókkulcs**, **a SAS URI**vagy a **Szolgáltatásnév lehetőséget.**  

    >[!TIP]
    >Ha az **Egyszerű szolgáltatás** metódust választja, legalább egy *storage Blob-adatközreműködői* szerepkört adjon a szolgáltatásnévnek. További információt az [Azure Blob storage-összekötő című](connector-azure-blob-storage.md#linked-service-properties)dokumentumban talál.

![Az Azure Blob storage-hoz csatolt szolgáltatás előkészítése az előkészítéshez](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>Azure-SSIS ir konfigurálása saját üzemeltetésű infravörös kapcsolattal proxyként

Miután előkészítette a saját üzemeltetésű infravörös és az Azure Blob storage-kapcsolt szolgáltatás átmeneti, most már konfigurálhatja az új vagy meglévő Azure-SSIS IR a saját üzemeltetésű ir, mint egy proxy az adat-előállító portálon vagy alkalmazásban. Mielőtt azonban ezt tenné, ha a meglévő Azure-SSIS-ir már fut, állítsa le, majd indítsa újra.

1. Az **Integrációs futásidejű beállítási** ablaktáblán ugorjon át az **Általános beállítások** és az **SQL-beállítások** szakaszon a **Tovább**gombra kattintva. 

1. A **Speciális beállítások csoportban** tegye a következőket:

   1. Jelölje be a **Saját üzemeltetésű integrációs futásidő beállítása az Azure-SSIS-integrációs futásidejű proxyként** jelölőnégyzetet. 

   1. A **saját üzemeltetésű integrációs futásidejű** legördülő listában válassza ki a meglévő saját üzemeltetésű infravörös az Azure-SSIS IR proxyjaként.

   1. Az **átmeneti tárhoz kapcsolódó szolgáltatás** legördülő listájában válassza ki a meglévő Azure Blob storage-kapcsolt szolgáltatást, vagy hozzon létre egy újat az előkészítéshez.

   1. Az **átmeneti útvonal** mezőben adjon meg egy blobtárolót a kiválasztott Azure Blob-tárfiókban, vagy hagyja üresen az alapértelmezettet az előkészítéshez.

   1. Válassza a **Folytatás** elemet.

   ![Speciális beállítások saját üzemeltetésű infravörös kapcsolattal](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

Az új vagy meglévő Azure-SSIS-ir-t a saját üzemeltetésű infravörös hitelesítési kapcsolatproxyként is konfigurálhatja a PowerShell használatával.

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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Az SSIS-csomagok proxynkénti csatlakozásának engedélyezése

A Visual Studio legújabb SSDT ssis-projektbővítményének vagy egy önálló telepítőnek `ConnectByProxy` a használatával új tulajdonságot találhat az OLEDB vagy a Flat File kapcsolatkezelőkben.
* [Az SSDT SSIS-projektekkel bővítmény letöltése visual studio-hoz](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [Az önálló telepítő letöltése](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

Ha olyan új csomagokat tervez, amelyek OLEDB vagy síkfájl forrásokkal rendelkező adatfolyam-feladatokat tartalmaznak, amelyek lehetővé teszik az adatbázisok vagy fájlok helyszíni elérését, engedélyezheti ezt a tulajdonságot úgy, hogy a megfelelő kapcsolatkezelők **Tulajdonságok** ablaktáblájában *igaz* értékre állítja.

![ConnectByProxy tulajdonság engedélyezése](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Ezt a tulajdonságot meglévő csomagok futtatásakor is engedélyezheti, anélkül, hogy manuálisan kellene módosítania őket egyenként.  Két lehetőség érhető el:
- **A. lehetőség:** Nyissa meg, építse újra és telepítse újra a csomagot tartalmazó projektet a legújabb SSDT-vel az Azure-SSIS IR-en való futtatáshoz. Ezután engedélyezheti a tulajdonságot, ha a megfelelő kapcsolatkezelők számára *true* értékre állítja. Amikor az SSMS-ből származó csomagokat futtatnak, ezek a kapcsolatkezelők megjelennek a **Csomag végrehajtása** előugró ablak **Kapcsolatkezelők** lapján.

  ![ConnectByProxy tulajdonság engedélyezése2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, hogy a Data Factory-folyamatokban csomagok futtatásakor a megfelelő kapcsolatkezelők számára *igaz* értékre állítja az [Okates-csomagok](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) végrehajtása során megjelenő **Kapcsolatkezelők** lapon megjelenő megfelelő kapcsolatkezelők számára.
  
  ![ConnectByProxy tulajdonság engedélyezése3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **B. lehetőség:** Telepítse újra az ezeket a csomagokat tartalmazó projektet az SSIS ir-n való futtatáshoz. Ezután engedélyezheti a tulajdonságot a tulajdonság `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`elérési útjának megadásával, és tulajdonságfelülbírálásként *True* állításával a Csomag **végrehajtása** előugró ablak **Speciális** lapján, amikor az SSMS-ből származó csomagokat futtat.

  ![ConnectByProxy tulajdonság engedélyezése4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  A tulajdonságot úgy is engedélyezheti, hogy `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`megadja a tulajdonság elérési útját, és a Data [Execute SSIS Package activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) Factory-folyamatokban lévő csomagok futtatásakor a **Tulajdonságfelülbírálások** lapon tulajdonságfelülbírálásként *True* true értékre állítja.
  
  ![ConnectByProxy tulajdonság engedélyezése5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Az első és a második átmeneti feladat hibakeresése

A saját üzemeltetésű infravörös kapcsolaton a *C:\ProgramData\SSISTelemetry* mappában található futásidejű naplók, valamint a *C:\ProgramData\SSISTelemetry\ExecutionLog* mappában található első átmeneti feladatok végrehajtási naplói.  A második átmeneti feladatok végrehajtási naplóit az SSISDB-ben vagy a megadott naplózási útvonalakban találja, attól függően, hogy a csomagokat SSISDB-ben vagy fájlrendszerben, fájlmegosztásokban vagy Azure Files-ban tárolja. Az első átmeneti feladatok egyedi azonosítóit a második átmeneti feladatok végrehajtási naplóiban is megtalálhatja. 

![Az első átmeneti feladat egyedi azonosítója](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>Windows-hitelesítés használata átmeneti feladatokban

Ha a saját üzemeltetésű infravörös kapcsolaton végzett átmeneti feladatok hoznak windowsos hitelesítést, [konfigurálja úgy az SSIS-csomagokat, hogy ugyanazt a Windows-hitelesítést használják.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15) 

Az átmeneti feladatok meghívása a saját üzemeltetésű infravörös szolgáltatásfiókkal *(nt SERVICE\DIAHostService,* alapértelmezés szerint) történik, és az adattárak a Windows hitelesítési fiókkal lesznek elérhetők. Mindkét fiókhoz bizonyos biztonsági házirendek hozzárendelése szükséges. A saját üzemeltetésű infravörös számítógépen nyissa meg a Helyi biztonsági házirend > **helyi** > **házirendek felhasználói jogok hozzárendelése**című webhelyet, majd tegye a következőket: **Local Security Policy**

1. Rendelje hozzá a *Memóriakvóták beállítása folyamathoz,* és *cserélje le a folyamatszintű tokenházirendeket* a saját üzemeltetésű infravörös szolgáltatásfiókhoz. Ez automatikusan megtörténik, ha telepíti a saját üzemeltetésű infravörös az alapértelmezett szolgáltatásfiókkal. Ha nem, rendelje hozzá ezeket a házirendeket manuálisan. Ha másik szolgáltatásfiókot használ, rendelje hozzá ugyanazokat a házirendeket.

1. Rendelje hozzá *a Bejelentkezés szolgáltatásházirendként* szolgáltatást a Windows hitelesítési fiókhoz.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Az első és a második átmeneti feladat számlázása

A saját üzemeltetésű infravörös kapcsolaton futó első átmeneti feladatokat külön számlázunk, ahogy a saját üzemeltetésű infravörös kapcsolaton futó adatmozgatási tevékenységeket is. Ez az Azure [Data Factory adatfolyamat díjszabási](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) cikkben van megadva.

Az Azure-SSIS IR-en futó második átmeneti feladatokat nem külön számlázunk, de a futó Azure-SSIS-ir-t az [Azure-SSIS ir díjszabási](https://azure.microsoft.com/pricing/details/data-factory/ssis/) cikkben megadott módon számlázunk.

## <a name="enabling-tls-12"></a>A TLS 1.2 engedélyezése

Ha erős titkosítást/biztonságosabb hálózati protokollt (TLS 1.2) kell használnia, és le kell tiltania a régebbi SSL/TLS-verziókat a saját üzemeltetésű infravörös kapcsolaton, letöltheti és futtathatja a nyilvános előzetes verziótároló *CustomSetupScript/UserScenarios/TLS 1.2* mappájában található *main.cmd* parancsfájlt.  Az [Azure Storage Explorer](https://storageexplorer.com/)használatával a következő SAS-URI megadásával csatlakozhat nyilvános előzetes verziós tárolónkhoz:

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>Aktuális korlátozások

- Jelenleg csak az ODBC (OPENBC)/OLEDB/Flat File forrásokkal rendelkező adatfolyam-feladatok támogatottak. 
- Jelenleg csak a *Fiókkulccsal,* *a Megosztott hozzáférés-aláírás (SAS) URI-val*vagy az egyszerű *szolgáltatáshitelesítéssel* konfigurált Azure Blob-tárolóhoz kapcsolódó szolgáltatások támogatottak.
- Az OLEDB-forrás *parametermapping-ja* még nem támogatott. Kerülő megoldásként használja az *SQL Command From Variable* metódust *AccessMode-ként,* és a *Kifejezés* segítségével szúrja be a változókat/paramétereket egy SQL parancsba. Szemléltetésképpen tekintse meg a *ParameterMappingSample.dtsx* csomagot, amely a nyilvános előzetes verziótároló *SelfHostedIRProxy/Limitations* mappájában található. Az Azure Storage Explorer használatával a fenti SAS URI-kiszolgáló megadásával csatlakozhat a nyilvános előzetes verziós tárolóhoz.

## <a name="next-steps"></a>További lépések

Miután konfigurálta a saját üzemeltetésű infravörös tanúsítványát az Azure-SSIS ir proxyjaként, üzembe helyezheti és futtathatja a csomagokat a helyszíni adatok eléréséhez SSIS-csomagtevékenységek végrehajtása a Data Factory-folyamatokban. Ennek módjáról az [SSIS-csomagok futtatása SSIS-csomagtevékenységek végrehajtása a Data Factory folyamatokban című témakörben olvashat.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
