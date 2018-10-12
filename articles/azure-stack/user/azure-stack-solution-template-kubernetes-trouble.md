---
title: Hibaelhárítás a Kubernetes (K8) az Azure Stackhez |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a központi telepítés (K8) kubernetes az Azure Stackhez.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: fbb51d8dc3b1ea4c6b34120e8fe35474ae949cf2
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49116912"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>A Kubernetes az Azure Stack üzembe helyezés hibaelhárítása

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban.

A következő cikket úgy tűnik, a Kubernetes-fürt hibaelhárítása. Tekintse át a központi telepítési riasztás, és a telepítéshez szükséges elemek által a központi telepítés állapotának áttekintése. Szükség lehet a telepítési naplók gyűjtését az Azure Stack vagy a Linux rendszerű virtuális gépek, amelyeken Kubernetes. Ezenkívül szükség lehet felügyeleti végpont naplók lekérése az Azure Stack rendszergazdával együttműködve.

## <a name="overview-of-deployment"></a>Üzembe helyezés áttekintése

Mielőtt kap azokat a lépéseket a fürt hibaelhárítása, előfordulhat, hogy szeretné ellenőrizni az Azure Stack-beli Kubernetes-fürt üzembe helyezési folyamat. Az üzembe helyezés egy Azure Resource Manager-megoldás sablont használ a virtuális gépek létrehozásához, és telepíti az ACS Engine, a fürt számára.

### <a name="deployment-workflow"></a>Telepítési munkafolyamat

Az alábbi ábrán látható, az általános folyamat a fürt üzembe helyezéséhez.

![Kubernetes-folyamat üzembe helyezése](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Gyűjti bemeneti paraméterek, a Piactéri elemet.

    Adja meg az értékeket, be kell állítania a Kubernetes fürt többek között:
    -  **Felhasználónév** a Linux rendszerű virtuális gépek, a Kubernetes-fürt részét képező és a DVM felhasználónevet.
    -  **Nyilvános SSH-kulcs** engedély az összes Linux a Kubernetes-fürt és a DVM részeként létrehozott használt kulcs
    -  **Egyszerű szolgáltatás** az azonosító a Kubernetes Azure-felhő szolgáltató által használt. Az ügyfél-azonosító az Alkalmazásazonosítót azonosította az eseményt az egyszerű szolgáltatás létrehozásakor. 
    -  **Titkos Ügyfélkód** azok fő létrehozott egyszerű szolgáltatás létrehozásakor.

2. Üzembe helyezés virtuális Gépet hoz létre és az egyéni szkriptek futtatására szolgáló bővítmény.
    -  Hozza létre a központi telepítés a Marketplace-en, Linux-rendszerképek használata Linux rendszerű virtuális gép **Ubuntu Server 16.04-LTS**.
    -  Töltse le, és hajtsa végre az ügyfél parancsprogramok futtatására szolgáló bővítmény a marketplace-ről. A szkript a **egyéni parancsfájl Linux 2.0**.
    -  A DVM egyéni parancsfájlt futtatja. A parancsfájlt:
        1. A katalógus végpont lekérése az Azure Resource Manager-metaadatok végpontja.
        2. Az active directory erőforrás-azonosító lekérése az Azure Resource Manager-metaadatok végpontja.
        3. Az API-modell betölti az ACS-motor.
        4. A Kubernetes-fürt üzembe helyezi az ACS Engine, és menti az Azure Stack felhő profilt `/etc/kubernetes/azurestackcloud.json`.
3. Fő virtuális gépeket hoz létre.

    Letölti és végrehajtja az ügyfél parancsprogramok futtatására szolgáló bővítmény.

4. A fő parancsfájlt futtatja.

    A parancsfájlt:
    - Telepíti a etcd, a Docker és a Kubernetes erőforrások, például kubelet. etcd egy elosztott kulcs-érték tároló, amely lehetővé teszi a számítógépfürtökön tárolja adatait. Docker operációs csontot operációs rendszer szintű virtualizations tárolók néven támogatja. Kubelet a csomóponti ügynök, amely a Kubernetes-csomópontokon.
    - Beállítja a etcd szolgáltatás.
    - Kubelet szolgáltatás beállítása.
    - Kubelet elindul. Ez az alábbiakat foglalja magában:
        1. API szolgáltatás elindul.
        2. Elindítja a Hálózativezérlő-szolgáltatás.
        3. A Feladatütemező szolgáltatás elindul.
5. Az ügynök virtuális gépeket hoz létre.

    Letölti és végrehajtja az ügyfél-szkriptek bővítménye.

6. Az ügynök parancsfájlt futtatja. Az ügynök egyéni szkriptet:
    - Telepítse a etcd.
    - Kubelet szolgáltatás beállítása.
    - A Kubernetes-fürthöz csatlakozik.

## <a name="steps-for-troubleshooting"></a>Hibaelhárítási lépések

A Kubernetes-fürt támogatása virtuális gépeken is összegyűjtheti a naplókat. Emellett áttekintheti a telepítési naplót. Szükség lehet az Azure Stack rendszergazdai ellenőrizni a verziószámot, használja az Azure Stack, és a naplók lekérése az Azure Stack a telepítéshez kapcsolódó kommunikáljon.

1. Tekintse át a [központi telepítési állapot](#review-deployment-status) és a [a naplók begyűjtéséről](#get-logs-from-a-vm) a Kubernetes-fürt fő csomópontból.
2. Szeretné használni az Azure Stack legújabb verzióját. Ha biztos benne, hogy az Azure Stack verziójával, lépjen kapcsolatba az Azure Stack rendszergazdai. A Kubernetes-fürt marketplace idő 0.3.0 1808 vagy nagyobb Azure Stack-verzió szükséges.
3.  Tekintse át a virtuális gép létrehozása fájljait. Előfordulhat, hogy a észlelt a következő problémákat:  
    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcs, amelyet létrehozott.  
    - A virtuális gép létrehozása belső hiba lehet, hogy rendelkezik aktivált, vagy aktivált-létrehozási hiba. Hibák többek között az Azure Stack-előfizetéshez tartozó kapacitás korlátozások tényező okozhatja.
    - A virtuális gép teljesen minősített tartománynevét (FQDN) ismétlődő előtaggal kezdődhet?
4.  Ha a virtuális gép **OK**, majd kiértékelheti a DVM. Ha a DVM hibaüzenetet:

    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcs, amelyet létrehozott.  
     - Az Azure Stack-rendszergazdától a naplók begyűjtéséről az Azure Stack használatával a privilegizált végpontok kell. További információkért lásd: [Azure Stack-diagnosztikai eszközök](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Ha az üzembe helyezéssel kapcsolatos kérdése van, tegye fel a kérdéseit, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Tekintse át a telepítés állapota

A Kubernetes-fürtöt, tekintse át az esetleges problémák központi telepítésekor a központi telepítés állapotát tekintheti meg.

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).
2. Válassza ki **erőforráscsoportok**, és válassza ki az erőforráscsoport nevét használja, amikor üzembe kell helyezni a Kubernetes-fürt.
3. Válassza ki **központi telepítések** , majd a **üzemelő példány neve**.

    ![Hibaelhárítás](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Tekintse meg a hibaelhárítási ablakot. Minden üzembe helyezett erőforrás a következő adatokat tartalmazza.
    
    | Tulajdonság | Leírás |
    | ----     | ----        |
    | Erőforrás | Az erőforrás neve. |
    | Típus | Az erőforrás-szolgáltató és az erőforrás típusát. |
    | status | Az elem állapota. |
    | Időbélyeg | Az az idő, UTC-időbélyeg. |
    | Művelet részletei | A művelet részleteit, például az erőforrás-szolgáltató részt vesz a művelet, az erőforrás-végpontot, és az erőforrás nevét. |

    Minden elem lesz egy állapotikon zöld vagy piros.

## <a name="get-logs-from-a-vm"></a>Virtuális gép naplók lekérése

Akkor lesz kell főcsomóponthoz való kapcsolódáshoz a virtuális Gépet a fürthöz, nyisson meg egy bash-parancssort, és futtasson egy szkriptet létrehozni a naplók. A fő tekintheti meg az erőforráscsoportot, és nevű `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Előfeltételek

A bash kell kérni a gépen a használata kezelheti az Azure Stack. A bash használatával futtassa a szkripteket, amelyek a naplók elérése. Egy Windows-gépen a Git segítségével telepítve a bash-parancssorban is használhatja. A git legújabb verziójának beszerzéséhez, lásd: [git letöltési](https://git-scm.com/downloads).

### <a name="get-logs"></a>Naplók lekérése

1. Nyissa meg a bash parancssorban. Git használja egy Windows-gépen, ha egy bash-parancssort megnyithatja az a következő elérési úton: `c:\programfiles\git\bin\bash.exe`.
2. Futtassa az alábbi bash-parancsokat:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > A Windows, nem kell futtatni `sudo` is egyszerűen használható és `chmod 744 getkuberneteslogs.sh`.

3. Ugyanabban a munkamenetben futtassa a következő parancsot a frissítve, hogy a környezet megfelelő paraméterekkel.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Tekintse át a paramétereket, és állítsa az értékeket az adott környezet alapján.
    | Paraméter           | Leírás                                                                                                      | Példa                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i – identitás-fájlja | A kubernetes fő virtuális gép csatlakoztatása az RSA titkos kulccsal fájl. A kulcs kell kezdődnie `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.PEM                                                        |
    | -h,--gazdagép          | A nyilvános IP-cím vagy a Kubernetes-fürt fő virtuális gép teljesen minősített tartománynevét (FQDN). A virtuális gép neve kezdődik `k8s-master-`.                       | IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u: – a felhasználó          | A Kubernetes-fürt fő virtuális gép felhasználóneve. A Piactéri elem konfigurálásakor beállíthatja ezt a nevet.                                                                    | azureuser                                                                     |
    | -d-,--vmdhost       | A nyilvános IP-cím vagy a DVM teljes Tartománynevét. A virtuális gép neve kezdődik `vmd-`.                                                       | IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Amikor hozzáadja a paraméterek értékeit, akkor előfordulhat, hogy következőhöz hasonló:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Sikeres futtatás a naplókat hoz létre.

    ![Generált naplók](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. A parancs által létrehozott a mappák a naplók begyűjtéséről. A parancs létrehoz egy új mappát, és időbélyege azt.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine – kubernetes-dvm.log

## <a name="next-steps"></a>További lépések

[Az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

[Kubernetes felvétele a Marketplace-en (az Azure Stack-operátorokról)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
