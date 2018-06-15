---
title: Parancsok végrehajtása a futó tárolók Azure tároló példányát
description: Ismerje meg, hogy egy parancs hajtható végre olyan tároló, amely jelenleg fut az Azure-tároló példányok
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.openlocfilehash: 43211f620efb16cbcd722d3d386b1bb862fc6280
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32165646"
---
# <a name="execute-a-command-in-a-running-azure-container-instance"></a>Egy parancs hajtható végre a futó Azure-tárolót példánya

Azure tároló példányokat támogatja a futó tároló egy parancs végrehajtása. A parancs futtatása már használatba tároló akkor hasznos alkalmazásfejlesztés és hibaelhárítás során. Ez a szolgáltatás leggyakoribb használata, hogy a futó tárolók problémákat is hibakeresése, indítsa el az interaktív rendszerhéjat.

## <a name="run-a-command-with-azure-cli"></a>Futtassa a parancsot az Azure parancssori felület

Egy parancs hajtható végre egy futó tárolóhoz [az tároló exec] [ az-container-exec] a a [Azure CLI][azure-cli]:

```azurecli
az container exec --resource-group <group-name> --name <container-group-name> --exec-command "<command>"
```

Ha például elindíthatja a rendszerhéjakba egy Nginx-tároló:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
```

Az alábbi példa kimenetben a rendszerhéjakba a indul el egy futó Linux-tároló, a biztosít, amelyben egy terminált `ls` végrehajtása:

```console
$ az container exec --resource-group myResourceGroup --name mynginx --exec-command "/bin/bash"
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@caas-83e6c883014b427f9b277a2bba3b7b5f-708716530-2qv47:/# exit
exit
Bye.
```

Ebben a példában egy futó Nanoserver tároló a parancssor indul el:

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

Ha a [a tárolócsoport](container-instances-container-groups.md) használandó futtassa a parancsot a tároló nevét meg több tároló, például egy alkalmazás-tárolót és a naplózás oldalkocsi `--container-name`.

Például a tároló csoportban *mynginx* két tárolói, *nginx-alkalmazás* és *naplózó*. A rendszerhéj el a *nginx-alkalmazás* tároló:

```azurecli
az container exec --resource-group myResourceGroup --name mynginx --container-name nginx-app --exec-command "/bin/bash"
```

## <a name="restrictions"></a>Korlátozások

Azure tároló példányok jelenleg az egyetlen folyamat elindítása [az tároló exec][az-container-exec], és a parancssori argumentumok nem adható át. Például nem láncolt parancsok például `sh -c "echo FOO && echo BAR"`, vagy hajtsa végre a `echo FOO`.

## <a name="next-steps"></a>További lépések

Tudnivalók az egyéb hibaelhárítási eszközök és a gyakori telepítési problémákat [Azure tároló példányát tároló és a telepítési problémák elhárításához](container-instances-troubleshooting.md).

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[azure-cli]: /cli/azure