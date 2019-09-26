---
title: Azure Service Fabric patch-előkészítési alkalmazás | Microsoft Docs
description: Alkalmazás az operációs rendszer javításának automatizálásához Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289424"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>A Windows operációs rendszer javítása a Service Fabric-fürtben

> 
> [!IMPORTANT]
> Az alkalmazás 1,2. * verziója nem támogatja a támogatást a 2019. április 30-ig. Frissítsen a legújabb verzióra.


Az Azure-beli [virtuálisgép-méretezési csoport automatikus operációsrendszer-rendszerképének frissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) az ajánlott eljárás az operációs rendszerek az Azure-ban való javításához, és a patch-előkészítési alkalmazás (Poa) a Service Fabric RepairManager Systems szolgáltatásának olyan burkolója, amely a következő szolgáltatásokat nyújtja: a nem Azure-beli üzemeltetett fürtök konfiguráció-alapú operációsrendszer-javításának ütemezését teszi lehetővé. A nem Azure-beli üzemeltetett fürtökhöz nem szükséges a POA, de a frissítési tartományok frissítésének ütemezését frissíteni kell, Service Fabric-fürtök leállása nélkül.

A POA egy Azure Service Fabric alkalmazás, amely leállás nélkül automatizálja az operációs rendszer javítását egy Service Fabric-fürtön.

A javítási előkészítési alkalmazás a következő funkciókat biztosítja:

- Az **operációs rendszer automatikus frissítésének telepítése**. Az operációs rendszer frissítéseinek letöltése és telepítése automatikusan megtörténik. A fürtcsomópontok a fürt leállása nélkül, igény szerint újraindulnak.

- **Fürtöket támogató javítások és állapot-integráció**. A frissítések alkalmazása közben a javítási előkészítési alkalmazás figyeli a fürtcsomópontok állapotát. A fürtcsomópontok egyszerre egy csomópontot vagy egyszerre egy frissítési tartományt frissítenek. Ha a fürt állapota a javítási folyamat miatt leáll, a javítás leáll a probléma súlyosbodása érdekében.

## <a name="internal-details-of-the-app"></a>Az alkalmazás belső adatai

A patch-előkészítési alkalmazás a következő alösszetevőkből áll:

- **Koordinátori szolgáltatás**: Ez az állapot-nyilvántartó szolgáltatás a következőkért felelős:
    - A Windows Update feladatok koordinálása a teljes fürtön.
    - A befejezett Windows Update műveletek eredményének tárolása.
- **Csomópont-ügynök szolgáltatás**: Ez az állapot nélküli szolgáltatás minden Service Fabric fürtcsomóponton fut. A szolgáltatás feladatai a következők:
    - A csomópont-ügynök NTService elindításához.
    - A csomópont-ügynök NTService figyelése.
- **Csomópont-ügynök NTService**: Ez a Windows NT-szolgáltatás magasabb szintű jogosultsággal (rendszer) fut. Ezzel szemben a Node Agent szolgáltatás és a koordinátor szolgáltatás alacsonyabb szintű jogosultsággal (hálózati szolgáltatás) fut. A szolgáltatás felelős a következő Windows Update feladatok végrehajtásához az összes fürtcsomóponton:
    - Az automatikus Windows Update letiltása a csomóponton.
    - Windows Update letöltése és telepítése a felhasználó által megadott szabályzatnak megfelelően.
    - A gép újraindítása Windows Update telepítés után.
    - A Windows-frissítések eredményeinek feltöltése a koordinátori szolgáltatásba.
    - Jelentések jelentése, ha egy művelet meghiúsult az újrapróbálkozások kimerülése után.

> [!NOTE]
> A patch-előkészítési alkalmazás a Service Fabric Repair Manager rendszerszolgáltatást használja a csomópont letiltásához vagy engedélyezéséhez, illetve az állapot-ellenőrzés elvégzéséhez. A javítás-előkészítési alkalmazás által létrehozott javítási feladat nyomon követi az egyes csomópontok Windows Update folyamatát.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A .NET-keretrendszer minimálisan szükséges verziója 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A Repair Manager szolgáltatás engedélyezése (ha már nem fut)

A javítás-előkészítési alkalmazáshoz a Repair Manager rendszerszolgáltatásának engedélyezése szükséges a fürtön.

#### <a name="azure-clusters"></a>Azure-fürtök

Az ezüst tartóssági szinten található Azure-fürtökön alapértelmezés szerint engedélyezve van a Repair Manager szolgáltatás. Az arany tartóssági szinten lévő Azure-fürtök esetében előfordulhat, hogy a Repair Manager szolgáltatás engedélyezve van, attól függően, hogy mikor hozták létre a fürtöket. A bronz tartóssági szinten lévő Azure-fürtök alapértelmezés szerint nem rendelkeznek engedélyezve a Repair Manager szolgáltatással. Ha a szolgáltatás már engedélyezve van, a rendszer a Service Fabric Explorer rendszerszolgáltatások szakaszában tekintheti meg.

