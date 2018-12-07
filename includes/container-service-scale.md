---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: 2ed74a4ba19af3a441bcf26a48890f033e6c365f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53002254"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

[Az Azure Container Service-fürt üzembe helyezését követően](../articles/container-service/dcos-swarm/container-service-deployment.md) előfordulhat, hogy módosítania kell az ügynökcsomópontok számát. Például ha több ügynökre van szüksége további tárolóalkalmazások vagy -példányok futtatásához. 

Módosíthatja a DC/OS, Docker Swarm vagy Kubernetes-fürtben ügynökcsomópontok számát az Azure Portalon vagy az Azure CLI használatával. 

## <a name="scale-with-the-azure-portal"></a>Méretezés az Azure Portal használatával

1. Az [Azure Portalon](https://portal.azure.com) lépjen a **Container Services** felületre, majd kattintson a módosítani kívánt tárolószolgáltatásra.
2. A **Tárolószolgáltatás** panelen kattintson az **Ügynökök** elemre.
3. A **Virtuális gépek száma** mezőben adja meg az ügynökcsomópontok kívánt számát.

    ![Készlet méretezése a portálon](./media/container-service-scale/container-service-scale-portal.png)

4. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

## <a name="scale-with-the-azure-cli"></a>Méretezés az Azure CLI-vel

Győződjön meg arról, hogy Ön [telepítve](/cli/azure/install-az-cli2) a legfrissebb Azure CLI és a egy Azure-fiókkal jelentkezett be (`az login`).

### <a name="see-the-current-agent-count"></a>Az ügynökök aktuális számának megtekintése
A jelenleg a fürtben lévő ügynökök számának megtekintéséhez futtassa az `az acs show` parancsot. Ez megjeleníti a fürtkonfigurációt. Az alábbi parancs például a `myResourceGroup` erőforráscsoportban lévő `containerservice-myACSName` nevű tárolószolgáltatás konfigurációját jeleníti meg:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

A parancs az ügynökök számát a `Count` értékében adja vissza az `AgentPoolProfiles` területen.

### <a name="use-the-az-acs-scale-command"></a>Az acs scale parancs használata
Az ügynökcsomópontok számának módosításához futtassa az `az acs scale` parancsot, és adja meg az **erőforráscsoportot**, a **tárolószolgáltatás nevét** és az **ügynökök új számának** kívánt értékét. Alacsonyabb illetve magasabb érték megadásával vertikálisan le- illetve felskálázhatja a fürtöt.

Például ha az előző fürtben az ügynökök számát 10-re szeretné módosítani, a következő parancsot adja meg:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Az Azure CLI, beleértve az ügynökök új számát, a tárolószolgáltatás új konfigurációját jelöli JSON-karakterláncot ad vissza.

További parancsbeállításokért futtassa az `az acs scale --help` parancsot.

## <a name="scaling-considerations"></a>Méretezési szempontok

* Az ügynökcsomópontok számának 1 és 100 közé kell esnie, a határokat is beleértve. 

* Magkvótája korlátozhatja a lehetséges ügynökcsomópontok számát a fürtben.

* Az ügynökcsomópontok méretezésére irányuló műveletek egy, az ügynökkészletet tartalmazó Azure virtuálisgép-méretezési csoporton lesznek végrehajtva. A DC/OS-fürtökben a jelen cikkben bemutatott műveletekkel csak a magánkészletekben lévő ügynökcsomópontok méretezhetők.

* A fürtben üzembe helyezett vezénylőtől függően külön méretezheti a fürtben futó tárolók példányainak számát. A DC/OS-fürtökben például a [Marathon felhasználói felület](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) használatával módosíthatja a tárolóalkalmazások példányainak számát.


## <a name="next-steps"></a>További lépések
* Lásd: [további példákat](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) az Azure CLI-parancsok használata az Azure Container Service.
* Ismerkedjen meg a [DC/OS-ügynökkészletekkel](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) az Azure Container Service szolgáltatásban.

