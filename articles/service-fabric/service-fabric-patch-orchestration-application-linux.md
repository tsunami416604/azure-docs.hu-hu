---
title: "Azure Service Fabric javítás vezénylési alkalmazással Linux |} Microsoft Docs"
description: "Alkalmazás automatizálhatja az operációs rendszer a Linux Service Fabric-fürt javítását."
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
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: dac8068705e284b04d84d128eb1ce62c459d44ff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>A Linux operációs rendszert a Service Fabric-fürt javítás

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
    - A feladat az operációs rendszer frissítése a teljes fürtre koordinációs.
    - Az operációs rendszer frissítési végrehajtott műveletek eredményét tárolásához.
- **Csomópont-ügynökszolgáltatás**: Ez az állapot nélküli szolgáltatás fut a Service Fabric-fürt összes csomópontján. A szolgáltatás felelős:
    - A csomópont ügynök démon Linux rendszerindítása.
    - A démon szolgáltatás figyelése.
- **Csomópont ügynök démon**: A Linux-démon szolgáltatás lefuttat egy magasabb szintű jogosultság (root). Ezzel szemben a csomópont ügynök és a koordinátor szolgáltatást, futtassa alacsonyabb szintű jogosultság. A szolgáltatás felelős a fürt összes csomópontján a következő frissítési feladatok végrehajtására:
    - Letiltása folyamatban van a csomópont automatikus az operációs rendszer frissítése.
    - Letölti és telepíti az operációs rendszer frissítés a házirendnek megfelelően a felhasználó van megadva.
    - A gép feladás egy vagy több operációs rendszer telepítése újraindítása, ha szükséges.
    - Operációs rendszer frissítése érdekében eredményeinek feltöltése a koordinátor szolgáltatást.
    - Jelentéskészítési rendszerállapot-jelentéseket abban az esetben, ha egy művelet sikertelen volt a kimerítsék összes ismételt próbálkozás után.

> [!NOTE]
> A javítás vezénylési alkalmazás a Service Fabric javítási manager szolgáltatás használ a letiltása vagy engedélyezése a csomópont és állapotát ellenőrzi. A javítási feladat, a javítás vezénylési alkalmazás által létrehozott követi nyomon, hogy minden csomópont a frissítés folyamatban.

## <a name="prerequisites"></a>Előfeltételek

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Győződjön meg arról, hogy az Azure virtuális gépeken futnak Ubuntu 16.04
Ebben a dokumentumban Ubuntu 16.04 írása idején (`Xenial Xerus`) az egyetlen támogatott verzió.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Győződjön meg arról, hogy a service fabric linux-fürt verzió 6.1.x vagy újabb verzió

Javítás vezénylési app linux használja a futásidejű bizonyos funkcióinak, amely csak a service fabric futásidejű verzióját 6.1.x vagy újabb verzió.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A kezelő szolgáltatás engedélyezése (Ha nem már fut)

A javítás vezénylési alkalmazásnak van szüksége. a javítás manager szolgáltatás engedélyezni kell a fürtön.

#### <a name="azure-clusters"></a>Az Azure-fürtök

Az Azure linux fürtökön használható a az ezüst és arany tartóssági szint a repair-kezelő szolgáltatás alapértelmezés szerint engedélyezve van. Az Azure-fürtöket helyez a bronz tartóssági szint, alapértelmezés szerint nem rendelkeznek a repair-kezelő szolgáltatás engedélyezve van. Ha a szolgáltatás már engedélyezve van, megtekintheti a Service Fabric Explorer rendszer szolgáltatások szakaszának futnak.

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

Különálló Service Fabric Linux-fürtök nem támogatják a jelen dokumentum írása idején.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Tiltsa le az összes olyan csomóponton, automatikus az operációs rendszer

