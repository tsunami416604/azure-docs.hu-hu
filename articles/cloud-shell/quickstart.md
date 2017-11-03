---
title: "Az Azure-felhőbe rendszerhéj (előzetes verzió) gyors üzembe helyezés bash |} Microsoft Docs"
description: "A felhő rendszerhéj Bash gyors üzembe helyezés"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: bbad2735acf06ebefda020410731fb307c4ec614
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Az Azure-felhőbe rendszerhéj Bash gyors üzembe helyezés

Ez a dokumentum részletesen Bash használata az Azure felhőalapú Shell a [Azure-portálon](https://ms.portal.azure.com/).

> [!NOTE]
> A [PowerShell Azure Cloud rendszerhéj](quickstart-powershell.md) gyors üzembe helyezés akkor is elérhető.

## <a name="start-cloud-shell"></a>Indítsa el a felhő rendszerhéj
1. Indítsa el **felhő rendszerhéj** az Azure portál felső navigációs sáv <br>
![](media/quickstart/shell-icon.png)
2. Kiválaszthat egy tárfiókot és Azure fájlmegosztás létrehozása
3. Válassza a "Create a storage"

> [!TIP]
> Akkor automatikusan megtörténik az Azure CLI 2.0 minden munkamenetet a.

### <a name="select-the-bash-environment"></a>Válassza ki a Bash
1. Válassza ki a legördülő környezet a rendszerhéj ablak bal oldalán <br>
![](media/quickstart/env-selector.png)
2. Válassza ki a Bash

### <a name="set-your-subscription"></a>Az előfizetés beállítása
1. Lista előfizetések rendelkezik hozzáféréssel: <br>
`az account list`
2. Állítsa be az előnyben részesített előfizetés: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> A rendszer tárolja a későbbi kapcsolatok használata az előfizetés `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy új erőforráscsoportot "MyRG" nevű WestUS: <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása
Ubuntu virtuális gép létrehozása az új erőforráscsoportban. Az Azure CLI 2.0 SSH-kulcsok létrehozása, és a velük a virtuális gép beállítása. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> A nyilvános és titkos kulcsokat, a virtuális gép hitelesítéséhez használt kerülnek `/User/.ssh/id_rsa` és `/User/.ssh/id_rsa.pub` Azure CLI 2.0 alapértelmezés szerint. Az .ssh mappa a csatolt Azure fájlmegosztás 5 GB-os kép megőrződjenek.

A felhasználónév, a virtuális gép lesz a felhő rendszerhéj használt felhasználónév ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH a Linux virtuális gép be
1. Keresse meg a virtuális gép nevét, az Azure portál keresési sávon
2. Kattintson a "Csatlakozás" gombra, és futtassa:`ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Az SSH-kapcsolatot létesít, akkor meg kell jelennie az Ubuntu üdvözlő kérés. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Takarítás 
Az erőforráscsoport és bármely erőforrása törlése: <br>
Futtassa a `az group delete -n MyRG` parancsot.

## <a name="next-steps"></a>Következő lépések
[A felhő rendszerhéj Bash tárolásakor fájlok megismerése](persisting-shell-storage.md) <br>
[További tudnivalók az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[További tudnivalók az Azure File storage](../storage/files/storage-files-introduction.md) <br>