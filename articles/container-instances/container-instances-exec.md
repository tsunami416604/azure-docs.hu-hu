---
title: Hajtsa végre a parancsokat a futó tárolók az Azure Container Instances szolgáltatásban
description: Ismerje meg, hogyan hajtsa végre a parancsot egy tároló, amely jelenleg fut az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: danlep
ms.openlocfilehash: 577e2386c352798bc21a2c78b22726128ac7cf0a
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854088"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Futtassa a parancsot a futó Azure container instance-példány

Az Azure Container Instances támogatja a parancs végrehajtása a futó tárolót. A parancs futtatása egy tárolóban, hogy már használatba különösen hasznos az alkalmazások fejlesztését és a hibaelhárítás során. A leggyakoribb Ez a funkció használata interaktív shell elindításához, így Ön is a futó tárolót kapcsolatos problémák megoldásában.

## <a name="run-a-command-with-azure-cli"></a>Az Azure CLI-parancs futtatása

Futtassa a parancsot a futó tároló [az tároló exec] [ az-container-exec] a a [Azure CLI-vel][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Ha például egy Bash-rendszerhéjból az Nginx-tároló elindításához:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Az alábbi példa kimenetében, a Bash felületen elindításkor mindig futó Linux-tárolók biztosít, amelyben parancsot egy terminálban a `ls` hajtja végre:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Ebben a példában egy futó Nanoserver-tárolót a parancssor használatával indul el:

```console
$ az container exec --resource-group myResourceGroup --name myiis --exec-command "cmd.exe"
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

Ha a [tárolócsoport](container-instances-container-groups.md) rendelkezik több tárolót, például egy alkalmazástárolót, és a naplózás az oldalkocsi, adja meg a használandó futtassa a parancsot a tároló neve `--container-name`.

Például a tárolócsoport *mynginx* két tárolói *nginx-alkalmazás* és *naplózó*. Az-shell elindításához a *nginx-alkalmazás* tároló:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Korlátozások

Az Azure Container Instances jelenleg támogatja az egyetlen folyamat elindítása [az tároló exec][az-container-exec], és a parancs argumentumai nem adhatók át. Például nem láncolt parancsok például a `sh -c "echo FOO && echo BAR"`, vagy hajtsa végre a `echo FOO`.

## <a name="next-steps"></a>További lépések

Ismerje meg más hibaelhárítási eszközei és a gyakori üzembe helyezési problémák [tároló és a telepítési problémák az Azure Container Instances szolgáltatásban](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure