---
title: A Kubernetes üzembe helyezés az Azure Stack hibaelhárítása |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a Kubernetes üzembe helyezés az Azure Stackhez.
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
ms.author: mabvrigg
ms.reviewer: waltero
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: 6a5efce2f50a25902b33f2cb85d470a280000305
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002064"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>A Kubernetes üzembe helyezés az Azure Stack hibaelhárítása

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

> [!Note]  
> Az Azure Stacken Kubernetes szolgáltatás előzetes verzióban. Az Azure Stack kapcsolat nélküli forgatókönyv jelenleg nem érhető el az előzetes verzió.

A következő cikket úgy tűnik, a Kubernetes-fürt hibaelhárítása. Tekintse át a központi telepítési riasztás, és a telepítéshez szükséges elemek által a központi telepítés állapotának áttekintése. Szüksége lehet a telepítési naplók gyűjtése az Azure Stack vagy a Linux rendszerű virtuális gépek, amelyeken Kubernetes. Emellett szüksége lehet az Azure Stack rendszergazdai naplók lekérése egy felügyeleti végpont használata.

## <a name="overview-of-deployment"></a>Üzembe helyezés áttekintése

A fürt hibaelhárítás megkezdése előtt érdemes áttekinteni az Azure Stack-beli Kubernetes-fürt üzembe helyezési folyamat. A központi telepítés a virtuális gépek létrehozása és telepítése az ACS Engine, a fürt számára egy megoldás Azure Resource Manager-sablon használatával.

### <a name="deployment-workflow"></a>Telepítési munkafolyamat

Az alábbi ábrán látható, az általános folyamat a fürt üzembe helyezéséhez.

