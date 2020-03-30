---
title: Parancsok végrehajtása futó tárolópéldányban
description: Megtudhatja, hogyan hajtson végre egy parancsot egy olyan tárolóban, amely jelenleg fut az Azure Container Instances-ban
ms.topic: article
ms.date: 03/30/2018
ms.openlocfilehash: de48e6ac246e2b0751561b4c60bb63d88b599bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247201"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Parancs végrehajtása egy futó Azure-tárolópéldányban

Az Azure Container Instances támogatja a parancsok végrehajtását a futó tárolókban. A parancsok a már elindított tárolókban való végrehajtása különösen hasznos lehet az alkalmazásfejlesztés és a hibaelhárítás során. Ezt a lehetőséget a leggyakrabban akkor veszik igénybe, amikor elindítanak egy interaktív felületet, amellyel elháríthatók a futó tároló hibái.

## <a name="run-a-command-with-azure-cli"></a>Parancs futtatása az Azure CLI-vel

Parancs végrehajtása egy futó tárolóban [az a container exec][az-container-exec] az [Azure CLI:][azure-cli]

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Például egy Bash-rendszerhéj indításához egy Nginx-tárolóban:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Az alábbi kimeneti példában a Bash rendszerhéj egy futó Linux-tárolóban indul el, amely egy terminált biztosít, amelyben `ls` a rendszer végrehajtja:

```output
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Ebben a példában a parancssor egy futó Nanokiszolgáló-tárolóban indul el:

```azurecli
az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
```

```output
Microsoft Windows [Version 10.0.14393]
(c) 2016 Microsoft Corporation. All rights reserved.

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 76E0-C852

 Directory of C:\

03/23/2018  09:13 PM    <DIR>          inetpub
11/20/2016  11:32 AM             1,894 License.txt
03/23/2018  09:13 PM    <DIR>          Program Files
07/16/2016  12:09 PM    <DIR>          Program Files (x86)
03/13/2018  08:50 PM           171,616 ServiceMonitor.exe
03/23/2018  09:13 PM    <DIR>          Users
03/23/2018  09:12 PM    <DIR>          var
03/23/2018  09:22 PM    <DIR>          Windows
               2 File(s)        173,510 bytes
               6 Dir(s)  21,171,609,600 bytes free

C:\>exit
Bye.
```

## <a name="multi-container-groups"></a>Többtárolós csoportok

Ha a [tárolócsoport](container-instances-container-groups.md) több tárolóval rendelkezik, például egy alkalmazástárolóval és egy naplózási `--container-name`oldalkocsival, adja meg annak a tárolónak a nevét, amellyel a parancsot futtatni szeretné.

Például a konténercsoportban *a mynginx* két tartály, *a nginx-app* és *a logger*. Shell indítása a *nginx-app* tárolón:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Korlátozások

Az Azure Container Instances jelenleg támogatja az [az container exec][az-container-exec]egyetlen folyamat elindítását, és nem adhatja át a parancsargumentumokat. Például nem lehet olyan `sh -c "echo FOO && echo BAR"`parancsokat `echo FOO`láncolni, mint a alkalmazásban, vagy végrehajtani.

## <a name="next-steps"></a>További lépések

További információ az Azure [Container Instances tároló- és telepítési problémák elhárítása című](container-instances-troubleshooting.md)témakörben található egyéb hibaelhárító eszközökről és gyakori telepítési problémákról.

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure