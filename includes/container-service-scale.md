# <a name="scale-agent-nodes-in-a-container-service-cluster"></a>Ügynökcsomópontok méretezése a Container Service-fürtökben
[Az Azure Container Service-fürt üzembe helyezését követően](../articles/container-service/dcos-swarm/container-service-deployment.md) előfordulhat, hogy módosítania kell az ügynökcsomópontok számát. Például ha több ügynökre van szüksége további tárolóalkalmazások vagy -példányok futtatásához. 

Az Azure Portal vagy az Azure CLI 2.0 használatával módosíthatja az ügynökcsomópontok számát DC/OS-, Docker Swarm- vagy Kubernetes-fürtökben. 

## <a name="scale-with-the-azure-portal"></a>Méretezés az Azure Portal használatával

1. Az [Azure Portalon](https://portal.azure.com) lépjen a **Container Services** felületre, majd kattintson a módosítani kívánt tárolószolgáltatásra.
2. A **Tárolószolgáltatás** panelen kattintson az **Ügynökök** elemre.
3. A **Virtuális gépek száma** mezőben adja meg az ügynökcsomópontok kívánt számát.

    ![Készlet méretezése a portálon](./media/container-service-scale/container-service-scale-portal.png)

4. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

## <a name="scale-with-the-azure-cli-20"></a>Méretezés az Azure CLI 2.0 használatával

Győződjön meg arról, hogy [telepítve van](/cli/azure/install-az-cli2) a legfrissebb Azure CLI 2.0, és hogy bejelentkezett egy Azure-fiókba (`az login`).

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

Az Azure CLI 2.0 egy JSON-karakterláncot ad vissza, amely a tárolószolgáltatás új konfigurációját jelöli, beleértve az ügynökök új számát is.

További parancsbeállításokért futtassa az `az acs scale --help` parancsot.

## <a name="scaling-considerations"></a>Méretezési szempontok

* Az ügynökcsomópontok számának 1 és 100 közé kell esnie, a határokat is beleértve. 

* Magkvótája korlátozhatja a lehetséges ügynökcsomópontok számát a fürtben.

* Az ügynökcsomópontok méretezésére irányuló műveletek egy, az ügynökkészletet tartalmazó Azure virtuálisgép-méretezési csoporton lesznek végrehajtva. A DC/OS-fürtökben a jelen cikkben bemutatott műveletekkel csak a magánkészletekben lévő ügynökcsomópontok méretezhetők.

* A fürtben üzembe helyezett vezénylőtől függően külön méretezheti a fürtben futó tárolók példányainak számát. A DC/OS-fürtökben például a [Marathon felhasználói felület](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) használatával módosíthatja a tárolóalkalmazások példányainak számát.

* Az ügynökcsomópontok automatikus méretezése a Container Service-fürtökben jelenleg nem támogatott.

## <a name="next-steps"></a>Következő lépések
* Tekintse meg az Azure CLI 2.0-parancsok az Azure Container Service szolgáltatásban való használatát bemutató [további példákat](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).
* Ismerkedjen meg a [DC/OS-ügynökkészletekkel](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) az Azure Container Service szolgáltatásban.