##### <a name="azure-portal"></a>Azure Portal
A Repair Manager Azure Portal a fürt beállításakor is engedélyezhető. Jelölje be a **javításkezelő belefoglalása** lehetőséget a fürt konfigurálásának időpontjában a **kiegészítő funkciók** területen.
![A javításkezelő engedélyezésének képe Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager üzembe helyezési modell
Azt is megteheti, hogy az [Azure Resource Manager üzembe helyezési modell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) segítségével engedélyezi a Repair Manager szolgáltatást az új és a meglévő Service Fabric fürtökön. Szerezze be a telepíteni kívánt fürt sablonját. Használhatja a minta sablonokat, vagy létrehozhat egy egyéni Azure Resource Manager telepítési modell sablonját. 

A Repair Manager szolgáltatás engedélyezése [Azure Resource Manager telepítési modell sablon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)használatával:

1. Először győződjön `apiversion` `Microsoft.ServiceFabric/clusters` meg arról, hogy az az erőforráshoz van beállítva. `2017-07-01-preview` Ha ez különbözik, akkor frissítenie kell a `apiVersion` értéket a értékre `2017-07-01-preview` vagy magasabbra:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most engedélyezze a Repair Manager szolgáltatást úgy, hogy hozzáadja `addonFeatures` a következő szakaszt `fabricSettings` a szakasz után:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Miután frissítette a fürtözött sablont ezekkel a módosításokkal, alkalmazza őket, és hagyja befejezni a frissítést. Most már megtekintheti a fürtben futó Repair Manager rendszerszolgáltatást. A rendszer a `fabric:/System/RepairManagerService` Service Fabric Explorer rendszerszolgáltatások szakaszában hívja meg. 

### <a name="standalone-on-premises-clusters"></a>Önálló helyszíni fürtök

Az [önálló Windows-fürt konfigurációs beállításait](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) használhatja a Repair Manager szolgáltatás új és meglévő Service Fabric-fürtön való engedélyezéséhez.

A Repair Manager szolgáltatás engedélyezése:

1. Először győződjön meg arról `apiversion` , hogy a [fürt általános konfigurációi](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) beállítás `04-2017` értéke vagy magasabb:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. A Repair Manager szolgáltatás engedélyezéséhez adja hozzá a `addonFeatures` következő szakaszt az `fabricSettings` alább látható szakasz után:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Frissítse a fürt jegyzékfájlját ezekkel a módosításokkal, a frissített fürt jegyzékfájljának használatával [hozzon létre egy új fürtöt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) , vagy [frissítse a fürtkonfiguráció beállításait](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). Miután a fürt frissítette a fürtszolgáltatást, megtekintheti a fürtben futó Repair Manager rendszerszolgáltatást, amely neve `fabric:/System/RepairManagerService`a Service Fabric Explorer rendszerszolgáltatások szakaszában található.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-frissítések konfigurálása az összes csomóponthoz

Az automatikus Windows-frissítések a rendelkezésre állás elvesztéséhez vezethetnek, mert több fürtcsomópont is újraindulhat egyszerre. A javítási előkészítési alkalmazás alapértelmezés szerint megkísérli letiltani az automatikus Windows Update az egyes fürtcsomópontokon. Ha azonban a beállításokat rendszergazda vagy Csoportházirend felügyeli, javasoljuk, hogy a Windows Update szabályzatot explicit módon "értesítés a letöltés előtt" beállítással állítsa be.

## <a name="download-the-app-package"></a>Az alkalmazáscsomag letöltése

Az alkalmazáscsomag letöltéséhez látogasson el a patch-előkészítési alkalmazás GitHub kiadási [oldalára](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) .

## <a name="configure-the-app"></a>Az alkalmazás konfigurálása

A javítási előkészítési alkalmazás viselkedését az igényeinek megfelelően konfigurálhatja. Az alapértelmezett értékek felülbírálásához adja meg az alkalmazás paraméterét az alkalmazás létrehozása vagy frissítése során. Az alkalmazás paramétereinek megadásához meg kell `ApplicationParameter` adni a `Start-ServiceFabricApplicationUpgrade` vagy `New-ServiceFabricApplication` a parancsmagot.

|**A paraméter**        |**Típus**                          | **Részletek**|
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | A gyorsítótárazható Windows Update eredmények maximális száma. <br>Az alapértelmezett érték 3000, feltételezve, hogy: <br> – A csomópontok száma 20. <br> – A csomópontok által havonta megjelenő frissítések száma öt. <br> – Az eredmények száma legfeljebb 10 lehet. <br> – Az elmúlt három hónap eredményeit tárolni kell. |
|TaskApprovalPolicy   |Felsorolás <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy azt a házirendet jelzi, amelyet a koordinátori szolgáltatás a Windows-frissítések a Service Fabric-fürt csomópontjain való telepítéséhez használ.<br>                         Engedélyezett értékek a következők: <br>                                                           <b>NodeWise</b>. Windows Update egyszerre egy csomópontot telepített. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update egyszerre egy frissítési tartományt telepít. (Legfeljebb a frissítési tartományhoz tartozó összes csomópont Windows Update.)<br> Tekintse meg a [Gyakori kérdések](#frequently-asked-questions) című szakaszt, amelyből eldöntheti, hogy melyik a legmegfelelőbb a fürthöz.
|LogsDiskQuotaInMB   |Hosszú  <br> Alapértelmezett 1024)               |A patch-előkészítési alkalmazások naplófájljainak maximális mérete MB-ban, ami helyileg tárolható a csomópontokon.
| WUQuery               | Karakterlánc<br>Alapértelmezett "IsInstalled = 0")                | Lekérdezés a Windows-frissítések beszerzéséhez. További információ: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logikai <br> (alapértelmezett: hamis)                 | Ezzel a jelzővel szabályozhatja, hogy mely frissítéseket kell letölteni és telepíteni. A következő értékek engedélyezettek <br>True – csak a Windows operációs rendszer frissítéseinek telepítése.<br>false (hamis) – telepíti az összes elérhető frissítést a gépre.          |
| WUOperationTimeOutInMinutes | Int <br>Alapértelmezett 90)                   | Megadja a Windows Update művelet időtúllépését (keresés vagy letöltés vagy telepítés). Ha a művelet nem fejeződött be a megadott időkorláton belül, a rendszer megszakítja a műveletet.       |
| WURescheduleCount     | Int <br> Alapértelmezett 5)                  | A szolgáltatás által a Windows Update újraütemezett maximális száma, ha egy művelet tartósan leáll.          |
| WURescheduleTimeInMinutes | Int <br>Alapértelmezett 30) | Az az időtartam, amikor a szolgáltatás átütemezze a Windows Update szolgáltatást, ha a hiba továbbra is fennáll. |
| WUFrequency           | Vesszővel tagolt karakterlánc (alapértelmezett: "Weekly, szerda, 7:00:00")     | A Windows Update telepítésének gyakorisága. A formátum és a lehetséges értékek a következők: <br>-Havonta, DD, óó: PP: MM, például: havi, 5, 12:22:32.<br>A DD (nap) mező megengedett értékei a 1-28 és a "Last" tartomány közötti számok. <br> -Hetente, naponta, óó: PP: MM, például hetente, kedd, 12:22:32.  <br> – Naponta, óó: PP: MM, például napi, 12:22:32.  <br> – A none érték azt jelzi, hogy Windows Update nem kell elvégezni.  <br><br> Vegye figyelembe, hogy az időpontok UTC-ben vannak.|
| AcceptWindowsUpdateEula | Logikai <br>(Alapértelmezett: true) | A jelző beállításával az alkalmazás elfogadja a számítógép tulajdonosának nevében Windows Update végfelhasználói licencszerződést.              |

