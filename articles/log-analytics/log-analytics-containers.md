---
title: Figyelés megoldásra az Azure Log Analyticsben |} A Microsoft Docs
description: A Tárolómonitorozási megoldás a Log Analytics segítségével megtekintése és kezelése a Docker és a Windows tárológazdagép egyetlen helyen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 087e11f906fe7f5c2fe67c43a597de8551dfc951
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013054"
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Figyelés a Log Analytics megoldásra

![Tárolók szimbólum](./media/log-analytics-containers/containers-symbol.png)

Ez a cikk bemutatja, hogyan állíthatja be, és a Tárolómonitorozási megoldás a Log Analytics, amely segít megtekintése és kezelése a Docker és a Windows tárológazdagép egyetlen helyen. A docker egy olyan virtualizációs rendszer, amely automatizálja az IT-infrastruktúráját a szoftver központi telepítése tárolók létrehozásához használt.

A megoldás bemutatja, hogy mely tárolók fut, milyen tárolórendszerkép futnak, és ahol a tárolók futnak. Megtekintheti a tárolókkal használt parancsok részletes naplózási információkat. És a tárolók elhárítását megtekintésével és központosított naplók keresése távolról megtekintheti a Docker és a Windows gazdagépekre nélkül. Tárolók, amelyek lehetnek zajos és a felhasználó felesleges erőforrások a gazdagépen található. És megtekintheti a központi Processzor, memória, tárolási és hálózati használatának és teljesítményének information for containers szolgáltatásban. Windows rendszerű számítógépeken központosítása és hasonlítsa össze a Windows Server naplóinak Hyper-V és a Docker-tárolókat. A megoldás a következő tárolóvezénylőt támogat:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Ha érdekli az üzembe helyezett munkaterhelések teljesítményének figyelése a Kubernetes-környezetben üzemeltetett Azure Kubernetes Service (AKS), lásd: [figyelő Azure Kubernetes Service](../monitoring/monitoring-container-insights-overview.md). A Tárolómonitorozási megoldás nem tartalmazza az adott platform figyelésének támogatását.  

Az alábbi ábrán látható, különböző tároló gazdagépek és az ügynökök a Log Analytics használatával kapcsolatai.

