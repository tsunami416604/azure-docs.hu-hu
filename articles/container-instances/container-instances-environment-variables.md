---
title: Környezeti változók beállítása a tárolópéldányban
description: Megtudhatja, hogyan állíthatja be a környezeti változókat az Azure Container Instances tárolóiban futtatott tárolókban
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247227"
---
# <a name="set-environment-variables-in-container-instances"></a>Környezeti változók beállítása tárolópéldányokban

A tárolópéldányok környezeti változóinak beállítása lehetővé teszi a tárolóban futó alkalmazás vagy szkript dinamikus konfigurálását. Ez hasonló a `--env` parancssori `docker run`argumentumhoz. 

Környezeti változók beállítása a tárolóban, adja meg őket, amikor létrehoz egy tárolópéldányt. Ez a cikk példákat mutat be a környezeti változók beállítására, amikor egy tárolót az [Azure CLI,](#azure-cli-example)az [Azure PowerShell](#azure-powershell-example)és az [Azure Portal](#azure-portal-example)használatával indít el. 

Ha például a Microsoft [aci-wordcount tárolórendszerképet][aci-wordcount] futtatja, a következő környezeti változók megadásával módosíthatja annak működését:

*NumWords*: Az STDOUT-nak küldött szavak száma.

*MinLength*: A szavak ban lévő karakterek minimális száma, hogy meg lehessen számolni. A nagyobb szám figyelmen kívül hagyja az olyan gyakori szavakat, mint a ""- és "a".

Ha környezeti változókként kell titkos kulcsokat átadnia, az Azure Container Instances támogatja a Windows és a Linux-tárolók [biztonságos értékeit.](#secure-values)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Példa az Azure CLI-re

Az [aci-wordcount][aci-wordcount] tároló alapértelmezett kimenetének megtekintéséhez először futtassa ezzel az [az container create][az-container-create] paranccsal (nincs megadva környezeti változó):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimenet módosításához indítson el `--environment-variables` egy második tárolót a hozzáadott argumentummal, és adja meg a *NumWords* és a *MinLength* változók értékeit. (Ebben a példában feltételezi, hogy a CLI-t egy Bash rendszerhéjban vagy az Azure Cloud Shellben futtatja. Ha a Windows parancssorát használja, adja meg a változókat dupla idézőjelekkel, például `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Miután mindkét tároló állapota *leállítva* jelenik meg (használja [az container show][az-container-show] állapot ellenőrzéséhez), jelenítse meg a naplókat az az tároló [naplók][az-container-logs] a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A tárolók kimenete azt mutatja, hogyan módosította a második tároló parancsfájljának viselkedését a környezeti változók beállításával.

**sajtakonténer1**
```output
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

**startály2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Példa az Azure PowerShellre

A környezeti változók beállítása a PowerShellben hasonló `-EnvironmentVariable` a CLI-hez, de a parancssori argumentumot használja.

Először indítsa el az [aci-wordcount][aci-wordcount] tárolót az alapértelmezett konfigurációban ezzel a [New-AzContainerGroup][new-Azcontainergroup] paranccsal:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Most futtassa a következő [New-AzContainerGroup][new-Azcontainergroup] parancsot. Ez a *tömbváltozó* feltöltése után a NumWords és *a MinLength* környezeti változókat adja meg: `envVars`

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Miután mindkét tároló állapota *levan állítva* (használja [a Get-AzContainerInstanceLog][azure-instance-log] állapot ellenőrzéséhez), húzza le a naplókat a [Get-AzContainerInstanceLog][azure-instance-log] paranccsal.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Az egyes tárolók kimenete bemutatja, hogyan módosította a tároló által futtatott parancsfájlt a környezeti változók beállításával.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Példa az Azure Portalra

Környezeti változók beállítása, amikor egy tárolót az Azure Portalon, adja meg őket a **Speciális** lapon, amikor létrehozza a tárolót.

1. A **Speciális** lapon állítsa az **Újraindítás házirendet** *A hiba után*
2. A **Környezeti változók**csoportban adja meg `NumWords` az első változó értékét, `5` és írja be `MinLength` a második változó `8` értékét. 
1. Válassza **a Véleményezés + létrehozás** lehetőséget a tároló ellenőrzéséhez és központi telepítéséhez.

![Portállap az Engedélyezés gombbal és szövegdobozokkal][portal-env-vars-01]

A tároló naplóinak megtekintéséhez a **Beállítások csoportban** válassza a **Tárolók**lehetőséget, majd **a Naplók lehetőséget.** Az előző CLI- és PowerShell-szakaszokban látható kimenethez hasonlóan láthatja, hogy a környezeti változók hogyan módosították a parancsfájl viselkedését. Csak öt szó jelenik meg, mindegyik legalább nyolc karakter hosszú.

![Tárolónapló kimenetét megjelenítő portál][portal-env-vars-02]

## <a name="secure-values"></a>Biztonságos értékek

A biztonságos értékekkel rendelkező objektumok bizalmas adatokat, például jelszavakat vagy kulcsokat tartalmazhatnak az alkalmazáshoz. A környezeti változók biztonságos értékeinek használata biztonságosabb és rugalmasabb, mint a tároló rendszerképébe való felvétel. Egy másik lehetőség a titkos kötetek használata, amelyet [az Azure Container Instances titkos kötetének csatlakoztatása ismertet.](container-instances-volume-secret.md)

A biztonságos értékekkel rendelkező környezeti változók nem láthatók a tároló tulajdonságaiban – értékeik csak a tárolón belül érhetők el. Például az Azure Portalon vagy az Azure CLI-ben megtekintett tárolótulajdonságok csak egy biztonságos változó nevét jelenítik meg, az értékét nem.

Állítson be egy biztonságos `secureValue` környezeti változót `value` úgy, hogy a változó típusának szabályos a tulajdonsága helyett a tulajdonságot adja meg. A következő YAML-ben meghatározott két változó mutatja be a két változótípust.

### <a name="yaml-deployment"></a>YAML-telepítés

Hozzon `secure-env.yaml` létre egy fájlt a következő kódrészlettel.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Futtassa a következő parancsot a tárolócsoport YAML-rel történő telepítéséhez (szükség szerint módosítsa az erőforráscsoport nevét):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Környezeti változók ellenőrzése

Futtassa az [az container show][az-container-show] parancsot a tároló környezeti változóinak lekérdezéséhez:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A JSON-válasz a nem biztonságos környezeti változó kulcsát és értékét is megjeleníti, de csak a biztonságos környezeti változó nevét:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Az [az container exec][az-container-exec] paranccsal, amely lehetővé teszi egy parancs futtatását egy futó tárolóban, ellenőrizheti, hogy a biztonságos környezet változó be van-e állítva. Futtassa a következő parancsot egy interaktív bash munkamenet elindításához a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Miután megnyitotta az interaktív rendszerhéjat a tárolón belül, elérheti a `SECRET` változó értékét:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>További lépések

A feladatalapú forgatókönyvek, például a több tárolóval rendelkező nagy adatkészlet kötegelt feldolgozása, futásidőben egyéni környezeti változók előnyeit élvezhetik. A feladatalapú tárolók futtatásáról további információt a [Tárolóalapú feladatok futtatása újraindítási házirendekkel című témakörben talál.](container-instances-restart-policy.md)

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
