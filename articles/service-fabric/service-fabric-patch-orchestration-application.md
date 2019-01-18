---
title: Az Azure Service Fabric patch orchestration application |} A Microsoft Docs
description: Az alkalmazás egy Service Fabric-fürtöt operációs rendszer javításának automatizálása.
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 6bd71b7cecfb8a5decd3049152a2293dc7867bde
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54382734"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>A Windows operációs rendszer a Service Fabric-fürtben

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

[Azure virtuális gép méretezési csoport automatikus operációs rendszer lemezkép frissítéseinek](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) az ajánlott eljárás, megtartja az operációs rendszerek javítani az Azure-ban, és a Patch Orchestration Application (POA) szolgáltatás hálók RepairManager Systems service – burkolója amely lehetővé teszi, hogy-alapú konfigurációs operációs rendszer javítási ütemezése nem Azure-beli üzemeltetett fürtök esetén. POA nem Azure-beli üzemeltetett fürtök esetén nem kötelező, de a frissítési tartományok szerint javítás telepítésének ütemezése szükség javítások a Service Fabric-fürtök gazdagépek üzemkimaradás nélkül.

POA az Azure Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javításának Service Fabric-fürt, üzemkimaradás nélkül.

A patch orchestration alkalmazást a következő szolgáltatásokat biztosítja:

- **Az operációs rendszer telepítése**. Az operációs rendszer frissítése automatikusan letöltődjön és települjön. Fürtcsomópontok fürt állásidő nélkül igény szerint indulnak újra.

- **A fürttámogató javításait és egészségügyi integrációs**. Frissítések alkalmazása során a patch orchestration alkalmazást a fürt csomópontjainak állapotát figyeli. Fürtcsomópontok frissített egy csomópont egyszerre egy időben vagy több frissítési tartományt. Ha a fürt állapota miatt a javítási folyamat leáll, javítás leállt súlyosbító a probléma elkerülése érdekében.

## <a name="internal-details-of-the-app"></a>Az alkalmazás belső részletei

A patch orchestration alkalmazást a következő alösszetevők tevődik össze:

- **Koordinátor szolgáltatást**: Az állapotalapú szolgáltatás felelős:
    - A Windows Update feladatot az egész fürt összehangolása.
    - Az eredmény a befejezett Windows-frissítési műveletek tárolására.
- **Csomópont-ügynökszolgáltatás**: Az állapotmentes szolgáltatás fut, a Service Fabric-fürt összes csomópontján. A szolgáltatás felelős:
    - A Node-Agent NTService rendszerindításra.
    - A Node-Agent NTService figyelése.
- **NODE-Agent NTService**: A Windows NT-szolgáltatás fut, a magasabb szintű jogosultság (rendszer). Ezzel szemben a csomópont az ügynök és a koordinátor szolgáltatást futtatni egy alacsonyabb szintű jogosultság (hálózati szolgáltatás). A szolgáltatás felelős a a fürt összes csomópontján a következő Windows-frissítési feladatok végrehajtása:
    - A csomóponton automatikus Windows-frissítési letiltása.
    - Letöltése és telepítése a Windows Update a házirend szerint a felhasználó adta meg.
    - A gép utáni Windows-frissítés telepítésének újraindítása.
    - Feltöltése a Windows-frissítések eredményeit a koordinátor szolgáltatást.
    - Jelentéskészítési rendszerállapot-jelentéseket abban az esetben, ha egy művelet nem sikerült kimerítése összes újrapróbálkozás után.

> [!NOTE]
> A patch orchestration app letiltása vagy engedélyezése a csomópont és állapotellenőrzéseket hajthat végre a Service Fabric javítási manager rendszer szolgáltatást használja. A javítási feladat a patch orchestration alkalmazás által létrehozott minden egyes csomópont esetében a Windows Update folyamatot követi nyomon.

## <a name="prerequisites"></a>Előfeltételek

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A repair-kezelő szolgáltatás engedélyezése (Ha nincs már fut)

A patch orchestration alkalmazás szükséges a javítási manager rendszerszolgáltatás engedélyezni kell a fürtön.

#### <a name="azure-clusters"></a>Az Azure-fürtök

