---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781459"
---
## <a name="access-the-virtual-machine"></a>A virtuális gép elérése

A következő lépések az Azure-beli CLI-t használják az Azure Cloud Shellben. Ha szeretné, [telepítheti az Azure CLI-t a](/cli/azure/install-azure-cli) fejlesztői gépen, és helyileg futtathatja a parancsokat.

A következő lépések bemutatják, hogyan konfigurálhatja az Azure virtuális gépet **az SSH-hozzáférés** engedélyezéséhez. A bemutatott lépések feltételezik, hogy a megoldásgyorsítóhoz választott név **contoso-szimuláció** – cserélje le ezt az értéket a központi telepítés nevére:

1. A megoldásgyorsító erőforrásait tartalmazó erőforráscsoport tartalmának listázása:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Jegyezze fel a virtuális gép nevét, a nyilvános IP-címet és a hálózati biztonsági csoportot – ezekre az értékekre később szüksége lesz.

1. Frissítse a hálózati biztonsági csoportot az SSH-hozzáférés engedélyezéséhez. A következő parancs feltételezi, hogy a hálózati biztonsági csoport neve **contoso-simulation-nsg** - cserélje le ezt az értéket a hálózati biztonsági csoport nevére:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Csak a tesztelés és fejlesztés során engedélyezze az SSH-hozzáférést. Ha engedélyezi az [SSH-t, a lehető leghamarabb tiltsa le újra](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Frissítse az **azureuser** fiók jelszavát a virtuális gépen egy ismert jelszóra. A következő parancs futtatásakor válassza ki saját jelszavát:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Keresse meg a virtuális gép nyilvános IP-címét. A következő parancs feltételezi, hogy a virtuális gép neve **vm-vikxv** -- cserélje le ezt az értéket a korábban feljegyzett virtuális gép nevére:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Jegyezze fel a virtuális gép nyilvános IP-címét.