Automatikus operációs rendszer frissítése érdekében előfordulhat, hogy előfordulhat, hogy a rendelkezésre állást és vagy a futó alkalmazások viselkedését. A javítás vezénylési alkalmazás alapértelmezés szerint próbál tiltsa le az automatikus frissítés az operációs rendszer a fürt minden csomópontján, az ilyen helyzetek megelőzése érdekében.
Az Ubuntu [felügyelet nélküli frissítések](https://help.ubuntu.com/community/AutomaticSecurityUpdates) javítás vezénylési alkalmazás le vannak tiltva.

## <a name="download-the-app-package"></a>A csomag letöltése

Töltse le az alkalmazást a [letöltése hivatkozásra](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Az alkalmazás konfigurálása

A javítás vezénylési alkalmazás viselkedése beállítható úgy, hogy az igényeinek. Az alapértelmezett érték felülírására történő alkalmazás paraméterében alkalmazás létrehozása vagy frissítése során. Alkalmazás paraméterek megadásával megadható `ApplicationParameter` számára a `Start-ServiceFabricApplicationUpgrade` vagy `New-ServiceFabricApplication` parancsmagok.

|**Parameter**        |**Típus**                          | **Részletek**|
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | Frissítés eredmények, amelyek gyorsítótárazza maximális száma. <br>Alapértelmezett érték 3000 feltéve, hogy a: <br> -Csomópontok száma 20. <br> -A csomópont havonta történik frissítések száma: öt. <br> -Művelet eredmények száma 10 is lehet. <br> -Az elmúlt három hónap eredmények kell tárolni. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy azt jelzi, hogy a házirendet, amely a frissítések telepítése a Service Fabric-fürt csomópontjai között a koordinátor-szolgáltatás által használandó.<br>                         Engedélyezett értékek a következők: <br>                                                           <b>NodeWise</b>. A frissítései telepített egy csomópont egyszerre. <br>                                                           <b>UpgradeDomainWise</b>. A frissítései telepített több frissítési tartományt egyszerre. (A maximumot, a frissítési tartományokhoz tartozó összes számítógépen lépjen a frissítéshez.)
| UpdateOperationTimeOutInMinutes | Int <br>(Alapértelmezett: 180)                   | Megadja azt az időtartamot, a frissítési művelet (letöltése vagy telepítése). Ha a művelet nem végzi el a megadott időkorláton belül, megszakadt.       |
| RescheduleCount      | Int <br> (Alapértelmezett: 5).                  | Maximális száma a szolgáltatás reschedules az operációs rendszer frissítése, abban az esetben, ha egy művelet hiba folyamatosan fennáll.          |
| RescheduleTimeInMinutes  | Int <br>(Alapértelmezett: 30). | Az időköz, ahol a szolgáltatás miatti újraütemezés az operációs rendszer frissítése, ha a probléma továbbra is fennáll. |
| UpdateFrequency           | Vesszővel elválasztott karakterlánc (alapértelmezett: "Heti, szerda, 7:00:00")     | Az operációs rendszer frissítéseinek telepítése a fürt gyakoriságát. A formátum és a lehetséges értékek a következők: <br>– Havonta, NN ÓÓ: pp:, például havi, 5, 12:22:32. <br> -Hetente, nap, formátumban, például hetente, kedd, 12:22:32.  <br> -Napi, óó: pp:, ha például naponta, 12:22:32.  <br> -Nincs azt jelzi, ez a frissítés nem végezhető el.  <br><br> Minden időpontok UTC formátumban.|
| UpdateClassification | Vesszővel elválasztott karakterlánc (alapértelmezett: "securityupdates") | A fürt csomópontjain telepítendő frissítések típusát. Elfogadható értékek a következők securityupdates, minden. <br> -securityupdates - csak a biztonsági frissítéseket telepíti <br> az összes rendelkezésre álló frissítéseket a apt - összes - telepíti.|
| ApprovedPatches | Vesszővel elválasztott karakterlánc (alapértelmezett: "") | A rendszer a jóváhagyott frissítések fürtcsomóponton telepítve kell lennie. A vesszővel elválasztott listáját tartalmazza a jóváhagyott csomagok és opcionálisan kívánt célverzió.<br> például: "apt utils 1.2.10ubuntu1, python3-jwt-t, apt átviteli https < 1.2.194, libsystemd0 = > 229-4ubuntu16 =" <br> A fenti telepíti <br> -apt utils rendelkező verzió 1.2.10ubuntu1, ha az elérhető apt-gyorsítótárában. Adott verzió nem érhető el, akkor célszerű műveletvégzés. <br> -python3-jwt frissítések elérhető legújabb verzióra. A csomag nincs jelen, majd esetén műveletvégzés. <br> -verziójához, amely kisebb, mint 1.2.194 apt átviteli https-frissítések. Ebben a verzióban nincs jelen, majd esetén műveletvégzés. <br> -verziójához, amely nagyobb vagy egyenlő 229-4ubuntu16 libsystemd0 frissítések. Ha ilyen verzió nem létezik, akkor célszerű műveletvégzés.|
| RejectedPatches | Vesszővel elválasztott karakterlánc (alapértelmezett: "") | A lista nem kell telepíteni a fürtcsomópontokon <br> például: "bash, sudo" <br> Az előző kiszűri a bash, sudo a frissítéseket. |


> [!TIP]
> Ha azt szeretné, azonnal megtörténjen-e az operációs rendszer frissítése, `UpdateFrequency` relatív az alkalmazás telepítési idejét. Tegyük fel, hogy öt csomópontból tesztet a fürt, és tervezze meg az alkalmazás központi telepítése a következő körülbelül 5:00 PM UTC. Ha azt feltételezi, hogy az alkalmazás frissítése vagy a központi telepítési 30 percig tart a lehető, állítsa be a UpdateFrequency "Naponta, 17:30:00."

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

1. Készítse elő a fürt befejező az előzetesen szükséges lépések leírását.
2. A javítás vezénylési alkalmazás mint bármely más Service Fabric-alkalmazás telepítése. PowerShell vagy Azure Service Fabric parancssori felület használatával telepítheti az alkalmazást. Kövesse a [központi telepítése és törlése alkalmazás PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) vagy [Azure Service Fabric parancssori felület használatával alkalmazás központi telepítése](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Konfigurálja az alkalmazást a telepítés során, adja át a `ApplicationParamater` számára a `New-ServiceFabricApplication` parancsmag vagy a parancsfájlok. Az Ön kényelme érdekében powershell (Deploy.ps1) és (Deploy.sh) bash parancsfájlok megadott együtt az alkalmazást. A parancsprogram használata:

    - Csatlakozás a Service Fabric-fürt.
    - Hajtsa végre a központi telepítés parancsfájlt. Az alkalmazás paraméter nem kötelező továbbadni a parancsfájlnak. például:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Naponta, 11:00:00"} OR./Deploy.sh "{\"UpdateFrequency\":\"naponta, 11:00:00\"}" 

> [!NOTE]
> A parancsfájl és az alkalmazás mappájában PatchOrchestrationApplication ne ugyanabban a könyvtárban.

## <a name="upgrade-the-app"></a>Az alkalmazás frissítése

Egy meglévő javítás vezénylési alkalmazást frissítéséhez kövesse a [az alkalmazásfrissítés Service Fabric PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) vagy [Service Fabric az alkalmazásfrissítés Azure Service Fabric parancssori felület használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Távolítsa el az alkalmazást

Az alkalmazás eltávolításához kövesse [központi telepítése és törlése alkalmazás PowerShell-lel](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) vagy [Azure Service Fabric CLI-t használó alkalmazások eltávolítása](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Az Ön kényelme érdekében powershell (Undeploy.ps1) és (Undeploy.sh) bash parancsfájlok megadott együtt az alkalmazást. A parancsprogram használata:

  - Csatlakozás a Service Fabric-fürt.
  - A parancsfájl Undeploy.ps1 vagy Undeploy.sh végrehajtása

> [!NOTE]
> A parancsfájl és az alkalmazás mappájában PatchOrchestrationApplication ne ugyanabban a könyvtárban.

## <a name="view-the-update-results"></a>A frissítési eredmények megtekintése

A javítás vezénylési alkalmazás közzététele a REST API-kat a felhasználó számára a korábbi eredmények megtekintése céljából. Az alábbiakban látható egy minta eredmény: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

A JSON a mezők az alábbiak szerint:

Mező | Értékek | Részletek
-- | -- | --
OperationResult | 0 - sikeres<br> 1 – sikeres volt, a hibák<br> 2 – nem sikerült<br> 3 - megszakítva<br> 4 - megszakadt az időkorlát | Azt jelzi, hogy általános műveletet (jellemzően egy vagy több frissítés telepítését) eredményét.
ResultCode | Ugyanaz, mint az operationresult adatokat a | Ez a mező egy egyedi frissítés telepítési művelet eredményét jelzi.
OperationType | 1 - telepítés<br> 0 - keresés, és töltse le.| A csak művelettípus, amelyek akkor jelennek meg az eredmények alapértelmezés szerint egy telepítés.
UpdateClassification | Alapértelmezett érték "securityupdates" | A frissítési művelet során telepített frissítések típusa
UpdateFrequency | Alapértelmezett érték "Heti, szerda, 7:00:00" | Frissítési gyakoriság, a frissítés konfigurálva.
RebootRequired | igaz – volt szükség újraindításra<br> hamis - nem volt szükség újraindításra | Azt jelzi, újraindítás szükséges a frissítések telepítésének befejezéséhez.
ApprovedList | Alapértelmezett érték a "" | A frissítés jóváhagyott javítások
RejectedList | Alapértelmezett érték a "" | A frissítés elutasított javítások

Ha még nincs frissítés ütemezett, JSON eredménye üres.

Jelentkezzen be a fürt frissítési érhetünk el a lekérdezést. Ezután az a koordinátor szolgáltatást az elsődleges replika cím, majd nyomja le az URL-CÍMÉT a böngésző: http://&lt;REPLIKA IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

A koordinátor szolgáltatás REST-végpont dinamikus-porttal rendelkezik. A pontos URL-cím ellenőrzéséhez tekintse meg a Service Fabric Explorerben talál. Például az eredmények érhetők el: `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnosztika/állapotával kapcsolatos események

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Javítás vezénylési app naplókat a rendszer a Service Fabric-futtatókörnyezet naplók részeként gyűjti.

Abban az esetben, ha szeretné rögzíteni a naplók diagnosztikai eszköz/folyamat a kiválasztott keresztül. Javítás vezénylési alkalmazás a következő használja rögzített szolgáltató azonosítók keresztül események naplózása [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Állapotjelentések száma

A javítás vezénylési app állapotjelentések a koordinátor szolgáltatást vagy a csomópont-ügynökszolgáltatás is közzéteszi a következő esetekben:

#### <a name="an-update-operation-failed"></a>Frissítési művelet nem sikerült

Frissítési művelet nem sikerül, a csomópont, a jelentés jön létre, a csomópont Agent szolgáltatásra. Az állapotjelentés részleteit tartalmazzák a problematikus csomópont nevét.

Miután javítása sikeresen befejeződött a problematikus csomóponton, a rendszer automatikusan törli a jelentést.

#### <a name="the-node-agent-daemon-service-is-down"></a>A csomópont ügynök démon szolgáltatás nem működik

A csomópont ügynök démon szolgáltatás nem működik egy csomóponton, szemben a csomópont-ügynökszolgáltatás figyelmeztetési szintű állapotjelentése jön létre.

#### <a name="the-repair-manager-service-is-not-enabled"></a>A kezelő szolgáltatás nincs engedélyezve

Figyelmeztetési szintű állapotjelentés a koordinátor szolgáltatás generál, ha a kezelő szolgáltatás nem található a fürtön.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Q. **Miért látom azt a fürt állapota a javítás vezénylési alkalmazás futtatásakor?**

A. A telepítés során a javítás vezénylési app letiltja, vagy újraindítja a csomópontot. Ez a művelet ideiglenesen is a fürt állapotának eredményez.

Az alkalmazás a házirend alapján, vagy több csomópont is leáll a javítási művelet során *vagy* teljes frissítési tartományok egyidejűleg is leáll.

A telepítés végén, a csomópont újra engedélyezve utáni újraindítás.

A következő példában a fürt lett hibaállapota ideiglenesen jön létre, mert a két csomópont le volt, és a MaxPercentageUnhealthyNodes házirend megsértette a kapott a következő. A hiba nem ideiglenes, amíg a javítási művelet folyamatban.

![A nem megfelelő fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma továbbra is fennáll, nézze meg a hibaelhárítási szakaszban.

Q. **Javítás vezénylési app figyelmeztetési állapotban van.**

A. Ellenőrizze, hogy ha egy állapotjelentése az alkalmazáshoz képest közzé-e az alapvető okát. A figyelmeztetés általában, a probléma részleteit tartalmazza. Ha a probléma nem átmeneti, az alkalmazás automatikus helyreállítás ebből az állapotból várt.

Q. **Mi a teendő, ha a fürt állapota nem megfelelő, és a sürgős operációs rendszer frissítés teendő?**

A. A javítás vezénylési app nem telepíti a frissítések, miközben a fürt állapota nem megfelelő. A javítás vezénylési app munkafolyamat feloldásához a fürt hálózatra kapcsolása, Kifogástalan állapotba.

Q. **Miért több fürtjére kiterjedő javítását valóban túl sokáig futtatásához?**

A. Az idő a javítás vezénylési alkalmazás szükséges alapvetően a következő tényezőktől függ:

- A házirend a koordinátor szolgáltatást. 
  - Az alapértelmezett házirend `NodeWise`, egyszerre csak egy csomópont javítását eredményez. Különösen, ha egy nagyobb fürt, azt javasoljuk, hogy használja a `UpgradeDomainWise` házirend fürtön gyorsabb javítását eléréséhez.
- A letöltés és telepítés elérhető frissítések száma. 
- Töltse le és telepítse a frissítéshez szükséges átlagos időtartam amely nem lehet hosszabb néhány óra múlva.
- A virtuális gép és a hálózati sávszélesség teljesítményét.

Q. **Biztonsági frissítések úgy dönt, hogyan segített javítás vezénylési alkalmazás mely frissítések érhetők el.**

A. Javítás vezénylési alkalmazás distro-specifikus logika használ a meghatározásához, hogy mely frissítések közül a rendelkezésre álló frissítéseket biztonsági frissítések érhetők el. Például: az ubuntu archívumokat $RELEASE frissítéseket keres az alkalmazás-biztonsági, $RELEASE-frissítések ($RELEASE = xenial, illetve a linux szabványos alap). 

 
Q. **Hogyan zárolhatja be egy csomag verzióját?**

A. A ApprovedPatches beállítások segítségével zárolja a csomagok egy adott verzióra. 


Q. **Mi történik, az automatikus frissítések engedélyezve az Ubuntu?**

A. Amint telepítette a javítás vezénylési alkalmazást a fürtön lévő, a felügyelet nélküli frissítéseket a fürtcsomóponton le fogja tiltani. A rendszeres frissítés munkafolyamat volna vezérlik a javítás vezénylési alkalmazást.
Ahhoz, hogy a környezet konzisztencia fürtön, javasoljuk, keresztül javítás vezénylési alkalmazás csak a frissítések telepítése. 
 
Q. **Frissítés utáni vezénylési app tegye a nem használt csomagok karbantartása nem javítás?**

A. Igen, a tisztítás telepítés utáni lépéseket részeként történik. 

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

A javítás vezénylési app megpróbál telepíteni egy frissítést a átütemezése házirendnek megfelelően. A szolgáltatás megpróbálja helyreállítani a csomópontot, és az alkalmazás házirendjének megfelelően a frissítés kihagyása.

Ebben az esetben a figyelmeztetési szintű állapotjelentése szemben a csomópont-ügynökszolgáltatás jön létre. A frissítés eredménye is tartalmazza a hiba lehetséges okát.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A fürt állapotának hibás kerül, amíg a frissítés telepítése

Hibás frissítés leállíthatja az alkalmazások vagy a fürt egy adott csomópont vagy a frissítési tartomány állapotát. A javítás vezénylési app bármely ezt követő frissítési műveletek megszünteti, addig, amíg a fürt újra nem működik megfelelően.

A rendszergazda kell beavatkozásra, és határozza meg, miért az alkalmazás vagy a fürt nem kifogástalanná válásának miatt a korábban telepített frissítést.

## <a name="disclaimer"></a>Jogi nyilatkozat

A javítás vezénylési app telemetriai nyomon követéséhez a használati és teljesítményadatokat gyűjt. A telemetriai követi a beállítást, a Service Fabric-futtatókörnyezet telemetriai beállítás (amely alapértelmezés szerint be van).

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="version-010"></a>0.1.0 verzió
- Privát előzetes kiadás

### <a name="version-200-latest"></a>2.0.0 verzió (legújabb)
- Nyilvános kiadás