> [!TIP]
> Ha azt szeretné, hogy a Windows Update azonnal történjen `WUFrequency` , állítsa az alkalmazás központi telepítésének időpontjához viszonyítva. Tegyük fel például, hogy rendelkezik egy öt csomópontos tesztelési fürttel, és tervezi az alkalmazás üzembe helyezését a 5:00 PM UTC-kor. Ha azt feltételezi, hogy az alkalmazás frissítése vagy üzembe helyezése legfeljebb 30 percet vesz igénybe, állítsa a WUFrequency a "napi, 17:30:00" értékre.

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

1. Fejezze be az összes előfeltételként szükséges lépést a fürt előkészítéséhez.
2. Telepítse a javítási előkészítési alkalmazást, mint bármely más Service Fabric alkalmazást. Az alkalmazást a PowerShell használatával helyezheti üzembe. Kövesse az [alkalmazások telepítése és eltávolítása a PowerShell használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)című témakör lépéseit.
3. Az alkalmazás az üzembe helyezés időpontjában történő konfigurálásához adja `ApplicationParameter` át a `New-ServiceFabricApplication` parancsot a parancsmagnak. Az Ön kényelme érdekében a. ps1 parancsfájlt az alkalmazással együtt adtuk meg. A szkript használata:

    - Kapcsolódjon Service Fabric-fürthöz a `Connect-ServiceFabricCluster`használatával.
    - Futtassa a. ps1 PowerShell-parancsfájlt a megfelelő `ApplicationParameter` értékkel.

> [!NOTE]
> Tartsa meg a parancsfájlt és az alkalmazás mappáját a PatchOrchestrationApplication ugyanabban a címtárban.

## <a name="upgrade-the-app"></a>Az alkalmazás frissítése

Ha egy meglévő patch-előkészítési alkalmazást a PowerShell használatával szeretne frissíteni, kövesse az [alkalmazás frissítése Service Fabric a PowerShell használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)című témakör lépéseit.