![Kubernetes-folyamat üzembe helyezése](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>A központi telepítés lépései

1. Bemeneti paraméterek gyűjteni a Piactéri elemet.

    Adja meg az értékeket, akkor be kell állítania a Kubernetes-fürtöt, többek között:
    -  **Felhasználónév**: A felhasználónév, a Linux rendszerű virtuális gépek, amelyek a Kubernetes-fürt és a DVM részei.
    -  **Nyilvános SSH-kulcs**: A kulcs, amely az összes Linux-számítógép, a Kubernetes-fürt és a DVM részeként létrehozott engedély szolgál.
    -  **Egyszerű szolgáltatás**: A Kubernetes Azure felhőszolgáltató által használt azonosítója. Az ügyfél-azonosító az Alkalmazásazonosítót azonosította az eseményt, az egyszerű szolgáltatás létrehozásakor. 
    -  **Titkos Ügyfélkód**: Az egyszerű szolgáltatás létrehozásakor létrehozott kulcs azokat.

2. Az üzemelő példány virtuális gép létrehozása és az egyéni szkriptek futtatására szolgáló bővítmény.
    -  Az üzembe helyezés Linux rendszerű virtuális gép létrehozása a marketplace Linux-rendszerképek használatával **Ubuntu Server 16.04-LTS**.
    -  Töltse le és futtassa az egyéni szkriptek futtatására szolgáló bővítmény a marketplace-ről. A parancsfájl **egyéni parancsfájl Linux 2.0**.
    -  A DVM egyéni parancsfájl futtatásával. A szkript a következő feladatokat hajtja végre:
        1. A katalógus végpont lekérése az Azure Resource Manager-metaadatok végpontja.
        2. Az active directory erőforrás-azonosító lekérése az Azure Resource Manager-metaadatok végpontja.
        3. Az API-modell betölti az ACS-motor.
        4. Az ACS Engine telepíti a Kubernetes-fürt, és menti az Azure Stack felhő profilt `/etc/kubernetes/azurestackcloud.json`.
3. A fő virtuális gépek létrehozásához.

4. Töltse le és futtassa egyéni parancsfájl-kiterjesztés.

5. A fő parancsfájl futtatásával.

    A szkript a következő feladatokat hajtja végre:
    - Telepíti a etcd, a Docker és a Kubernetes erőforrások, például kubelet. etcd egy elosztott kulcs-érték tároló, amely lehetővé teszi a számítógépfürtökön tárolja adatait. Docker tárolók néven operációs csontot operációsrendszer-szintű virtualizations támogatja. Kubelet a csomóponti ügynök, amely a Kubernetes-csomópontokon.
    - Beállítja a etcd szolgáltatás.
    - Beállítja a kubelet szolgáltatás.
    - Kubelet elindul. Ez a feladat az alábbi lépésekből áll:
        1. Elindítja az API-szolgáltatást.
        2. A hálózativezérlő-szolgáltatás elindul.
        3. A scheduler szolgáltatás elindul.
6. Az ügynök virtuális gépek létrehozása.

7. Töltse le és futtassa az egyéni szkriptek futtatására szolgáló bővítmény.

7. Futtassa az ügynök parancsfájlt. Az ügynök egyéni szkript a következő feladatokat hajtja végre:
    - Etcd telepíti.
    - Beállítja a kubelet szolgáltatás
    - A Kubernetes-fürthöz csatlakozik.

## <a name="steps-for-troubleshooting"></a>Hibaelhárítási lépések

A virtuális gépeken, amelyek támogatják a Kubernetes-fürtöt is összegyűjtheti a naplókat. Emellett áttekintheti a telepítési naplót. Szüksége lehet az Azure Stack rendszergazdai ellenőrizni a verziószámot, amely használja, és a naplók lekérése az Azure Stacken, amely a központi telepítés kapcsolódó van szüksége az Azure Stack-kommunikációhoz.

1. Tekintse át a [központi telepítési állapot](#review-deployment-status) és [a naplók begyűjtéséről](#get-logs-from-a-vm) a Kubernetes-fürt fő csomópontból.
2. Győződjön meg arról, hogy használ-e az Azure Stack legújabb verzióját. Ha biztos abban, hogy melyik verziót használ, lépjen kapcsolatba az Azure Stack rendszergazdai.
3.  Tekintse át a virtuális gép létrehozása fájljait. Előfordulhat, hogy a következő problémák rendelkeztek:  
    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcsot, amelyet Ön hozott létre.  
    - A virtuális gép létrehozása előfordulhat, hogy indított belső hiba történt, vagy aktivált-létrehozási hiba. Számos tényezőtől okozhat hibát, beleértve a kapacitás-korlátozások az Azure Stack-előfizetéshez.
    - Győződjön meg arról, hogy a teljesen minősített tartománynevét (FQDN) a virtuális gép olyan ismétlődő előtaggal kezdődik.
4.  Ha a virtuális gép **OK**, majd kiértékelheti a DVM. Ha a DVM hibaüzenetet:

    - Lehet, hogy a nyilvános kulcs érvénytelen. Tekintse át a kulcsot, amelyet Ön hozott létre.  
    - Az Azure Stack-rendszergazdától a naplók begyűjtéséről az Azure Stack használatával a privilegizált végpontokat kell. További információkért lásd: [Azure Stack-diagnosztikai eszközök](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Ha az üzembe helyezéssel kapcsolatos kérdése van, közzéteheti, vagy tekintse meg, ha valaki már megválaszolta a kérdést a [Azure Stack-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Tekintse át a telepítés állapota

Ha a Kubernetes-fürtöt telepít, a telepítés állapota minden olyan problémákat tekintheti meg.

1. Nyissa meg a [Azure Stack portálon](https://portal.local.azurestack.external).
2. Válassza ki **erőforráscsoportok**, majd válassza ki a nevét, amelyet a Kubernetes-fürt üzembe helyezésekor használt erőforráscsoport.
3. Válassza ki **központi telepítések**, majd válassza ki a **üzemelő példány neve**.

    ![Hibaelhárítás](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Tekintse meg a hibaelhárítási ablakot. Minden üzembe helyezett erőforrás a következő információkat biztosítja:
    
    | Tulajdonság | Leírás |
    | ----     | ----        |
    | Erőforrás | Az erőforrás neve. |
    | Typo | Az erőforrás-szolgáltató és az erőforrás típusát. |
    | status | Az elem állapota. |
    | Időbélyeg | Az az idő, UTC-időbélyeg. |
    | Művelet részletei | A művelet részleteit, például az erőforrás-szolgáltató, amely során a műveletet az erőforrás-végpont és az erőforrás nevét. |

    Minden elem, zöld vagy a piros állapot ikonja van.

## <a name="get-logs-from-a-vm"></a>Virtuális gép naplók lekérése

A naplófájlokat hoznak létre, meg főcsomóponthoz való kapcsolódáshoz a virtuális Gépet a fürthöz, nyisson meg egy bash-parancssort, és futtassa a parancsfájlt. A fő virtuális gép tekintheti meg az erőforráscsoportot és az nevű `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Előfeltételek

Kell egy bash parancssor, amellyel kezelheti az Azure Stack a gépen. A bash használatával futtassa a szkripteket, amelyek a naplók elérése. Egy Windows-gépen használhatja a bash parancssorban, amely a Git segítségével telepítve van. A git legújabb verziójának beszerzéséhez, lásd: [Git letöltési](https://git-scm.com/downloads).

### <a name="get-logs"></a>Naplók lekérése

Naplók lekérése, hajtsa végre az alábbi lépéseket:

1. Nyissa meg a bash parancssorban. Git használja egy Windows-gépen, ha egy bash-parancssort megnyithatja az a következő elérési úton: `c:\programfiles\git\bin\bash.exe`.
2. Futtassa az alábbi bash-parancsokat:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > A Windows, nem kell futtatni `sudo`. Ehelyett egyszerűen használhatja `chmod 744 getkuberneteslogs.sh`.

3. Ugyanabban a munkamenetben futtassa a következő parancsot a frissítve, hogy a környezet megfelelő paraméterekkel:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmd-host 192.168.102.37
    ```

4. Tekintse át a paramétereket, és állítsa az értékeket az adott környezet alapján.

    | Paraméter           | Leírás                                                                                                      | Példa                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host       | A nyilvános IP-cím vagy a DVM teljes Tartománynevét. A virtuális gép neve kezdődik `vmd-`.                                                       | IP-cím: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |
    | -f, --force | Ne jelenjen meg újra a titkos kulcs feltöltése előtt. | |
    | -i – identitás-fájlja | Az RSA titkos kulcs fájlját a Kubernetes fő virtuális gép csatlakozni. A kulcs a kezdéshez: <br>`-----BEGIN RSA PRIVATE KEY-----` | C:\data\id_rsa.pem                                                        |
    | -h, --help  | A parancs használatát, a nyomtatási `getkuberneteslogs.sh` parancsfájlt. | |
    | -m, --master-host          | A nyilvános IP-cím vagy a Kubernetes-fürt fő virtuális gép teljesen minősített tartománynevét (FQDN). A virtuális gép neve kezdődik `k8s-master-`.                       | IP-cím: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u: – a felhasználó          | A Kubernetes-fürt fő virtuális gép felhasználóneve. A Piactéri elem konfigurálásakor beállíthatja ezt a nevet.                                                                    | azureuser                                                                     |




   Amikor hozzáadja a paraméterértékeket, lehet például a következő kódot:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Sikeres futtatás a naplókat hoz létre.

    ![Generált naplók](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


1. A mappák, a parancs által létrehozott a naplók begyűjtéséről. A parancs létrehozza az új mappák és időbélyegzői őket.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>További lépések

[Az Azure Stack üzembe helyezése Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)

[Kubernetes-fürt hozzáadása a Marketplace-en (az Azure Stack-operátorokról)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes az Azure-ban](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
