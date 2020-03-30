---
title: A Windows operációs rendszer javítása a Service Fabric-fürtben
description: Ez a cikk ismerteti, hogyan automatizálhatja az operációs rendszer javítását egy Service Fabric-fürtön a Patch Orchestration Application használatával.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366314"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>A Windows operációs rendszer javítása a Service Fabric-fürtben

> 
> [!IMPORTANT]
> 2019. április 30-tól a Patch Orchestration Application 1.2-es verziója már nem támogatott. Ügyeljen arra, hogy frissítsen a legújabb verzióra.

> [!NOTE]
> Az [operációs rendszer rendszerkép-frissítéseinek beszerzése a virtuálisgép-méretezési csoporton](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) az ajánlott eljárás az operációs rendszer Azure-ban való javításának megőrzéséhez. Virtuálisgép-méretezési készlet alapú automatikus operációsrendszer-lemezkép-frissítések et igényel ezüst vagy nagyobb tartósság ot a méretezési csoportban.
>

 Patch Orchestration Application (POA) egy burkoló körül az Azure Service Fabric Repair Manager szolgáltatás, amely lehetővé teszi a konfigurációalapú operációs rendszer javítás ütemezése nem Azure-üzemeltetett fürtök. Poa nem szükséges a nem Azure-üzemeltetett fürtök, de ütemezési patch telepítése frissítési tartomány szükséges a Service Fabric fürtállomások javítása állásidő nélkül.

A POA egy Service Fabric-alkalmazás, amely munkakörnyezetben történő javítása a Service Fabric-fürtön állásidő nélkül automatizálja az operációs rendszer javítását.

A POA a következő szolgáltatásokat nyújtja:

- **Az operációs rendszer automatikus frissítésének telepítése**. Az operációs rendszer frissítései automatikusan letöltődnek és települnek. A fürtcsomópontok szükség szerint újraindulnak fürtleállás nélkül.

- **Fürtbarát javítás és egészségügyi integráció**. A POA frissítések alkalmazása közben figyeli a fürtcsomópontok állapotát. A fürtcsomópontok egyszerre egy vagy frissítési tartományban frissülnek. Ha a javítási folyamat miatt a fürt állapota leáll, a javítás leáll, hogy megakadályozza a probléma súlyosbodását.

## <a name="internal-details-of-poa"></a>A POA belső adatai

A POA a következő alösszetevőkből áll:

- **Koordinátori szolgáltatás**: Ez az állapotalapú szolgáltatás a következőkért felelős:
    - A Windows Update feladat koordinálása a teljes fürtön.
    - A befejezett Windows Update-műveletek eredményének tárolása.

- **Node Agent Service:** Ez az állapotmentes szolgáltatás fut az összes Service Fabric fürtcsomópontok. A szolgáltatás a következőkért felelős:
    - Az NTService csomópontügynök indítása.
    - Az NTService csomópontügynök figyelése.

- **NTService csomópontügynök**: Ez a Windows NT szolgáltatás magasabb szintű jogosultsággal (SYSTEM) fut. Ezzel szemben a Node Agent szolgáltatás és a Koordinátor szolgáltatás alacsonyabb szintű jogosultsággal (NETWORK SERVICE) fut. A szolgáltatás felelős a következő Windows Update feladatok végrehajtásáért az összes fürtcsomóponton:
    - Az automatikus Windows-frissítések letiltása a csomóponton.
    - A Windows-frissítések letöltése és telepítése a felhasználó által megadott házirendnek megfelelően.
    - A számítógép újraindítása a Windows frissítések telepítése után.
    - A Windows-frissítések eredményeinek feltöltése a koordinátori szolgáltatásba.
    - Állapotjelentések jelentése, ha egy művelet sikertelen volt, miután kimeríti az összes újrapróbálkozást.

> [!NOTE]
> A POA a Service Fabric Repair Manager szolgáltatás segítségével letiltja vagy engedélyezi a csomópontot, és állapot-ellenőrzést hajt végre. A POA által létrehozott javítási feladat nyomon követi a Windows Update előrehaladását az egyes csomópontoknál.

## <a name="prerequisites"></a>Előfeltételek

> [!NOTE]
> A szükséges minimális .NET Framework verzió 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A Repair Manager szolgáltatás engedélyezése (ha még nem fut)

A POA használatához engedélyezni kell a Repair Manager szolgáltatást a fürtön.

#### <a name="azure-clusters"></a>Azure-fürtök

Az ezüst tartóssági rétegben lévő Azure-fürtök alapértelmezés szerint engedélyezve vannak a Repair Manager szolgáltatással. Az arany tartóssági rétegben lévő Azure-fürtök lehet, hogy nem engedélyezve van a Repair Manager szolgáltatás, attól függően, hogy mikor jöttek létre. Az Azure-fürtök a bronz tartóssági rétegben, alapértelmezés szerint nem rendelkezik a Repair Manager szolgáltatás engedélyezve van. Ha a szolgáltatás már engedélyezve van, láthatja, hogy fut a rendszerszolgáltatások szakaszban a Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Az Azure Portal
A fürt beállításakor engedélyezheti a Javításkezelőt az Azure Portalról. A fürt konfigurálásakor válassza a **Javításkezelővel együtt** lehetőséget a **Bővítmény szolgáltatásai csoportban.**

