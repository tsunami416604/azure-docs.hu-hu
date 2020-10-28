---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 817c41a969f03ad04d372c516a16ef6b770f3e18
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92756030"
---
## <a name="access-the-virtual-machine"></a>A virtuális gép elérése

A következő lépések a Azure Cloud Shell Azure CLI-t használják. Ha szeretné, [telepítheti az Azure CLI](/cli/azure/install-azure-cli) -t a fejlesztői gépre, és helyileg is futtathatja a parancsokat.

Az alábbi lépések bemutatják, hogyan konfigurálhatja az Azure-beli virtuális gépet az **SSH** -hozzáférés engedélyezéséhez. A megjelenő lépések feltételezik, hogy a megoldás-gyorsító neve a **contoso-szimulációs** – ezt az értéket cserélje le az üzemelő példány nevére:

1. A megoldás-gyorsító erőforrásokat tartalmazó erőforráscsoport tartalmának listázása:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Jegyezze fel a virtuális gép nevét, a nyilvános IP-címet és a hálózati biztonsági csoportot – később szüksége lesz ezekre az értékekre.

1. Frissítse a hálózati biztonsági csoportot az SSH-hozzáférés engedélyezéséhez. A következő parancs feltételezi, hogy a hálózati biztonsági csoport neve **contoso-szimulációs-NSG** – ezt az értéket cserélje le a hálózati biztonsági csoport nevére:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Csak a tesztelés és a fejlesztés során engedélyezze az SSH-hozzáférést. Ha engedélyezi az SSH- [t, a lehető leghamarabb le kell tiltania](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Frissítse az **azureuser** -fiók jelszavát a virtuális gépen, és Ismerje meg a jelszót. A következő parancs futtatásakor válassza ki a saját jelszavát:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Keresse meg a virtuális gép nyilvános IP-címét. A következő parancs feltételezi, hogy a virtuális gép neve **VM-vikxv** – ezt az értéket cserélje le az előzőleg megjegyzett virtuális gép nevére:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Jegyezze fel a virtuális gép nyilvános IP-címét.
