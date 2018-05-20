---
title: Környezeti változók beállítása az Azure-tároló példányok
description: 'Útmutató: Azure tároló példányát futtatja a tárolókban lévő környezeti változók beállítása'
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/16/2018
ms.author: marsma
ms.openlocfilehash: 1a025ce647cb3c071a6549a433e6505b85409fdc
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="set-environment-variables"></a>Környezeti változók beállítása

A tároló példányát környezeti változók beállítása lehetővé teszi az alkalmazás vagy a tároló által futtatott parancsfájl dinamikus konfigurálása. Környezeti változók tároló beállításához adja meg azokat a tároló példány létrehozásakor. Környezeti változók állíthatja be, amikor hozzákezd a tárolóhoz a [Azure CLI](#azure-cli-example), [Azure PowerShell](#azure-powershell-example), és a [Azure-portálon](#azure-portal-example).

Például, ha futtatja a [microsoft/aci-wordcount] [ aci-wordcount] tároló kép, módosíthatja annak viselkedését a következő környezeti változók megadásával:

*NumWords*: STDOUT küldött szót.

*A MinLength*: ahhoz, hogy a megszámlálandó szót karakterek minimális száma. Minél nagyobb karakterkészletszámot figyelmen kívül hagyja a gyakori szavakat, például "a" és "a."

## <a name="azure-cli-example"></a>Az Azure parancssori felület – példa

Az alapértelmezett eredményének megtekintéséhez a [microsoft/aci-wordcount] [ aci-wordcount] tároló, először futtassa a [az tároló létrehozása] [ az-container-create] parancsot (nem környezeti változók megadva):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

A kimeneti módosításához indítsa el a második tárolóhoz a `--environment-variables` argumentum hozzáadott, értékeinek meghatározása a *NumWords* és *MinLength* változók:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Miután mindkét tárolók állapotát jeleníti meg, mint a *kilépett* (használata [az tároló megjelenítése] [ az-container-show] ellenőrizze, hogy), megjeleníti a naplók és a [az tároló naplók] [ az-container-logs] a kimenet megtekintéséhez.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A tárolók kimenetének megjelenítése hogyan módosította a második tároló parancsfájl viselkedés környezeti változók beállításával.

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

Környezeti változók beállítása a PowerShell a CLI hasonló, de használja a `-EnvironmentVariable` parancssori argumentum.

Először indítsa el a [microsoft/aci-wordcount] [ aci-wordcount] alapértelmezett konfigurációja a tároló [New-AzureRmContainerGroup] [ new-azurermcontainergroup]parancs:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Most futtassa a következő [New-AzureRmContainerGroup] [ new-azurermcontainergroup] parancsot. Ez egy határozza meg a *NumWords* és *MinLength* környezeti változó egy tömbváltozó feltöltése után `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Miután mindkét tárolók állapota *kilépett* (használata [Get-AzureRmContainerInstanceLog] [ azure-instance-log] ellenőrizze, hogy), a naplók és lekéréses a [ Get-AzureRmContainerInstanceLog] [ azure-instance-log] parancsot.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Minden egyes tároló az alábbiakat mutatja be, hogyan módosította a parancsfájl futtatása a tároló környezeti változók beállításával.

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

## <a name="azure-portal-example"></a>Az Azure portál – példa

Környezeti változók egy tárolót az Azure portálon indításakor beállításához adja meg azokat a **konfigurációs** a tároló létrehozása esetén.

A portál telepítésekor három változóra jelenleg korlátozott, és meg kell adnia azokat a következő formátumban: `"variableName":"value"`

Példa megtekintéséhez indítsa el a [microsoft/aci-wordcount] [ aci-wordcount] tárolóhoz a *NumWords* és *MinLength* változók.

1. A **konfigurációs**, beállíthatja a **indítsa újra a házirend** való *hiba esetén*
2. Adja meg `"NumWords":"5"` az első változó, válassza ki **Igen** alatt **adja hozzá a további környezeti változókat**, és írja be `"MinLength":"8"` a második változó. Válassza ki **OK** ellenőrzése és a tároló telepítését.

![Portál oldaláról, valamint a környezeti változó engedélyezése gombra és a szöveg mezőbe][portal-env-vars-01]

A tároló naplók megtekintéséhez az **beállítások** válasszon **tárolók**, majd **naplók**. A kimeneti hasonló látható az előző CLI és a PowerShell szakaszok, láthatja, hogyan a parancsfájl viselkedés módosította a környezeti változók. Csak öt szavak jelennek meg, az legalább nyolc karakterből.

![Portál ábrázoló tároló kimenet][portal-env-vars-02]

## <a name="next-steps"></a>További lépések

Feladatalapú helyzetekben, például egy olyan nagy több tároló adatkészlet kötegfeldolgozási is kihasználhatja a le egyéni környezeti változók futásidőben. Tárolókat feladatalapú futtatásával kapcsolatos további információkért lásd: [indexelése feladatok futtatása az Azure-tároló példányok](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com