---
title: "Az Azure Service Fabric-javítás vezénylési alkalmazás |} Microsoft Docs"
description: "Alkalmazás automatizálhatja az operációs rendszer a Service Fabric-fürt javítását."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/07/2018
ms.author: nachandr
ms.openlocfilehash: 43a0675b1613e7bcf338537c1203de7df9a02fc4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>A Windows operációs rendszer a Service Fabric-fürt javítás

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

A javítás vezénylési alkalmazása az Azure Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javítását a Service Fabric-fürt leállítása nélkül.

A javítás vezénylési alkalmazást a következő szolgáltatásokat biztosítja:

- **Operációs rendszer automatikus frissítés telepítése**. Az operációs rendszer frissítéseinek automatikusan letöltődjön és települjön. Fürtcsomópont újraindítása van szükség esetén a fürt leállítása nélkül.

- **Fürttámogató javítását és állapotfigyelő integrációs**. Frissítések alkalmazása során a javítás vezénylési alkalmazás-csomópont állapotát figyeli. Fürtcsomópontok frissített egy csomópont egyszerre egy időben vagy több frissítési tartományt. Ha a fürt állapotának miatt a javítási folyamat leáll, javítását leáll súlyosbító a probléma megelőzése érdekében.

## <a name="internal-details-of-the-app"></a>Az alkalmazás belső részletei

A javítás vezénylési alkalmazást a következő alösszetevők áll:

- **Koordinátora szolgáltatás**: az állapotalapú szolgáltatás felelős:
    - A Windows Update feladatot a teljes fürt koordinációs.
    - Az eredmény a Windows Update végrehajtott műveletek tárolása.
- **Csomópont-ügynökszolgáltatás**: Ez az állapot nélküli szolgáltatás fut a Service Fabric-fürt összes csomópontján. A szolgáltatás felelős:
    - A csomópont ügynök NTService rendszerindítása.
    - A csomópont ügynök NTService figyelése.
- **Csomópont ügynök NTService**: A Windows NT-szolgáltatás lefuttat egy magasabb szintű jogosultság (rendszer). Ezzel szemben a csomópont ügynök és a koordinátor szolgáltatást, futtassa alacsonyabb szintű jogosultság (hálózati szolgáltatás). A szolgáltatás felelős a fürt összes csomópontján a következő Windows-frissítési feladatok végrehajtására:
    - A csomópont a Windows Update automatikus letiltása.
    - Letöltése és telepítése a Windows Update a házirendnek megfelelően a felhasználó van megadva.
    - A gép feladás egy vagy több Windows Update-telepítés újraindítása.
    - Windows-frissítések eredményeinek feltöltése a koordinátor szolgáltatást.
    - Jelentéskészítési rendszerállapot-jelentéseket abban az esetben, ha egy művelet sikertelen volt a kimerítsék összes ismételt próbálkozás után.

> [!NOTE]
> A javítás vezénylési alkalmazás a Service Fabric javítási manager szolgáltatás használ a letiltása vagy engedélyezése a csomópont és állapotát ellenőrzi. A javítási feladat, a javítás vezénylési alkalmazás által létrehozott nyomon követi, hogy a Windows Update folyamatot, az egyes csomópontok.

## <a name="prerequisites"></a>Előfeltételek

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A kezelő szolgáltatás engedélyezése (Ha nem már fut)

A javítás vezénylési alkalmazásnak van szüksége. a javítás manager szolgáltatás engedélyezni kell a fürtön.

#### <a name="azure-clusters"></a>Az Azure-fürtök

Azure-fürtöket helyez az ezüst tartóssági szint a repair-kezelő szolgáltatás alapértelmezés szerint engedélyezve van. Az Azure-fürtöket helyez az arany tartóssági szint esetleg, vagy nem rendelkezik a repair-kezelő szolgáltatás engedélyezve van, attól függően, hogy ezek a fürt létrehozásakor volt. Az Azure-fürtöket helyez a bronz tartóssági szint, alapértelmezés szerint nem rendelkeznek a repair-kezelő szolgáltatás engedélyezve van. Ha a szolgáltatás már engedélyezve van, megtekintheti a Service Fabric Explorer rendszer szolgáltatások szakaszának futnak.

##### <a name="azure-portal"></a>Azure Portal
Azure-portálon manager javítást engedélyezheti a fürt beállítása során. Válassza ki **Manager javítást tartalmaz** lehetőség alatt **bővítményeire** fürtkonfiguráció időpontjában.
![Az engedélyezés Manager javítást kép Azure-portálon](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Az Azure Resource Manager telepítési modell
Másik lehetőségként használhatja a [Azure Resource Manager üzembe helyezési modellben](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) ahhoz, hogy a javítási manager szolgáltatás a meglévő és új Service Fabric-fürtök. Szerezze be a sablon a fürt, amely számára telepíteni kívánja. A minta-sablonok, vagy hozzon létre egy egyéni Azure Resource Manager telepítési modell sablont. 

Ahhoz, hogy a javítási manager szolgáltatás használ [Azure Resource Manager telepítési modell sablon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Először ellenőrizze, hogy a `apiversion` értéke `2017-07-01-preview` a a `Microsoft.ServiceFabric/clusters` erőforrás. Ha nem egyezik, akkor frissítenie kell a `apiVersion` értékre `2017-07-01-preview` vagy magasabb:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most a repair-kezelő szolgáltatás engedélyezéséhez adja hozzá az alábbiakat `addonFeatures` szakasz után a `fabricSettings` szakasz:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. A fürt sablon ezekkel a módosításokkal frissítése után alkalmazza őket, és lehetővé teszik a frissítés befejezéséhez. Most már megtekintheti a javítási manager szolgáltatás fut a fürtön. Azt nevezzük `fabric:/System/RepairManagerService` részében a rendszer szolgáltatások a Service Fabric Explorerben talál. 

### <a name="standalone-on-premises-clusters"></a>Önálló helyszíni fürtök

Használhatja a [önálló Windows-fürt konfigurációs beállításainak](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) ahhoz, hogy a javítási manager szolgáltatás a meglévő és új Service Fabric-fürt.

A kezelő szolgáltatás engedélyezése:

1. Először ellenőrizze, hogy a `apiversion` a [általános fürtkonfigurációk](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) értéke `04-2017` vagy magasabb:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Most lehetővé teszi a kezelő szolgáltatás a következő `addonFeatures` szakasz után a `fabricSettings` szakasz a lent látható módon:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. A fürtjegyzékben frissítéséhez ezekkel a módosításokkal, használja a frissített fürtjegyzékben [hozzon létre egy új fürtöt](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) vagy [a fürt konfigurációjának frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). Miután a fürt frissített fürtjegyzékben fut, a javítási manager szolgáltatás fut a fürtön, melynek neve most már megtekintheti `fabric:/System/RepairManagerService`, a rendszer services szakaszban a Service Fabric Explorer.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Tiltsa le a Windows Update automatikus minden csomópont

A Windows automatikus frissítések rendelkezésre állást vezethet, mivel egyszerre több fürtcsomóponton újraindíthatja. A javítás vezénylési alkalmazás alapértelmezés szerint, megpróbálja a fürt minden csomópontján Windows automatikus frissítését letiltani. Azonban ha beállításait a rendszergazda vagy a csoportházirend felügyeli, ajánlott a Windows Update-házirendet "Értesíti, mielőtt letöltése" beállítást explicit módon.

## <a name="download-the-app-package"></a>A csomag letöltése

Telepítési parancsfájlok mellett alkalmazást is letölthető [archív hivatkozás](https://go.microsoft.com/fwlink/?linkid=869566).

Alkalmazás sfpkg formátumban letölthető a [sfpkg hivatkozás](https://go.microsoft.com/fwlink/?linkid=869567). Ez hasznos előre [Azure Resource Manager-alapú alkalmazás központi telepítésének](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Az alkalmazás konfigurálása

A javítás vezénylési alkalmazás viselkedése beállítható úgy, hogy az igényeinek. Az alapértelmezett érték felülírására történő alkalmazás paraméterében alkalmazás létrehozása vagy frissítése során. Alkalmazás paraméterek megadásával megadható `ApplicationParameter` számára a `Start-ServiceFabricApplicationUpgrade` vagy `New-ServiceFabricApplication` parancsmagok.

|**Parameter**        |**Típus**                          | **Részletek**|
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | A Windows Update eredmények, amelyek gyorsítótárazza maximális száma. <br>Alapértelmezett érték 3000 feltéve, hogy a: <br> -Csomópontok száma 20. <br> -A csomópont havonta történik frissítések száma: öt. <br> -Művelet eredmények száma 10 is lehet. <br> -Az elmúlt három hónap eredmények kell tárolni. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy azt jelzi, hogy a házirendet, amely a Windows-frissítések telepítése a Service Fabric-fürt csomópontjai között a koordinátor-szolgáltatás által használandó.<br>                         Engedélyezett értékek a következők: <br>                                                           <b>NodeWise</b>. A Windows Update telepítve egy csomópont egyszerre. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update telepítve egy frissítési tartományt egyszerre. (A maximumot, a frissítési tartományokhoz tartozó összes számítógépen lépjen a Windows Update.)
|LogsDiskQuotaInMB   |Hosszú  <br> (Alapértelmezett: 1024)               |Javítás vezénylési alkalmazás maximális mérete (MB), amely helyileg őrizhető csomópontján naplózza.
| WUQuery               | karakterlánc<br>(Alapértelmezett: "IsInstalled = 0")                | Lekérdezés, amely a Windows-frissítéseket. További információkért lásd: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Logikai <br> (alapértelmezett: igaz)                 | Ez a jelző lehetővé teszi, hogy a Windows operációs rendszer frissítések telepítését.            |
| WUOperationTimeOutInMinutes | Int <br>(Alapértelmezett: 90).                   | Megadja azt az időtartamot, a Windows Update művelet (keresési vagy letöltése vagy telepítése). Ha a művelet nem végzi el a megadott időkorláton belül, megszakadt.       |
| WURescheduleCount     | Int <br> (Alapértelmezett: 5).                  | Maximális száma a szolgáltatás reschedules a Windows update, abban az esetben, ha egy művelet hiba folyamatosan fennáll.          |
| WURescheduleTimeInMinutes | Int <br>(Alapértelmezett: 30). | Az időköz, ahol a a szolgáltatás a Windows update reschedules, abban az esetben, ha hiba továbbra is fennáll. |
| WUFrequency           | Vesszővel elválasztott karakterlánc (alapértelmezett: "Heti, szerda, 7:00:00")     | A Windows-frissítések telepítésével kapcsolatos gyakoriságát. A formátum és a lehetséges értékek a következők: <br>– Havonta, NN ÓÓ: pp:, például havi, 5, 12: 22:32. <br> -Hetente, nap, formátumban, például hetente, kedd, 12:22:32.  <br> -Napi, óó: pp:, ha például naponta, 12:22:32.  <br> -Nincs azt jelzi, hogy a Windows Update nem végezhető el.  <br><br> Vegye figyelembe, hogy hányszor UTC formátumban.|
| AcceptWindowsUpdateEula | Logikai <br>(Alapértelmezett: igaz) | Ez a jelző beállításával az alkalmazás Windows Update végfelhasználói licenc a gép a tulajdonos nevében fogad el.              |

> [!TIP]
> Ha azt szeretné, azonnal megtörténjen-e a Windows Update, `WUFrequency` relatív az alkalmazás telepítési idejét. Tegyük fel, hogy öt csomópontból tesztet a fürt, és tervezze meg az alkalmazás központi telepítése a következő körülbelül 5:00 PM UTC. Ha azt feltételezi, hogy az alkalmazás frissítése vagy a központi telepítési 30 percig tart a lehető, állítsa be a WUFrequency "Naponta, 17:30:00."

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

1. Fejezze be a előfeltételként szükséges lépéseket a fürt előkészítéséhez.
2. A javítás vezénylési alkalmazás mint bármely más Service Fabric-alkalmazás telepítése. A PowerShell használatával telepítheti az alkalmazást. Kövesse a [központi telepítése és törlése alkalmazás PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. A telepítés során az alkalmazás konfigurálásához adja át a `ApplicationParamater` számára a `New-ServiceFabricApplication` parancsmag. Az Ön kényelme érdekében nyújtunk a parancsfájl Deploy.ps1 és az alkalmazás. A parancsprogram használata:

    - Csatlakozás a Service Fabric-fürt használatával `Connect-ServiceFabricCluster`.
    - Hajtsa végre a megfelelő PowerShell parancsfájlt Deploy.ps1 `ApplicationParameter` érték.

> [!NOTE]
> A parancsfájl és az alkalmazás mappájában PatchOrchestrationApplication ne ugyanabban a könyvtárban.

## <a name="upgrade-the-app"></a>Az alkalmazás frissítése

Egy meglévő javítás vezénylési alkalmazás frissítése a PowerShell használatával, kövesse a [az alkalmazásfrissítés Service Fabric PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Távolítsa el az alkalmazást

Az alkalmazás eltávolításához kövesse [központi telepítése és törlése alkalmazás PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Az Ön kényelme érdekében nyújtunk a parancsfájl Undeploy.ps1 és az alkalmazás. A parancsprogram használata:

  - Csatlakozás a Service Fabric-fürt használatával ```Connect-ServiceFabricCluster```.

  - A PowerShell parancsfájl Undeploy.ps1 hajtható végre.

> [!NOTE]
> A parancsfájl és az alkalmazás mappájában PatchOrchestrationApplication ne ugyanabban a könyvtárban.

## <a name="view-the-windows-update-results"></a>A Windows Update eredmények megtekintése

A javítás vezénylési alkalmazás közzététele a REST API-kat a felhasználó számára a korábbi eredmények megtekintése céljából. Az eredmény JSON példát:
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

A JSON mezőjének az alábbiakban található.

Mező | Értékek | Részletek
-- | -- | --
OperationResult | 0 - sikeres<br> 1 – sikeres volt, a hibák<br> 2 – nem sikerült<br> 3 - megszakítva<br> 4 - megszakadt az időkorlát | Azt jelzi, hogy általános műveletet (jellemzően egy vagy több frissítés telepítését) eredményét.
ResultCode | Ugyanaz, mint az operationresult adatokat a | Ez a mező egy egyedi frissítés telepítési művelet eredményét jelzi.
OperationType | 1 - telepítés<br> 0 - keresés, és töltse le.| A csak művelettípus, amelyek akkor jelennek meg az eredmények alapértelmezés szerint egy telepítés.
WindowsUpdateQuery | Alapértelmezett érték "IsInstalled = 0" |A Windows update frissítések kereséséhez használt lekérdezés. További információkért lásd: [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | igaz – volt szükség újraindításra<br> hamis - nem volt szükség újraindításra | Azt jelzi, ha újraindítás szükséges a frissítések telepítésének befejezéséhez.

Ha még nincs frissítés ütemezett, JSON eredménye üres.

Jelentkezzen be a fürtöt, a Windows Update lekérdezés eredményeit. Ezután az a koordinátor szolgáltatást az elsődleges replika cím, majd nyomja le az URL-CÍMÉT a böngésző: http://&lt;REPLIKA IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

A koordinátor szolgáltatás REST-végpont dinamikus-porttal rendelkezik. A pontos URL-cím ellenőrzéséhez tekintse meg a Service Fabric Explorerben talál. Például az eredmények érhetők el: `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


A fordított proxy engedélyezve van a fürt, ha az URL-CÍMÉT, valamint a fürtön kívüli végezheti el.
A végpont, amelyet a kell találati: http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Ahhoz, hogy a fürt fordított proxy, kövesse a lépéseket [fordított proxy az Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> A fordított proxy konfigurálása után minden micro szolgáltatások a fürt egy HTTP-végpont visszaállítását a fürtön kívüli megcímezhető.

## <a name="diagnosticshealth-events"></a>Diagnosztika/állapotával kapcsolatos események

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Javítás vezénylési app naplókat a rendszer a Service Fabric-futtatókörnyezet naplók részeként gyűjti.

Abban az esetben, ha szeretné rögzíteni a naplók diagnosztikai eszköz/folyamat a kiválasztott keresztül. Javítás vezénylési alkalmazás használ rögzített szolgáltató azonosítók alatt keresztül események naplózása [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Állapotjelentések száma

A javítás vezénylési app állapotjelentések a koordinátor szolgáltatást vagy a csomópont-ügynökszolgáltatás is közzéteszi a következő esetekben:

#### <a name="a-windows-update-operation-failed"></a>A Windows Update művelet sikertelen volt

A Windows-frissítési művelet nem sikerül, a csomópont, a jelentés a csomópont-ügynökszolgáltatás elleni jön létre. Az állapotjelentés részleteit tartalmazzák a problematikus csomópont nevét.

Miután javítása sikeresen befejeződött a problematikus csomóponton, a rendszer automatikusan törli a jelentést.

#### <a name="the-node-agent-ntservice-is-down"></a>A csomópont ügynök NTService nem működik

A csomópont ügynök NTService a csomópont nem működik, a csomópont-ügynökszolgáltatás elleni figyelmeztetési szintű állapotjelentése jön létre.

#### <a name="the-repair-manager-service-is-not-enabled"></a>A kezelő szolgáltatás nincs engedélyezve

A kezelő szolgáltatás nem található a fürtön, a koordinátor szolgáltatás figyelmeztetési szintű állapotjelentése jön létre.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Q. **Miért látom azt a fürt állapota a javítás vezénylési alkalmazás futtatásakor?**

A. A telepítés során a javítás vezénylési app letiltja, vagy újraindítja a csomópontot, ideiglenesen is a fürt állapotának ennek eredményeként.

Az alkalmazás a házirend alapján, vagy több csomópont is leáll a javítási művelet során *vagy* teljes frissítési tartományok egyidejűleg is leáll.

A Windows Update telepítés végén, a csomópont újra engedélyezve utáni újraindítás.

A következő példában a fürt lett hibaállapota ideiglenesen jön létre, mert a két csomópont le volt, és a MaxPercentageUnhealthNodes házirend megsértettek. A hiba nem ideiglenes, amíg a javítási művelet folyamatban.

![A nem megfelelő fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma továbbra is fennáll, nézze meg a hibaelhárítási szakaszban.

Q. **Javítás vezénylési app figyelmeztetési állapotban van.**

A. Ellenőrizze, hogy ha egy állapotjelentése az alkalmazáshoz képest közzé-e az alapvető okát. A figyelmeztetés általában, a probléma részleteit tartalmazza. Ha a probléma nem átmeneti, az alkalmazás automatikus helyreállítás ebből az állapotból várt.

Q. **Mi a teendő, ha a fürt állapota nem megfelelő, és a sürgős operációs rendszer frissítés teendő?**

A. A javítás vezénylési app nem telepíti a frissítések, miközben a fürt állapota nem megfelelő. Próbálja meg a fürt hálózatra kapcsolása a tiltásának feloldása a javítás vezénylési app munkafolyamat Kifogástalan állapotba.

Q. **Miért több fürtjére kiterjedő javítását valóban túl sokáig futtatásához?**

A. Az idő a javítás vezénylési alkalmazás szükséges alapvetően a következő tényezőktől függ:

- A házirend a koordinátor szolgáltatást. 
  - Az alapértelmezett házirend `NodeWise`, egyszerre csak egy csomópont javítását eredményez. Különösen, ha egy nagyobb fürt, azt javasoljuk, hogy használja a `UpgradeDomainWise` házirend több fürtjére kiterjedő gyorsabb javítását eléréséhez.
- A letöltés és telepítés elérhető frissítések száma. 
- Töltse le és telepítse a frissítéshez szükséges átlagos időtartam amely nem lehet hosszabb néhány óra múlva.
- A virtuális gép és a hálózati sávszélesség teljesítményét.

Q. **Miért látom néhány frissítést, a Windows Update eredmények REST API-n keresztül, de nem a számítógépen a Windows Update előzmények alapján?**

A. Egyes frissítéseket csak a megfelelő frissítési/javítás előzmények jelennek meg. Például a Windows Defender frissítések nem jelennek meg a Windows Server 2016-os Windows Update előzmények.

## <a name="disclaimers"></a>Felelősséget kizáró nyilatkozatok

- A javítás vezénylési alkalmazást a Windows Update szoftverre vonatkozó végfelhasználói licencet a felhasználó nevében fogad el. Szükség esetén a beállítást is ki kell kapcsolni az alkalmazás konfigurációjában.

- A javítás vezénylési app telemetriai nyomon követéséhez a használati és teljesítményadatokat gyűjt. A telemetriai követi a beállítást, a Service Fabric-futtatókörnyezet telemetriai beállítás (amely alapértelmezés szerint be van).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="a-node-is-not-coming-back-to-up-state"></a>A csomópont nem érkezik vissza állapot mentése

**A csomópont Beragadt letiltása állapotban mert**:

A biztonsági ellenőrzés folyamatban. Ebben a helyzetben a kijavításához ellenőrizze, hogy elég csomópont elérhető állapota kifogástalan.

**A csomópont Beragadt letiltott állapotban, mert**:

- A csomópont manuálisan le lett tiltva.
- A csomópont egy Azure infrastruktúra folyamatban lévő feladat miatt le lett tiltva.
- A csomópont ideiglenesen letiltotta a javítás vezénylési alkalmazás javítására, de a csomópont.

**A csomópont Beragadt lefelé állapotban mert**:

- A csomópont manuálisan lefelé állapotba helyezték.
- A csomópont (ami előfordulhat, hogy a javítás vezénylési alkalmazás által kiváltott) újraindítás alatt állnak.
- A csomópont hibás VM vagy a gép vagy a hálózati csatlakozási problémák miatt nem működik.

### <a name="updates-were-skipped-on-some-nodes"></a>Frissítések kihagyta egyes csomópontokon

A javítás vezénylési app is megpróbál telepíteni egy Windows update a átütemezése házirendnek megfelelően. A szolgáltatás megpróbálja helyreállítani a csomópontot, és az alkalmazás házirendjének megfelelően a frissítés kihagyása.

Ebben az esetben a figyelmeztetési szintű állapotjelentése szemben a csomópont-ügynökszolgáltatás jön létre. A Windows Update eredménye is tartalmazza a hiba lehetséges okát.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A fürt állapotának hibás kerül, amíg a frissítés telepítése

A hibás Windows update leállíthatja az alkalmazások vagy a fürt egy adott csomópont vagy a frissítési tartomány állapotát. A javítás vezénylési app bármely későbbi Windows-frissítési művelet megszünteti az addig, amíg a fürt újra nem működik megfelelően.

A rendszergazda kell beavatkozhat, és határozza meg, miért az alkalmazás vagy a fürt nem kifogástalanná válásának Windows Update miatt.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="version-110"></a>1.1.0-ás verzió
- Nyilvános kiadás

### <a name="version-111"></a>1.1.1 verziója
- Programhiba rögzített SetupEntryPoint a NodeAgentService, amely megakadályozta a NodeAgentNTService telepítését.

### <a name="version-120"></a>1.2.0 verzió

- Hibajavítások körül rendszer indítsa újra a munkafolyamatot.
- Hiba a javítás mely állapota miatt ellenőrzése során a helyreállítási feladatok előkészítése nem történik, várt módon RM feladatok létrehozásához.
- Megváltozott az indítási mód windows POANodeSvc Service automatikus késleltetett automatikusra.

### <a name="version-121-latest"></a>1.2.1-es (legújabb)

- Hiba a javítás fürt lefelé méretezéshez munkafolyamatban. Szemétgyűjtési gyűjtemény logika POA javítási tevékenységek nem létező csomóponthoz tartozó vezette be.