![Tárolók diagramja](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Rendszerkövetelmények és a támogatott platformok

Mielőtt hozzákezdene, tekintse át a következő adatokat, ellenőrizze, hogy megfelel az előfeltételeknek.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Tárolómonitorozási megoldás támogatja a Docker Orchestrator, illetve az operációs rendszer platform
Az alábbi táblázat ismerteti a Docker vezénylési és az operációs rendszer figyelési támogatást a tároló szoftverleltár, a teljesítmény és a naplók a Log Analytics használatával.   

| | ACS | Linux | Windows | Tároló<br>Leltározás | Kép<br>Leltározás | Csomópont<br>Leltározás | Tároló<br>Teljesítmény | Tároló<br>Esemény | Esemény<br>Napló | Tároló<br>Napló |
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

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Támogatott Windows operációs rendszer

- Windows Server 2016
- Windows 10 Évfordulós kiadás (Professional és Enterprise)

### <a name="docker-versions-supported-on-windows"></a>A Windows támogatott docker-verziók

- A docker 1.12 és 1,13.
- Docker 17.03.0 és újabb verziók

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

1. A Tárolómonitorozási megoldás hozzáadása a Log Analytics-munkaterületet [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) vagy leírt folyamatot követve [adja hozzá a Log Analytics solutions kövesse a megoldástárban](../monitoring/monitoring-solutions.md).

2. Telepítse, és a Docker és a egy Log Analytics-ügynököket a használata. Az operációs rendszer és a Docker orchestrator alapján, használhatja az alábbi módszerek konfigurálása az ügynök.
  - Önálló gazdagépek:
    - A támogatott Linux operációs rendszer telepítése és Docker futtatása és telepítse és konfigurálja a [Linuxhoz készült Log Analytics-ügynök](log-analytics-quick-collect-linux-computer.md).  
    - A CoreOS a Linuxhoz készült Log Analytics-ügynök nem futtatható. Ehelyett a Linuxhoz készült Log Analytics-ügynök tárolóalapú verzióját futtatja. Felülvizsgálat [többek között a CoreOS Linux tárológazdagép](#for-all-linux-container-hosts-including-coreos) vagy [CoreOS többek között az Azure Government Linux tárológazdagép](#for-all-azure-government-linux-container-hosts-including-coreos) Ha tárolók az Azure Government felhőben dolgozik.
    - A Windows Server 2016 és Windows 10-es a Docker-motor és az ügyfél telepítéséhez, majd kösse adatainak összegyűjtése, majd azokat elküldi a Log Analytics-ügynököt. Tekintse át [telepítése és konfigurálása a Windows tárológazdagép](#install-and-configure-windows-container-hosts) Ha egy Windows-környezettel rendelkezik.
  - A Docker-gazdagép több vezénylési:
    - Ha egy Red Hat OpenShift környezettel rendelkezik, tekintse át [konfigurálása a Log Analytics-ügynököket, a Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Ha rendelkezik egy Kubernetes-fürtöt az Azure Container Service használatával:
       - Felülvizsgálat [Kubernetes a Log Analytics Linux-ügynökének konfigurálása](#configure-an-oms-linux-agent-for-kubernetes).
       - Felülvizsgálat [a Kubernetes-napló Analytis Windows-ügynök konfigurálása](#configure-an-oms-windows-agent-for-kubernetes).
       - Felülvizsgálat [Helm használata a Linux Kubernetes a Log Analytics-ügynök telepítése](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - Ha rendelkezik egy Azure Container Service DC/OS-fürtön, további tudnivalókat talál [egy Azure Container Service DC/OS fürt megfigyelése a Log Analytics](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Ha egy Docker Swarm módú környezettel rendelkezik, további tudnivalókat talál [konfigurálása egy Log Analytics-ügynököket a Docker Swarmra](#configure-an-oms-agent-for-docker-swarm).
    - Ha egy Service Fabric-fürtöt, további tudnivalókat talál [a Log Analytics Log Analytics-tárolók monitorozása](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Tekintse át a [Windows Docker-motor](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) ismertető cikkben talál további információt a telepítése és konfigurálása a Docker-motor a Windows rendszerű számítógépeken.

> [!IMPORTANT]
> Docker futnia kell **előtt** telepíti a [Linuxhoz készült Log Analytics-ügynök](log-analytics-quick-collect-linux-computer.md) a tároló-gazdagépeken. Ha már telepítette az ügynököt a Docker telepítése előtt, telepítse újra a Linuxhoz készült Log Analytics-ügynököt szeretné. Docker kapcsolatos további információkért lásd: a [Docker webhely](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Telepítse és konfigurálja a Linux-tároló gazdagépek

Ha már telepítette a Docker, használja a következő beállításokat, a tároló gazdagép használható az ügynök konfigurálásához a docker használatával. Először szükség van a Log Analytics-munkaterület Azonosítójára és a kulcs, amely az Azure Portalon található. A munkaterületén kattintson **gyors üzembe helyezés** > **számítógépek** megtekintéséhez a **munkaterület-Azonosítót** és **elsődleges kulcs**.  Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

**Az összes Linux tárológazdagép CoreOS kivételével:**

- További információkért és a Linuxhoz készült Log Analytics-ügynök telepítéséhez szükséges lépéseket lásd: [Linux rendszerű számítógépek csatlakoztatása a Log Analytics](log-analytics-concept-hybrid.md).

**Minden Linux tároló gazdagéphez CoreOS is beleértve:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Minden Linux-alapú Azure Government tároló gazdagéphez CoreOS is beleértve:**

Indítsa el a figyelni kívánt tárolót. Módosítsa és használja a következő példát:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Váltás a Linux-tárolóban egy telepített ügynök használatával**

Ha korábban a közvetlenül telepített ügynököt használja, és szeretné inkább használja a tárolóban futó ügynök, először el kell távolítania a Linuxhoz készült Log Analytics-ügynököt. Lásd: [a Linuxhoz készült Log Analytics-ügynök eltávolítása](log-analytics-quick-collect-linux-computer.md) megtudhatja, hogyan sikerült az ügynök eltávolítása.  

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

* [A Linuxhoz készült Log Analytics-ügynök telepítése](log-analytics-quick-collect-linux-computer.md) közvetlenül a OpenShift csomópontokon  
* [Log Analytics Virtuálisgép-bővítmény engedélyezése](log-analytics-quick-collect-azurevm.md) minden OpenShift csomóponton levő az Azure-ban  
* A Log Analytics-ügynök telepítése egy OpenShift démon-készletben  

Ez a szakasz ismerteti a Log Analytics-ügynököket telepítse az OpenShift démon beállított szükséges lépéseket.  

1. Jelentkezzen be az OpenShift fő csomóponttal, és másolja a yaml-fájlt [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) a Githubról, a fő csomópontot és módosíthatja a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcs értékét.
2. Futtassa az alábbi parancsokat a Log Analytics hozzon létre egy projektet, és állítsa be a felhasználói fiókot.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. A démon-set üzembe helyezéséhez futtassa a következőt:

    `oc create -f ocp-omsagent.yaml`

5. Ellenőrizze, hogy konfigurálva van és megfelelően működik, írja be a következőt:

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

1. Jelentkezzen be az OpenShift fő csomóponttal, és másolja a yaml-fájlt [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) és titkos kulcs parancsfájljának [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) a Githubról.  Ez a szkript a titkos kulcsok yaml-fájlt a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcs biztonságos hoz létre a secrete információkat.  
2. Futtassa az alábbi parancsokat a Log Analytics hozzon létre egy projektet, és állítsa be a felhasználói fiókot. A Log Analytics-munkaterület Azonosítójára kéri a titkos kulcs parancsfájljának <WSID> és az elsődleges kulcs <KEY> és a befejezéskor az ocp-secret.yaml fájlt hoz létre.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. A titkos kód fájlját telepítése a következő futtatásával:

    `oc create -f ocp-secret.yaml`

5. Telepítés ellenőrzése a következő futtatásával:

    `oc describe secret omsagent-secret`  

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

6. A Log Analytics ügynök démon-set yaml-fájl telepítése a következő futtatásával:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Telepítés ellenőrzése a következő futtatásával:

    `oc describe ds oms`

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>A Kubernetes a Log Analytics Linux-ügynök konfigurálása

A Kubernetes esetében, a szkript használatával hozza létre a titkos kulcsok yaml-fájlt a munkaterület-Azonosítót és elsődleges kulcsot a Linuxhoz készült Log Analytics-ügynök telepítése. Jelenleg a [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) lapon, a fájlokat, vagy a titkos információk nélkül használható.

- Az alapértelmezett a Log Analytics-ügynök Linux DaemonSet nem rendelkezik titkos információk (omsagent.yaml)
- A Log Analytics-ügynök Linux DaemonSet yaml-fájl titkos információk (omsagent-ds-secrets.yaml) titkos kód generálása parancsfájlok létrehozni a titkos kulcsokat (omsagentsecret.yaml) yaml-fájlt használ.

Ha szeretné, omsagent DaemonSets létrehozása vagy a titkos kódok nélkül.

**Alapértelmezett OMSagent DaemonSet yaml-fájl titkos kódok nélkül**

- Az alapértelmezett a Log Analytics ügynök DaemonSet yaml-fájl esetében cserélje le a `<WSID>` és `<KEY>` a WSID és a kulcsot. Másolja a fájlt a fő csomópontot, és futtassa a következő parancsot:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Alapértelmezett OMSagent DaemonSet yaml-fájl titkos kulcsok**

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

    5. A omsagent futtatásával démon-készlet létrehozása ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Győződjön meg arról, hogy a Log Analytics-ügynököket DaemonSet fut, a következőhöz hasonló:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


A Kubernetes esetében a szkript használatával hozza létre a titkos kulcsok yaml fájlt a munkaterület-Azonosítót és elsődleges kulcsot a Linuxhoz készült Log Analytics-ügynök. Az alábbi példa információk a [omsagent yaml-fájl](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) a titkos adatok védelmére.

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
A Windows Kubernetes esetében, parancsfájl segítségével hozza létre a titkos kulcsok yaml-fájlt a munkaterület-Azonosítót és elsődleges kulcs a Log Analytics-ügynök telepítése. Jelenleg a [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) lapon, a titkos információkkal használható fájlok vannak.  A Log Analytics-ügynököket a fő- és ügynökcsomópontokat esetében külön-külön telepítenie kell.  

1. Log Analytics-ügynököket DaemonSet használandó titkos információk felhasználásával a fő csomópont, jelentkezzen be, és először hozza létre a titkos kulcsok.
    1. Másolja a parancsfájlt és a titkos sablonfájl, és ellenőrizze, hogy ugyanabban a könyvtárban legyenek.
        - Titkos kulcs generálása szkriptet - titkos kód – gen.sh
        - titkos kód sablon - secret-template.yaml

    2. Futtassa a szkriptet az alábbi példához hasonlóan. A parancsprogram kéri a Log Analytics-munkaterület Azonosítójára és az elsődleges kulcsot, és ad meg őket, miután a parancsfájl létrehozza-e egy titkos yaml-fájlt, így is futtatható legyen.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. A omsagent futtatásával démon-készlet létrehozása ``` kubectl create -f omsagentsecret.yaml ```
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

    5. A omsagent futtatásával démon-készlet létrehozása ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Győződjön meg arról, hogy a Log Analytics-ügynököket DaemonSet fut, a következőhöz hasonló:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Telepítse az ügynököt a munkavégző csomópont Windows futnak, hajtsa végre a szakasz lépéseit [telepítése és konfigurálása a Windows tárológazdagép](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>A Linux Kubernetes a Log Analytics-ügynök telepítése a Helm használatával
A Linux Kubernetes-környezetet a Log Analytics-ügynök telepítése a helm használatával, hajtsa végre az alábbi lépéseket.

1. A omsagent futtatásával démon-készlet létrehozása ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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
3. A omsagent állapotának ellenőrzéséhez futtassa: ```helm status "omsagent"``` és a kimenet az alábbihoz hasonlóan néz ki:

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
További információkért látogasson el [tároló megoldás Helm-diagram](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Telepítse és konfigurálja a Windows-tárolók gazdagép

A szakaszban az információk segítségével telepítheti és konfigurálhatja a Windows-tárolók gazdagép.

#### <a name="preparation-before-installing-windows-agents"></a>Előkészítő lépések Windows-ügynökök telepítése

Ügynökök telepítése Windows rendszerű számítógépeken, előtt kell beállítani a Docker-szolgáltatást. A konfiguráció lehetővé teszi, hogy a Windows-ügynök vagy a Log Analytics virtuálisgép-bővítmény használata a Docker TCP szoftvercsatorna, hogy az ügynökök távolról elérjék a Docker-démont és a figyelési adatok rögzítéséhez.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Indítsa el a Docker és a konfiguráció ellenőrzése

Vannak olyan nevesített cső a Windows Server TCP beállításához szükséges lépések:

1. A Windows PowerShellben engedélyezze a TCP függőleges vonal és a nevesített cső.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. A konfigurációs fájl TCP pipe Docker konfigurálásához, valamint a nevesített cső. A konfigurációs fájl nem található: C:\ProgramData\docker\config\daemon.json.

    A daemon.json fájlban a következőkre lesz szüksége:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

A Docker-démon konfigurációját, használja a Windows-tárolókkal kapcsolatos további információkért lásd: [Windows Docker-motor](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Windows-ügynökök telepítése

Windows és a Hyper-V-tárolók monitorozása engedélyezéséhez telepítse a Microsoft Monitoring Agent (MMA) tárológazdagép Windows számítógépek. A helyszíni környezetben Windows rendszerű számítógépek, lásd: [a Log Analyticshez való csatlakozáshoz Windows számítógépek](log-analytics-agent-windows.md). A virtuális gépek futtatása az Azure-ban csatlakoztathatja őket a Log Analytics használatával az [virtuálisgép-bővítmény](log-analytics-quick-collect-azurevm.md).

A Service Fabricen futó Windows-tárolók figyelése Azonban csak [az Azure-ban futó virtuális gépek](log-analytics-quick-collect-azurevm.md) és [a helyszíni környezetben Windows rendszerű számítógépek](log-analytics-agent-windows.md) Service Fabric jelenleg támogatja.

Ellenőrizheti, hogy a a Tárolómonitorozási megoldás megfelelően van-e állítva a Windows. Annak ellenőrzéséhez, hogy a felügyeleti csomag megfelelően lett-e letöltése, keressen *ContainerManagement.xxx*. A fájlok a C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management szervizcsomagok mappában kell lennie.


## <a name="solution-components"></a>Megoldás-összetevők

Az Azure Portalról nyissa meg a *Megoldástárából* , és adja hozzá a **Tárolómonitorozási megoldás**. Ha Windows-ügynököt használ, akkor a következő felügyeleti csomag telepítve van az ügynök minden számítógépen ez a megoldás hozzáadásakor. Nincsenek konfigurációs vagy karbantartási feladatokat nem szükséges a felügyeleti csomag.

- *ContainerManagement.xxx* telepítve a C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management szervizcsomagok

## <a name="container-data-collection-details"></a>Tároló adatainak gyűjtése részletei
A Tárolómonitorozási megoldás tároló-gazdagépek és -tárolók használatával az ügynököket, amely engedélyezi a különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt.

A következő ügynök típusú percen át 3 percenként adatokat gyűjti.

- [A Linuxhoz készült log Analytics-ügynök](log-analytics-quick-collect-linux-computer.md)
- [Windows-ügynök](log-analytics-agent-windows.md)
- [Log Analytics Virtuálisgép-bővítmény](log-analytics-quick-collect-azurevm.md)


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

Címkék hozzáfűzi *PodLabel* adattípusok a következők a saját egyéni címkék. A hozzáfűzött PodLabel címkék táblázatban szereplő példák. Tehát `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` lesz a környezet adatkészlet különböznek egymástól, és általános csúcsos `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Tárolók figyelése
A megoldás engedélyezve van a Log Analytics-portálon, miután a **tárolók** csempe a tároló-gazdagépek és a gazdagépeken futó tárolók összegző információit jeleníti meg.


![Tárolókkal csempére](./media/log-analytics-containers/containers-title.png)

A csempe rendelkezik hány tárolót áttekintése látható a környezetben, és hogy azok még nem sikerült, fut vagy leállítva.

### <a name="using-the-containers-dashboard"></a>A tároló irányítópultjának használata
Kattintson a **tárolók** csempére. Itt láthatja a nézetek szerint vannak rendezve:

- **Tárolóesemények** -tároló állapota és a sikertelen tárolókkal rendelkező számítógépeket jeleníti meg.
- **Tárolónaplók** -látható a naplófájlok számát vesszük figyelembe az idő függvényében, és azon számítógépek listáját létrehozott tároló naplófájlokat.
- **Kubernetes-események** -látható a Kubernetes-ellenőrzési eseményeit idő és az okokat, miért érdemes a podok generált események listája. *Ez az adatkészlet csak a Linux-környezetek szolgál.*
- **Kubernetes-Namespace készlet** - névterek és a podok számát jeleníti meg, és megjeleníti a hierarchiában. *Ez az adatkészlet csak a Linux-környezetek szolgál.*
- **Tárolócsomópont-készlet** -tároló csomópontok/gazdagépeken használt vezénylési típusok számát jeleníti meg. A számítógép-csomópontok/gazdagépeket is a tárolók száma alapján vannak listázva. *Ez az adatkészlet csak a Linux-környezetek szolgál.*
- **Tárolólemezképek leltára** -képtípussal száma és a használt tárolórendszerképek teljes számát mutatja. A lemezképek számát is a képcímke alapján vannak listázva.
- **Tárolók állapota** -csomópontok/tartalmazó számítógépek futó tárolók tároló teljes számát jeleníti meg. Számítógépek is futó gazdagépek száma alapján vannak listázva.
- **Tárolófolyamat** – egy vonaldiagram idővel futó tároló folyamatokat mutatja. Tárolók is megtalálható a futó tárolók belül parancs/folyamat. *Ez az adatkészlet csak a Linux-környezetek szolgál.*
- **Tároló CPU-teljesítmény** -a számítógép-csomópontok/gazdagépek idővel átlagos CPU-kihasználtság egy vonaldiagram látható. Is a számítógép-csomópontok/gazdagépek listája alapján az átlagos CPU-kihasználtság.
- **Tároló memóriahasználati teljesítménye** -memória használata egy vonaldiagram látható idővel. Számítógép memória kihasználtsága alapján a példány nevét is megjeleníti.
- **A számítógép teljesítménye** -jeleníti meg a CPU-teljesítmény az idő múlásával százaléka vonaldiagramok memóriahasználat időpontja, valamint (MB) szabad lemezterület százaléka idővel. További részletek megtekintéséhez diagram bármely sor fölé.


Az irányítópult egyes területen, amely az összegyűjtött adatokat keresés ábrázolása.

![Tárolók irányítópultja](./media/log-analytics-containers/containers-dash01.png)

![Tárolók irányítópultja](./media/log-analytics-containers/containers-dash02.png)

Az a **tároló állapota** területen kattintson a felső területen, az alább látható módon.

![Tárolók állapota](./media/log-analytics-containers/containers-status.png)

Naplóbeli keresés jelenik meg, amelyen a tárolókat állapotával kapcsolatos információk.

![Log Search for containers szolgáltatásban](./media/log-analytics-containers/containers-log-search.png)

Itt szerkesztheti a keresési lekérdezés módosítása, hogy az adott információk kereséséhez érdekli. Naplókeresések kapcsolatos további információkért lásd: [Log Analytics naplóbeli kereséseivel](log-analytics-queries.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Hibás tárolók felderítésével hibaelhárítása

A log Analytics jelöli meg a tárolóban, **sikertelen** Ha egy nem nulla értékű kilépési kóddal kilépett. A hibák és a környezetben, a hibák áttekintése látható a **tárolók sikertelen** területen.

### <a name="to-find-failed-containers"></a>Hibás tárolók keresése
1. Kattintson a **tároló állapota** területen.  
   ![Tárolók állapota](./media/log-analytics-containers/containers-status.png)
2. Naplóbeli keresés megnyílik, és a tárolók, a következőhöz hasonló állapotát jeleníti meg.  
   ![tárolók állapota](./media/log-analytics-containers/containers-log-search.png)
3. Ezután kattintson a további információk megjelenítéséhez hibás tárolók összesített értékét. Bontsa ki a **Továbbiak megjelenítése** megtekintéséhez a rendszerkép azonosítóját.  
   ![hibás tárolók](./media/log-analytics-containers/containers-state-failed.png)  
4. Ezután írja be a következőt a keresési lekérdezésben. `ContainerInventory <ImageID>` a kép, például a lemezkép mérete és a leállított és a sikertelen képek számát részleteinek megtekintéséhez.  
   ![hibás tárolók](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Keresési naplókat tároló adatok
Ha egy adott hiba hibaelhárítás segítségével megtekintheti, hol lépett fel a környezetben. A következő naplófájl-típusok segítséget szeretne az információt lekérdezések létrehozása.


- **ContainerImageInventory** – ezt a típust használja, ha megpróbál kapcsolatos információk kép szerint vannak rendezve, és a kép adatait, például az azonosítók vagy méretű kép megtekintéséhez.
- **ContainerInventory** – Ez a típus használható, ha azt szeretné, hogy a tároló helye, Mik azok a nevük, és mi lemezképek futnak.
- **ContainerLog** – Ez a típus használható, ha meg szeretne keresni az adott hibanaplóban szereplő információkat, és bejegyzéseket.
- **ContainerNodeInventory_CL** ezt a típust használja, ha azt szeretné, hogy a gazdagép/node-információkat hol tartózkodnak a tárolókat. Is tartalmaz a Docker verzió, vezénylés típusát, tárolási és hálózati információkat.
- **ContainerProcess_CL** gyorsan megtekintheti a folyamat a tárolóban fut ez a típus használható.
- **ContainerServiceLog** – ezt a típust használja a naplózási beállításainak adatok megkereséséhez, a Docker-démont, mint az indítás, Leállítás, törlése és lekérési parancsok a regisztráláskor.
- **KubeEvents_CL** ezt használja a Kubernetes-események megtekintéséhez.
- **KubePodInventory_CL** ezt a típust használja, ha szeretné megismerni a hierarchia fürtinformációkat.


### <a name="to-search-logs-for-container-data"></a>A naplókat tároló adatok keresése
* Válassza ki, hogy tudja, hogy képet nemrég sikertelen volt, és keresse meg a hibanaplókat. Első lépésként egy tároló-neve, amelyen fut a rendszerkép keresése a **ContainerInventory** keresés. Ha például keresése `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Ubuntu tárolók keresése](./media/log-analytics-containers/search-ubuntu.png)

  Az a tároló neve **neve**, és keresse meg ezeket a naplókat. Ebben a példában ez `ContainerLog | where Name == "cranky_stonebreaker"`.

**Teljesítmény-adatok megtekintése**

Ha Ön már kezdve a lekérdezéseket hozhat létre, megkönnyíti, mi minden lehetséges először. Minden teljesítményadat megtekintéséhez, próbáljon meg például a széles körű lekérdezés írja be a következő keresési lekérdezéssel.

```
Perf
```

![tárolók teljesítménye](./media/log-analytics-containers/containers-perf01.png)

A teljesítményadatok lát egy adott tárolóba, a jobb oldalon a lekérdezés nevének beírásával is körét.

```
Perf <containerName>
```

A tároló gyűjtött teljesítmény-mérőszámok listáját jeleníti meg, amely.

![tárolók teljesítménye](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések
Gyakran hasznos építhetők fel lekérdezések például vagy két kezdődő és annak módosításával a környezethez illeszkedve kell őket. Kiindulási pontként, kísérletezhet a **mintalekérdezések** terület segíteni bonyolultabb lekérdezéseket.

![Tárolók lekérdezések](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Naplófájl-keresési lekérdezések mentése
A Log Analytics alapvető szolgáltatás lekérdezéseket elmenti. Menti őket, amelyek hasznos talált kell praktikus későbbi használatra.

Miután létrehozott egy lekérdezést, amely akkor hasznosak, mentse kattintva **Kedvencek** a naplók keresése lap tetején. Ezután könnyedén elérheti később a **saját irányítópult** lapot.

## <a name="next-steps"></a>További lépések
* [Naplók keresése](log-analytics-queries.md) részletes tároló adatfelderítési rekordok megtekintéséhez.
