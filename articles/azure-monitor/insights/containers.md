---
title: Konténerfigyelési megoldás az Azure Monitorban | Microsoft dokumentumok
description: Az Azure Monitor tárolófigyelési megoldása egyetlen helyen segíti a Docker- és Windows-tárolóállomások megtekintését és kezelését.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664694"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Tárolófigyelési megoldás az Azure Monitorban

![Konténerek szimbólum](./media/containers/containers-symbol.png)

Ez a cikk ismerteti, hogyan állíthatja be és használhatja a Tárolófigyelés i Solution it As A Docker és a Windows tárolóállomásait egyetlen helyen. A Docker egy szoftveres virtualizációs rendszer, amely olyan tárolók at hoz létre, amelyek automatizálják a szoftvertelepítést az informatikai infrastruktúrájukban.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A megoldás megmutatja, hogy mely tárolók futnak, milyen tárolórendszerképet futtatnak, és hol futnak a tárolók. A tárolókkal használt parancsokat megjelenítő részletes naplózási információkat tekintheti meg. A tárolókat pedig a központosított naplók megtekintésével és keresésével háríthatja el anélkül, hogy távolról kellene megtekintenie a Docker- vagy Windows-gazdagépeket. Olyan tárolókat találhat, amelyek zajosak lehetnek, és felesleges erőforrásokat fogyasztanak a gazdagépen. A központi processzor,memória, tárolás és a tárolók hálózati használati és teljesítményinformációi is megtekinthetők. Windows rendszert futtató számítógépeken központosíthatja és összehasonlíthatja a Windows Server, Hyper-V és Docker tárolók naplóit. A megoldás a következő tárolóvezőket támogatja:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Ha az [Azure Service Fabricben](../../service-fabric/service-fabric-overview.md)telepített tárolókat, azt javasoljuk, hogy engedélyezve mind a [Service Fabric-megoldás,](../../service-fabric/service-fabric-diagnostics-oms-setup.md) mind ez a megoldás fürtesemények figyelése. A Service Fabric-megoldás engedélyezése előtt tekintse át [a Service Fabric-megoldás használatával,](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) hogy mi biztosítja, és hogyan használhatja azt.

Ha az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett Kubernetes-környezetekben üzembe helyezett számítási feladatok teljesítményének figyelése érdekli, olvassa el [az Azure Kubernetes-szolgáltatás figyelése című témakört.](../../azure-monitor/insights/container-insights-overview.md) A tárolófigyelési megoldás nem támogatja a platform figyelését.  

Az alábbi ábrán a különböző tároló-gazdagépek és ügynökök közötti kapcsolatok az Azure Monitor.

![Tárolók diagramja](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Rendszerkövetelmények és támogatott platformok

Mielőtt elkezdené, tekintse át az alábbi részleteket, hogy ellenőrizze, megfelel-e az előfeltételeknek.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Tárolófigyelési megoldások támogatása a Docker Orchestrator és az OS platform számára

Az alábbi táblázat ismerteti a Docker vezénylési és operációs rendszer figyelése támogatja a tárolók leltár, teljesítmény és naplók az Azure Monitor.   

| | ACS | Linux | Windows | Tároló<br>Leltár | Kép<br>Leltár | Csomópont<br>Leltár | Tároló<br>Teljesítmény | Tároló<br>Esemény | Esemény<br>Napló | Tároló<br>Napló |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mezoszféra<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Raj | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Szolgáltatás<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Nyitott<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(önálló) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux szerver<br>(önálló) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Linuxon támogatott Docker-verziók

- Docker 1,11-1,13
- Docker CE és EE 17.06-os

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>az x64-es Linux-disztribúciók tárolóállomásként támogatottak

- Ubuntu 14.04 LTS és 16.04 LTS
- CoreOS(stabil)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 és 7.3
- SLES 12
- RHEL 7.2 és 7.3
- Red Hat OpenShift konténerplatform (OCP) 3.4 és 3.5
- ACS Mezoszféra DC/OS 1.7.3 -1.8.8
- ACS Kubernetes 1.4.5-1.6
    - A Kubernetes-eseményeket, a Kubernetes-leltárt és a tárolófolyamatokat csak a Linux-alapú Log Analytics-ügynök 1.4.1-45-ös és újabb verziójával támogatják.
- ACS Docker Raj

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional vagy Enterprise)