## <a name="remove-the-app"></a>Az alkalmazás eltávolítása

Az alkalmazás eltávolításához kövesse az [alkalmazások telepítése és eltávolítása a PowerShell használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)című témakör lépéseit.

Az Ön kényelme érdekében az alkalmazással együtt megadták a szkriptet a Deploy. ps1 parancsfájllal. A szkript használata:

  - Kapcsolódjon Service Fabric-fürthöz a ```Connect-ServiceFabricCluster```használatával.

  - Futtassa a PowerShell-szkriptet a Deploy. ps1 eszközön.

> [!NOTE]
> Tartsa meg a parancsfájlt és az alkalmazás mappáját a PatchOrchestrationApplication ugyanabban a címtárban.

## <a name="view-the-windows-update-results"></a>A Windows Update eredményeinek megtekintése

A patch-előkészítési alkalmazás a REST API-kat teszi elérhetővé a felhasználók korábbi eredményeinek megjelenítéséhez. Példa az eredmény JSON-ra:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

A JSON mezői az alábbiakban olvashatók.

Mező | Értékek | Részletek
-- | -- | --
OperationResult | 0 – sikeres<br> 1 – sikeres hibák<br> 2 – sikertelen<br> 3 – megszakítva<br> 4 – időtúllépés miatt megszakítva | A teljes művelet eredményét jelzi (általában egy vagy több frissítés telepítését is beleértve).
resultCode | Ugyanaz, mint a Operationresult tevékenységen | Ebben a mezőben egy egyedi frissítés telepítési műveletének eredménye látható.
OperationType | 1 – telepítés<br> 0 – keresés és letöltés.| A telepítés az egyetlen olyan OperationType, amely alapértelmezés szerint az eredményekben jelenik meg.
WindowsUpdateQuery | Az alapértelmezett érték a "IsInstalled = 0" |A frissítések kereséséhez használt Windows Update-lekérdezés. További információ: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | igaz – újraindítás szükséges<br> hamis – újraindítás nem szükséges | Azt jelzi, hogy szükséges-e újraindítás a frissítések telepítésének befejezéséhez.
OperationStartTime | DateTime | A művelet (Letöltés/telepítés) elindításának időpontját jelzi.
OperationTime | DateTime | Azt jelzi, hogy mikor fejeződött be a művelet (Letöltés/telepítés).
HResult | 0 – sikeres<br> egyéb – hiba| A "7392acaf-6a85-427c-8a8d-058c25beb0d6" updateID rendelkező Windows Update hibájának okát jelzi.

Ha még nincs ütemezve frissítés, az eredmény JSON üres.

Windows Update eredmények lekérdezéséhez jelentkezzen be a fürtbe. Ezután keresse meg a koordinátori szolgáltatás elsődleges adatreplikájának címét, és nyomja le az URL-címet a böngészőből&lt;: http://replika&gt;-&lt;IP&gt;: ApplicationPort/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

A koordinátori szolgáltatás REST-végpontja dinamikus porttal rendelkezik. A pontos URL-cím megadásához tekintse meg a Service Fabric Explorer. Az eredmények például a következő címen érhetők el `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`:.

![REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Ha a fordított proxy engedélyezve van a fürtön, akkor a fürtön kívülről is elérheti az URL-címet.
A http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.&gt;

Ha engedélyezni szeretné a fordított proxyt a fürtön, kövesse a [fordított proxy lépéseit az Azure Service Fabricban](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> A fordított proxy konfigurálása után a fürtben lévő összes olyan Micro-szolgáltatás, amely egy HTTP-végpontot tesz elérhetővé, a fürtön kívülről címezhető.

## <a name="diagnosticshealth-events"></a>Diagnosztika/állapotadatok

A következő szakasz azt ismerteti, hogyan lehet hibakeresési/diagnosztizálni a javításokat a javítások és a javítások összekapcsolásával Service Fabric fürtökön.

> [!NOTE]
> A 1.4.0-nek telepítve kell lennie a POA-nak.

A NodeAgentNTService a frissítések telepítésére szolgáló [javítási feladatokat](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) hoz létre a csomópontokon. Ezután a CoordinatorService a feladat-jóváhagyási házirend szerint készíti el a feladatokat. Az előkészített feladatokat a javításkezelő véglegesen jóváhagyja, amely nem hagy jóvá semmilyen feladatot, ha a fürt nem megfelelő állapotú. A go lépésről lépésre megtudhatja, hogy a frissítések hogyan folytatódnak a csomópontokon.

1. A NodeAgentNTService minden csomóponton fut, és a rendelkezésre álló Windows Update az ütemezett időpontban keresi. Ha vannak elérhető frissítések, akkor a rendszer a csomóponton tölti le őket.
2. A frissítések letöltése után a NodeAgentNTService a POS___ < unique_id > nevű csomóponthoz tartozó javítási feladatot hoz létre. Ezeket a javítási feladatokat megtekintheti a [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) vagy az SFX-parancsmag használatával a csomópont részletei szakaszban. A javítási feladat létrehozása után gyorsan áthelyezi az [igényelt állapotba](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. A koordinátori szolgáltatás rendszeresen megkeresi az igényelt állapotú javítási feladatokat, és előre frissíti őket, hogy a TaskApprovalPolicy alapján előkészítse az állapotot. Ha a TaskApprovalPolicy úgy van konfigurálva, hogy NodeWise, a csomópontnak megfelelő javítási feladat csak akkor készüljön fel, ha nincs más javítási feladat, amely jelenleg előkészítési/jóváhagyási/végrehajtási/visszaállítási állapotban van. Hasonlóképpen, ha a UpgradeWise TaskApprovalPolicy, azt minden olyan ponton biztosítani kell, hogy a fenti állapotok csak ugyanahhoz a frissítési tartományhoz tartozó csomópontok esetén legyenek feladataik. Ha a javítási feladatot áthelyezik az előkészítési állapotba, a megfelelő Service Fabric csomópontot a rendszer a szándék szerint [letiltotta](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) a "restart" utasítással.

   A POA (v 1.4.0 és újabb verziók) az "ClusterPatchingStatus" tulajdonsággal rendelkező eseményeket a CoordinaterService-on jeleníti meg a javított csomópontok megjelenítéséhez. Az alábbi képen látható, hogy a frissítések telepítése a _poanode_0:

    [![A fürt javítási állapotának képe](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. Ha a csomópont le van tiltva, a javítási feladat végrehajtva állapotba kerül.
   
   >[!NOTE]
   > Egy letiltott állapotú csomópont blokkolhat egy új javítási feladatot, amely leállítja a javítás műveletét a fürtön.

5. Ha a javítási feladat végrehajtása folyamatban van, a rendszer megkezdi a javítás telepítését a csomóponton. Itt a javítás telepítése után a (z) csomópont a javítástól függően esetleg nem indítható újra. A javítási feladat áthelyezése a visszaállítási állapotba, amely lehetővé teszi a csomópont újbóli visszaadását, majd befejezettként van megjelölve.

   A v 1.4.0 és az alkalmazás korábbi verzióiban a frissítés állapota a "WUOperationStatus-[csomópontnév]" tulajdonsággal megtekinthető a NodeAgentService állapotú eseményeinek megtekintésével. Az alábbi képek Kiemelt fejezetei a Windows Update állapotát mutatják be a "poanode_0" és a "poanode_2" csomóponton:

   [![A Windows Update művelet állapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![A Windows Update művelet állapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Az egyik a PowerShell használatával is beolvashatja a részleteket a fürthöz való csatlakozással és a javítási feladat állapotának beolvasásával a [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)használatával. Az alábbi példához hasonlóan látható, hogy a "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" feladat DownloadComplete állapotban van. Ez azt jelenti, hogy a frissítések le lettek töltve a "poanode_2" csomóponton, és a telepítés után a rendszer megkísérli, hogy a feladat végrehajtsa az állapotot.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Ha még többet talál, jelentkezzen be adott virtuális gépre/virtuális gépekre, és keresse fel a problémát a Windows-eseménynaplók használatával. A fent említett javítási feladat csak a következő végrehajtói alállapotokat veheti fel:

      ExecutorSubState | Részletek
    -- | -- 
      None=1 |  Azt jelenti, hogy nem volt folyamatban művelet a csomóponton. Lehetséges állapot-átváltások.
      DownloadCompleted=2 | Azt jelenti, hogy a letöltési művelet sikeres, részleges meghibásodás vagy hiba miatt befejeződött.
      InstallationApproved = 3 | Azt jelenti, hogy a letöltési művelet korábban fejeződött be, és javításkezelő jóváhagyta a telepítést.
      InstallationInProgress=4 | Megfelel a javítási feladat végrehajtásának állapotának.
      InstallationCompleted=5 | Azt jelenti, hogy a telepítés sikeres, részleges sikerrel vagy hibával fejeződött be.
      RestartRequested=6 | Azt jelenti, hogy a javítás telepítése befejeződött, és a csomóponton van függőben lévő újraindítási művelet.
      RestartNotNeeded=7 |  Azt jelenti, hogy a javítás telepítésének befejezése után nem volt szükség újraindításra.
      RestartCompleted=8 | Azt jelenti, hogy az újraindítás sikeresen befejeződött.
      OperationCompleted=9 | A Windows Update művelet sikeresen befejeződött.
      OperationAborted=10 | Azt jelenti, hogy a Windows Update művelet meg lett szakítva.

6. Az alkalmazás v 1.4.0 és újabb verziójában a csomóponton végzett frissítési kísérlet során a rendszer egy "WUOperationStatus-[csomópontnév]" tulajdonsággal rendelkező eseményt küld a NodeAgentService, hogy értesítést kapjon, amikor a következő kísérlet után letölti és telepíti a frissítést, indítsa el. Tekintse meg az alábbi ábrát:

     [![A Windows Update művelet állapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

A javítási előkészítési alkalmazások naplói a Service Fabric futtatókörnyezeti naplók részeként lesznek összegyűjtve.

Ha a naplókat az Ön által választott diagnosztikai eszközön vagy folyamaton keresztül szeretné rögzíteni. A patch-előkészítési alkalmazás a rögzített szolgáltatói azonosítók használatával naplózza az eseményeket az [esemény forrásán](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1) keresztül

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Állapotjelentés

A patch-összehangoló alkalmazás a következő esetekben is közzéteszi a koordinátori szolgáltatás vagy a csomópont-ügynök szolgáltatás állapotát:

#### <a name="the-node-agent-ntservice-is-down"></a>A csomópont-ügynök NTService nem érhető el

Ha a csomópont-ügynök NTService leállt egy csomóponton, a rendszer figyelmeztetési szintű állapotjelentést hoz létre a csomópont-ügynök szolgáltatással szemben.

#### <a name="the-repair-manager-service-is-not-enabled"></a>A Repair Manager szolgáltatás nincs engedélyezve

Ha a Repair Manager szolgáltatás nem található a fürtön, a rendszer figyelmeztetési szintű állapotjelentést hoz létre a koordinátori szolgáltatáshoz.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Miért jelenik meg a fürt hibás állapotban, amikor a javítás-előkészítési alkalmazás fut?**

A. A telepítési folyamat során a javítási előkészítési alkalmazás letiltja vagy újraindítja a csomópontokat, ami átmenetileg eredményezheti a fürt állapotát lefelé haladva.

Az alkalmazás házirendje alapján az egyik csomópont leállhat a javítási művelet során, *vagy* egy teljes frissítési tartomány lelassulhat.

A Windows Update telepítésének végére a csomópontok újraindítást követően újraindulnak.

A következő példában a fürt átmenetileg hibás állapotba került, mert két csomópont leállt, és a MaxPercentageUnhealthyNodes szabályzat megsértése megsértette. A hiba ideiglenes, amíg a javítási művelet folyamatban van.

![Nem kifogástalan állapotú fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma továbbra is fennáll, tekintse meg a hibaelhárítás szakaszt.

K. **A javítási előkészítési alkalmazás figyelmeztetési állapotban van**

A. Ellenőrizze, hogy az alkalmazásban közzétett állapotjelentés az alapvető ok-e. A figyelmeztetés általában a probléma részleteit tartalmazza. Ha a probléma átmeneti, az alkalmazásnak az állapotból való automatikus helyreállítást kell végeznie.

K. **Mi a teendő, ha a fürtem nem kifogástalan állapotú, és sürgős operációs rendszerre van szükségem?**

A. A javítási előkészítési alkalmazás nem telepíti a frissítéseket, amíg a fürt állapota nem megfelelő. Próbálja meg a fürtöt Kifogástalan állapotba hozni, hogy feloldja a javítás-előkészítési alkalmazás munkafolyamatát.

K. **Be kell állítani a TaskApprovalPolicy "NodeWise" vagy "UpgradeDomainWise" értékre a fürthöz?**

A. A "UpgradeDomainWise" lehetővé teszi, hogy a fürt teljes javítását gyorsabban, a frissítési tartományba tartozó összes csomópontot párhuzamosan javítja. Ez azt jelenti, hogy a teljes frissítési tartományhoz tartozó csomópontok nem érhetők el ( [letiltott](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) állapotban) a javítási folyamat során.

Ezzel szemben a "NodeWise" házirend egyszerre csak egy csomópontot javít, ez azt jelenti, hogy a fürt teljes javítása hosszabb időt is igénybe vett. Max esetében azonban a javítási folyamat során csak egy csomópont elérhetetlenné válik ( [letiltott](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) állapotban).

Ha a fürtön a javítási ciklusban N-1 számú frissítési tartomány fut (ahol N a fürt frissítési tartományának teljes száma), akkor a szabályzatot "UpgradeDomainWise" értékként állíthatja be, máskülönben a "NodeWise" értéket állíthatja be.

K. **Mennyi ideig tart a csomópontok javítása?**

A. A csomópontok javítása néhány percet is igénybe vehet (például: [Windows Defender-definíció frissítései](https://www.microsoft.com/en-us/wdsi/definitions)) órákig (például: [Windows kumulatív frissítések](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). A csomópontok javításához szükséges idő főleg a következőtől függ 
 - A frissítések mérete
 - Frissítések száma, amelyeket egy javítási ablakban kell alkalmazni
 - A frissítések telepítéséhez szükséges idő, indítsa újra a csomópontot (ha szükséges), és fejezze be a telepítés utáni lépéseket.
 - A virtuális gép/számítógép és a hálózati feltételek teljesítménye.

K. **Mennyi ideig tart a teljes fürt javítása?**

A. A teljes fürt javításához szükséges idő a következő tényezőktől függ:

- A csomópontok javításához szükséges idő.
- A koordinátori szolgáltatás szabályzata. – Az alapértelmezett házirend, `NodeWise`amely egyszerre csak egy csomópont javítását eredményezi, ami `UpgradeDomainWise`lassabb lehet. Példa: Ha egy csomóponton ~ 1 óra kerül javításra, egy 20 csomópontot (azonos típusú csomópontot) tartalmazó fürtöt kell feljavítani 5 frissítési tartománnyal, amelyek mindegyike 4 csomópontot tartalmaz.
    - A teljes fürt javításához ~ 20 órát kell igénybe venni, ha a szabályzat`NodeWise`
    - Ha a szabályzat`UpgradeDomainWise`
- Fürt terhelése – az egyes javítási műveletekhez szükséges, hogy az ügyfelek számítási feladatait a fürt más elérhető csomópontjaira helyezze át. A javítás alatt lévő csomópont letiltási állapotban lenne ebben az időszakban. [](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) Ha a fürt a csúcs terhelése közelében fut, a letiltási folyamat hosszabb időt is igénybe veheti. Ennek megfelelően előfordulhat, hogy az általános javítási folyamat lassúnak tűnhet az ilyen Kiemelt körülmények között.
- A fürtök állapotával kapcsolatos hibák javítás közben [](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) – [a fürt állapotának](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) romlása megszakítja a javítási folyamatot. Ez a teljes fürt javításához szükséges teljes időt adja hozzá.

K. **Miért látok néhány frissítést a Windows Update REST API-on keresztül kapott eredmények között, de nem a gép Windows Update előzményeiben?**

A. Egyes termékek frissítései csak a megfelelő frissítési/javítási előzményekben jelennek meg. Előfordulhat például, hogy a Windows Defender frissítései nem jelennek meg Windows Update előzményekben a Windows Server 2016-ben.

K. **Javítható az összehangoló alkalmazás a fejlesztői fürt (egy csomópontos fürt) javításához?**

A. Nem, a patch-előkészítési alkalmazás nem használható egy csomópontos fürt kijavításához. Ez a korlátozás úgy van kialakítva, mint a [Service Fabric rendszerszolgáltatások](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) vagy bármely ügyfél-alkalmazás, és így a javítási feladatok nem lesznek jóváhagyva a Repair Manager használatával.

K. **Hogyan a Linuxon futó fürtcsomópontok javítása?**

A. Tekintse meg az Azure-beli [virtuálisgép-méretezési csoport automatikus operációsrendszer-rendszerképének frissítése](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) című témakört a Linux rendszerű frissítések előkészítéséhez

K.**miért tart ilyen sokáig a frissítési ciklus?**

A. Lekérdezés az eredmény JSON-hez, majd az összes csomópont frissítési ciklusának beléptetése, majd a OperationStartTime és a OperationTime (OperationCompletionTime) használatával próbálja meg megkeresni a frissítés telepítésének idejét az összes csomóponton. Ha nagy idő van az ablakban, amelyben a frissítés nem történt meg, annak oka az lehet, hogy a fürt hibás állapotban volt, és a javítási vezető nem hagyta jóvá a további POA javítási feladatokat. Ha a frissítés telepítése bármely csomóponton sokáig tartott, akkor lehetséges, hogy a csomópontot nem frissítették hosszú időre, és sok frissítés lett függőben, ami időbe telt. Az is előfordulhat, hogy egy csomóponton a javítások blokkolása azért történt meg, mert a csomópont letiltott állapotba kerül, ami általában azért fordul elő, mert a csomópont letiltásával kvórum/adatvesztés fordulhat elő.

K. **Miért van szükség a csomópont letiltására, amikor a POA a javítást?**

A. A patch-előkészítési alkalmazás letiltja a csomópontot az "újraindítás" szándékkal, amely leállítja/újraosztja a csomóponton futó összes Service Fabric-szolgáltatást. Ennek segítségével biztosítható, hogy az alkalmazások ne legyenek az új és a régi DLL-fájlok vegyesen, ezért nem ajánlott a csomópontot letiltani anélkül.

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- A javítási előkészítési alkalmazás a felhasználó nevében elfogadja Windows Update végfelhasználói licencszerződését. Opcionálisan a beállítás kikapcsolható az alkalmazás konfigurációjában.

- A javítási előkészítési alkalmazás telemetria gyűjt a használat és a teljesítmény nyomon követéséhez. Az alkalmazás telemetria követi a Service Fabric futtatókörnyezet telemetria beállítását (amely alapértelmezés szerint be van kapcsolva).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="a-node-is-not-coming-back-to-up-state"></a>Egy csomópont nem jön vissza állapotba

**Lehet, hogy a csomópont letiltási állapotban van, mert**:

A biztonsági ellenőrzések függőben vannak. A helyzet orvoslása érdekében győződjön meg arról, hogy elegendő csomópont áll rendelkezésre kifogástalan állapotban.

**Előfordulhat, hogy a csomópont letiltott állapotban van, mert**:

- A csomópontot manuálisan letiltották.
- A csomópontot egy folyamatban lévő Azure-infrastruktúra feladata miatt letiltották.
- A csomópontot a javítási előkészítési alkalmazás átmenetileg letiltotta.

**Előfordulhat, hogy a csomópont le van ragadva, mert**:

- A csomópontot a rendszer leállított állapotba helyezte manuálisan.
- A csomópont újraindítást végez (amelyet a javítás-előkészítési alkalmazás aktiválhat).
- A csomópont le van állítva, mert hibás a virtuális gép vagy a számítógép vagy a hálózat kapcsolódási problémája.

### <a name="updates-were-skipped-on-some-nodes"></a>Egyes csomópontokon a frissítések ki lettek hagyva

A javítás-előkészítési alkalmazás az átütemezési házirendnek megfelelően megpróbál telepíteni egy Windows-frissítést. A szolgáltatás megkísérli helyreállítani a csomópontot, és az alkalmazás házirendjének megfelelően kihagyja a frissítést.

Ebben az esetben a rendszer figyelmeztetési szintű állapotjelentést hoz létre a csomópont-ügynök szolgáltatással kapcsolatban. A Windows Update eredménye a hiba lehetséges okát is tartalmazza.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A fürt állapota hiba miatt leáll a frissítés telepítésekor.

Egy hibás Windows-frissítés egy adott csomóponton vagy frissítési tartományban lévő alkalmazás vagy fürt állapotát leállíthatja. A javítás-előkészítési alkalmazás minden további Windows Update műveletet felhagy, amíg a fürt ismét Kifogástalan állapotba nem kerül.

A rendszergazdának beavatkoznia kell, és meg kell határoznia, hogy az alkalmazás vagy a fürt miért lett a Windows Update miatt sérült.

## <a name="release-notes"></a>Kibocsátási megjegyzések

>[!NOTE]
> A 1.4.0 verziótól kezdődően a kibocsátási megjegyzések és kiadások a GitHub kiadási [oldalán](https://github.com/microsoft/Service-Fabric-POA/releases/)találhatók.

### <a name="version-110"></a>1\.1.0-s verzió
- Nyilvános kiadás

### <a name="version-111"></a>1\.1.1-es verzió
- Kijavítva egy olyan hibát a NodeAgentService SetupEntryPoint, amely meggátolta a NodeAgentNTService telepítését.

### <a name="version-120"></a>1\.2.0-s vagy annál újabb verzió

- Hibajavítások a rendszerindító munkafolyamatok köré.
- Hibajavítás az RM-feladatok létrehozásakor, mert a javítási feladatok előkészítése során felmerülő állapot-ellenőrzés nem a várt módon történt.
- A Windows-szolgáltatás POANodeSvc indítási üzemmódjának módosítása automatikusról késleltetve – automatikus.

### <a name="version-121"></a>1\.2.1-es verzió

- Hibajavítás a fürt Felskálázási munkafolyamatában. A nem létező csomópontokhoz tartozó POA javítási feladatok esetében bevezette a Garbage-gyűjtési logikát.

### <a name="version-122"></a>1\.2.2-es verzió

- Egyéb hibajavítások.
- A bináris fájlok most már alá vannak írva.
- Az alkalmazáshoz hozzáadott sfpkg-hivatkozás.

### <a name="version-130"></a>Verzió 1.3.0

- A InstallWindowsOSOnlyUpdates beállítása hamis értékre most telepíti az összes elérhető frissítést.
- Módosult az automatikus frissítések letiltásának logikája. Ez egy olyan hibát javít, amelyben az automatikus frissítések nem voltak letiltva a 2016-es és újabb kiszolgálókon.
- Paraméteres elhelyezési megkötés a speciális használati esetekhez tartozó POA-szolgáltatások esetében is.

### <a name="version-131"></a>1\.3.1-es verzió
- A regresszió javítása, ha a POA 1.3.0 nem fog működni a Windows Server 2012 R2 vagy alacsonyabb verzióban, mert hiba történt az automatikus frissítések letiltásakor. 
- Kijavíthatja a hibát, hogy a InstallWindowsOSOnlyUpdates-konfiguráció mindig igaz legyen.
- A InstallWindowsOSOnlyUpdates alapértelmezett értékének hamisra váltása.

### <a name="version-132"></a>1\.3.2-es verzió
- Egy olyan probléma kijavítása, amely a javítási életciklust egy csomóponton hajtja végre, ha olyan csomópontok vannak, amelyeknek a neve az aktuális csomópont neve részhalmaza. Ilyen csomópontok esetén előfordulhat, hogy a javítások kimaradnak, vagy függőben van egy újraindítás. 
