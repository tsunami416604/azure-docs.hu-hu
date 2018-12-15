---
title: Az Azure Batch AI-fürtök használata |} A Microsoft Docs
description: Válassza ki a Batch AI-fürt konfigurációját, és hozzon létre és AI-fürtök kezeléséhez
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 9af8ce84805e48dd3c91dd7fb4fcf0b136fbfc60
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410091"
---
# <a name="work-with-batch-ai-clusters"></a>Batch AI-fürtök használata 

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Ez a cikk ismerteti az Azure Batch AI-fürtök használatának módját. Ez bemutatja a fürtöket, -konfigurációkat, lehetséges, és a példákat. A legtöbb a példákat, ebben a cikkben egy fürt létrehozása és kezelése az Azure CLI használatával. Azonban más eszközöket, mint például az Azure portal és az Azure Batch AI SDK-k használatával működik a fürtökkel.

Egy Batch AI-fürtöt az egyik a szolgáltatásban számos erőforrás. Tekintse meg a [áttekintése a Batch AI-erőforrások](resource-concepts.md) tudni, hogy a hatókör-fürtök a szolgáltatásban.

## <a name="introduction-to-clusters"></a>Fürtök bemutatása

A Batch AI-fürt tartalmazza a futó gépi tanulási és AI betanítási feladatokat a számítási erőforrások. A fürt összes csomópontja rendelkezik, az ugyanazon Virtuálisgép-méretet és operációsrendszer-lemezkép. A Batch AI-fürtök létrehozására, amelyeket a különböző igények számos lehetőséget kínál. Általában egy másik fürtön különböző kategóriájú projekt befejezéséhez szükséges feldolgozási beállítása. A csomópontok számát felfelé és igény szerint és kapacitásigényeket, illetve egy fürtben skálázhatja. Fürtök üzembe helyezhető és megosztják egymással egy csapatot, vagy egyéni felhasználók számára is minden egyes fürt üzembe helyezése saját. 

