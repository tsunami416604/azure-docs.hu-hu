---
title: Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása
description: Ismerje meg a hozzáféréshez bizalmas adatok tárolására a tárolópéldányok által titkos kötet csatlakoztatása
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: marsma
ms.openlocfilehash: 572e6701bbe69bbb07c76d468a309030fc37d984
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159889"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatása

Használja a *titkos* kötetet adjon meg bizalmas adatokat egy tárolócsoport tárolókhoz. A *titkos* kötet tárolócsoportban a tárolók által elérhető köteten lévő fájlok a titkos kulcsokat tárol. A titkok tárolásával egy *titkos* kötet, elkerülheti a bizalmas adatokat – például SSH-kulcsokat, vagy az adatbázis-hitelesítő adatok hozzáadása az alkalmazás kódjában.

Minden *titkos kulcs* kötetek élvezik [tmpfs][tmpfs], a RAM-alapú fájlrendszer; állandó tárolóba soha nem írt azok tartalmát.

> [!NOTE]
> *Titkos kulcs* kötetek Linux-tárolók jelenleg korlátozva. Ismerje meg, hogyan adhatók át a biztonságos környezeti változókat a Windows- és Linux-tárolókhoz [környezeti változókat](container-instances-environment-variables.md). Idővel az összes funkció a Windows-tárolók dolgozunk, miközben Ön aktuális eltérésekről platform a [kvóták és régiók rendelkezésre állása az Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Csatlakoztassa a titkos kötet – Azure CLI-vel

Egy vagy több titkokkal-tárolók üzembe helyezését az Azure CLI-vel, például a `--secrets` és `--secrets-mount-path` paramétereket a [az tároló létrehozása] [ az-container-create] parancsot. Ebben a példában csatlakoztatja egy *titkos* két titkos kulcsokat, a "mysecret1" és "mysecret2," a kötet `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image microsoft/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

A következő [az tároló exec] [ az-container-exec] a kimenet mutatja, nyissa meg egy rendszerhéjat a futó tárolót, a titkos köteten lévő fájlok listázása, majd azok tartalmát megjelenítése:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Titkos kötet - YAML csatlakoztatásához

Tárolócsoportok az Azure CLI-vel is telepítheti és a egy [YAML sablon](container-instances-multi-container-yaml.md). YAML-sablon üzembe helyezése a előnyben részesített módszer, több tárolóból álló tárolócsoportok üzembe helyezésekor.

Amikor egy YAML-sablonból telepít, a titkos értékkel kell **Base64-kódolású** a sablonban. Azonban egyszerű szövegként a fájlokat a tárolóban lévő titkos értékek jelennek meg.

A következő YAML-sablon meghatározása egy tárolócsoportot egy tároló, amely csatlakoztatja a *titkos* kötet `/mnt/secrets`. A titkos kötet rendelkezik két titkos kulcsokat, a "mysecret1" és "mysecret2."

```yaml
apiVersion: '2018-06-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
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

A YAML-sablon üzembe helyezéséhez mentse az előző YAML-nevű `deploy-aci.yaml`, majd hajtsa végre a [az tároló létrehozása] [ az-container-create] parancsot a `--file` paramétert:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Csatlakoztassa a titkos kötet – Resource Manager

CLI-t és a YAML üzembe helyezés mellett üzembe helyezése egy Azure-beli használatával tárolócsoport [Resource Manager-sablon](/azure/templates/microsoft.containerinstance/containergroups).

Első lépésként töltse fel a `volumes` tömb tárolócsoportban `properties` szakaszában a sablont. Amikor telepít egy Resource Manager-sablonnal, a titkos értékkel kell **Base64-kódolású** a sablonban. Azonban egyszerű szövegként a fájlokat a tárolóban lévő titkos értékek jelennek meg.

Tovább, amelyben szeretné csatlakoztatni a tárolócsoport minden tároló esetén a *titkos* kötet, töltse fel a `volumeMounts` Pole a `properties` a tároló-definíció szakasza.

A következő Resource Manager-sablon meghatározása egy tárolócsoportot egy tároló, amely csatlakoztatja a *titkos* kötet `/mnt/secrets`. A titkos kötet rendelkezik két titkos kulcsokat, a "mysecret1" és "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json --> [!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

A Resource Manager-sablon üzembe helyezéséhez mentse a fenti JSON-nevű `deploy-aci.json`, majd hajtsa végre a [az csoport központi telepítésének létrehozása] [ az-group-deployment-create] parancsot a `--template-file` paramétert:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>További lépések

### <a name="volumes"></a>Kötetek

Ismerje meg, hogyan csatlakoztathat egyéb kötettípusok az Azure Container Instances szolgáltatásban:

* [Azure-tároló példányát az Azure fájlmegosztások csatlakoztatása](container-instances-volume-azure-files.md)
* [Az Azure-tároló példányát emptyDir kötet csatlakoztatása](container-instances-volume-emptydir.md)
* [Azure-tároló példányát gitRepo kötet csatlakoztatása](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Biztonságos környezeti változók

Bizalmas információk (beleértve a Windows-tárolók) tárolókkal, amelyek biztosítják egy másik módszer a következő: használatával [környezeti változók biztonságos](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
