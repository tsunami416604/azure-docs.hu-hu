---
title: Titkos kötet csatlakoztatása tárolócsoporthoz
description: Megtudhatja, hogyan csatlakoztathat titkos kötetet a bizalmas adatok tárolására a tárolópéldányok számára
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 756828e71174246450245938595c8872afc62961
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657152"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Titkos kötet csatlakoztatása az Azure Container-példányokban

Titkos *kötet* használatával bizalmas információkat szolgáltat a tárolócsoportban lévő tárolók számára. A *titkos* kötet tárolja a titkos kulcsokat a köteten belüli fájlokban, a tárolócsoportban lévő tárolók által elérhető. Titkos kulcsok titkos *kötetben* való tárolásával elkerülheti a bizalmas adatok, például az SSH-kulcsok vagy az adatbázis hitelesítő adatainak hozzáadását az alkalmazáskódhoz.

* Miután egy tárolócsoportban titkos rendszerű titkos kulcsot telepített, a titkos kötet *írásvédett.*
* Az összes titkos kötetet [tmpfs][tmpfs], ram-alapú fájlrendszer támogatja; tartalmukat soha nem írják nem felejtő tárolásra.

> [!NOTE]
> *A titkos* kötetek jelenleg linuxos tárolókra korlátozódnak. Ismerje meg, hogyan adhatja át a biztonságos környezeti változókat mind a Windows, mind a [Linux-tárolókhoz a Set környezeti változókban.](container-instances-environment-variables.md) Miközben azon dolgozunk, hogy az összes funkciót a Windows tárolók, megtalálja a jelenlegi platform különbségek az [áttekintést.](container-instances-overview.md#linux-and-windows-containers)

## <a name="mount-secret-volume---azure-cli"></a>Titkos kötet csatlakoztatása - Azure CLI

Egy vagy több titkos rendszerű tároló üzembe helyezéséhez `--secrets` az `--secrets-mount-path` Azure CLI használatával, adja meg a és a paramétereket az [az container create][az-container-create] parancs. Ez a példa egy *titkos* kötetet csatol, amely két titkos fájlt tartalmaz, `/mnt/secrets`"mysecret1" és "mysecret2", a következő helyen:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A következő [az container exec][az-container-exec] kimenet egy rendszerhéj megnyitását mutatja a futó tárolóban, felsorolva a fájlokat a titkos köteten belül, majd megjeleníti azok tartalmát:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Titkos kötet csatlakoztatása - YAML

Tárolócsoportokat is üzembe helyezhet az Azure CLI-vel és egy [YAML-sablonnal.](container-instances-multi-container-yaml.md) A YAML-sablon általi üzembe helyezés az előnyben részesített módszer több tárolóból álló tárolócsoportok üzembe helyezésekor.

YAML-sablonnal történő üzembe helyezéskor a titkos értékeknek **Base64-kódolásúnak** kell lenniük a sablonban. A titkos értékek azonban egyszerű szövegként jelennek meg a tárolóban lévő fájlokban.

A következő YAML-sablon egy tárolócsoportot határoz meg egy `/mnt/secrets`tárolóval, amely *titkos* kötetet csatlakoztat. A titkos kötet két fájlt tartalmaz, amelyek titkos kulcsokat tartalmaznak, "mysecret1" és "mysecret2".

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

A YAML-sablonnal történő telepítéshez mentse az `deploy-aci.yaml`előző YAML-kódot egy `--file` fájlba, majd hajtsa végre az az container [create][az-container-create] parancsot a következő paraméterrel:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Titkos kötet csatlakoztatása – Erőforrás-kezelő

A CLI és a YAML központi telepítése mellett egy tárolócsoportot is telepíthet egy Azure [Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups)használatával.

Először a `volumes` sablon tárolócsoport-szakaszában `properties` népesítse be a tömböt. Erőforrás-kezelő sablonnal történő üzembe helyezéskor a titkos értékeknek **Base64-kódolásúnak** kell lenniük a sablonban. A titkos értékek azonban egyszerű szövegként jelennek meg a tárolóban lévő fájlokban.

Ezután a tárolócsoport minden olyan tárolójának, amelyben a *titkos* kötetet `volumeMounts` csatlakoztatni `properties` szeretné, a tárolódefiníció szakaszában feltölti a tömböt.

A következő Erőforrás-kezelő sablon egy tárolócsoportot határoz *secret* meg egy `/mnt/secrets`tárolóval, amely titkos kötetet csatlakoztat. A titkos kötetnek két titka van: "mysecret1" és "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Az Erőforrás-kezelő sablonnal történő telepítéshez mentse az `deploy-aci.json`előző JSON-t egy `--template-file` fájlba, majd hajtsa végre az [az-telepítési csoport létrehozási][az-deployment-group-create] parancsát a következő paraméterrel:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>További lépések

### <a name="volumes"></a>Kötetek

Megtudhatja, hogyan csatlakoztathat más kötettípusokat az Azure Container-példányokban:

* [Azure-fájlmegosztás csatlakoztatása az Azure Container Instancesben](container-instances-volume-azure-files.md)
* [EmptyDir kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-emptydir.md)
* [GitRepo-kötet csatlakoztatása az Azure Container-példányokban](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Biztonságos környezeti változók

A tárolók (beleértve a Windows-tárolókat is) bizalmas információinak biztosításának másik módja a [biztonságos környezeti változók](container-instances-environment-variables.md#secure-values)használata.

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
