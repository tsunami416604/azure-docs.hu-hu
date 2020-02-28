---
title: Tároló-figyelési megoldás a Azure Monitorban | Microsoft Docs
description: A Azure Monitor tároló-figyelési megoldás segítségével egyetlen helyen tekintheti meg és kezelheti a Docker-és a Windows-tároló gazdagépeit.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664694"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Tároló-figyelési megoldás a Azure Monitor

![Tárolók szimbólum](./media/containers/containers-symbol.png)

Ez a cikk bemutatja, hogyan állíthatja be és használhatja a Azure Monitor tároló-figyelési megoldást, amely segítségével egyetlen helyen tekintheti meg és kezelheti a Docker-és a Windows-tároló gazdagépeit. A docker egy olyan virtualizációs rendszer, amely automatizálja az IT-infrastruktúráját a szoftver központi telepítése tárolók létrehozásához használt.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A megoldás bemutatja, hogy mely tárolók fut, milyen tárolórendszerkép futnak, és ahol a tárolók futnak. Megtekintheti a tárolókkal használt parancsok részletes naplózási információkat. És a tárolók elhárítását megtekintésével és központosított naplók keresése távolról megtekintheti a Docker és a Windows gazdagépekre nélkül. Tárolók, amelyek lehetnek zajos és a felhasználó felesleges erőforrások a gazdagépen található. És megtekintheti a központi Processzor, memória, tárolási és hálózati használatának és teljesítményének information for containers szolgáltatásban. Windows rendszerű számítógépeken központosítása és hasonlítsa össze a Windows Server naplóinak Hyper-V és a Docker-tárolókat. A megoldás a következő tárolóvezénylőt támogat:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Ha az [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)-ban üzembe helyezett tárolókkal rendelkezik, javasoljuk, hogy a [Service Fabric megoldást](../../service-fabric/service-fabric-diagnostics-oms-setup.md) és ezt a megoldást is engedélyezze a fürt eseményeinek figyeléséhez. A Service Fabric megoldás engedélyezése előtt tekintse át [a Service Fabric megoldás használata](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) című témakört, amelyből megismerheti, hogy mit nyújt, és hogyan használhatja azt.

Ha érdekli az Azure Kubernetes szolgáltatásban (ak) üzemeltetett Kubernetes-környezetekben üzembe helyezett számítási feladatok teljesítményének figyelése, tekintse meg az [Azure Kubernetes szolgáltatás figyelése](../../azure-monitor/insights/container-insights-overview.md)című témakört. A tároló-figyelési megoldás nem támogatja a platform figyelését.  

Az alábbi ábrán a különböző tárolók és ügynökök közötti kapcsolatok láthatók Azure Monitorsal.

![Tárolók diagramja](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Rendszerkövetelmények és a támogatott platformok

Mielőtt hozzákezdene, tekintse át a következő adatokat, ellenőrizze, hogy megfelel az előfeltételeknek.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Tárolómonitorozási megoldás támogatja a Docker Orchestrator, illetve az operációs rendszer platform

Az alábbi táblázat ismerteti a Docker-hangolást és az operációs rendszer figyelését a tárolók, a teljesítmény és a naplók Azure Monitor-vel való felügyeletének támogatásával.   

| | ACS | Linux | Windows | Tároló<br>Leltár | Image (Kép)<br>Leltár | Csomópont<br>Leltár | Tároló<br>Teljesítmény | Tároló<br>Esemény | Esemény<br>Napló | Tároló<br>Napló |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Szolgáltatás<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(önálló) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-kiszolgáló<br>(önálló) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Támogatott Linux rendszeren docker-verziók

- Docker 1.11-1,13.
- A docker CE és EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 tároló gazdagépként támogatott Linux-disztribúciók

- Ubuntu 14.04 LTS és 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE, 13.2
- openSUSE LEAP 42.2
- CentOS 7.3 és a 7.2
- SLES 12
- Az RHEL 7.2 és 7.3.
- Red Hat OpenShift Tárolóplatform (OCP) 3.4-es és a 3.5.
- Az ACS Mesosphere DC/OS 1.7.3 való 1.8.8
- Az ACS Kubernetes 1.4.5 az 1.6-os
    - Kubernetes-események, a Kubernetes-készlet és a tároló folyamatok csak támogatottak, a verzió 1.4.1-45 és újabb verzióiban a Linuxhoz készült Log Analytics-ügynök
- Az ACS a Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

- Windows Server 2016
- Windows 10 Évfordulós kiadás (Professional és Enterprise)

### <a name="docker-versions-supported-on-windows"></a>A Windows támogatott docker-verziók

- A docker 1.12 és 1,13.
- Docker 17.03.0 és újabb verziók

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

1. Adja hozzá a tároló-figyelési megoldást a Log Analytics munkaterületéhez az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) -en, vagy a [felügyeleti megoldások hozzáadása a Solutions Gallery](../../azure-monitor/insights/solutions.md)használatával című témakörben ismertetett eljárással.