Az Azure-fürtöket a Silver szintű tartóssági szint a repair-kezelő szolgáltatás alapértelmezés szerint engedélyezve van. Az Azure-fürtöket az arany tartóssági szint előfordulhat, hogy, vagy nem rendelkezik a javítás-kezelő szolgáltatás engedélyezve van, attól függően, amikor ezek a fürtök létrejöttek. Az Azure-fürtöket a bronz tartóssági szint, alapértelmezés szerint nem rendelkezik a repair-kezelő szolgáltatás engedélyezve van. Ha a szolgáltatás már engedélyezve van, a rendszer-services szakaszban a Service Fabric Explorert a működés láthatja.

##### <a name="azure-portal"></a>Azure Portal
A fürt beállításának idején manager javítást igényel az Azure Portal használatával engedélyezheti. Válassza ki **közé tartozik a javításkezelő** lehetőség **bővítmény szolgáltatásai** fürtkonfiguráció időpontjában.
![Az Azure Portalról, amely lehetővé teszi javításkezelő kép](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Az Azure Resource Manager üzemi modell
Másik lehetőségként használhatja a [Azure Resource Manager üzemi modell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) ahhoz, hogy a javítási manager szolgáltatás új és meglévő Service Fabric-fürtökön. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. A mintasablonokat használja, vagy hozzon létre egy egyéni Azure Resource Manager üzembe helyezési modell sablont. 

Ahhoz, hogy a javítási manager szolgáltatás használata [Azure Resource Manager üzembe helyezési modell sablon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Először ellenőrizze, hogy a `apiversion` értékre van állítva `2017-07-01-preview` számára a `Microsoft.ServiceFabric/clusters` erőforrás. Ha nem egyeznek, akkor frissítenie kell a `apiVersion` értékre `2017-07-01-preview` vagy magasabb:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Mostantól engedélyezheti a repair-kezelő szolgáltatás a következő `addonFeatures` szakasz után a `fabricSettings` szakaszban:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Ezek a módosítások a fürtsablonhoz frissítése után alkalmazza őket, és lehetővé teszik a frissítés befejezéséhez. Most már megtekintheti a javítási manager rendszer szolgáltatás fut a fürtben. Azt nevezzük `fabric:/System/RepairManagerService` a Service Fabric Explorert a rendszer szolgáltatások szakaszában. 

### <a name="standalone-on-premises-clusters"></a>Önálló helyszíni fürtök

Használhatja a [különálló Windows-fürt konfigurációs beállításainak](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) a javítási manager szolgáltatások új és meglévő Service Fabric-fürtön történő engedélyezéséhez.

A repair-kezelő szolgáltatás engedélyezése:

1. Először ellenőrizze, hogy a `apiversion` a [általános fürtkonfigurációk](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) értékre van állítva `04-2017` vagy újabb verziója:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Most már engedélyezheti a javítási manager szolgáltatás a következő `addonFeatures` szakasz után a `fabricSettings` szakasz alább látható módon:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Ezeket a módosításokat, a frissített fürtjegyzék használatával frissítse a fürtjegyzék [hozzon létre egy új fürtöt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) vagy [a fürt konfigurációjának frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Ha a fürt már fut a frissített fürtjegyzék, a javítási manager rendszer szolgáltatás fut a fürtben, amelynek a neve most már megtekintheti `fabric:/System/RepairManagerService`, a rendszer services szakaszban a Service Fabric Explorerben.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Tiltsa le az összes csomóponton automatikus Windows-frissítés

Windows automatikus frissítések rendelkezésre állás elvesztése vezethet, mivel egyszerre több fürtcsomópont újraindíthatja. A patch orchestration alkalmazás alapértelmezés szerint próbálja meg a fürt minden csomópontján Windows automatikus frissítését letiltani. Javasoljuk azonban, ha a beállításokat egy rendszergazda vagy a csoportházirend által felügyelt, a Windows-frissítési szabályzat "Értesíti, mielőtt letöltéséhez" beállítás explicit módon.

## <a name="download-the-app-package"></a>Az alkalmazás-csomag letöltése

Telepítési parancsfájlok mellett alkalmazást is letölthető [archív hivatkozás](https://go.microsoft.com/fwlink/?linkid=869566).

Alkalmazás sfpkg formátumban letölthető [sfpkg hivatkozás](https://aka.ms/POA/POA.sfpkg). Ez hasznos tartalmaz [Azure Resource Manager-alapú alkalmazás központi telepítésének](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Az alkalmazás konfigurálása

A patch orchestration app viselkedését konfigurálhatja az igényeinek. Alkalmazás létrehozása vagy módosítása során az alkalmazás paraméter megadásával bírálja felül az alapértelmezett értékeket. Alkalmazás paraméterek megadásával adható meg `ApplicationParameter` , a `Start-ServiceFabricApplicationUpgrade` vagy `New-ServiceFabricApplication` parancsmagok.

|**A paraméter**        |**Típus**                          | **Részletek**|
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | Windows Update-eredményeket, amely a gyorsítótárba kerüljenek maximális számát. <br>Alapértelmezett érték 3000 feltéve, hogy a: <br> -Csomópontok száma, 20. <br> -Történik a havi csomópont frissítések száma, öt. <br> -Művelet eredmények száma 10 lehet. <br> -Az elmúlt három havi eredmény kell tárolni. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy azt jelzi, hogy a szabályzatot, amely a Windows-frissítések telepítése a Service Fabric-fürt csomópontjain a koordinátor-szolgáltatás által használandó.<br>                         Engedélyezett értékek a következők: <br>                                                           <b>NodeWise</b>. Windows Update telepítve egy csomópont egyszerre. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update egyszerre több frissítési tartományt telepítve. (A maximumot, a frissítési tartományokhoz tartozó összes csomópontját meg a Windows Update.)<br> Tekintse meg [– gyakori kérdések](#frequently-asked-questions) szakasz útmutatást a fürt számára leginkább alkalmas házirend kiválasztásában.
|LogsDiskQuotaInMB   |Hosszú  <br> (Alapértelmezett: 1024)               |Patch orchestration alkalmazás maximális mérete (MB), amely megőrizhetők a csomópontok helyi naplózza.
| WUQuery               | sztring<br>(Alapértelmezett: "IsInstalled = 0")                | Windows-frissítések lekérdezése. További információkért lásd: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logikai <br> (alapértelmezett: igaz)                 | Használja ezt a jelzőt, hogy mely frissítések letölti és telepíti. Következő értékek használata engedélyezett <br>Igaz - telepítése csak a Windows operációs rendszer frissítéseit.<br>hamis – a gép a rendelkezésre álló frissítéseket telepíti.          |
| WUOperationTimeOutInMinutes | Int <br>(Alapértelmezett: 90)                   | Megadja azt az időtartamot, bármely Windows-frissítési művelet (keresési vagy letöltése vagy telepítése). A művelet a megadott időkorláton belül nem végzi el, ha megszakadt.       |
| WURescheduleCount     | Int <br> (Alapértelmezett: 5)                  | Maximális száma a szolgáltatás átütemez a Windows update abban az esetben, ha egy művelet meghiúsul, osztályozását.          |
| WURescheduleTimeInMinutes | Int <br>(Alapértelmezett: 30) | Az időköz, amelyen a a szolgáltatás a Windows update átütemez, abban az esetben, ha hiba továbbra is fennáll. |
| WUFrequency           | Vesszővel tagolt karakterláncot (alapértelmezett: "Hetente, szerda, 7:00:00")     | Az Windows frissítésének gyakoriságát. A formátum és a lehetséges értékek a következők: <br>-Havi nn óó, például havonta, 5., 12: 22:32.<br>Megengedett értékek mező nn (nap) értékek, amelyek a tartomány 1 – 28 és a "last" között. <br> -Hetente, nap, formátumban, például hetente, kedd, 12:22:32.  <br> – Napi, formátumban, például naponta, 12:22:32.  <br> – Nincs azt jelzi, hogy a Windows Update nem végezhető.  <br><br> Vegye figyelembe, hogy idő (UTC).|
| AcceptWindowsUpdateEula | Logikai <br>(Alapértelmezett: igaz) | Állít be ezt a jelzőt, az alkalmazás fogad a Windows Update végfelhasználói licenc a gép a tulajdonos nevében.              |

> [!TIP]
> Ha azt szeretné, hogy a Windows Update azonnal elvégzi, `WUFrequency` az alkalmazás üzembe helyezési idő viszonyítva. Tegyük fel, hogy egy öt csomópontot számláló tesztfürt és tervez üzembe helyezni az alkalmazást, körülbelül 5:00 Órakor (UTC). Ha azt feltételezik, hogy az alkalmazás frissítése vagy telepítése 30 percig tart, a maximális, állítsa be a WUFrequency "Naponta, 17:30:00"

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

1. Fejezze be a fürt előkészítése az összes előfeltételként felsorolt lépéseket.
2. A patch orchestration alkalmazás például a többi Service Fabric-alkalmazást üzembe helyezése. PowerShell használatával telepítheti az alkalmazást. Kövesse a [PowerShell-lel telepítés és eltávolítás alkalmazások](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Az alkalmazás konfigurálása a központi telepítés alkalmával, át kell adnia a `ApplicationParameter` , a `New-ServiceFabricApplication` parancsmagot. Az Ön kényelme érdekében megadtuk a parancsfájl Deploy.ps1 együtt az alkalmazás. A parancsprogram használata:

    - Csatlakozás a Service Fabric-fürt használatával `Connect-ServiceFabricCluster`.
    - Hajtsa végre a megfelelő PowerShell parancsfájlt Deploy.ps1 `ApplicationParameter` értéket.

> [!NOTE]
> A parancsfájl, és az alkalmazás mappájában PatchOrchestrationApplication tartsa ugyanabban a címtárban.

## <a name="upgrade-the-app"></a>Az alkalmazás frissítése

Patch orchestration meglévő alkalmazás frissítése a PowerShell használatával, kövesse a lépéseket a [PowerShell használatával a Service Fabric-alkalmazás frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Távolítsa el az alkalmazást

Az alkalmazás eltávolításához kövesse [PowerShell-lel telepítés és eltávolítás alkalmazások](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Az Ön kényelme érdekében megadtuk a parancsfájl Undeploy.ps1 együtt az alkalmazás. A parancsprogram használata:

  - Csatlakozás a Service Fabric-fürt használatával ```Connect-ServiceFabricCluster```.

  - Hajtsa végre a PowerShell-parancsfájl Undeploy.ps1.

> [!NOTE]
> A parancsfájl, és az alkalmazás mappájában PatchOrchestrationApplication tartsa ugyanabban a címtárban.

## <a name="view-the-windows-update-results"></a>Az eredmények megtekintése a Windows Update

A patch orchestration app közzététele a REST API-k, a felhasználó korábbi eredményeinek megjelenítésére. Az eredmény JSON példát:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
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

A JSON-mezőt az alábbiakban tekintheti át.

Mező | Értékek | Részletek
-- | -- | --
OperationResult | 0 – sikeres<br> 1 – sikeres hibákkal<br> 2 – nem sikerült<br> 3 – megszakítva<br> 4 – megszakítva időkorlát | Azt jelzi, hogy általános művelet (általában egy vagy több frissítés telepítését) eredményét.
Eredménykód | Ugyanaz, mint az operationresult adatokat a | Ebben a mezőben azt jelzi, hogy egyedi frissítés telepítési művelet eredményét.
OperationType | 1 – telepítés<br> 0 – keresse meg és töltse le.| Telepítés a, amelyek akkor jelennek meg az eredményeket alapértelmezés szerint csak OperationType.
WindowsUpdateQuery | Alapértelmezett érték a "IsInstalled = 0" |Windows update frissítések kereséséhez használt lekérdezés. További információkért lásd: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | igaz – volt szükség újraindításra<br> hamis – nem volt szükség újraindításra | Azt jelzi, ha újraindítás szükséges a frissítések telepítésének befejezéséhez.

Nincs frissítés még van ütemezve, akkor az eredmény JSON je prázdná.

Jelentkezzen be a fürt Windows Update lekérdezési eredmények. Ismerje meg, a koordinátor szolgáltatást az elsődleges replika címét, és nyomja le az URL-címet a böngésző: http://&lt;REPLIKA IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

A REST-végpont a koordinátor szolgáltatást a dinamikus port van. Ellenőrizze a pontos URL-címet, tekintse meg a Service Fabric Explorert. Ha például az eredmények esetén érhető el `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


A fordított proxy engedélyezve van a fürtön, akkor is hozzáférhet, ha az URL-címet, valamint a fürtön kívüli.
A végpont, akit el kell találati: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

A fordított proxy a fürtön engedélyezéséhez kövesse a lépéseket a [fordított proxy az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> A fordított proxy konfigurálása után minden mikroszolgáltatásokhoz a fürtben, amely közzétenni egy HTTP-végpontot a fürtön kívülről címezhető.

## <a name="diagnosticshealth-events"></a>Diagnosztikai/hálózatállapot-események

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Patch orchestration alkalmazásnaplókat a rendszer a Service Fabric-futtatókörnyezet naplóiban részeként gyűjti.

Szeretne diagnosztikai eszköz vagy folyamat tetszőleges keresztül naplók rögzítésére. Patch orchestration application használ rögzített szolgáltató azonosítók alábbi eseményeket keresztül [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Rendszerállapot-jelentések

A patch orchestration alkalmazás rendszerállapot-jelentések a koordinátor szolgáltatást vagy a csomópont-ügynökszolgáltatás is közzéteszi a következő esetekben:

#### <a name="a-windows-update-operation-failed"></a>Egy Windows-frissítési művelet sikertelen

Egy csomópontot egy Windows-frissítési művelet meghiúsul, ha egy jelentés jön létre a csomópont-ügynök szolgáltatáshoz. Az állapotjelentés részleteit a problematikus csomópontot nevét tartalmazzák.

Javítás sikeresen befejeződött a problematikus csomópontot, miután a rendszer automatikusan törli a jelentést.

#### <a name="the-node-agent-ntservice-is-down"></a>A Node-Agent NTService nem működik

A Node-Agent NTService a csomópont nem működik, ha a csomópont-ügynök szolgáltatás figyelmeztetési szintű állapotjelentés jön létre.

#### <a name="the-repair-manager-service-is-not-enabled"></a>A javítási manager szolgáltatás nincs engedélyezve

Ha a repair-kezelő szolgáltatás nem található a fürtön, figyelmeztetési szintű állapotjelentés a koordináló szolgáltatás jön létre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Miért látok a fürt hibás állapotú, a patch orchestration alkalmazás futása során?**

A. A telepítési folyamat során a patch orchestration app letiltja, vagy újraindul a csomópontokon, ami növelheti ideiglenesen leáll a fürt állapotát.

Az alkalmazás a házirend alapján, vagy egy csomópont is leáll a javítási művelet során *vagy* teljes frissítési tartomány egyszerre is leáll.

A végén a Windows telepítését, a csomópontok ismét vannak engedélyezve. Indítsa újra közzé.

A következő példában a fürt hiba történt a hibás állapotú ideiglenesen két csomópont volt, mert a megsértette a MaxPercentageUnhealthNodes szabályzatot. A hiba nem átmeneti, amíg folyamatban a javítási művelet.

![Nem megfelelő állapotú-fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma nem szűnik meg, tekintse meg a hibaelhárítási szakaszt.

K. **Patch orchestration alkalmazás figyelmeztetési állapotban van.**

A. Ellenőrizze, hogy ha egy jelentés tesznek közzé az alkalmazáshoz képest-e a hiba okát. Általában a figyelmeztetés a probléma részleteit tartalmazza. Ha a probléma átmeneti, az alkalmazás az automatikus helyreállítás ebből az állapotból kellene szerepelnie.

K. **Mi a teendő, ha a fürt nem kifogástalan, és egy sürgős operációsrendszer-frissítés van szükség?**

A. A patch orchestration app nem frissítések telepítése, míg a fürt állapota nem kifogástalan. Próbálja meg a fürt hálózatra kapcsolása a tiltásának feloldása a patch orchestration munkafolyamat Kifogástalan állapotba.

K. **Kell i beállítása "NodeWise" vagy "UpgradeDomainWise" TaskApprovalPolicy a fürtöm számára?**

A. "UpgradeDomainWise" lehetővé teszi a teljes fürt javítás gyorsabban azáltal, hogy kijavítja a párhuzamos frissítési tartományokhoz tartozó összes csomópontját. Ez azt jelenti, hogy a teljes frissítési tartományokhoz tartozó lenne nem érhető el (a [letiltott](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) állapot) a javítási folyamat során.

Ezzel szemben a NodeWise"szabályzat javítások egyszerre csak egy csomópont, ez azt jelenti, hogy általános fürt javítás hosszabb ideig igénybe. Azonban, max, csak egy csomópont lenne nem érhető el (a [letiltott](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) állapot) a javítási folyamat során.

Ha a fürt fut az frissítési tartományok száma N-1 javítás ciklus (ahol N az a fürt frissítési tartományok száma), majd a szabályzat másként "UpgradeDomainWise" alatt képes elviselni, ellenkező esetben állítsa "NodeWise".

K. **Mennyi idő mindezt hajtsa végre a megfelelő csomópont javítására?**

A. Egy csomópont javítás percet is igénybe vehet (például: [A Windows Defender definíciófrissítéseit](https://www.microsoft.com/wdsi/definitions)) óra (például: [Windows összegző frissítések](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Egy csomópont javítása szükséges idő leggyakrabban függ 
 - Frissítések mérete
 - Frissítéseket, amelyek azt a javítási időszakot a alkalmazni kell száma
 - Telepítse a frissítéseket, újraindíthatja a csomópontot (ha szükséges), és újraindítás utáni telepítési lépéseket szükséges idő.
 - Virtuális gép és számítógép- és hálózati körülményekhez teljesítményét.

K. **Mennyi ideig tart egy teljes fürtre javítására?**

A. A teljes fürtöt javítása szükséges idő a következő tényezőktől függ:

- Egy csomópont javítása szükséges idő.
- A szabályzat a koordinátor szolgáltatást. – Az alapértelmezett házirend `NodeWise`, egyszerre, amelyek alacsonyabb, mint egyetlen csomópont javítás eredményeként `UpgradeDomainWise`. Példa: Ha egy csomópont a javítandó ~ 1 órát vesz igénybe, fenntartani egy 20 csomópontot (a csomópontok ugyanazon típus) javítása a fürt 5 frissítési tartománnyal 4 csomópontot tartalmazó.
    - Az egész fürt javítására irányuló körülbelül 20 óra múlva szabályzat a `NodeWise`
    - Ha a házirend ~ 5 óra múlva `UpgradeDomainWise`
- Fürt betöltés – minden javítási műveletet igényel, az ügyfél számítási feladata áthelyezése más elérhető csomópontokhoz a fürtben. Javítás alatt áll csomópont kell, hogy [letiltása](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) állapot ebben az időszakban. Ha a fürt csúcsterhelés között közel fut, a letiltását folyamat hosszabb ideig igénybe. Ezért átfogó a javítási folyamat lassú ilyen teherhordó feltételek jelenhet meg.
- Állapottal kapcsolatos hibák bármilyen során javítás - fürt [teljesítménycsökkenés](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) a [a fürt állapota](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) megszakítaná a javítási folyamatot. Ez az egész fürt javítása szükséges teljes időt volna hozzá.

K. **Miért látok néhány frissítést, a Windows Update eredmények REST API-n keresztül, de nem a számítógépen a Windows Update előzmények alapján?**

A. Egyes frissítéseket csak tűnik, a megfelelő frissítések/javítások előzményeiben. Például előfordulhat, hogy a Windows Defender frissítések vagy előfordulhat, hogy nem jelennek meg a Windows Server 2016-on Windows Update előzmények.

K. **Patch Orchestration alkalmazás használható fejlesztési fürt (egycsomópontos fürt) javítására?**

A. Nem, Patch orchestration alkalmazás javítás egycsomópontos fürt nem használható. Ezt a korlátozást a rendszer kialakításából fakad, [service fabric-rendszerszolgáltatások](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) vagy vevő alkalmazások leállhatnak, és ezért bármely javítás a javítási feladat lenne soha nem get jóvá manager javítást igényel.

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- A patch orchestration alkalmazást a felhasználó nevében fogadja el a Windows Update szoftverre a végfelhasználói licenc. Szükség esetén a beállítást is ki kell kapcsolni az alkalmazás konfigurációjában.

- A patch orchestration alkalmazás használatának és teljesítményének nyomon követéséhez telemetriát gyűjt. Az alkalmazás telemetriai követi a beállítást, a Service Fabric-futtatókörnyezet telemetriai beállítás (amely alapértelmezés szerint be van kapcsolva).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="a-node-is-not-coming-back-to-up-state"></a>A csomópont nem várható biztonsági állapot mentése

**A csomópont előfordulhat, hogy elakadt letiltását állapotban, mert**:

Egy biztonsági ellenőrzés függőben. A helyzet orvoslása érdekében győződjön meg arról, hogy elegendő csomópontok elérhető kifogástalan állapotban.

**A csomópont előfordulhat, hogy elakadt tiltva, mert**:

- A csomópont manuálisan le lett tiltva.
- A csomópont egy folyamatban lévő Azure-infrastruktúra feladat miatt le lett tiltva.
- A csomópont ideiglenesen letiltotta a patch orchestration alkalmazás javítása a csomópontot.

**A csomópont előfordulhat, hogy elakadt lefelé állapotban, mert**:

- A csomópont manuálisan okot lefelé állapotban.
- A csomópont (ami előfordulhat, hogy a patch orchestration alkalmazás által kiváltott) újraindítás alatt állnak.
- A csomópont hibás virtuális gép vagy a gép vagy a hálózati kapcsolódási problémák miatt nem működik.

### <a name="updates-were-skipped-on-some-nodes"></a>Frissítések ki lett hagyva az egyes csomópontok

A patch orchestration alkalmazást próbál meg telepíteni a Windows frissítése a átütemezése házirendnek megfelelően. A szolgáltatás megpróbálja helyreállítani a csomópontot, és hagyja ki a frissítést az alkalmazás-házirend szerint.

Ebben az esetben a csomópont-ügynök szolgáltatás figyelmeztetési szintű állapotjelentés jön létre. A Windows-frissítés eredménye is tartalmazza a hiba lehetséges okát.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A fürt állapotának hiba kerül, amíg a frissítés telepítése

Egy hibás Windows update alkalmazást vagy egy adott csomópont vagy a frissítési tartomány a fürt állapotának leállíthatja. A patch orchestration alkalmazás bármely ezt követő Windows-frissítési művelet megszünteti, mindaddig, amíg a fürt állapota kifogástalan újra.

A rendszergazdák beavatkozhat kell, és határozza meg, miért érdemes az alkalmazás vagy a fürt vált Windows-frissítés miatt nem kifogástalan.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="version-110"></a>1.1.0-s verzió
- Nyilvános kiadása

### <a name="version-111"></a>1.1.1-es
- Kijavítva a hiba a SetupEntryPoint, NodeAgentService NodeAgentNTService telepítését megakadályozó.

### <a name="version-120"></a>1.2.0-s vagy annál újabb verzió

- Hibajavítások kerülő rendszer indítsa újra a munkafolyamatot.
- Hibajavítás a létrehozásakor erőforrás-kezelő feladatot, mely állapota miatt ellenőrizze a javítási feladatok előkészítése során nem a várt módon történik.
- Windows POANodeSvc Service automatikus késleltetett automatikus módosítani az indítási módja.

### <a name="version-121"></a>1.2.1-es

- Hibajavítás a fürt skálázási munkafolyamatban. Szemétgyűjtési gyűjtemény logikai POA javítási tevékenységek nem létező csomóponthoz tartozó vezetett be.

### <a name="version-122"></a>Verzió 1.2.2

- Vegyes hibajavításokat tartalmaz.
- Bináris fájlok most már van bejelentkezve.
- Az alkalmazás sfpkg hivatkozás hozzá.

### <a name="version-130"></a>Verzió 1.3.0

- Az összes elérhető frissítés InstallWindowsOSOnlyUpdates beállítás FALSE mostantól telepíti.
- Módosult az automatikus frissítések letiltása logikáját. Ez kijavítja a hibát, az automatikus frissítések nem első letiltottuk a Server 2016 vagy újabb.
- A speciális usecases POA, mind a mikroszolgáltatások paraméteres elhelyezési korlátozás.