![A Javításkezelő engedélyezése az Azure Portalról](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Az Azure Resource Manager telepítési modellje
Másik lehetőségként használhatja az [Azure Resource Manager telepítési modell,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) hogy a Repair Manager szolgáltatás új és meglévő Service Fabric-fürtök. A telepíteni kívánt fürt sablonjának beszereznie. Használhatja a mintasablonokat, vagy létrehozhat egy egyéni Azure Resource Manager telepítési modell sablont. 

Ha engedélyezni szeretné a Repair Manager szolgáltatást az [Azure Resource Manager telepítési modellsablonhasználatával,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)tegye a következőket:

1. Ellenőrizze, hogy `apiVersion` a *Microsoft.ServiceFabric/clusters* erőforrás *2017-07-01-preview-ra van-e* állítva. Ha ez más, frissítenie `apiVersion` kell a *2017-07-01-preview* vagy újabb verzióra:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Engedélyezze a Repair Manager `addonFeatures` szolgáltatást a `fabricSettings` következő szakasz hozzáadásával a szakasz után:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Miután frissítette a fürtsablont ezekkel a módosításokkal, alkalmazza őket, és hagyja, hogy a frissítés befejeződjön. Most már láthatja, hogy a Javításkezelő szolgáltatás fut a fürtben. Ezt a neve *fabric:/System/RepairManagerService* a Service Fabric Explorer rendszerszolgáltatások szakaszában. 

### <a name="standalone-on-premises-clusters"></a>Önálló helyszíni fürtök

Ha engedélyezni szeretné a Repair Manager szolgáltatást egy új vagy meglévő Service Fabric-fürtön, használhatja az [önálló Windows-fürt konfigurációs beállításait.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)

A Repair Manager szolgáltatás engedélyezése:

1. Ellenőrizze, hogy `apiVersion` az [általános fürtkonfigurációk](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) *04-2017 vagy* újabb beállításra vannak-e állítva, ahogy az itt látható:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Engedélyezze a Repair Manager `addonFeatures` szolgáltatást a `fabricSettings` következő szakasz hozzáadásával a szakasz után, az itt látható módon:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Frissítse a fürtjegyzéket ezekkel a módosításokkal a frissített fürtjegyzék használatával [hozzon létre egy új fürtöt,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) vagy [frissítse a fürtkonfigurációt.](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server) 

   Miután a fürt egy frissített fürtjegyzékkel fut, láthatja, hogy a Repair Manager szolgáltatás fut a fürtben. Ezt *háló:/System/RepairManagerService-nek*hívják, és a Service Fabric Explorer rendszerszolgáltatások szakaszában található.

### <a name="configure-windows-updates-for-all-nodes"></a>Windows-frissítések konfigurálása az összes csomóponthoz

A Windows automatikus frissítései rendelkezésre állásvesztéshez vezethetnek, mivel egyszerre több fürtcsomópont is újraindulhat. A POA alapértelmezés szerint megpróbálja letiltani az automatikus Windows-frissítéseket minden fürtcsomóponton. Ha azonban a beállításokat rendszergazda vagy csoportházirend kezeli, javasoljuk, hogy a Windows Update házirendet kifejezetten "Értesítés letöltés előtt" beállításra állítja.

## <a name="download-the-application-package"></a>Az alkalmazáscsomag letöltése

Az alkalmazáscsomag letöltéséhez nyissa meg a [GitHub Patch Orchestration Application kiadási lapját.](https://github.com/microsoft/Service-Fabric-POA/releases/latest/)

## <a name="configure-poa-behavior"></a>A POA viselkedésének konfigurálása

Beállíthatja poa viselkedését, hogy megfeleljen az Ön igényeinek. Az alapértelmezett értékek felülbírálása az alkalmazás paraméterének átadásával az alkalmazás létrehozása vagy frissítése közben. Az alkalmazás paramétereit megadhatja `Start-ServiceFabricApplicationUpgrade` a `New-ServiceFabricApplication` vagy a parancsmagok `ApplicationParameter` megadásával.

| Paraméter        | Típus                          | Részletek |
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | A Windows Update által gyorsítótárazandó eredmények maximális száma. <br><br>Az alapértelmezett érték 3000, feltételezve, hogy: <br> &nbsp;&nbsp;- A csomópontok száma 20. <br> &nbsp;&nbsp;- A csomópont frissítéseinek száma havonta 5. <br> &nbsp;&nbsp;- A műveletenkénti eredmények száma 10 lehet. <br> &nbsp;&nbsp;- Az elmúlt három hónap eredményeit tárolni kell. |
|TaskApprovalPolicy (Feladatjóváhagyási házirend)   |Felsorolás <br> { NodeWise, UpgradeDomainWise }                          |A TaskApprovalPolicy azt a házirendet jelzi, amelyet a koordinátori szolgáltatásnak a Windows-frissítések nek a Service Fabric fürtcsomópontokon keresztül történő telepítéséhez kell használnia.<br><br>Az engedélyezett értékek a következők: <br>*NodeWise*: A Windows-frissítések egyszerre csak egy csomópontot telepítenek. <br> *UpgradeDomainWise*: A Windows-frissítések egyszerre csak egy frissítési tartományt telepítenek. (Legfeljebb a frissítési tartományhoz tartozó összes csomópont windowsos frissítést kaphat.)<br><br> Ha el döntheti, hogy melyik házirend a legmegfelelőbb a fürtszámára, tekintse meg a [GYIK](#frequently-asked-questions) című részt.
|LogsDiskQuotaInMB   |Hosszú  <br> (Alapértelmezett: *1024*)               | A patch vezénylési alkalmazásnaplók maximális mérete MB-ban, amely helyileg megőrizhető a csomópontokon.
| WUQuery               | sztring<br>(Alapértelmezett: *IsInstalled=0*)                | Lekérdezés a Windows-frissítések bekereséséhez. További információ: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| WindowsOSOnlyfrissítések telepítése | *Logikai* <br> (alapértelmezett: hamis)                 | Ezzel a jelzővel szabályozhatja, hogy mely frissítéseket kell letölteni és telepíteni. A következő értékek megengedettek <br>true - Csak a Windows operációs rendszer frissítéseit telepíti.<br>false - Telepíti az összes rendelkezésre álló frissítést a készülékre.          |
| WUOperationtimeoutinminutes (WUműveletidőoutperc) | Int <br>(Alapértelmezett: *90*)                   | Megadja a Windows Update bármely műveletének (keresés, letöltés vagy telepítés) időhatot. Ha a művelet nem fejeződik be a megadott időtúlidőn belül, megszakad.       |
| WURescheduleCount (WURescheduleCount)     | Int <br> (Alapértelmezett: *5*)                  | A szolgáltatás legfeljebb annyiszor ütemezi át a Windows-frissítést, ha egy művelet tartósan sikertelen.          |
| WURescheduleTimeInMinutes | Int <br>(Alapértelmezett: *30*) | Az az időszak, amikor a szolgáltatás átütemezi a Windows frissítéseit, ha a hiba továbbra is fennáll. |
| WUGyakoriság           | Vesszővel tagolt karakterlánc (Alapértelmezett: *Heti, szerda, 7:00:*)     | A Windows-frissítések telepítésének gyakorisága. A formátum és a lehetséges értékek a következők: <br>&nbsp;&nbsp;- Havonta: DD, ÓÓ:PP:SS (például *Havi, 5,12:22:32*)<br>A DD mező (nap) megengedett értékei az 1 és 28 és az "utolsó" számok. <br> &nbsp;&nbsp;- Hetente, NAP, ÓÓ:PP:SS (például *Hetente, Kedd, 12:22:32*)  <br> &nbsp;&nbsp;- Naponta, ÓÓ:PP:SS (például *Naponta, 12:22:32*)  <br> &nbsp;&nbsp;-  *Egyik sem* jelzi, hogy a Windows-frissítéseket nem szabad elvégezni.  <br><br> Az idők UTC-ben vannak.|
| AcceptWindowsUpdateEula | Logikai <br>(Alapértelmezett: *igaz*) | A jelző beállításával az alkalmazás elfogadja a végfelhasználói licencszerződést a Windows Update szolgáltatáshoz a számítógép tulajdonosának nevében.              |

> [!TIP]
> Ha azt szeretné, hogy a `WUFrequency` Windows-frissítések azonnal megtörténjenek, állítsa be az alkalmazás telepítési idejéhez viszonyítva. Tegyük fel például, hogy rendelkezik egy öt csomópontos tesztfürttel, és azt tervezi, hogy az alkalmazást körülbelül 17:00 ÓRAKOR (UTC) telepíti. Ha feltételezi, hogy az alkalmazás frissítése vagy központi telepítése legutoljára 30 percet vesz igénybe, állítsa be a WUFrequency-t *napi 17:30:00-ra.*

## <a name="deploy-poa"></a>POA telepítése

1. Végezze el a fürt előkészítéséhez szükséges összes előfeltételi lépést.
1. Poa üzembe helyezése, mint bármely más Service Fabric-alkalmazás. A PowerShell használatával történő üzembe helyezésről az [Alkalmazások telepítése és eltávolítása a PowerShell használatával.](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)
1. Az alkalmazás központi telepítéskor történő `ApplicationParameter` konfigurálásához `New-ServiceFabricApplication` adja át a parancsmasalapot. Az Ön kényelme érdekében a Deploy.ps1 parancsfájlt és az alkalmazást biztosítottuk. A parancsfájl használata:

    - Csatlakozzon egy Service Fabric-fürthöz a használatával. `Connect-ServiceFabricCluster`
    - Hajtsa végre a PowerShell-parancsfájl `ApplicationParameter` deploy.ps1 a megfelelő értékkel.

> [!NOTE]
> Tartsa a parancsfájlt és az alkalmazásmappát *PatchOrchestrationApplication* ugyanabban a könyvtárban.

## <a name="upgrade-poa"></a>Poa frissítése

A POA-verzió PowerShell használatával történő frissítéséhez kövesse a [Service Fabric-alkalmazások frissítésének](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)a PowerShell használatával című utasításait.

## <a name="remove-poa"></a>Termelői ép eltávolítása

Az alkalmazás eltávolításához kövesse az [alkalmazások központi telepítésével és eltávolításával a PowerShell használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)című útmutatóutasításait.

Az Ön kényelme érdekében az Undeploy.ps1 parancsfájlt és az alkalmazást biztosítottuk. A parancsfájl használata:

  - Csatlakozzon egy Service Fabric-fürthöz a használatával. ```Connect-ServiceFabricCluster```
  - Hajtsa végre az Undeploy.ps1 PowerShell-parancsfájlt.

> [!NOTE]
> Tartsa a parancsfájlt és az alkalmazásmappát *PatchOrchestrationApplication* ugyanabban a könyvtárban.

## <a name="view-the-windows-update-results"></a>A Windows Update eredményeinek megtekintése

A POA elérhetővé teszi a REST API-kat, hogy megjelenítse a korábbi eredményeket a felhasználók számára. Íme egy példa az eredmény JSON:

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

A JSON-mezőket az alábbi táblázat ismerteti:

Mező | Értékek | Részletek
-- | -- | --
OperationResult műveleteredménye | 0 - Sikerült<br> 1 - Sikeres hibákkal<br> 2 - Nem sikerült<br> 3 - Megszakítva<br> 4 - Megszakítva időhatolással | A teljes művelet eredményét jelzi, amely általában egy vagy több frissítés telepítését jelenti.
ResultCode (Eredménykód) | Megegyezik az OperationResult | Ez a mező az egyes frissítések telepítési műveletének eredményét jelzi.
OperationType (Művelettípus) | 1 - Telepítés<br> 0 - Keresés és letöltés| Alapértelmezés szerint a telepítés az egyetlen OperationType, amely megjelenik az eredményekben.
WindowsUpdateQuery | Az alapértelmezett érték: "IsInstalled=0" | A frissítések keresésére használt Windows Update lekérdezés. További információ: [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
ÚjraindításSzükséges | igaz - újraindításra volt szükség<br> false - újraindítás nem volt szükség | Azt jelzi, hogy szükség volt-e újraindításra a frissítések telepítésének befejezéséhez.
OperationStartTime | DateTime | Azt az időpontot jelzi, amikor a művelet (Letöltés/telepítés) elindult.
OperationTime | DateTime | A művelet (Letöltés/telepítés) befejezésének időpontja.
Hresult | 0 - Sikeres<br> egyéb - hiba| A Windows frissítés hibájának okát jelzi a "7392acaf-6a85-427c-8a8d-058c25beb0d6" frissítéssel.

Ha még nincs frissítés ütemezve, a JSON eredmény üres.

Jelentkezzen be a fürtbe a Windows Update eredményeinek lekérdezéséhez. Ismerje meg a koordinátori szolgáltatás elsődleges címének replika IP-címét,&lt;és nyissa&gt;&lt;meg&gt;a következő URL-címet a böngészőből: http:// REPLICA-IP : ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

A koordinátori szolgáltatás REST-végpontja dinamikus porttal rendelkezik. A pontos URL-cím ellenőrzéséhez tekintse meg a Service Fabric Explorer. Az eredmények például a *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*következő helyen érhetők el: .

![A REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Ha a fordított proxy engedélyezve van a fürtön, az URL-cím a fürtön kívülről is elérhető.

A lenyomandó végpont *a&lt;HTTP://&gt;SERVERURL&lt;:&gt;REVERSEPROXYPORT /PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

A fordított proxy engedélyezéséhez kövesse a [Fordított proxy](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)az Azure Service Fabric utasításait. 

> 
> [!WARNING]
> A fordított proxy konfigurálása után a fürt összes olyan mikroszolgáltatása, amely HTTP-végpontot elérhetővé teszi, a fürtön kívülről címezhető.

## <a name="diagnostics-and-health-events"></a>Diagnosztika és egészségügyi események

Ez a szakasz ismerteti, hogyan hibakeresés vagy diagnosztizálása problémák a javítás frissítések et POA a Service Fabric-fürtök.

> [!NOTE]
> Az alábbi kihívott, öndiagnosztikai fejlesztések nagy részének lekéréséhez telepítenie kell a POA 1.4.0-s vagy újabb verzióját.

A Node Agent NTService [javítási feladatokat](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) hoz létre a frissítések csomópontokon történő telepítéséhez. Ezután a koordinátori szolgálat minden feladatot előkészít a feladat-jóváhagyási házirendnek megfelelően. Végül az előkészített feladatokat a Repair Manager hagyja jóvá, amely nem hagy jóvá semmilyen feladatot, ha a fürt sérült állapotban van. 

Annak érdekében, hogy könnyebben megértsd, hogyan folytatódnak a frissítések egy csomóponton, lépésről lépésre haladjunk:

1. NodeAgentNTService, fut minden csomóponton, megkeresi az elérhető Windows-frissítések az ütemezett időben. Ha a frissítések elérhetők, letölti őket a csomóponton.

1. A frissítések letöltése után a Node Agent NTService létrehoz egy megfelelő javítási feladatot a *csomópontszámára,\<amelynek *a neve POS___ unique_id>. Ezeket a javítási feladatokat a [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) parancsmag használatával vagy az SFX használatával tekintheti meg a csomópont részletei szakaszban. A javítási feladat létrehozása után gyorsan átkerül [ *az Igényelt* állapotba.](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet)

1. A koordinátori szolgáltatás rendszeresen megkeresi a javítási feladatokat *igényelt* állapotban, majd frissíti őket a TaskApprovalPolicy alapján *az állapot előkészítése* állapotra. Ha a TaskApprovalPolicy nodewise értékre van konfigurálva, akkor a csomópontnak megfelelő javítási feladat csak akkor készül el, ha jelenleg nincs más javítási feladat a *Felkészülés*, *Jóváhagyva*, *Végrehajtó*vagy *Visszaállítás* állapotban. 

   Hasonlóképpen, a UpgradeWise TaskApprovalPolicy esetében az előző állapotokban csak az azonos frissítési tartományhoz tartozó csomópontok esetében vannak feladatok. Miután egy javítási feladat átkerült *a Felkészülés* állapotba, a megfelelő Service Fabric-csomópont le van [tiltva](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) az *Újraindítás*szándékkal.

   Poa 1.4.0-s és újabb verziói eseményeket könyvelnek a CoordinatorService ClusterPatchingStatus tulajdonságával a javítás alatt álló csomópontok megjelenítéséhez. A frissítések _poanode_0 települnek, ahogy az az alábbi képen látható:

    [![A fürtjavítás állapotának képe](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. A csomópont letiltása után a javítási feladat *a végrehajtó* állapotba kerül. 
   
   > [!NOTE]
   > A *letiltott* állapotban beragadt csomópont blokkolhat egy új javítási feladatot, amely leállítja a fürt javítási műveletét.

1. Ha a javítási feladat *végrehajtási* állapotban van, megkezdődik a csomóponton lévő javítás telepítése. A javítás telepítése után előfordulhat, hogy a csomópont újraindul, vagy nem. Ezután a javítási feladat *a visszaállítási* állapotba kerül, amely újraengedélyezi a csomópontot. A javítási feladat ezután befejezettként lesz megjelölve.

   A POA 1.4.0-s és újabb verzióiban megtalálhatja a frissítés állapotát, ha megtekinti\<a NodeAgentService állapoteseményeit a WUOperationStatus - NodeName> tulajdonsággal. Az alábbi képek kiemelt szakaszai a Windows-frissítések állapotát mutatják *a poanode_0* és *poanode_2*csomópontokon:

   [![A Windows Update műveletállapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![A Windows Update műveletállapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   A részleteket a PowerShell használatával is lejuthat. Ehhez csatlakozzon a fürthöz, és a [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps)segítségével olvassa be a javítási feladat állapotát. 
   
   A következő példában a "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" feladat *DownloadComplete* állapotban van. Ez azt jelenti, hogy a frissítések le lettek töltve a *poanode_2* csomóponton, és a telepítés megkísérli, amikor a feladat a végrehajtó állapotba *kerül.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Ha további problémák at talál, jelentkezzen be a virtuális gépre (VM) vagy a virtuális gépekre, és ismerje meg őket a Windows eseménynaplók használatával. A korábban említett javítási feladat csak a végrehajtó alállapotokban létezhet:

      ExecutorSubState | Leírás
    -- | -- 
      Nincs=1 |  Azt jelenti, hogy nem volt folyamatban lévő művelet a csomóponton. Lehet, hogy az állam átalakulóban van.
      LetöltésCompleted=2 | Azt jelenti, hogy a letöltési művelet sikeres, részleges hibával vagy hibával fejeződött be.
      InstallationApproved=3 | Azt jelenti, hogy a letöltési művelet korábban befejeződött, és a Javításkezelő jóváhagyta a telepítést.
      TelepítésInProgress=4 | A javítási feladat végrehajtásának állapotának felel meg.
      InstallationCompleted=5 | Azt jelenti, hogy a telepítés sikeres, részleges sikerrel vagy sikertelenséggel fejeződött be.
      RestartRequested=6 | Azt jelenti, hogy a javítás telepítése befejeződött, és van egy függőben lévő újraindítási művelet a csomóponton.
      RestartNotNeeded=7 |  Azt jelenti, hogy az újraindításra nem volt szükség a javítás telepítése után.
      RestartCompleted=8 | Azt jelenti, hogy az újraindítás sikeresen befejeződött.
      OperationCompleted=9 | A Windows Update művelet sikeresen befejeződött.
      OperationAborted=10 | Azt jelenti, hogy a Windows Update művelet megszakadt.

1. A POA 1.4.0-s és újabb verzióiban, amikor egy csomópontfrissítési kísérlet befejeződik, a NodeAgentService szolgáltatáson megjelenik egy "WUOperationStatus-[NodeName]" tulajdonsággal rendelkező esemény, amely értesíti, hogy mikor kezdődik a Windows-frissítések letöltésére és telepítésére tett következő kísérlet. Ez a következő képen jelenik meg:

     [![A Windows Update műveletállapotának képe](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Diagnosztikai naplók

Patch vezénylési alkalmazás naplók a Service Fabric futásidejű naplók részeként gyűjtik.

A naplókat az Ön által választott diagnosztikai eszközzel vagy folyamattal rögzítheti. A POA a következő rögzített szolgáltatóazonosítókat használja az [események eseményforráson](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)keresztüli naplózásához:

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Egészségügyi jelentések

A POA a következő esetekben is közzétesz állapotjelentéseket a Node Agent szolgáltatás vagy a koordinátori szolgáltatás ellen:

* A Node Agent NTService nem működik

   Ha az NTService csomóponton nem működik, a rendszer figyelmeztető szintű állapotjelentést hoz létre a Csomópontügynök-szolgáltatás sal szemben.

* A Repair Manager szolgáltatás nincs engedélyezve

   Ha a Javításkezelő szolgáltatás nem található a fürtön, a rendszer figyelmeztető szintű állapotjelentést készít a koordinátori szolgáltatásszámára.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért jelenik meg a fürt hibaállapotban, amikor a POA fut?**

V: A telepítési folyamat során a POA letiltja vagy újraindítja a csomópontokat, ami ideiglenesen sérült fürtöt eredményezhet.

Az alkalmazás házirendjétől függően egy csomópont leléphet a javítási művelet során, *vagy* egy teljes frissítési tartomány egyszerre leléphet.

A Windows-frissítések telepítésének végére a csomópontok újra engedélyezve lesznek az újraindítás után.

A következő példában a fürt átmenetileg hibaállapotba került, mert két csomópont nem volt megfelelő, és a MaxPercentageUnhealthyNodes házirend et megsértették. A hiba ideiglenes, amíg a javítási művelet meg nem kezdődik.

![Nem megfelelő állapotú fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma továbbra is fennáll, olvassa el a Hibaelhárítás című szakaszt.

**K: Mit tehetek, ha a POA figyelmeztető állapotban van?**

A: Ellenőrizze, hogy az alkalmazással szemben közzétett állapotjelentés jelzi-e a kiváltó okot. A figyelmeztetés általában a probléma részleteit tartalmazza. Ha a probléma átmeneti, az alkalmazás várhatóan automatikusan helyreáll.

**K: Mi a teendő, ha a fürt nem megfelelő állapotú, és sürgős operációsrendszer-frissítést kell végeznem?**

V: A POA nem telepíti a frissítéseket, amíg a fürt nem kifogástalan. Próbálja meg a fürtöt kifogástalan állapotba hozni a POA-munkafolyamat blokkolásának feloldásához.

**K: Állítsam be a TaskApprovalPolicy-et "NodeWise" vagy "UpgradeDomainWise" beállításra a fürtömhöz?**

A: A "UpgradeDomainWise" beállítás felgyorsítja a fürt teljes javítását azáltal, hogy párhuzamosan javítja a frissítési tartományhoz tartozó összes csomópontot. A folyamat során a teljes frissítési tartományhoz tartozó csomópontok nem érhetők el [ *(letiltott* állapotban).](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)

Ezzel szemben a "NodeWise" beállítás egyszerre csak egy csomópontot javít, ami azt jelentené, hogy a fürt teljes javítása hosszabb időt vehet igénybe. A javítási folyamat során azonban legfeljebb egy csomópont lenne elérhető *(letiltott* állapotban).

Ha a fürt tolerálja az N-1 frissítési tartományok száma futtatását a javítási ciklus során (ahol N a fürt frissítési tartományainak teljes száma), beállíthatja a házirendet "UpgradeDomainWise" értékre. Ellenkező esetben állítsa "NodeWise" (NodeWise" beállításra.

**K: Mennyi időt vesz igénybe egy csomópont javítása?**

V: A csomópont javítása percektől (például [a Windows Defender definíciófrissítéseitől)](https://www.microsoft.com/en-us/wdsi/definitions)órákig (például Windows [összesítő frissítések)](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)is eltarthat. A csomópont javításához szükséges idő leginkább a következőktől függ: 
 - A frissítések mérete.
 - A javítási ablakban alkalmazandó frissítések száma.
 - A frissítések telepítéséhez, a csomópont újraindításához (szükség esetén) és az újraindítás utáni telepítési lépések befejezéséhez szükséges idő.
 - A virtuális gép vagy a gép teljesítménye és a hálózati feltételek.

**K: Mennyi ideig tart egy teljes fürt javítása?**

A: A teljes fürt javításához szükséges idő a következőktől függ:

- A csomópont javításához szükséges idő.

- A koordinátori szolgálat szabályzata. Az alapértelmezett házirend, a "NodeWise" egyszerre csak egy csomópontot javít, és ez a megközelítés lassabb, mint a "UpgradeDomainWise" használata. 

   Például: Ha egy csomópont javítása ~1 órát vesz igénybe, akkor egy 20 csomópontos (azonos típusú) fürt öt frissítési tartománnyal kell javítani, amelyek mindegyike 4 csomópontot tartalmaz, és a következőt igényli:
    - A "NodeWise" esetében: ~20 óra.
    - A "UpgradeDomainWise": ~5 óra.

- A fürt terhelése. Minden egyes javítási művelet hez át kell helyezni az ügyfél-munkaterhelést a fürt más elérhető csomópontjaira. A javított csomópont [ *letiltási* állapotban](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) lenne ez idő alatt. Ha a fürt csúcsterhelés közelében fut, a letiltási folyamat hosszabb időt vesz igénybe. Ezért az általános javítási folyamat lassúnak tűnhet ilyen stresszes körülmények között.

- Fürt állapothibái a javítás során. A fürt [állapotának](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) [bármilyen romlása](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) megszakítaná a javítási folyamatot. Ez a probléma növelné a teljes fürt javításához szükséges teljes időt.

**K: Miért jelennek meg a Windows Update eredményeiben a REST API-n keresztül kapott frissítések, de nem a Windows Update előzményei alatt a számítógépen?**

A: Egyes termékfrissítések csak a saját frissítési vagy javítási előzményeikben jelennek meg. Előfordulhat például, hogy a Windows Defender frissítései megjelennek a Windows Update előzményeiközött a Windows Server 2016 rendszeren.

**K: PoA használható a fejlesztői fürt (egycsomópontos fürt) javításához?**

V: Nem, a POA nem használható egycsomópontos fürt javításához. Ez a korlátozás szándékosan, mert [a Service Fabric rendszerszolgáltatások](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) vagy más ügyfélalkalmazások állásidőt. Ezért a javítási feladatok javítása soha nem kap jóváhagyást Repair Manager.

**K: Hogyan patch fürt csomópontok Linux?**

V: A frissítések Linuxon történő vezényléséről az [Azure virtuálisgép-méretezési készlet automatikus operációsrendszer-rendszerfrissítési készletének témakörben](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)olvashat.

**K: Miért tart ilyen sokáig a frissítési ciklus?**

A: Lekérdezés az eredmény JSON, adja meg a frissítési ciklus az összes csomópont, majd megpróbálhatja kideríteni, hogy a frissítés telepítése által minden csomóponton az OperationStartTime és operationTime (OperationCompletionTime) használatával. 

Ha van egy nagy időablak, amelyben nincs frissítés, a fürt lehet, hogy hibaállapotban van, és ennek következtében a Javítás-kezelő nem tudja jóváhagyni a POA javítási feladatokat. Ha a frissítés telepítése hosszú időt vesz igénybe bármely csomóponton, előfordulhat, hogy a csomópont egy ideig nem lett frissítve. Sok frissítés telepítés rejlehet függőben, ami késéseket okozhat. 

Az is lehetséges, hogy a csomópont-javítás blokkolva van, mert *letiltási* állapotban van. Ez általában azért fordul elő, mert a csomópont letiltása kvórum- vagy adatvesztési helyzetekhez vezethet.

**K: Miért kell a csomópontot letiltani, amikor a POA javítja?**

V: A POA letiltja a csomópont *újraindítási* szándékkal, amely leállítja vagy újralefoglalja a service Fabric-szolgáltatások, amelyek a csomóponton futó összes. Poa ezt annak érdekében, hogy az alkalmazások nem a végén egy mix az új és a régi DL-ek, ezért azt javasoljuk, hogy ne folt ozott csomópont letiltása nélkül.

**K: Mi a POA használatával frissíthető csomópontok maximális száma?**

V: A POA a Service Fabric Repair Manager segítségével hozza létre a frissítések csomópontjainak javítási feladatait. Egyszerre azonban legfeljebb 250 javítási feladat lehet jelen. A POA jelenleg egyszerre hoz létre javítási feladatokat az egyes csomópontokhoz, így a POA legfeljebb 250 csomópontot frissíthet egy fürtben. 

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- A POA elfogadja a végfelhasználói licencszerződést a Windows Update szolgáltatáshoz a felhasználó nevében. Szükség esetén a beállítás kikapcsolható az alkalmazás konfigurációjában.

- A POA összegyűjti a telemetriai adatokat a használat és a teljesítmény nyomon követéséhez. Az alkalmazás telemetriai adatok követi a service fabric futásidejű telemetriai beállítás (amely alapértelmezés szerint be van kapcsolva) beállítását követi.

## <a name="troubleshooting"></a>Hibaelhárítás

Ez a rész a csomópontok javításával kapcsolatos problémák lehetséges hibaelhárítási megoldásait ismerteti.

### <a name="a-node-is-not-coming-back-to-up-state"></a>Egy csomópont nem jön vissza az állapotba

* Előfordulhat, hogy a csomópont *letiltási* állapotban van, mert:

  - A biztonsági ellenőrzés folyamatban van. A helyzet orvoslásához győződjön meg arról, hogy elegendő csomópont áll rendelkezésre kifogástalan állapotban.

* Előfordulhat, hogy a csomópont *letiltott* állapotban van, mert:

  - Manuálisan volt kikapcsolva.
  - Egy folyamatban lévő Azure-infrastruktúra-feladat miatt le volt tiltva.
  - A POA ideiglenesen letiltotta a csomópont javításához.

* Előfordulhat, hogy a csomópont lefelé van dugva, mert:

  - Kézzel állították lefelé.
  - Újraindításon megy keresztül (amelyet a POA aktiválhat).
  - Hibás virtuális géppel vagy géppel rendelkezik, vagy hálózati kapcsolati problémákkal küzd.

### <a name="updates-were-skipped-on-some-nodes"></a>A frissítések kimaradtak néhány csomóponton

A POA az átütemezési házirendnek megfelelően próbál meg windowsos frissítést telepíteni. A szolgáltatás megpróbálja helyreállítani a csomópontot, és kihagyja a frissítést az alkalmazásházirendnek megfelelően.

Ebben az esetben egy figyelmeztetési szintű állapotjelentés jön létre a csomópontügynök-szolgáltatás sal. A Windows Update eredménye a hiba lehetséges okát is tartalmazza.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>A fürt állapota hibás, miközben a frissítés telepítése folyamatban van

A hibás Windows-frissítések csökkenthetik egy adott csomóponton vagy frissítési tartományban lévő alkalmazások vagy fürtök állapotát. A POA minden további Windows Update-műveletet leáll, amíg a fürt ismét kifogástalan állapotba nem kerül.

A rendszergazdának közbe kell lépnie, és meg kell határoznia, hogy az alkalmazás vagy a fürt miért lett sérült meg a Windows Update miatt.

## <a name="poa-release-notes"></a>POA kibocsátási megjegyzések

>[!NOTE]
> A POA 1.4.0-s és újabb verzióihoz kiadási megjegyzéseket és kiadásokat talál a [GitHub Patch Orchestration Application kiadási oldalán.](https://github.com/microsoft/Service-Fabric-POA/releases/)

### <a name="version-110"></a>1.1.0-s verzió
- Nyilvános kiadás

### <a name="version-111"></a>1.1.1-es verzió
- Javítva a NodeAgentService SetupEntryPoint szolgáltatásának egyik hibája, amely megakadályozta a NodeAgentNTService telepítését.

### <a name="version-120"></a>1.2.0-s verzió

- Hibajavítások körül rendszer újraindításmunkafolyamat.
- Hibajavítás az RM-feladatok létrehozásában, amelyek miatt a javítási feladatok előkészítése során az állapotfelmérés nem a várt módon történt.
- A POANodeSvc Windows-szolgáltatás indítási módját automatikusról késleltetett-automatikusra módosította.

### <a name="version-121"></a>1.2.1-es verzió

- Hibajavítás a fürt lekicsinyítési munkafolyamatában. A nem létező csomópontokhoz tartozó POA-javítási feladatokhoz vezetett be szemétgyűjtési logika.

### <a name="version-122"></a>1.2.2-es verzió

- Egyéb hibajavítások.
- A bináris fájlok aláírva.
- Hozzáadott sfpkg linket az alkalmazás.

### <a name="version-130"></a>1.3.0-s verzió

- Az InstallWindowsOSOnlyUpdates beállítás hamisra állítása most telepíti az összes rendelkezésre álló frissítést.
- Megváltoztatta az automatikus frissítések letiltásának logikáját. Ez egy olyan hibát javít, amely miatt az automatikus frissítések nem voltak letiltva a Server 2016-os és újabb kiszolgálóin.
- Paraméterezett elhelyezési megkötés a POA mikroszolgáltatásaihoz a speciális használati esetekhez.

### <a name="version-131"></a>1.3.1-es verzió
- A REGresszió kijavítása, amelynek esetén a POA 1.3.0 nem működik windows Server 2012 R2 vagy korábbi rendszeren az automatikus frissítések letiltásának hibája miatt. 
- A hiba kijavítása, ahol az InstallWindowsOSOnlyUpdates konfiguráció mindig True-ként lesz kiválasztva.
- Az InstallWindowsOSOnlyUpdates alapértelmezett értékének módosítása Hamis értékre.

### <a name="version-132"></a>1.3.2-es verzió
- Egy olyan probléma megoldása, amely hatással van a csomópont javítási életciklusára, ha vannak olyan csomópontok, amelyek neve az aktuális csomópontnév egy részhalmaza. Az ilyen csomópontok esetében lehetséges, hogy a javítás kimaradt, vagy újraindítás van függőben.