2. Telepítse, és a Docker és a egy Log Analytics-ügynököket a használata. Az operációs rendszer és a Docker orchestrator alapján, használhatja az alábbi módszerek konfigurálása az ügynök.
   - Önálló gazdagépek:
     - A támogatott Linux operációs rendszereken telepítse és futtassa a Docker-t, majd telepítse és konfigurálja a [Linux rendszerhez készült log Analytics-ügynököt](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - A CoreOS a Linuxhoz készült Log Analytics-ügynök nem futtatható. Ehelyett a Linuxhoz készült Log Analytics-ügynök tárolóalapú verzióját futtatja. Tekintse át a Linux Container hosts szolgáltatásait, beleértve a CoreOS vagy a Azure Government Linux Container hosts gazdagépeket, beleértve a CoreOS, ha Azure Government-felhőben tároló
     - A Windows Server 2016 és a Windows 10 rendszeren telepítse a Docker-motort és az ügyfelet, majd csatlakoztasson egy ügynököt az adatok gyűjtéséhez és a Azure Monitor küldéséhez. Ha Windows-környezettel rendelkezik, tekintse át a [Windows Container hosts telepítése és konfigurálása](#install-and-configure-windows-container-hosts) című ismertetőt.
   - A Docker-gazdagép több vezénylési:
     - Ha Red Hat OpenShift-környezettel rendelkezik, tekintse át a Log Analytics ügynök konfigurálása a Red Hat OpenShift.
     - Ha rendelkezik egy Kubernetes-fürtöt az Azure Container Service használatával:
       - Tekintse át [a Kubernetes log Analytics Linux-ügynök konfigurálása](#configure-a-log-analytics-linux-agent-for-kubernetes)című ismertetőt.
       - Tekintse át [az log Analytics Windows-ügynök konfigurálása a Kubernetes-hez](#configure-a-log-analytics-windows-agent-for-kubernetes)című ismertetőt.
       - Tekintse át az Log Analytics Agent telepítését a Linux Kubernetes.
     - Ha Azure Container Service DC/OS-fürttel rendelkezik, további információ: [Azure Container Service DC/os-fürt figyelése Azure Monitorsal](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Ha Docker Swarm módú környezettel rendelkezik, további információt a Log Analytics ügynök konfigurálása a Docker Swarmhoz című témakörben olvashat.
     - Ha Service Fabric-fürttel rendelkezik, további információt a [tárolók figyelése Azure Monitorsal](../../service-fabric/service-fabric-diagnostics-oms-containers.md)című témakörben olvashat.

A Windows rendszerű számítógépeken a Docker-motorok telepítésével és konfigurálásával kapcsolatos további információkért tekintse át a [Docker-motor a Windowsban](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) című cikkét.

> [!IMPORTANT]
> A Docker-nek futnia kell, **mielőtt** telepítené a Linux rendszerhez készült [log Analytics-ügynököt](../../azure-monitor/learn/quick-collect-linux-computer.md) a tároló gazdagépeken. Ha már telepítette az ügynököt a Docker telepítése előtt, telepítse újra a Linuxhoz készült Log Analytics-ügynököt szeretné. A Docker-ról további információt a [Docker webhelyén](https://www.docker.com)talál.

### <a name="install-and-configure-linux-container-hosts"></a>Telepítse és konfigurálja a Linux-tároló gazdagépek

Ha már telepítette a Docker, használja a következő beállításokat, a tároló gazdagép használható az ügynök konfigurálásához a docker használatával. Először szükség van a Log Analytics-munkaterület Azonosítójára és a kulcs, amely az Azure Portalon található. A munkaterületen kattintson **gyorskonfigurálás** > **számítógépek** elemre a **munkaterület-azonosító** és az **elsődleges kulcs**megtekintéséhez.  Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

**Az összes Linux-tároló gazdagépen, a CoreOS kivételével:**

- További információ és a Linux rendszerhez készült Log Analytics-ügynök telepítésének lépései: [log Analytics Agent – áttekintés](../../azure-monitor/platform/log-analytics-agent.md).

**Minden Linux-tároló gazdagéphez, beleértve a CoreOS is:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Az összes Azure Government Linux-tároló gazdagép, beleértve a CoreOS:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Váltás egy telepített linuxos ügynök használatával egy tárolóban**

Ha korábban a közvetlenül telepített ügynököt használja, és szeretné inkább használja a tárolóban futó ügynök, először el kell távolítania a Linuxhoz készült Log Analytics-ügynököt. Lásd: [a log Analytics-ügynök](../../azure-monitor/learn/quick-collect-linux-computer.md) eltávolítása a Linuxra, hogy megtudja, hogyan távolíthatja el sikeresen az ügynököt.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>A Log Analytics-ügynököket a Docker Swarmra konfigurálása

A Log Analytics-ügynököket a Docker Swarm globális szolgáltatásként is futtathatja. Az alábbi információk segítségével hozzon létre a Log Analytics-ügynök szolgáltatást. Meg kell adnia a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot.

- Futtassa a következő a fő csomóponttal.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>A Docker Swarmra titkos kulcsok védelme

A Docker Swarm a titkos kulcsot, a munkaterület-Azonosítót és elsődleges kulcs létrehozása után használja az alábbi adatokat hozhat létre a titkos adatokat.

1. Futtassa a következő a fő csomóponttal.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Ellenőrizze, hogy a titkos kulcsokat a megfelelően létrejöttek-e.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Futtassa a következő parancsot a titkos kulcsok a tárolóalapú Log Analytics-ügynök csatlakoztatása.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>A Log Analytics-ügynököket, a Red Hat OpenShift konfigurálása

Red Hat OpenShift elindításához a tároló monitorozási adatok gyűjtése a Log Analytics-ügynököket hozzáadandó három módja van.

* [A Linux rendszerhez készült log Analytics-ügynök telepítése](../../azure-monitor/learn/quick-collect-linux-computer.md) közvetlenül az egyes OpenShift-csomópontokon  
* [Log Analytics virtuálisgép-bővítmény engedélyezése](../../azure-monitor/learn/quick-collect-azurevm.md) az Azure-ban élő összes OpenShift-csomóponton  
* A Log Analytics-ügynök telepítése OpenShift Daemon-set  

Ez a szakasz ismerteti a Log Analytics-ügynököket telepítse az OpenShift démon beállított szükséges lépéseket.  

1. Jelentkezzen be a OpenShift fő csomópontjára, és másolja a [OCP-omsagent. YAML YAML-](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) fájlt a githubról a fő csomópontjára, és módosítsa az értéket a log Analytics munkaterület-azonosítójával és az elsődleges kulccsal.
2. A következő parancsok futtatásával hozzon létre egy projektet a Azure Monitorhoz, és állítsa be a felhasználói fiókot.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. A démon-set üzembe helyezéséhez futtassa a következőt:

    `oc create -f ocp-omsagent.yaml`

4. Ellenőrizze, hogy konfigurálva van és megfelelően működik, írja be a következőt:

    `oc describe daemonset omsagent`  

    és a kimeneti hasonlóan kell kinéznie:

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

Ha azt szeretné, titkos kulcsok használatával védeni kell a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot, amikor a Log Analytics ügynök démon-set yaml-fájllal, hajtsa végre az alábbi lépéseket.

1. Jelentkezzen be a OpenShift fő csomópontjára, és másolja a YAML [-fájlt a OCP-DS-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) és a Secret generáló szkript [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) a githubból.  Ez a szkript a titkos kulcsok yaml-fájlt a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcs biztonságos hoz létre a secrete információkat.  
2. A következő parancsok futtatásával hozzon létre egy projektet a Azure Monitorhoz, és állítsa be a felhasználói fiókot. A titkos létrehozási parancsfájl kéri a Log Analytics-munkaterület AZONOSÍTÓjának `<WSID>` és az elsődleges kulcs `<KEY>` és befejezés után létrehozza a OCP. YAML fájlt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. A titkos kód fájlját telepítése a következő futtatásával:

    `oc create -f ocp-secret.yaml`

4. Telepítés ellenőrzése a következő futtatásával:

    `oc describe secret omsagent-secret`  

    és a kimeneti hasonlóan kell kinéznie:  

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

5. A Log Analytics ügynök démon-set yaml-fájl telepítése a következő futtatásával:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Telepítés ellenőrzése a következő futtatásával:

    `oc describe ds oms`

    és a kimeneti hasonlóan kell kinéznie:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>A Kubernetes a Log Analytics Linux-ügynök konfigurálása

A Kubernetes esetében, a szkript használatával hozza létre a titkos kulcsok yaml-fájlt a munkaterület-Azonosítót és elsődleges kulcsot a Linuxhoz készült Log Analytics-ügynök telepítése. A [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) lapján vannak olyan fájlok, amelyek a titkos adatokkal vagy anélkül is használhatók.

- Az alapértelmezett a Log Analytics-ügynök Linux DaemonSet nem rendelkezik titkos információk (omsagent.yaml)
- A Log Analytics-ügynök Linux DaemonSet yaml-fájl titkos információk (omsagent-ds-secrets.yaml) titkos kód generálása parancsfájlok létrehozni a titkos kulcsokat (omsagentsecret.yaml) yaml-fájlt használ.

Ha szeretné, omsagent DaemonSets létrehozása vagy a titkos kódok nélkül.

**Alapértelmezett OMSagent Daemonset elemet YAML-fájl titkos kódok nélkül**

- Az alapértelmezett Log Analytics Agent Daemonset elemet YAML fájl esetében cserélje le a `<WSID>` és a `<KEY>`t a WSID és a KULCSra. Másolja a fájlt a fő csomópontot, és futtassa a következő parancsot:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Alapértelmezett OMSagent Daemonset elemet YAML-fájl titkos kulcsokkal**

1. Log Analytics használatához az ügynök DaemonSet titkos információk, először hozza létre a titkos kulcsok.
    1. Másolja a parancsfájlt és a titkos sablonfájl, és ellenőrizze, hogy ugyanabban a könyvtárban legyenek.
        - Titkos kulcs generálása szkriptet - titkos kód – gen.sh
        - titkos kód sablon - secret-template.yaml
    2. Futtassa a szkriptet az alábbi példához hasonlóan. A parancsprogram kéri a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot, és ad meg őket, miután a parancsfájl létrehozza-e egy titkos yaml-fájlt, így is futtatható legyen.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Hozza létre a titkos kulcsok pod a következő futtatásával:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Győződjön meg arról, hogy futtassa a következőt:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Kimeneti hasonlóan kell kinéznie:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Kimeneti hasonlóan kell kinéznie:

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

    5. Hozza létre a omsagent démont a ```sudo kubectl create -f omsagent-ds-secrets.yaml``` futtatásával

2. Győződjön meg arról, hogy a Log Analytics-ügynököket DaemonSet fut, a következőhöz hasonló:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

A Kubernetes esetében a szkript használatával hozza létre a titkos kulcsok yaml fájlt a munkaterület-Azonosítót és elsődleges kulcsot a Linuxhoz készült Log Analytics-ügynök. A titkos adatok védelméhez használja a következő példában szereplő információkat a [omsagent YAML fájllal](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) .

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>A Kubernetes a Log Analytics Windows-ügynök konfigurálása

A Windows Kubernetes esetében, parancsfájl segítségével hozza létre a titkos kulcsok yaml-fájlt a munkaterület-Azonosítót és elsődleges kulcs a Log Analytics-ügynök telepítése. A [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) oldalán vannak olyan fájlok, amelyeket használhat a titkos adataival.  A Log Analytics-ügynököket a fő- és ügynökcsomópontokat esetében külön-külön telepítenie kell.  

1. Log Analytics-ügynököket DaemonSet használandó titkos információk felhasználásával a fő csomópont, jelentkezzen be, és először hozza létre a titkos kulcsok.
    1. Másolja a parancsfájlt és a titkos sablonfájl, és ellenőrizze, hogy ugyanabban a könyvtárban legyenek.
        - Titkos kulcs generálása szkriptet - titkos kód – gen.sh
        - titkos kód sablon - secret-template.yaml

    2. Futtassa a szkriptet az alábbi példához hasonlóan. A parancsprogram kéri a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot, és ad meg őket, miután a parancsfájl létrehozza-e egy titkos yaml-fájlt, így is futtatható legyen.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Hozza létre a omsagent démont a ```kubectl create -f omsagentsecret.yaml``` futtatásával
    4. Ellenőrizze, hogy futtassa a következőt:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Kimeneti hasonlóan kell kinéznie:

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

    5. Hozza létre a omsagent démont a ```kubectl create -f ws-omsagent-de-secrets.yaml``` futtatásával

2. Győződjön meg arról, hogy a Log Analytics-ügynököket DaemonSet fut, a következőhöz hasonló:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Ha a Windows rendszert futtató munkavégző csomóponton szeretné telepíteni az ügynököt, kövesse a [Windows-tároló gazdagépek telepítése és konfigurálása](#install-and-configure-windows-container-hosts)című szakasz lépéseit.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>A Linux Kubernetes a Log Analytics-ügynök telepítése a Helm használatával

A Linux Kubernetes-környezetet a Log Analytics-ügynök telepítése a helm használatával, hajtsa végre az alábbi lépéseket.

1. Hozza létre a omsagent démont a ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` futtatásával
2. Az eredmények a következőhöz hasonlóan néz ki:

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

3. A omsagent állapotát a következő parancs futtatásával tekintheti meg: ```helm status "omsagent"```, a kimenet az alábbihoz hasonlóan fog kinézni:

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
   
    További információkért tekintse meg a [Container Solution Helm diagramot](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Telepítse és konfigurálja a Windows-tárolók gazdagép

A szakaszban az információk segítségével telepítheti és konfigurálhatja a Windows-tárolók gazdagép.

#### <a name="preparation-before-installing-windows-agents"></a>Előkészítő lépések Windows-ügynökök telepítése

Ügynökök telepítése Windows rendszerű számítógépeken, előtt kell beállítani a Docker-szolgáltatást. A konfiguráció lehetővé teszi, hogy a Windows-ügynök vagy a Azure Monitor virtuálisgép-bővítmény a Docker TCP-szoftvercsatornát használja, hogy az ügynökök távolról hozzáférhessenek a Docker-démonhoz, és rögzíteni tudják a figyeléshez szükséges adatmennyiséget.

##### <a name="to-configure-the-docker-service"></a>A Docker szolgáltatás konfigurálása  

Hajtsa végre a következő PowerShell-parancsokat a TCP-cső és a nevesített cső a Windows Serveren való engedélyezéséhez:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

A Windows-tárolók által használt Docker Daemon-konfigurációval kapcsolatos további információkért lásd: [Docker-motor Windows rendszeren](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Windows-ügynökök telepítése

Windows és a Hyper-V-tárolók monitorozása engedélyezéséhez telepítse a Microsoft Monitoring Agent (MMA) tárológazdagép Windows számítógépek. A helyszíni környezetben található Windows rendszerű számítógépeken lásd: Windows rendszerű [számítógépek Összekapcsolása Azure monitorhoz](../../azure-monitor/platform/agent-windows.md). Az Azure-ban futó virtuális gépek esetében a [virtuálisgép-bővítmény](../../azure-monitor/learn/quick-collect-azurevm.md)használatával kapcsolja össze őket a Azure monitor.

A Service Fabricen futó Windows-tárolók figyelése A Service Fabric azonban jelenleg csak [Az Azure-ban futó virtuális gépek](../../azure-monitor/learn/quick-collect-azurevm.md) és a helyszíni [környezetben Windows rendszert futtató számítógépek](../../azure-monitor/platform/agent-windows.md) támogatottak.

Ellenőrizheti, hogy a a Tárolómonitorozási megoldás megfelelően van-e állítva a Windows. Keresse meg a *ContainerManagement.xxx*, és ellenőrizze, hogy a felügyeleti csomag megfelelően lett-e letöltve. A fájlok a C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management szervizcsomagok mappában kell lennie.

## <a name="solution-components"></a>Megoldás-összetevők

A Azure Portal navigáljon a *Solutions Galleryhoz* , és adja hozzá a **tároló-figyelési megoldást**. Ha Windows-ügynököt használ, akkor a következő felügyeleti csomag telepítve van az ügynök minden számítógépen ez a megoldás hozzáadásakor. Nincsenek konfigurációs vagy karbantartási feladatokat nem szükséges a felügyeleti csomag.

- *ContainerManagement.xxx* telepítve a C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Tároló adatainak gyűjtése részletei

A Tárolómonitorozási megoldás tároló-gazdagépek és -tárolók használatával az ügynököket, amely engedélyezi a különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt.

A következő ügynök típusú percen át 3 percenként adatokat gyűjti.

- [Linux-Log Analytics ügynök](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows-ügynök](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics virtuálisgép-bővítmény](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázat a Tárolómonitorozási megoldás és a naplókeresések megjelenő adattípusok által összegyűjtött rekordokkal példái láthatók.

| Adattípus | Naplókeresési adatok típusa | Mezők |
| --- | --- | --- |
| Gazdagépek és-tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;processzoridő, a lemez beolvassa a MB, lemezre ír MB memória kihasználtsága (MB), hálózati fogadott bájtok, hálózati küldése memória, processzor kihasználtsága (mp), hálózati&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló nevét, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, cseréjekor a Tárolóazonosító, ImageID |
| Tárolórendszerkép leltárát | `ContainerImageInventory` | TimeGenerated, számítógép, kép, ImageTag, ImageSize, VirtualSize, futó, fel van függesztve, leállítja, sikertelen, SourceSystem, ImageID, TotalContainer |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-azonosító, a Tárolónév esetén LogEntrySource, LogEntry, SourceSystem, cseréjekor a Tárolóazonosító |
| Container service-napló | `ContainerServiceLog`  | TimeGenerated, számítógép, TimeOfCommand, kép, a parancs, SourceSystem, cseréjekor a Tárolóazonosító |
| Tárolócsomópont-készlet | `ContainerNodeInventory_CL`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-készlet | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Tárolófolyamat | `ContainerProcess_CL` | TimeGenerated, számítógép, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-események | `KubeEvents_CL` | TimeGenerated, számítógép, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, üzenet |

A *PodLabel* -adattípusokhoz hozzáfűzött címkék saját egyéni címkék. A hozzáfűzött PodLabel címkék táblázatban szereplő példák. Tehát `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s``PodLabel_docker_registry_s` a környezet adatkészletében különbözik, és általánosságban hasonlít `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Tárolók figyelése
Miután engedélyezte a megoldást a Azure Portalban, a containers **(tárolók) csempén** a tároló gazdagépek és a gazdagépeken futó tárolók összegző információi láthatók.

![Tárolókkal csempére](./media/containers/containers-title.png)

A csempe rendelkezik hány tárolót áttekintése látható a környezetben, és hogy azok még nem sikerült, fut vagy leállítva.

### <a name="using-the-containers-dashboard"></a>A tároló irányítópultjának használata

Kattintson a **tárolók** csempére. Itt láthatja a nézetek szerint vannak rendezve:

- **Container Events** – a tároló állapotát és a meghibásodott tárolókat tartalmazó számítógépeket jeleníti meg.
- **Tároló naplófájljai** – megjeleníti a tároló naplófájljainak időbeli alakulását, valamint a legtöbb naplófájlt tartalmazó számítógépek listáját.
- **Kubernetes-események** – megjeleníti a Kubernetes események által generált diagramot, valamint azt, hogy a hüvely milyen okok miatt generálta az eseményeket. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Kubernetes-névtér leltározása** – a névterek és a hüvelyek számát jeleníti meg, és megjeleníti a hierarchiát. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló-csomópont leltározása** – a tároló csomópontjain/gazdagépeken használt hangolási típusok számát jeleníti meg. A számítógép-csomópontok/gazdagépeket is a tárolók száma alapján vannak listázva. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló lemezképek leltározása** – a felhasznált tároló-lemezképek és a képtípusok számának teljes számát jeleníti meg. A lemezképek számát is a képcímke alapján vannak listázva.
- **Tárolók állapota** – megjeleníti a tárolók és a gazdagépek futó számítógépeinek teljes számát. Számítógépek is futó gazdagépek száma alapján vannak listázva.
- **Tároló folyamata** – a tárolók folyamatainak időbeli diagramját jeleníti meg. Tárolók is megtalálható a futó tárolók belül parancs/folyamat. *Ezt az adatkészletet csak Linux-környezetekben használják.*
- **Tároló CPU-teljesítménye** – a számítógép-csomópontok/gazdagépek átlagos CPU-kihasználtságának grafikonját jeleníti meg. Is a számítógép-csomópontok/gazdagépek listája alapján az átlagos CPU-kihasználtság.
- **Tároló memória-teljesítménye** – megjeleníti a memóriahasználat időbeli diagramját. Számítógép memória kihasználtsága alapján a példány nevét is megjeleníti.
- **Számítógép teljesítménye** – MEGJELENÍTI a CPU-teljesítmény százalékos arányának időbeli alakulását, a memóriahasználat százalékos arányát, valamint a szabad lemezterületet (MB) az idő múlásával. További részletek megtekintéséhez diagram bármely sor fölé.

Az irányítópult egyes területen, amely az összegyűjtött adatokat keresés ábrázolása.

![Tárolók irányítópultja](./media/containers/containers-dash01.png)

![Tárolók irányítópultja](./media/containers/containers-dash02.png)

A **tároló állapota** területen kattintson az alább látható felső részre.

![Tárolók állapota](./media/containers/containers-status.png)

A Log Analytics megnyílik, és megjeleníti a tárolók állapotával kapcsolatos információkat.

![Log Analytics tárolók számára](./media/containers/containers-log-search.png)

Itt szerkesztheti a keresési lekérdezés módosítása, hogy az adott információk kereséséhez érdekli. További információ a naplók lekérdezéséről: [lekérdezések Azure monitorban való lekérdezéséhez](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Hibás tárolók felderítésével hibaelhárítása

Log Analytics egy tárolót **sikertelenként** jelöl meg, ha nem nulla kilépési kóddal kilépett. A **hibás tárolók** területén a környezetben található hibák és hibák áttekintése látható.

### <a name="to-find-failed-containers"></a>Hibás tárolók keresése

1. Kattintson a **tároló állapota** elemre.  
   ![tárolók állapota](./media/containers/containers-status.png)
2. A Log Analytics megnyílik, és megjeleníti a tárolók állapotát, a következőhöz hasonlóan.  
   ![tárolók állapota](./media/containers/containers-log-search.png)
3. Bontsa ki a meghibásodott sort, és kattintson a + gombra a feltételek a lekérdezéshez való hozzáadásához. Ezután adja meg a lekérdezés összefoglaló sorát.
   ![sikertelen tárolók](./media/containers/containers-state-failed-select.png)  
1. Futtassa a lekérdezést, majd az eredmények egyik sorát kibontva tekintse meg a rendszerkép AZONOSÍTÓját.  
   ![sikertelen tárolók](./media/containers/containers-state-failed.png)  
1. Írja be a következőt a napló lekérdezésbe. `ContainerImageInventory | where ImageID == <ImageID>` a lemezkép részleteit, például a képméretet és a leállított és sikertelen rendszerképek számát.  
   ![sikertelen tárolók](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Adattárolók lekérdezési naplói

Ha egy adott hiba hibaelhárítás segítségével megtekintheti, hol lépett fel a környezetben. A következő naplófájl-típusok segítséget szeretne az információt lekérdezések létrehozása.

- **ContainerImageInventory** – ezt a típust akkor használja, ha a rendszerkép által szervezett adatokat keres, és megtekinti a képi adatokat, például a képazonosítókat vagy a méreteket.
- **ContainerInventory** – ezt a típust akkor használja, ha a tároló helyéről, a nevükről, valamint a futó lemezképekről szeretne információt használni.
- **ContainerLog** – ezt a típust használja, ha konkrét hibanapló-információkat és-bejegyzéseket szeretne keresni.
- **ContainerNodeInventory_CL**  Akkor használja ezt a típust, ha szeretné, hogy a tárolók és csomópontok hová legyenek tárolva. Is tartalmaz a Docker verzió, vezénylés típusát, tárolási és hálózati információkat.
- **ContainerProcess_CL** Ezzel a típussal gyorsan megtekintheti a tárolón belül futó folyamatot.
- **ContainerServiceLog** – ezt a típust akkor használja, ha a Docker-démon (például indítási, leállítási, törlési és lekérési parancsok) naplózási adatait keresi.
- **KubeEvents_CL**  Ezzel a típussal megtekintheti a Kubernetes eseményeket.
- **KubePodInventory_CL**  Akkor használja ezt a típust, ha meg szeretné ismerni a fürt hierarchiájának információit.


### <a name="to-query-logs-for-container-data"></a>A tárolók adatnaplóinak lekérdezése

* Válassza ki, hogy tudja, hogy képet nemrég sikertelen volt, és keresse meg a hibanaplókat. Kezdje azzal, hogy megkeresi az adott képet futtató **ContainerInventory** -kereséssel rendelkező tároló nevét. Keressen például `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    Ubuntu-tárolók keresése ![](./media/containers/search-ubuntu.png)

  Bontsa ki az eredmények bármelyik sorát a tároló részleteinek megtekintéséhez.

## <a name="example-log-queries"></a>Példák a naplók lekérdezésére

Gyakran hasznos építhetők fel lekérdezések például vagy két kezdődő és annak módosításával a környezethez illeszkedve kell őket. A kiindulási pontként kísérletezheti a **példák lekérdezési** területén, így könnyebben hozhat létre összetettebb lekérdezéseket.

![Tárolók lekérdezések](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Naplózási lekérdezések mentése

A lekérdezések mentése a Azure Monitor egy standard funkciója. Menti őket, amelyek hasznos talált kell praktikus későbbi használatra.

Ha olyan lekérdezést hoz létre, amelyet hasznosnak talál, mentse azt a Keresés lap tetején található **Kedvencek** elemre kattintva. Ezután egyszerűen elérheti később a **saját irányítópult** lapon.

## <a name="next-steps"></a>Következő lépések

[Lekérdezési naplók](../log-query/log-query-overview.md) a részletes tároló-adatrekordok megtekintéséhez.