Minden egyes fürt nevű Batch AI erőforrás létezik egy *munkaterület*. Tetszőleges fürt kiépítése, előtt rendelkeznie kell egy Batch AI-munkaterület beállítása. Például, ha az Azure CLI használatával, használja a [az batchai munkaterület létrehozása](/cli/azure/batchai/workspace?view=azure-cli-latest#az-batchai-workspace-create) -munkaterület beállítása parancsot. 

Egy fürt létrehozása után egy üzenetsorba egy feladatokat küldhet egyszerre. A Batch AI majd elvégzi a feladatok a fürtön futó erőforrás lefoglalási folyamata. 

## <a name="cluster-configuration-options"></a>Fürt konfigurációs lehetőségek

A fürt megtervezésekor először határozza meg a számítási követelményeket. A Batch AI rugalmas konfigurációs lehetőségeket kínál, így testre szabhatja egy fürtöt az igényeinek megfelelően. A fürt üzembe helyezésekor érdemes figyelembe venni a majors lehetőségek a következők:

* **Virtuálisgép-méret** – válassza ki bármelyik [Virtuálisgép-méret](../virtual-machines/linux/sizes.md) elérhető egy [támogatott régió](https://azure.microsoft.com/global-infrastructure/services/) a fürtcsomópontok. Egyes fürtök csak egyet tartalmazhat méretű virtuális gép, így ha a tevékenységek szükség többféle típusú virtuális gépeket, külön számítási követelmény minden típusú fürt üzembe helyezése kell. Machine learning vagy fejlesztett ki, amelyek kihasználják a GPU-k, keretrendszerek és AI-modellek betanításához, tekintse meg a [GPU-optimalizált Virtuálisgép-méretek](../virtual-machines/linux/sizes-gpu.md) az Azure-ban.
  
* **Virtuális gép prioritása** – Batch AI-fürthöz tartozó dedikált vagy alacsony prioritású virtuális gépek kínál. Dedikált virtuális gépek a fürt a használatra vannak fenntartva. Az alacsony prioritás beállítást jelentős költségmegtakarítást engedélyezések feladatok folyamatban van a felfüggesztett, ha Azure szabadít fel a virtuális gépek lehetőséget a fel nem használt Azure-beli Virtuálisgép-kapacitás foglal le. Alacsony prioritású virtuális gép 24 óránál hosszabb ideig futó feladatok is automatikusan kimarad. Költségvetés fontos, ha érdemes alacsony prioritású virtuális gépek rövid Kísérletezési feladatokhoz. Ezután váltson dedikált virtuális gépek ideje hosszabb feladatok futtatása során.

* **A csomópontok száma** – Batch AI a fürtben található csomópontok számának manuális és automatikus skálázási lehetőségeket kínál. A manuális lehetőséggel azt szabályozhatja, mikor kell a fürt méretezése felfelé és lefelé a saját számítási költségek kezelését. Az automatikus skálázási beállítás biztosítja, hogy a fürt mindig downscales amikor nem használ, így minimálisra csökkenthető a számítási költségeket. 

  Ha a fürt átméretezése manuálisan, majd meghatározhatja a kezdeti cél fürt létrehozása során. A cél, amely a Batch AI osztja ki először csomópontok számát. Később manuálisan átméretezheti a csomópontok számát.  

  Ha az automatikus skálázás lehetőséget választja, határozza meg a cél-csomópontok maximális és minimális számát a fürt létrehozásakor. A cél között lehet 0, a csomópontok által támogatott maximális száma a [Batch AI magkvótáját](quota-limits.md). 0 célja karbantartása a fürt konfigurációját, bármely számítási költségeket anélkül teszi lehetővé. Ha a kvóta korlátját támogatja, mint egy nagyobb cél kér, majd az üzembe helyezés sikertelen lesz. 

* **Virtuálisgép-lemezkép** – alapértelmezés szerint a Batch AI látja el egy alapértelmezett Ubuntu Server-lemezképet, amely támogatja a tárolók számítási feladatait a fürt virtuális gépein. Egy másik előre konfigurált Linux-rendszerképek közül választhat az Azure piactéren, mint például egy [adatelemző virtuális gép](../machine-learning/data-science-virtual-machine/overview.md). 

* **Tárolási** – Batch AI automatikus tárolási lehetőséget, kiválaszthatja, hogy amikor egy fürtöt az Azure CLI használatával hoz létre, amely biztosít. Ez a beállítás automatikusan létrehoz egy Azure-beli fájlmegosztás és a Blob tárolóhoz tartozó új storage-fiókot. A tárolási erőforrások a fürtben lévő csomópontok mindegyikének csatlakoztatva vannak a végrehajtási idő, lehetővé téve a fájlok egy helyi elérési út érni során. A tárfiókok nevének, a fájlmegosztás nevét, az Blobtároló neve és a csatlakoztatási elérési utak minden alapértelmezett értékeket, amelyek testre is szabható további paraméterek használatával a fürt létrehozásakor rendelkezik. 

  Ha nincs tárolási lehetőségeket vannak megadva, akkor szükséges, a tárolási erőforrásokat külön-külön létrehozni és meghatározni őket, feladatok elküldésekor. Ez a beállítás akkor hasznos, ha a fürt kezeli a szervezet szintjén, de a tárolási felhasználói szinten kezeli. Fájlok feltöltése az Azure Storage és a hozzáférésüket a végrehajtási idő alatt módjáról további információkért lásd: [Store Batch AI-feladat bemeneti és kimeneti az Azure Storage](use-azure-storage.md).

* **Felhasználói hozzáférés** – Batch AI lehetővé teszi, hogy hozzon létre nyilvános és titkos SSH kulcs fájlok, amikor egy fürtöt hoz létre, vagy adja meg a saját SSH-kulcsokat, az egyes csomópontok az SSH segítségével. Megadhat egy a felhasználónevet (alapértelmezés szerint az aktuális felhasználó beállítása) és a jelszót. Ezeket a hitelesítő adatokat ahhoz, hogy a különböző metrikák megtekintéséhez vagy betekintést nyerhet további, a feladatok végrehajtása során a csomópontok eléréséhez használható.

* **A telepítő feladat** – Batch AI lehetővé teszi, hogy a felosztás után minden egyes csomóponton futtatandó parancssori argumentumok megadása. Megadhatja, hogy az elérési utat, ahol a kimeneti fájlba kell jelentkeznie. Használja ezt a beállítást, ha az alaprendszerképet túl további üzembe helyezési lépéseket.

* **További konfigurációs** -számos kevésbé gyakori forgatókönyv, amely speciális konfigurációt igényelhetnek. Ebben az esetben egy [fürt konfigurációs fájl](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#using-cluster-configuration-file) csatolható a fürt létrehozása az Azure CLI-parancsot. 

## <a name="create-the-cluster"></a>A fürt létrehozása

Miután eldöntötte, hogy a fürt konfigurációs beállítások, használja az Azure CLI-vel, az Azure portal vagy a Batch AI API-kat a fürt létrehozásához. Például hozzon létre egy fürtöt az Azure CLI használatával, kövesse a [az batchai cluster létrehozása](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-create) dokumentációja a pontos parancsát, amely biztosítja a szükséges konfigurációk létrehozásához. 

A legalapvetőbb konfiguráció a következő parancsot egy standard nc6 rendelkezések fürt SSH-hozzáférés és a egy csomópont. Alapértelmezés szerint ez a fürt tartalmazza a dedikált virtuális gépek futtatása a legújabb alapértelmezett Ubuntu Server-lemezképet.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --target 1 \
    --generate-ssh-keys
```

A következő példa egy standard nc6 rendelkezések fürt méretezhető automatikusan a 0 – 4 csomópont és alacsony prioritású csomópontok és a egy automatikus – storage-fiókot használ. A telepítő egy helyes konfiguráció esetén szüksége lesz egy fürtre, az alacsony költségű és könnyen kezelhető. 

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --vm-priority lowpriority \
    --max 4 \
    --min 0 \
    --use-auto-storage 
```

Az alábbi példa egy adatelemző virtuális gép lemezképet tartalmazó Standard_NC6 fürt kiosztja, egyéni tároló és a csatlakoztatási lehetőségek, egyéni SSH hitelesítő adatai, a telepítő feladat, amely telepíti a *csomagolja ki* csomag és a egy fürthöz a további telepítési konfigurációs fájl. Ez a konfiguráció csak a saját igényei szerint testreszabott több fürt példaként szolgál.

```azurecli-interactive
az batchai cluster create \
    --name mycluster \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --vm-size Standard_NC6 \
    --image UbuntuDSVM \ 
    --config-file cluster.json \
    --setup-task 'apt install unzip -y'
    --storage-account-name <STORAGE ACCOUNT NAME> \
    --nfs-name nfsmount \
    --afs-name afsmount \
    --bfs-name bfsmount \
    --user-name adminuser \
    --ssh-key id_rsa.pub \
    --password secretpassword 
```

## <a name="monitor-the-cluster"></a>A fürt monitorozása

A [az batchai cluster list](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-list), [az batchai cluster show](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-show), és [az batchai cluster node list](/cli/azure/batchai/cluster/node?view=azure-cli-latest#az-batchai-cluster-node-list) parancsok is használhatók az egyes fürtök különböző információk figyelését.

### <a name="list-all-clusters"></a>Az összes fürtök listázása

A következő paranccsal listát mindenkinek a fürtök a munkaterületen.

```azurecli-interactive
az batchai cluster list \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
```

### <a name="show-information-about-a-cluster"></a>A fürttel kapcsolatos információk megjelenítése

A következő parancsot egy adott fürtben található részletes információ táblázatos formában jeleníti meg.

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --output table
```

Ha a fürthöz lett üzembe helyezve a automatikus tárolási lehetőséggel, érdemes beolvasni a szkriptek és feladatok feltöltésekor használandó tárfiók nevét. Használja az alábbi parancsot:

```azurecli-interactive
az batchai cluster show \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> \
    --query "nodeSetup.mountVolumes.azureFileShares[0].{storageAccountName:accountName}"
```

A kimenet az alábbihoz hasonló lesz.

```JSON
{
  "storageAccountName": "baixxxxxxxxx"
}
```

### <a name="list-cluster-nodes"></a>Fürtcsomópontok listázása

Ha kell csatlakoznia, a fürtcsomópontokon, a következő parancsot a csomópontok és a kapcsolati adatok listáját kérdezi le.  

```azurecli-interactive
az batchai cluster node list \
    --name <CLUSTER NAME> \
    --workspace <WORKSPACE> \
    --resource-group <RESOURCE GROUP> 
 ```

Minden egyes csomópont esetében a kimenet az alábbihoz hasonló lesz:

```JSON
[
  {
    "ipAddress": "40.68.xxx.xxx",
    "nodeId": "tvm-xxxxxxxxxx-xxxxxxxx",
    "port": 50000.0
  }
]
```
Ezen információk használatával győződjön meg arról, a következőhöz hasonló paranccsal csomóponthoz SSH-kapcsolatot.

```bash
ssh myusername@40.68.xxx.xxx -p 50000
``` 

## <a name="submit-jobs-to-the-cluster"></a>Feladat elküldése a fürthöz

Miután a fürt kiépítése, majd elküldheti feladatok a csomópontokon való futtatáshoz. Tekintse meg a [az Batch job](/cli/azure/batchai/job?view=azure-cli-latest) parancsot módjai, előkészítése, feladatokat küldhet és felügyelhet az Azure CLI használatával. 

## <a name="downscale-cluster-for-later-use"></a>Későbbi használat céljából linearity fürt

Amikor elkészült, futtassa a feladatokat, célszerű a fürt downscale. Javasoljuk, hogy mindig linearity fürtként, ha azok nincsenek használatban ahhoz, hogy a számítási költségeket. A fürt 0 csomópontra downscaling lehetővé teszi az elszámolási időszakban felszámított díjakat leállítása közben nem kellene építenie a fürtök a későbbiekben, ha upscale újra kell. Ha az automatikus skálázás választotta, a fürt létrehozásakor, a fürt automatikusan kell a minimális cél downscale. Ha a Manuális méretezés lett kiválasztva, downscale a fürthöz a következő parancs használatával.

```azurecli-interactive
az batchai cluster resize \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE> \
    --target 0
```

## <a name="delete-a-cluster"></a>Fürt törlése

Miután végzett egy fürtöt használ, a [az batchai cluster delete](/cli/azure/batchai/cluster?view=azure-cli-latest#az-batchai-cluster-delete) parancs törli-e.

```azurecli-interactive
az batchai cluster delete \
    --name <CLUSTER NAME> \
    --resource-group <RESOURCE GROUP> \
    --workspace <WORKSPACE>
```

Az erőforráscsoport törlése is automatikusan törli a kiépített adott erőforráscsoportba tartozó összes fürt.

```azurecli-interactive
az group delete --name <RESOURCE GROUP>
```

## <a name="next-steps"></a>További lépések

Egy Batch AI-fürtöt hoz létre további példákért lásd: a [portál](quickstart-create-cluster-portal.md) vagy [Azure CLI-vel](quickstart-create-cluster-cli.md) a rövid útmutatóban vagy az [Batch AI-receptek](https://github.com/Azure/BatchAI/tree/master/recipes) a Githubon.
