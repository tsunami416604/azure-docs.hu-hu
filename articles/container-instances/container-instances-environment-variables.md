---
title: Környezeti változók beállítása az Azure Container Instances szolgáltatásban
description: Ismerje meg, hogyan futtatja az Azure Container Instances a tárolók a környezeti változók beállítása
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: marsma
ms.openlocfilehash: 7a3d521d4382e3d9b5b1b1cf4eb3e43fa02c9a40
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159549"
---
# <a name="set-environment-variables"></a>Környezeti változók beállítása

A környezeti változók beállítása a container Instances szolgáltatásban az lehetővé teszi, hogy az alkalmazás vagy a tároló által futtatott parancsfájl dinamikus konfiguráció. A környezeti változók beállítása egy tárolóban, adja meg őket, amikor létrehoz egy tárolópéldányt. Beállíthatja a környezeti változók egy tárolóhoz, az indításakor a [Azure CLI-vel](#azure-cli-example), [Azure PowerShell-lel](#azure-powershell-example), és a [az Azure portal](#azure-portal-example).

Például, ha futtatja a [microsoft/aci-wordcount] [ aci-wordcount] tárolórendszerképet, és módosíthatja annak viselkedését adja meg az alábbi környezeti változókat:

*NumWords*: A STDOUT küldött szavak számát.

*A MinLength*: word, a megszámlálandó karaktereinek minimális száma. Ha nagyobb figyelmen kívül hagyja a gyakori szavakat, például a "," és "a."

Környezeti változókként adja át a titkos kulcsok van szüksége, ha támogatja az Azure Container Instances [értékek biztonságos](#secure-values) Windows- és Linux-tárolókhoz.

## <a name="azure-cli-example"></a>Az Azure CLI-példa

Az alapértelmezett kimenet, hogy a [microsoft/aci-wordcount] [ aci-wordcount] tárolót, először futtassa a [az tároló létrehozása] [ az-container-create] parancsot (nem környezeti változók megadva):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimenet módosítása esetén a második tároló indítása a `--environment-variables` argumentum hozzáadva, az értékek megadásával a *NumWords* és *MinLength* változókat:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Miután mindkét tárolók állapot jelenik meg értékként *kilépett* (használata [az container show] [ az-container-show] állapotának ellenőrzése), a naplók megjelenítése [az tárolónaplók] [ az-container-logs] a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A kimenet a tárolók bemutatják, hogyan úgy módosítottuk a második tárolót szkript viselkedését a környezeti változók beállításával.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Az Azure PowerShell-példa

Környezeti változók beállítása a PowerShell a parancssori felület hasonló, de használja a `-EnvironmentVariable` parancssori argumentum.

Először indítsa el a [microsoft/aci-wordcount] [ aci-wordcount] az alapértelmezett konfigurációban a tároló [New-AzureRmContainerGroup] [ new-azurermcontainergroup]parancsot:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Most futtassa a következő [New-AzureRmContainerGroup] [ new-azurermcontainergroup] parancsot. Ehhez adja meg a *NumWords* és *MinLength* környezeti változó egy tömbváltozó feltöltése után `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Miután mindkét tárolók állapot *kilépett* (használata [Get-AzureRmContainerInstanceLog] [ azure-instance-log] állapotának ellenőrzése), a naplók lekérése a [ Get-AzureRmContainerInstanceLog] [ azure-instance-log] parancsot.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

A kimenet az egyes tárolók bemutatja, hogyan úgy módosítottuk a környezeti változók beállításával a tároló által futtatott parancsfájl.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure portal – példa

Környezeti változók beállítása egy tárolót az Azure Portalon való indításkor, adja meg azokat a **konfigurációs** lapon, a tároló létrehozásakor.

A portál telepítésekor jelenleg legfeljebb három változókhoz, és meg kell adnia őket a következő formátumban: `"variableName":"value"`

Látható egy példa, indítsa el a [microsoft/aci-wordcount] [ aci-wordcount] -tárolóban a *NumWords* és *MinLength* változókat.

1. A **konfigurációs**állítsa be a **újraindítási házirend** való *hiba esetén*
2. Adja meg `"NumWords":"5"` az első változók **Igen** alatt **adjon hozzá további környezeti változókat**, és adja meg `"MinLength":"8"` a második változó. Válassza ki **OK** a, és ezután üzembe a tárolót.

![Környezeti változó engedélyezése gombra és a szövegmezők megjelenítő portáloldala][portal-env-vars-01]

A tároló naplóinak, a megtekintéséhez **beállítások** kiválasztása **tárolók**, majd **naplók**. Hasonló a kimenet a látható az előző CLI és PowerShell szakaszokban láthatja, hogyan a parancsfájl viselkedés módosította a környezeti változókat. Csak öt szó jelenik meg, amelyek mindegyike egy minimális hossza nyolc karakternél.

![Portál ábrázoló tároló napló kimenetét][portal-env-vars-02]

## <a name="secure-values"></a>Biztonságos értékek

Biztonságos értékek objektum bizalmas információkat, például jelszavak vagy -alkalmazásához kulcsok tárolására szolgálnak. Biztonságos értékek a környezeti változók használata biztonságosabb és rugalmasabb, mint a be a tároló rendszerképét is. Egy másik lehetőség az, hogy a titkos kötetek ismertetett [az Azure Container Instances szolgáltatásban titkos kötet csatlakoztatási](container-instances-volume-secret.md).

Környezeti változók a biztonságos értékek nem láthatók a tároló tulajdonságai – értékekre elérhető csak a tárolóban. Például a tároló tulajdonságainak megtekintett az Azure portal vagy az Azure CLI-vel megjelenített csak egy biztonságos változó neve, nem az értékét.

Adjon meg egy biztonságos környezeti változó beállítása a `secureValue` tulajdonság helyett a normál `value` a változó típushoz. A két változót a következő yaml-kódot definiált változó kétféle mutatják be.

### <a name="yaml-deployment"></a>YAML-telepítés

Hozzon létre egy `secure-env.yaml` fájlt a következő kódrészletre.

```yaml
apiVersion: 2018-06-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
        - "name": "SECRET"
          "secureValue": "my-secret-value"
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

Futtassa a következő parancsot az a tárolócsoportot YAML üzembe helyezéséhez (módosítsa szükség szerint az erőforráscsoport neve):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Ellenőrizze a környezeti változók

Futtassa a [az container show] [ az-container-show] parancsot a tároló a környezeti változók lekérdezéséhez:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A JSON-válasz jeleníti meg, mind a nem biztonságos környezeti változó kulcs-érték, de csak a biztonságos környezeti változó neve:

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

Az a [az tároló exec] [ az-container-exec] parancsot, amely lehetővé teszi, hogy végrehajtja a parancsot a futó tárolót, ellenőrizheti, hogy a biztonságos környezeti változó értéke. Futtassa a következő parancsot egy bash interaktív munkamenet elindításához a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Miután megnyitotta a tárolón belül interaktív kezelőfelület, elérheti a `SECRET` változó értéke:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>További lépések

Feladatalapú forgatókönyvek, például a kötegelt feldolgozási egy nagy méretű adathalmazt a különböző tárolók esetében is kihasználhatják a futásidőben egyéni környezeti változókat. Futó tárolók feladataival kapcsolatos további információkért lásd: [tárolóalapú feladatok futtatása az Azure Container Instances szolgáltatásban](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
