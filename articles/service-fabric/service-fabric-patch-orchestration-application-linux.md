---
title: Az Azure Service Fabric patch orchestration alkalmazás linuxos |} A Microsoft Docs
description: Az alkalmazás a Linux Service Fabric-fürt operációs rendszer javításának automatizálása.
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
ms.openlocfilehash: 27650605601a24e11d63e56343535c35c8b72f5d
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285152"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>A Linux operációs rendszer a Service Fabric-fürtben

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

A patch orchestration application az Azure Service Fabric-alkalmazás, amely automatizálja az operációs rendszer javításának Service Fabric-fürt, üzemkimaradás nélkül.

A patch orchestration alkalmazást a következő szolgáltatásokat biztosítja:

- **Az operációs rendszer telepítése**. Az operációs rendszer frissítése automatikusan letöltődjön és települjön. Fürtcsomópontok fürt állásidő nélkül igény szerint indulnak újra.

- **A fürttámogató javításait és egészségügyi integrációs**. Frissítések alkalmazása során a patch orchestration alkalmazást a fürt csomópontjainak állapotát figyeli. Fürtcsomópontok frissített egy csomópont egyszerre egy időben vagy több frissítési tartományt. Ha a fürt állapota miatt a javítási folyamat leáll, javítás leállt súlyosbító a probléma elkerülése érdekében.

## <a name="internal-details-of-the-app"></a>Az alkalmazás belső részletei

A patch orchestration alkalmazást a következő alösszetevők tevődik össze:

- **Koordinátor szolgáltatást**: az állapotalapú szolgáltatás felelős:
    - Az operációs rendszer frissítési feladat az egész fürt összehangolása.
    - Az eredmény a befejezett operációsrendszer-frissítési műveletek tárolására.
- **Csomópont-ügynökszolgáltatás**: A állapotmentes szolgáltatás fut a Service Fabric-fürt összes csomópontján. A szolgáltatás felelős:
    - A Linuxon futó csomóponti ügynök démon rendszerindításra.
    - A démon szolgáltatás figyelésére.
- **Csomópont ügynök démon**: A Linux-démon szolgáltatás fut le egy magasabb szintű jogosultságot (root). Ezzel szemben a csomópont az ügynök és a koordinátor szolgáltatást futtatni egy alacsonyabb szintű jogosultságokkal. A szolgáltatás felelős a a fürt összes csomópontján a következő frissítési feladatok végrehajtása:
    - A csomóponton automatikus operációsrendszer-frissítés letiltása.
    - Letöltése és telepítése az operációs rendszer frissítése a házirend szerint a felhasználó adta meg.
    - A gép bejegyzés operációsrendszer-frissítés telepítése újraindítása, ha szükséges.
    - Operációs rendszer frissítése eredményeinek feltöltését a koordinátor szolgáltatást.
    - Jelentéskészítési rendszerállapot-jelentéseket abban az esetben, ha egy művelet nem sikerült kimerítése összes újrapróbálkozás után.

> [!NOTE]
> A patch orchestration app letiltása vagy engedélyezése a csomópont és állapotellenőrzéseket hajthat végre a Service Fabric javítási manager rendszer szolgáltatást használja. A javítási feladat a patch orchestration alkalmazás által létrehozott minden egyes csomópont esetében a frissítési folyamatot követi nyomon.

## <a name="prerequisites"></a>Előfeltételek

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Győződjön meg arról, hogy az Azure-beli virtuális gépek futnak Ubuntu 16.04
Ez a dokumentum, Ubuntu 16.04 írása idején (`Xenial Xerus`) az egyetlen támogatott verzió.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Győződjön meg arról, hogy a service fabric linux-fürt verziója 6.2.x vagy újabb verzió

Patch orchestration app linux használ modul szolgáltatásokat, amelyek csak érhetők el a service fabric-futtatókörnyezet verziója 6.2.x vagy újabb verzió.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>A repair-kezelő szolgáltatás engedélyezése (Ha nincs már fut)