### <a name="docker-versions-supported-on-windows"></a>A Windows rendszer által támogatott Docker-verziók

- Docker 1.12 és 1.13
- Docker 17.03.0 és újabb

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

1. Adja hozzá a Tárolófigyelési megoldást a Log Analytics-munkaterülethez az [Azure piactérről,](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) vagy a [Megoldások galériából figyelési megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című részben ismertetett folyamat használatával.

2. Telepítse és használja a Docker-t egy Log Analytics-ügynökkel. Az operációs rendszer és a Docker orchestrator alapján a következő módszerekkel konfigurálhatja az ügynök.
   - Önálló gazdagépek esetén:
     - A támogatott Linux operációs rendszereken telepítse és futtassa a Dockert, majd telepítse és konfigurálja a [Log Analytics ügynököt Linuxra.](../../azure-monitor/learn/quick-collect-linux-computer.md)  
     - A CoreOS-on nem futtatható a Log Analytics-ügynök Linuxra. Ehelyett a Log Analytics-ügynök Linux-verziójának tárolóba adott verzióját futtatja. Tekintse át a Linux-tárológazdak, beleértve a CoreOS-t vagy az Azure Government Linux-tárolóállomásokat, beleértve a CoreOS-t is, ha tárolókkal dolgozik az Azure Government Cloudban.
     - Windows Server 2016 és Windows 10 rendszerben telepítse a Docker-motort és az ügyfelet, majd csatlakoztasson egy ügynököt az adatok gyűjtéséhez és az Azure Monitornak való elküldéséhez. Ha Windows-környezettel rendelkezik, [tekintse át a Windows-tárolóállomások telepítése és konfigurálása](#install-and-configure-windows-container-hosts) című témakört.
   - Docker többállomásos vezénylési:
     - Ha rendelkezik Red Hat OpenShift környezettel, tekintse át a Log Analytics-ügynök konfigurálása a Red Hat OpenShift-hez című részt.
     - Ha rendelkezik egy Kubernetes-fürttel az Azure Container Service használatával:
       - Tekintse [át A Log Analytics Linux-ügynök konfigurálása a Kubernetes számára című áttekintést.](#configure-a-log-analytics-linux-agent-for-kubernetes)
       - Tekintse [át A Log Analytics Windows-ügynök konfigurálása a Kubernetes alkalmazáshoz című áttekintést.](#configure-a-log-analytics-windows-agent-for-kubernetes)
       - Tekintse át a Helm használatával a Log Analytics-ügynök linuxos Kubernetes-en való üzembe helyezéséhez.
     - Ha rendelkezik egy Azure Container Service DC/OS-fürttel, további információ az [Azure Container Service DC/OS-fürt figyelése az Azure Monitor lal](../../container-service/dcos-swarm/container-service-monitoring-oms.md)című.
     - Ha docker-raj módú környezettel rendelkezik, további információ a Docker Swarm Log Analytics-ügynök konfigurálása című oldalon.
     - Ha rendelkezik egy Service Fabric-fürttel, további információ az [Azure Monitor tárolóinak figyelése oldalon.](../../service-fabric/service-fabric-diagnostics-oms-containers.md)

Tekintse át a [Docker Engine windowsos](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) cikkében további információt a Docker-motorok Windows rendszert futtató számítógépeken történő telepítéséről és konfigurálásáról.

> [!IMPORTANT]
> A Dockernek futnia kell, **mielőtt** telepíti a [Log Analytics-ügynököt Linuxra](../../azure-monitor/learn/quick-collect-linux-computer.md) a tárológazdagépeken. Ha már telepítette az ügynököt a Docker telepítése előtt, újra kell telepítenie a Log Analytics-ügynököt Linuxra. A Dockerről további információt a [Docker webhelyén talál.](https://www.docker.com)

### <a name="install-and-configure-linux-container-hosts"></a>Linux-tárolóállomások telepítése és konfigurálása

A Docker telepítése után a következő beállításokkal konfigurálhatja az ügynököt a Docker használatával. Először szüksége van a Log Analytics-munkaterület-azonosítójára és kulcsára, amelyet az Azure Portalon talál. A munkaterületen kattintson a **Gyorsindítási** > **számítógépek** elemre a **munkaterület-azonosító** és **az elsődleges kulcs**megtekintéséhez.  Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

**A CoreOS kivételével az összes Linux-tárolóállomás esetében:**

- A Log Analytics-ügynök Linuxhoz való telepítésével kapcsolatos további információkért és a [Log Analytics-ügynök áttekintése című témakörben olvashat bővebben.](../../azure-monitor/platform/log-analytics-agent.md)

**Az összes Linux tárolóállomásesetén, beleértve a CoreOS-t is:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Az Azure Government Linux tárolóállomások, beleértve a CoreOS-t is:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Váltás telepített Linux-ügynökről egy tárolóban lévőre**

Ha korábban a közvetlenül telepített ügynök, és szeretné, hogy ehelyett egy ügynök fut egy tárolóban, először el kell távolítania a Log Analytics ügynök Linuxra. Lásd: [A Log Analytics-ügynök linuxos eltávolítása](../../azure-monitor/learn/quick-collect-linux-computer.md) az ügynök sikeres eltávolításának megértéséhez.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Log Analytics-ügynök konfigurálása a Docker Swarm számára

A Log Analytics-ügynök globális szolgáltatásként futtatható a Docker Swarm.You can run the Log Analytics agent as a global service on Docker Swarm. A következő információk segítségével hozzon létre egy Log Analytics-ügynök szolgáltatás. Meg kell adnia a Log Analytics-munkaterület-azonosítóját és elsődleges kulcsát.

- Futtassa a következőket a fő csomóponton.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Biztonságos titkok a Docker Swarm számára

A Docker Swarm, miután a munkaterület-azonosító és elsődleges kulcs titkos létrehozása, használja a következő információkat a titkos adatok létrehozásához.

1. Futtassa a következőket a fő csomóponton.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Ellenőrizze, hogy a titkos kulcsok megfelelően jöttek-e létre.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Futtassa a következő parancsot a titkos kulcsok csatlakoztatásához a tárolóba állított Log Analytics-ügynökhöz.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Log Analytics-ügynök konfigurálása a Red Hat OpenShift szolgáltatáshoz

A Log Analytics-ügynök hozzáadása háromféleképpen a Red Hat OpenShift a tárolófigyelési adatok gyűjtésének megkezdéséhez.

* [Telepítse a Log Analytics ügynök Linuxhoz](../../azure-monitor/learn/quick-collect-linux-computer.md) közvetlenül minden OpenShift-csomópontra  
* [Log Analytics virtuálisgép-bővítmény engedélyezése](../../azure-monitor/learn/quick-collect-azurevm.md) az Azure-ban található minden OpenShift-csomóponton  
* A Log Analytics-ügynök telepítése OpenShift démonkészletként  

Ebben a szakaszban a Log Analytics-ügynök OpenShift démonkészletként történő telepítéséhez szükséges lépéseket ismertetik.  

1. Jelentkezzen be az OpenShift főkiszolgáló csomópontra, és másolja az [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) yaml fájlt a GitHubról a fő csomópontra, és módosítsa az értéket a Log Analytics-munkaterület-azonosítóval és az elsődleges kulccsal.
2. A következő parancsokkal hozzon létre egy projektet az Azure Monitor számára, és állítsa be a felhasználói fiókot.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. A démonkészlet telepítéséhez futtassa a következőket:

    `oc create -f ocp-omsagent.yaml`

4. A konfigurálás és a megfelelő működésének ellenőrzéséhez írja be a következőket:

    `oc describe daemonset omsagent`  

    és a kimenetnek a következőkhöz kell hasonlítania:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Ha titkos kulcsokat szeretne használni a Log Analytics-munkaterület-azonosító és az elsődleges kulcs védelméhez a Log Analytics-ügynök démonkészletű yaml fájl használatakor, hajtsa végre a következő lépéseket.

1. Jelentkezzen be az OpenShift főkiszolgáló csomópontra, és másolja az [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) yaml yaml yaml fájlt és a titkos parancsfájlt [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) a GitHubról.  Ez a parancsfájl létrehozza a titkos kulcsok yaml fájlt a Log Analytics-munkaterület-azonosító és elsődleges kulcs a titkos adatok védelme érdekében.  
2. A következő parancsokkal hozzon létre egy projektet az Azure Monitor számára, és állítsa be a felhasználói fiókot. A titkos létrehozási parancsfájl a Log `<WSID>` Analytics-munkaterület-azonosítót és az elsődleges kulcsot `<KEY>` kéri, és a befejezéskor létrehozza az ocp-secret.yaml fájlt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Telepítse a titkos fájlt a következő futtatásával:

    `oc create -f ocp-secret.yaml`

4. Ellenőrizze a telepítést a következők futtatásával:

    `oc describe secret omsagent-secret`  

    és a kimenetnek a következőkhöz kell hasonlítania:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Telepítse a Log Analytics-ügynök démonkészletű yaml fájlját a következő futtatásával:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Ellenőrizze a telepítést a következők futtatásával:

    `oc describe ds oms`

    és a kimenetnek a következőkhöz kell hasonlítania:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Log Analytics Linux-ügynök konfigurálása a Kubernetes számára

A Kubernetes, egy parancsfájl segítségével hozza létre a titkos yaml fájlt a munkaterület-azonosító és elsődleges kulcs a Log Analytics-ügynök Linux telepítéséhez. A [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) lapon vannak olyan fájlok, amelyek a titkos adatokkal vagy azok nélkül használhatók.

- A Linux DaemonSet alapértelmezett Log Analytics-ügynöke nem rendelkezik titkos adatokkal (omsagent.yaml)
- A Log Analytics ügynök Linux DaemonSet yaml fájl titkos információkat (omsagent-ds-secrets.yaml) titkos generációs parancsfájlok létrehozásához a titkos yaml (omsagentsecret.yaml) fájl létrehozásához.

Választhat, hogy hozzon létre omsagent DaemonSets vagy titkok nélkül.

**Alapértelmezett OMSagent DaemonSet yaml fájl titkok nélkül**

- Az alapértelmezett Log Analytics ügynök DaemonSet yaml fájlt, cserélje ki a `<WSID>` és `<KEY>` a WSID és a KEY. Másolja a fájlt a fő csomópontra, és futtassa a következőket:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Alapértelmezett OMSagent DaemonSet yaml fájl titkos**

1. A Log Analytics ügynök DaemonSet titkos adatok használatával, először hozza létre a titkos kulcsokat.
    1. Másolja a parancsfájlt és a titkos sablonfájlt, és győződjön meg arról, hogy ugyanazon a könyvtáron vannak.
        - Titkos generáló parancsfájl - secret-gen.sh
        - titkos sablon - secret-template.yaml
    2. Futtassa a parancsfájlt, mint például a következő. A parancsfájl kéri a Log Analytics-munkaterület azonosítóját és elsődleges kulcs, és miután beírta őket, a parancsfájl létrehoz egy titkos yaml fájlt, így futtathatja azt.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Hozza létre a titkos kulcsokpodat a következő futtatásával:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Az ellenőrzéshez futtassa a következőket:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        A kimenetnek a következőkre kell hasonlítania:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        A kimenetnek a következőkre kell hasonlítania:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Az omsagent démonkészlet létrehozása a```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Ellenőrizze, hogy a DaemonSet Log Analytics-ügynök fut-e, hasonlóan az alábbiakhoz:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

A Kubernetes, egy parancsfájl segítségével hozza létre a titkos kulcsokyaml fájlt a munkaterület-azonosító és elsődleges kulcs a Log Analytics-ügynök Linux. Használja a következő példa információkat az [omsagent yaml fájlt](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) a titkos adatok védelméhez.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Log Analytics Windows-ügynök konfigurálása a Kubernetes alkalmazáshoz

A Windows Kubernetes, egy parancsfájl segítségével hozza létre a titkos yaml fájlt a munkaterület-azonosító és elsődleges kulcs a Log Analytics-ügynök telepítéséhez. A [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) lapon vannak olyan fájlok, amelyek et a titkos adatokkal használhat.  A fő- és ügynökcsomópontokhoz külön kell telepítenie a Log Analytics-ügynököt.  

1. A Log Analytics-ügynök Használata DaemonSet titkos információk használatával a fő csomóponton, jelentkezzen be, és először hozza létre a titkos kulcsokat.
    1. Másolja a parancsfájlt és a titkos sablonfájlt, és győződjön meg arról, hogy ugyanazon a könyvtáron vannak.
        - Titkos generáló parancsfájl - secret-gen.sh
        - titkos sablon - secret-template.yaml

    2. Futtassa a parancsfájlt, mint például a következő. A parancsfájl kéri a Log Analytics-munkaterület azonosítóját és elsődleges kulcs, és miután beírta őket, a parancsfájl létrehoz egy titkos yaml fájlt, így futtathatja azt.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Az omsagent démonkészlet létrehozása a```kubectl create -f omsagentsecret.yaml```
    4. Az ellenőrzéshez futtassa a következőket:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        A kimenetnek a következőkre kell hasonlítania:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Az omsagent démonkészlet létrehozása a```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Ellenőrizze, hogy a DaemonSet Log Analytics-ügynök fut-e, hasonlóan az alábbiakhoz:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Ha telepíteni szeretné az ügynököt a Windows rendszert futtató munkavégző csomópontra, kövesse a [Windows tárolóállomások telepítése és konfigurálása](#install-and-configure-windows-container-hosts)című szakaszlépéseit.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>A Helm használata a Log Analytics-ügynök linuxos Kubernetes-en történő üzembe helyezéséhez

Helm használatával a Log Analytics-ügynök linuxos Kubernetes-környezetben való üzembe helyezéséhez hajtsa végre a következő lépéseket.

1. Az omsagent démonkészlet létrehozása a```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Az eredmények a következőhöz hasonlóan fognak kinézni:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Az omsagent állapotát a következő futással ellenőrizheti: ```helm status "omsagent"``` és a kimenet a következőhöz hasonlóan fog kinézni:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    További információkért kérjük, látogasson el [Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Windows-tárolóállomások telepítése és konfigurálása

A szakaszban található információk segítségével telepítse és konfigurálja a Windows tárolóállomásokat.

#### <a name="preparation-before-installing-windows-agents"></a>Előkészítés a Windows-ügynökök telepítése előtt

Mielőtt ügynököket telepítene a Windows rendszert futtató számítógépekre, konfigurálnia kell a Docker-szolgáltatást. A konfiguráció lehetővé teszi, hogy a Windows-ügynök vagy az Azure Monitor virtuálisgép-bővítmény a Docker TCP-szoftvercsatorna használatával, hogy az ügynökök hozzáférhetnek a Docker démon távolról, és a figyelési adatok rögzítése.

##### <a name="to-configure-the-docker-service"></a>A Docker szolgáltatás konfigurálása  

A TCP-cső és a elnevezett cső Windows Server hez való engedélyezéséhez hajtsa végre a következő PowerShell-parancsokat:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

A Windows-tárolókhoz használt Docker démonkonfigurációról a [Windows docker-motorjában](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)talál további információt.

#### <a name="install-windows-agents"></a>Windows-ügynökök telepítése

A Windows és a Hyper-V tárolófigyelés engedélyezéséhez telepítse a Microsoft Monitoring Agent (MMA) alkalmazást a tárolóállomásnak beálló Windows számítógépekre. A helyszíni környezetben Windows rendszert futtató számítógépek ről a [Windows-számítógépek csatlakoztatása az Azure Monitorhoz című](../../azure-monitor/platform/agent-windows.md)témakörben található. Az Azure-ban futó virtuális gépek hez csatlakoztassa őket az Azure Monitorhoz a [virtuálisgép-bővítmény](../../azure-monitor/learn/quick-collect-azurevm.md)használatával.

Figyelheti a Service Fabric en futó Windows-tárolókat. A Service Fabric azonban jelenleg csak [az Azure-ban futó virtuális gépek](../../azure-monitor/learn/quick-collect-azurevm.md) és a helyszíni [környezetben windowsos számítógépek támogatottak.](../../azure-monitor/platform/agent-windows.md)

Ellenőrizheti, hogy a tárolófigyelési megoldás megfelelően van-e beállítva a Windows rendszerben. Annak ellenőrzéséhez, hogy a felügyeleti csomag letöltése megfelelő volt-e, keresse meg *a ContainerManagement.xxx.* A fájloknak a C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs mappában kell lenniük.

## <a name="solution-components"></a>Megoldás-összetevők

Az Azure Portalon keresse meg a *Megoldások galériát,* és adja hozzá a **tárolófigyelési megoldást.** Ha Windows-ügynököket használ, a megoldás hozzáadásakor minden számítógépen telepítve lesz a következő felügyeleti csomag. A felügyeleti csomaghoz nincs szükség konfigurációra vagy karbantartásra.

- *ContainerManagement.xxx* telepítve a C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs mappában

## <a name="container-data-collection-details"></a>Tárolóadatgyűjtés részletei

A tárolófigyelési megoldás különböző teljesítménymutatókat és naplóadatokat gyűjt a tároló-tárolókból és a tárolókból az Ön által lehetővé teszügynökök használatával.

Az adatokat a következő ügynöktípusok hárompercenként gyűjtik.

- [Log Analytics ügynök Linuxra](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows-ügynök](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics virtuálisgép-bővítmény](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázat példákat mutat be a Tárolófigyelés idomítva gyűjtött rekordokra és a naplókeresési eredményekben megjelenő adattípusokra.

| Adattípus | Adattípus a naplókeresésben | Mezők |
| --- | --- | --- |
| Teljesítmény gazdagépekhez és tárolókhoz | `Perf` | Számítógép, Objektumnév, CounterName &#40;%Processzoridő, Lemezolvasások MB, Lemezírások MB, Memóriahasználat MB, Hálózati fogadási bájtok, Hálózati küldési bájtok, Processzorhasználat másodperc, Hálózati&#41;, CounterValue,TimeGenerated, CounterPath, SourceSystem |
| Konténerkészlet | `ContainerInventory` | TimeGenerált, Számítógép, tároló név, ContainerHostname, Kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Konténerkép-leltár | `ContainerImageInventory` | TimeGenerált, Számítógép, Kép, ImageTag, ImageSize, VirtualSize, Futás, Szüneteltetve, Leállítva, Nem sikerült, SourceSystem, ImageID, TotalContainer |
| Tárolónapló | `ContainerLog` | TimeGenerált, Számítógép, lemezképazonosító, tároló név, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Tároló szolgáltatásnaplója | `ContainerServiceLog`  | TimeGenerated, Számítógép, TimeOfCommand, Kép, Parancs, SourceSystem, ContainerID |
| Tárolócsomópont készlete | `ContainerNodeInventory_CL`| TimeGenerated, Számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, Forrásrendszer|
| Kubernetes készlet | `KubePodInventory_CL` | TimeGenerated, Számítógép, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, Forrásrendszer |
| Konténerfolyamat | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-események | `KubeEvents_CL` | TimeGenerated, Számítógép, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, Forrásrendszer, Üzenet |

A *PodLabel* adattípusokhoz hozzáfűzött címkék a saját egyéni címkék. A táblázatban látható hozzáfűzött PodLabel címkék példák. Így, `PodLabel_deployment_s` `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` , különbözni fog a környezet `PodLabel_yourlabel_s`adatkészletében, és általánosan hasonlít .

## <a name="monitor-containers"></a>Tárolók figyelése
Miután engedélyezte a megoldást az Azure Portalon, a **Tárolók** csempe összesítő információkat jelenít meg a tárológazdagépekről és az állomásokon futó tárolókról.

![Tárolók csempe](./media/containers/containers-title.png)

A csempe áttekintést nyújt arról, hogy hány tárolóvan a környezetben, és hogy azok sikertelenek, futnak vagy le vannak állítva.

### <a name="using-the-containers-dashboard"></a>A Tárolók irányítópult használata

Kattintson a **Tárolók** csempére. Innen a következő kszerint rendezve láthatja a nézeteket:

- **Tárolóesemények** – A tároló állapotát és a meghibásodott tárolókkal rendelkező számítógépeket jeleníti meg.
- **Tárolónaplók** – Az idő múlásával létrehozott tárolónapló-fájlok diagramját és a legtöbb naplófájllal rendelkező számítógépek listáját jeleníti meg.
- **Kubernetes-események** – Az idő során létrehozott Kubernetes-események diagramját jeleníti meg, valamint az események létrehozásának okait. *Ez az adatkészlet csak Linux-környezetekben használatos.*
- **Kubernetes névtér leltár** – a névterek és a podok számát jeleníti meg, és megjeleníti a hierarchiájukat. *Ez az adatkészlet csak Linux-környezetekben használatos.*
- **Tárolócsomópont-készlet** – a tárolócsomópontokon/-állomásokon használt vezénylési típusok számát jeleníti meg. A számítógép-csomópontok/állomások a tárolók száma szerint is megjelennek. *Ez az adatkészlet csak Linux-környezetekben használatos.*
- **Container Images Inventory** - A használt tárolórendszerképek teljes számát és a lemezképek számát jeleníti meg. A képek számát a képcímke is felsorolja.
- **Tárolók állapota** – A tárolókat futtató tárolócsomópontok/gazdaszámítógépek teljes számát jeleníti meg. A számítógépek a futó állomások száma szerint is megjelennek.
- **Tárolófolyamat** – Az idő múlásával futó tárolófolyamatok vonaldiagramját jeleníti meg. A tárolók a parancsok/folyamatok tárolókon belüli futtatásával is megjelennek. *Ez az adatkészlet csak Linux-környezetekben használatos.*
- **Tároló CPU-teljesítménye** – A számítógép-csomópontok/állomások átlagos CPU-kihasználtságának vonaldiagramját jeleníti meg. A számítógép-csomópontokat/állomásokat is felsorolja az átlagos processzorkihasználtság alapján.
- **Container Memory Performance** - A memóriahasználat időbeli felhasználásának vonaldiagramját jeleníti meg. A számítógép memóriakihasználtságát is felsorolja a példány neve alapján.
- **Számítógép teljesítménye** – A processzor teljesítményének időbeli százalékos arányát, a memóriahasználat időbeli százalékát és a szabad lemezterület megabájtját jeleníti meg. További részletek megtekintéséhez vigye az egérmutatót a diagram bármely sorára.

Az irányítópult minden egyes területe az összegyűjtött adatokon futó keresés vizuális ábrázolása.

![Tárolók irányítópultja](./media/containers/containers-dash01.png)

![Tárolók irányítópultja](./media/containers/containers-dash02.png)

A **Tároló állapota** területen kattintson a felső területre, az alábbiak szerint.

![Tárolók állapota](./media/containers/containers-status.png)

Megnyílik a Log Analytics, amely a tárolók állapotára vonatkozó információkat jelenít meg.

![A tárolók naplózása](./media/containers/containers-log-search.png)

Itt szerkesztheti a keresési lekérdezést, hogy módosítsa, hogy megtalálja az Önt érdeklő konkrét információkat. A naplólekérdezésekről további információt a [Lekérdezések naplózása az Azure Monitorban](../log-query/log-query-overview.md)című témakörben talál.

## <a name="troubleshoot-by-finding-a-failed-container"></a>Hibaelhárítás hibásan, hibás tároló keresésével

A Log Analytics sikertelenként jelöli meg a **tárolót,** ha nem nulla kilépési kóddal távozott. A környezetben előforduló hibák és hibák áttekintése a **Sikertelen tárolók** területen tekinthető meg.

### <a name="to-find-failed-containers"></a>Sikertelen tárolók keresése

1. Kattintson a **Tároló állapota** területre.  
   ![tárolók állapota](./media/containers/containers-status.png)
2. A Log Analytics megnyílik, és megjeleníti a tárolók állapotát, hasonlóan az alábbiakhoz.  
   ![tárolók állapota](./media/containers/containers-log-search.png)
3. Bontsa ki a Sikertelen sort, és kattintson a + gombra, ha hozzá szeretné adni a feltételeket a lekérdezéshez. Ezután fűzze hozzá a lekérdezés összegzése sort.
   ![sikertelen tárolók](./media/containers/containers-state-failed-select.png)  
1. Futtassa a lekérdezést, majd bontsa ki az eredmények egy sorát a képazonosító megtekintéséhez.  
   ![sikertelen tárolók](./media/containers/containers-state-failed.png)  
1. Írja be a következőt a naplólekérdezésbe. `ContainerImageInventory | where ImageID == <ImageID>`a kép részleteinek megtekintéséhez, például a kép méretéhez és a leállított és meghibásodott képek számához.  
   ![sikertelen tárolók](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Tárolóadatok lekérdezési naplói

Ha egy adott hibát hibaelhárításközben, az segíthet, hogy lássa, hol fordul elő a környezetben. A következő naplótípusok segítségével lekérdezéseket hozhat létre a kívánt információk visszaadásához.

- **ContainerImageInventory** - Használja ezt a típust, ha megpróbálja megtalálni a kép szerint rendezett információkat, és megtekintheti a képinformációkat, például a képazonosítókat vagy méreteket.
- **ContainerInventory** – Használja ezt a típust, ha információt szeretne a tároló helyéről, a nevükről és a lemezképekről.
- **ContainerLog** – Akkor használja ezt a típust, ha konkrét hibanapló-információkat és bejegyzéseket szeretne keresni.
- **ContainerNodeInventory_CL**  Akkor használja ezt a típust, ha a tárolók helyéről szeretne információt kapni. Docker-verziót, vezénylési típust, tárolást és hálózati információkat biztosít.
- **ContainerProcess_CL** Ezzel a típussal gyorsan megtekintheti a tárolón belül futó folyamatot.
- **ContainerServiceLog** – Használja ezt a típust, amikor a Docker démon naplózási nyomvonalának adatait keresi, például indítási, leállítási, törlési vagy lekéréses parancsokat.
- **KubeEvents_CL**  Ezzel a típussal megtekintheti a Kubernetes-eseményeket.
- **KubePodInventory_CL**  Ezt a típust akkor használja, ha meg szeretné érteni a fürthierarchia adatait.


### <a name="to-query-logs-for-container-data"></a>Tárolóadatok naplóinak lekérdezése

* Válasszon ki egy olyan lemezképet, amelyről tudja, hogy az utóbbi időben meghibásodott, és keresse meg a hibanaplókat. Kezdje azzal, hogy megtalálja a rendszerképet **ContainerInventory** tárolókészlet-kereséssel futtató tárolónevet. Például a`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Ubuntu-tárolók keresése](./media/containers/search-ubuntu.png)

  Bontsa ki az eredmények bármelyik sorát a tároló részleteinek megtekintéséhez.

## <a name="example-log-queries"></a>Példa naplólekérdezésekre

Gyakran hasznos egy-két példával kezdődő lekérdezések létrehozása, majd a környezetnek megfelelően történő módosítása. Kiindulási pontként kísérletezhet a **Mintalekérdezések** területtel, hogy segítsen a speciálisabb lekérdezések létrehozásában.

![Tárolólekérdezések](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Naplólekérdezések mentése

A lekérdezések mentése az Azure Monitor általános szolgáltatása. A mentés, akkor azokat, hogy megtalálta hasznos hasznos későbbi használatra.

Miután létrehozott egy hasznosnak talált lekérdezést, mentse azt a Naplókeresés lap tetején található **Kedvencek** elemre kattintva. Ezután később könnyedén elérheti a **Saját irányítópult** lapon.

## <a name="next-steps"></a>További lépések

[Lekérdezési naplók a](../log-query/log-query-overview.md) tároló részletes adatrekordjainak megtekintéséhez.