A patch orchestration alkalmazás szükséges a javítási manager rendszerszolgáltatás engedélyezni kell a fürtön.

#### <a name="azure-clusters"></a>Az Azure-fürtök

Az Azure linux-fürtök a tagjainak silver és gold tartóssági szint a repair-kezelő szolgáltatás alapértelmezés szerint engedélyezve van. Az Azure-fürtöket a bronz tartóssági szint, alapértelmezés szerint nem rendelkezik a repair-kezelő szolgáltatás engedélyezve van. Ha a szolgáltatás már engedélyezve van, a rendszer-services szakaszban a Service Fabric Explorert a működés láthatja.

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

Önálló Service Fabric Linux-fürtökön nem támogatottak a jelen dokumentum írása idején.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Az összes csomóponton automatikus operációsrendszer-frissítés letiltani

Operációs rendszer automatikus frissítései előfordulhat, hogy rendelkezésre állási elvesztését, és vagy módosítani a futó alkalmazások működésében. A patch orchestration alkalmazás alapértelmezés szerint próbálja meg a fürt minden csomópontján, hogy az ilyen forgatókönyvek például az automatikus operációsrendszer-frissítés letiltani.
Az Ubuntu [felügyelet nélküli frissítéseket](https://help.ubuntu.com/community/AutomaticSecurityUpdates) patch orchestration alkalmazás le van tiltva.

## <a name="download-the-app-package"></a>Az alkalmazás-csomag letöltése

Telepítési parancsfájlok mellett alkalmazást is letölthető [archív hivatkozás](https://go.microsoft.com/fwlink/?linkid=867984).

Alkalmazás sfpkg formátumban letölthető [sfpkg hivatkozás](https://aka.ms/POA/POA_v2.0.2.sfpkg). Ez hasznos tartalmaz [Azure Resource Manager-alapú alkalmazás központi telepítésének](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Az alkalmazás konfigurálása

A patch orchestration app viselkedését konfigurálhatja az igényeinek. Alkalmazás létrehozása vagy módosítása során az alkalmazás paraméter megadásával bírálja felül az alapértelmezett értékeket. Alkalmazás paraméterek megadásával adható meg `ApplicationParameter` , a `Start-ServiceFabricApplicationUpgrade` vagy `New-ServiceFabricApplication` parancsmagok.

|**A paraméter**        |**Típus**                          | **Részletek**|
|:-|-|-|
|MaxResultsToCache    |Hosszú                              | Frissítés eredményeket, amely a gyorsítótárba kerüljenek maximális számát. <br>Alapértelmezett érték 3000 feltéve, hogy a: <br> -Csomópontok száma, 20. <br> -Történik a havi csomópont frissítések száma, öt. <br> -Művelet eredmények száma 10 lehet. <br> -Az elmúlt három havi eredmény kell tárolni. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy azt jelzi, hogy a szabályzatot, amely a frissítések telepítése a Service Fabric-fürt csomópontjain a koordinátor-szolgáltatás által használandó.<br>                         Engedélyezett értékek a következők: <br>                                                           <b>NodeWise</b>. A frissítések olyan telepített egy csomópont egyszerre. <br>                                                           <b>UpgradeDomainWise</b>. Frissítések egyszerre több frissítési tartományt telepített állnak. (A maximumot, a frissítési tartományokhoz tartozó összes csomópontját meg frissítés.)
| UpdateOperationTimeOutInMinutes | Int <br>(Alapértelmezett: 180)                   | Megadja azt az időtartamot, minden olyan frissítési művelet (letöltése vagy telepítése). A művelet a megadott időkorláton belül nem végzi el, ha megszakadt.       |
| RescheduleCount      | Int <br> (Alapértelmezett: 5).                  | A szolgáltatás átütemez az operációs rendszer maximális száma abban az esetben, ha egy művelet meghiúsul, tartósan frissítése.          |
| RescheduleTimeInMinutes  | Int <br>(Alapértelmezés: 30). | Az időköz, ahol a szolgáltatás az operációs rendszer átütemez frissítés abban az esetben, ha hiba továbbra is fennáll. |
| UpdateFrequency           | Vesszővel tagolt karakterláncot (alapértelmezett: "Hetente, szerda, 7:00:00")     | A gyakoriság, az operációs rendszer frissítéseinek telepítése a fürtön. A formátum és a lehetséges értékek a következők: <br>-Ha például havonta, 5., 12:22:32 havi, NN formátumban. <br> -Hetente, nap, formátumban, például hetente, kedd, 12:22:32.  <br> – Napi, formátumban, például naponta, 12:22:32.  <br> – Nincs azt jelzi, hogy a frissítés nem hajtható végre.  <br><br> Minden esetben vannak (UTC).|
| UpdateClassification | Vesszővel tagolt karakterláncot (alapértelmezett: "securityupdates") | A fürt csomópontjain telepítendő frissítések típusát. Elfogadható értékek a következők securityupdates, minden. <br> -securityupdates – csak a biztonsági frissítéseket telepíti <br> az összes elérhető frissítés az apt - all - telepíti.|
| ApprovedPatches | Vesszővel tagolt karakterláncot (alapértelmezett: "") | Ez az a jóváhagyott frissítések, amelyek a fürtcsomópontokat, telepítenie kell. A vesszővel tagolt listában tartalmazza a jóváhagyott csomagok és igény szerint kívánt cílová verze.<br> például: "apt utils 1.2.10ubuntu1, python3-jwt, apt-átviteli – https < 1.2.194, libsystemd0 = > 229-4ubuntu16 =" <br> A fenti telepíti <br> -verzió 1.2.10ubuntu1, ha elérhető, az apt-gyorsítótár az apt utils. Ha adott verzió nem érhető el, majd, műveletvégzés. <br> -python3-jwt frissítéseit is elérhető legújabb verzióra. Ha a csomag nem található, majd műveletvégzés. <br> -verziójához, amely kisebb, mint 1.2.194 apt-átviteli – https-frissítések. Ez a verzió nem szerepel, majd, hogy műveletvégzés. <br> -verziójához, amely nagyobb vagy egyenlő 229-4ubuntu16 libsystemd0 frissítéseket. Ha egy ilyen verzió nem létezik, majd, műveletvégzés.|
| RejectedPatches | Vesszővel tagolt karakterláncot (alapértelmezett: "") | Ez az, hogy a frissítések, amelyek nem telepíthetők a fürtcsomópontokon <br> például: "a bash, a sudo" <br> Az előző kiszűri a bash, a sudo frissítések fogadását. |


> [!TIP]
> Ha azt szeretné, hogy operációsrendszer-frissítés azonnal elvégzi, `UpdateFrequency` az alkalmazás üzembe helyezési idő viszonyítva. Tegyük fel, hogy egy öt csomópontot számláló tesztfürt és tervez üzembe helyezni az alkalmazást, körülbelül 5:00 Órakor (UTC). Ha azt feltételezik, hogy az alkalmazás frissítése vagy telepítése 30 percig tart, a maximális, állítsa be a UpdateFrequency "Naponta, 17:30:00."

## <a name="deploy-the-app"></a>Az alkalmazás üzembe helyezése

1. Készítse elő a fürt befejező az előfeltételként felsorolt lépéseket.
2. A patch orchestration alkalmazás például a többi Service Fabric-alkalmazást üzembe helyezése. Az alkalmazás a PowerShell vagy az Azure Service Fabric parancssori felület segítségével telepíthet. Kövesse a [PowerShell-lel telepítés és eltávolítás alkalmazások](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) vagy [alkalmazás üzembe helyezése az Azure Service Fabric parancssori felület használatával](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. Az alkalmazás konfigurálása a központi telepítés alkalmával, át kell adnia a `ApplicationParameter` , a `New-ServiceFabricApplication` parancsmag vagy a parancsfájlokat. Az Ön kényelme érdekében a powershell (Deploy.ps1) és (Deploy.sh) bash-szkriptek megadott együtt az alkalmazás. A parancsprogram használata:

    - Csatlakozhat a Service Fabric-fürtön.
    - Hajtsa végre az üzembe helyezés parancsfájlt. Lehetősége van átadni az alkalmazás paraméter a parancsfájlt. például:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = "Naponta, 11:00:00"} vagy./Deploy.sh "{\"UpdateFrequency\":\"naponta, 11:00:00\"}" 

> [!NOTE]
> A parancsfájl, és az alkalmazás mappájában PatchOrchestrationApplication tartsa ugyanabban a címtárban.

## <a name="upgrade-the-app"></a>Az alkalmazás frissítése

A patch orchestration meglévő alkalmazás frissítéséhez kövesse [PowerShell használatával a Service Fabric-alkalmazás frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) vagy [Azure Service Fabric parancssori felület használatával a Service Fabric-alkalmazás frissítése](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Távolítsa el az alkalmazást

Az alkalmazás eltávolításához kövesse [PowerShell-lel telepítés és eltávolítás alkalmazások](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) vagy [alkalmazások eltávolítása az Azure Service Fabric parancssori felület használatával](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Az Ön kényelme érdekében a powershell (Undeploy.ps1) és (Undeploy.sh) bash-szkriptek megadott együtt az alkalmazás. A parancsprogram használata:

  - Csatlakozhat a Service Fabric-fürtön.
  - Hajtsa végre a parancsfájl Undeploy.ps1 vagy Undeploy.sh

> [!NOTE]
> A parancsfájl, és az alkalmazás mappájában PatchOrchestrationApplication tartsa ugyanabban a címtárban.

## <a name="view-the-update-results"></a>A frissítési eredmények megtekintése

A patch orchestration app közzététele a REST API-k, a felhasználó korábbi eredményeinek megjelenítésére. Következő egy minta eredmény: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
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

A JSON mezők az alábbiak szerint:

Mező | Értékek | Részletek
-- | -- | --
Operationresult adatokat a | 0 – sikeres<br> 1 – sikeres hibákkal<br> 2 – nem sikerült<br> 3 – megszakítva<br> 4 – megszakítva időkorlát | Azt jelzi, hogy általános művelet (általában egy vagy több frissítés telepítését) eredményét.
Eredménykód | Ugyanaz, mint az operationresult adatokat a | Ebben a mezőben azt jelzi, hogy egyedi frissítés telepítési művelet eredményét.
OperationType | 1 – telepítés<br> 0 – keresse meg és töltse le.| Telepítés a, amelyek akkor jelennek meg az eredményeket alapértelmezés szerint csak OperationType.
UpdateClassification | Az alapértelmezett érték "securityupdates" | A frissítési művelet során telepített frissítések típusa
UpdateFrequency | Az alapértelmezett érték "Hetente, szerda, 7:00:00" | Frissítés gyakorisága már konfigurálva van a frissítés.
RebootRequired | igaz – volt szükség újraindításra<br> hamis – nem volt szükség újraindításra | Azt jelzi, újraindítás szükséges a frissítések telepítésének befejezéséhez.
ApprovedList | Alapértelmezett érték a "" | A frissítés jóváhagyott javítások
RejectedList | Alapértelmezett érték a "" | A frissítés elutasított javítások

Nincs frissítés még van ütemezve, akkor az eredmény JSON je prázdná.

Jelentkezzen be a fürt frissítési lekérdezéseredmények. Ismerje meg, a koordinátor szolgáltatást az elsődleges replika címét, és nyomja le az URL-címet a böngésző: http://&lt;REPLIKA IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

A REST-végpont a koordinátor szolgáltatást a dinamikus port van. Ellenőrizze a pontos URL-címet, tekintse meg a Service Fabric Explorert. Ha például az eredmények esetén érhető el `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![REST-végpont képe](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnosztikai/hálózatállapot-események

### <a name="diagnostic-logs"></a>Diagnosztikai naplók

Patch orchestration alkalmazásnaplókat a rendszer a Service Fabric-futtatókörnyezet naplóiban részeként gyűjti.

Szeretne diagnosztikai eszköz vagy folyamat tetszőleges keresztül naplók rögzítésére. Események keresztül bejelentkezni a következő rögzített szolgáltató azonosítók használja Patch orchestration application [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Rendszerállapot-jelentések

A patch orchestration alkalmazás rendszerállapot-jelentések a koordinátor szolgáltatást vagy a csomópont-ügynökszolgáltatás is közzéteszi a következő esetekben:

#### <a name="an-update-operation-failed"></a>Egy olyan frissítési művelet nem sikerült

Ha egy csomópont-frissítési művelet meghiúsul, a jelentés a csomópont-ügynök szolgáltatás jön létre. Az állapotjelentés részleteit a problematikus csomópontot nevét tartalmazzák.

Javítás sikeresen befejeződött a problematikus csomópontot, miután a rendszer automatikusan törli a jelentést.

#### <a name="the-node-agent-daemon-service-is-down"></a>A csomópont ügynök démon szolgáltatás leállt

Ha a csomópont ügynök démon szolgáltatás nem működik egy csomóponton, a csomópont-ügynök szolgáltatás figyelmeztetési szintű állapotjelentés jön létre.

#### <a name="the-repair-manager-service-is-not-enabled"></a>A javítási manager szolgáltatás nincs engedélyezve

Figyelmeztetési szintű állapotjelentése az koordinátora szolgáltatás generál, ha a javítás manager szolgáltatás nem található a fürtön.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

K. **Miért látok a fürt hibás állapotú, a patch orchestration alkalmazás futása során?**

A. A telepítés során a patch orchestration app letiltja, vagy újraindítja a csomópontot. Ez a művelet átmenetileg leáll a fürt állapotának eredményezhet.

Az alkalmazás a házirend alapján, vagy egy csomópont is leáll a javítási művelet során *vagy* teljes frissítési tartomány egyszerre is leáll.

A telepítés végén, a csomópontok ismét vannak engedélyezve. Indítsa újra közzé.

A következő példában a fürt hiba történt a hibás állapotú ideiglenesen jön létre, mert a két csomópont volt, és a MaxPercentageUnhealthyNodes szabályzat megsértve van. A hiba nem átmeneti, amíg folyamatban a javítási művelet.

![Nem megfelelő állapotú-fürt képe](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Ha a probléma nem szűnik meg, tekintse meg a hibaelhárítási szakaszt.

K. **Patch orchestration alkalmazás figyelmeztetési állapotban van.**

A. Ellenőrizze, hogy ha egy jelentés tesznek közzé az alkalmazáshoz képest-e a hiba okát. Általában a figyelmeztetés a probléma részleteit tartalmazza. Ha a probléma átmeneti, az alkalmazás az automatikus helyreállítás ebből az állapotból kellene szerepelnie.

K. **Mi a teendő, ha a fürt nem kifogástalan, és egy sürgős operációsrendszer-frissítés van szükség?**

A. A patch orchestration app nem frissítések telepítése, míg a fürt állapota nem kifogástalan. A patch orchestration munkafolyamat feloldásához hozza a fürt állapota kifogástalan.

K. **Miért nem különböző fürtökben üzemelő javítás tart sokáig futtatásához?**

A. A patch orchestration alkalmazás számára szükséges idő nagyrészt a következő tényezőktől függ:

- A szabályzat a koordinátor szolgáltatást. 
  - Az alapértelmezett házirend `NodeWise`, egyszerre csak egy csomópont javítás eredménye. Különösen akkor, ha van egy nagyobb méretű fürt, azt javasoljuk, hogy a `UpgradeDomainWise` szabályzatot, hogy gyorsabban javítás fürtön.
- A letöltés és telepítés készült frissítések száma. 
- Az átlagos idő szükséges töltse le és telepítse a frissítést, amely nem lehet hosszabb néhány óra múlva.
- A teljesítmény, a virtuális gép és a hálózati sávszélességet.

K. **Hogyan segített patch orchestration alkalmazás úgy dönt, hogy mely frissítések a biztonsági frissítéseket.**

A. Patch orchestration alkalmazás meghatározására szolgál, hogy mely frissítések között az elérhető frissítések állnak biztonsági frissítések disztribúció-specifikus logika használ. Például: az ubuntu-archívumok $RELEASE frissítéseket keres az app-biztonság, $RELEASE-frissítések ($RELEASE = xenial vagy a linux standard alap végleges verziót). 

 
K. **Hogyan zárolhat be a csomag egy adott verzióját?**

A. A ApprovedPatches beállítások segítségével a csomagok egy adott verzió zárolása. 


K. **Mi történik, az automatikus frissítések engedélyezve van, az Ubuntu?**

A. Amint a fürt patch orchestration alkalmazást telepít, a felügyelet nélküli frissítéseket a fürtcsomóponton bővítés le lesz tiltva. A rendszeres frissítés minden munkafolyamat a patch orchestration alkalmazás által lenne meghatározni.
Ahhoz, hogy a környezet konzisztencia fürtszinten, javasoljuk, telepíti a frissítéseket csak a patch orchestration alkalmazás keresztül. 
 
K. **Frissítés utáni vezénylési alkalmazás tegye fel nem használt csomagokat a karbantartás nem javítására?**

A. Igen, a karbantartása telepítés utáni lépések részeként történik. 

K. **Patch Orchestration alkalmazás használható fejlesztési fürt (egycsomópontos fürt) javítására?**

A. Nem, Patch orchestration alkalmazás javítás egycsomópontos fürt nem használható. Ezt a korlátozást a rendszer kialakításából fakad, [service fabric-rendszerszolgáltatások](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) vagy vevő alkalmazások leállhatnak, és ezért bármely javítás a javítási feladat lenne soha nem get jóvá manager javítást igényel.

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

A patch orchestration alkalmazást próbál telepíteni egy frissítést a átütemezése házirendnek megfelelően. A szolgáltatás megpróbálja helyreállítani a csomópontot, és hagyja ki a frissítést az alkalmazás-házirend szerint.

Ebben az esetben a csomópont-ügynök szolgáltatás figyelmeztetési szintű állapotjelentés jön létre. Frissítés az eredmény tartalmazza a hiba lehetséges okainak is.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>A fürt állapotának hiba kerül, amíg a frissítés telepítése

A hibás frissítése egy alkalmazás vagy egy adott csomópont vagy a frissítési tartomány a fürt állapotának leállíthatja. A patch orchestration app megszünteti a későbbi frissítési műveleteket, mindaddig, amíg a fürt állapota kifogástalan újra.

A rendszergazdák beavatkozhat kell, és határozza meg, miért érdemes az alkalmazás vagy a fürt vált egy korábban telepített frissítés miatt nem kifogástalan.

## <a name="disclaimer"></a>Jogi nyilatkozat

A patch orchestration alkalmazás használatának és teljesítményének nyomon követéséhez telemetriát gyűjt. Az alkalmazás telemetriai követi a beállítást, a Service Fabric-futtatókörnyezet telemetriai beállítás (amely alapértelmezés szerint be van kapcsolva).

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="version-010"></a>Verzió 0.1.0
- Privát előzetes verzió

### <a name="version-200"></a>2.0.0-s verzió
- Nyilvános kiadása

### <a name="version-201"></a>2.0.1-es verziója
- Újrafordítani az alkalmazást a legújabb Service Fabric SDK használatával

### <a name="version-202-latest"></a>2.0.2-es verzió (legújabb)
- Kijavítva állapotfigyelési figyelmeztetése első magányos újraindítás közben.
