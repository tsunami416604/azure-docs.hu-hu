---
title: "Az Azure a dinamikus készletek kezelését Ansible használatával"
description: "Ansible használata az Azure a dinamikus készletek kezelése"
ms.service: ansible
keywords: "ansible, azure, devops, bash, cloudshell, dinamikus készlet"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 799be6d2bb521de38af952376bf8ee14a18846de
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Az Azure a dinamikus készletek kezelését Ansible használatával
Ansible használható lekéréses Hardverleltár-információk (beleértve a felhő adatforrások, például az Azure) különböző forrásokból történő egy *dinamikus készlet*. A cikkben, használja a [Azure Cloud rendszerhéj](./ansible-run-playbook-in-cloudshell.md) Ansible Azure dinamikus készlet két olyan virtuális gépet hoz létre konfigurálásához azon virtuális gépek egyik címkét, és Nginx telepítse a virtuális gép címkézett.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

- **Azure hitelesítő adataival** - [létrehozása Azure-felhasználó hitelesítő adatait, és Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>A teszt virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg [rendszerhéj felhőalapú](https://docs.microsoft.com/en-us/azure/cloud-shell/overview).

1. Hozzon létre egy Azure-erőforráscsoport ebben az oktatóanyagban a virtuális gépek tárolására.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Hozzon létre két Linux virtuális gépek Azure-ban a következő módszerek egyikét:

    - **Ansible alkalmazástervezési** – a cikk [alapvető virtuális gép létrehozása az Azure-ban Ansible](/azure/virtual-machines/linux/ansible-create-vm) bemutatja, hogyan hozható létre virtuális gépet egy Ansible forgatókönyv a. Ha a forgatókönyv segítségével határozza meg a virtuális gépek közül, győződjön meg arról, hogy az SSH-kapcsolat használt jelszó helyett.

    - **Az Azure CLI** -probléma az alábbi parancsokat a felhő rendszerhéj a két virtuális gép létrehozásához:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>A virtuális gépek címke
Is [címkék használata az Azure-erőforrások rendszerezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) felhasználó által definiált kategóriák szerint. 

Írja be a következő [az erőforrás címke](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) parancs használatával címkézhesse a virtuális gép `ansible-inventory-test-vm1` kulccsal `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>A dinamikus készlet létrehozása
Miután a virtuális gépek meghatározott (és címkézett), a dinamikus készlet létrehozásához idő. Ansible biztosít nevű Python-parancsfájl [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) azáltal, hogy az API-kérelmek az Azure erőforrás-kezelő létrehozó Azure-erőforrások dinamikus készlet. A következő lépések végigvezetik használatával a `azure_rm.py` csatlakozni a kétlépéses parancsfájl tesztelése az Azure virtuális gépek:

1. Használja a GNU `wget` parancs beolvasása a `azure_rm.py` parancsfájlt:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Használja a `chmod` parancs futtatásával módosíthatja a hozzáférési engedélyeket a `azure_rm.py` parancsfájl. A következő parancsot használja a `+x` paraméter lehetővé végrehajtásából (fut) a megadott fájl (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Használja a [ansible parancs](https://docs.ansible.com/ansible/2.4/ansible.html) az erőforráscsoport való csatlakozáshoz: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. A csatlakozás után eredmények eléréséhez a következő eredmény jelenik meg:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>A virtuálisgép-tag engedélyezése
Miután beállította a kívánt címkét, kell "Engedélyezés" címke. Ahhoz, hogy a címke egyik módja a címke exportálásával egy környezeti változó neve `AZURE_TAGS` keresztül a **exportálása** parancs:

```azurecli-interactive
export AZURE_TAGS=nginx
```

A címke exportálása után próbálja meg a `ansible` újra a parancsot:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Ekkor megjelenik a csak egy virtuális gép (amelynek címke értéke megegyezik egy exportált a **AZURE_TAGS** környezeti változó):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>A virtuális gép címkézett Nginx beállítása
A címkék célja a virtuális gépek alcsoportokat gyorsan és könnyen használható lehetőség. Például tételezzük fel Nginx telepíti, amelyhez egy címkét a hozzárendelt virtuális gépek csak a `nginx`. A következő lépések bemutatják, hogyan könnyen ez elvégzéséhez:

1. Hozzon létre egy fájlt (kell tartalmaznia a forgatókönyv) `nginx.yml` az alábbiak szerint:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Az alábbi kód beszúrása az újonnan létrehozott `nginx.yml` fájlt:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Futtassa a `nginx.yml` forgatókönyv:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. A forgatókönyv futtatása után eredmények eléréséhez a következő eredmény jelenik meg:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Nginx-telepítés sikerességének ellenőrzése
Ez a szakasz bemutatja, tesztelheti, hogy az Nginx telepítve van-e a virtuális gép egyik módszer.

1. Használja a [az vm-ip-címeinek listáját](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) parancsot IP-címének lekéréséhez a `ansible-inventory-test-vm1` virtuális gépet. A visszaadott érték (a virtuális gép IP-cím) majd az SSH-parancs paramétereként való kapcsolódásra szolgál a virtuális géphez.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. A [nginx - v](https://nginx.org/en/docs/switches.html) parancs szolgál nyomtatni az Nginx-verzió. Azonban azt is segítségével határozza meg, ha telepítve van-e a Nginx. Adja meg, ha csatlakozik a `ansible-inventory-test-vm1` virtuális gépet.

    ```azurecli-interactive
    nginx -v
    ```

1. Miután lefuttatta a a `nginx -v` parancsot az Nginx verzió meg (a második sor), amely azt jelzi, hogy telepítve van-e a Nginx.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Nyomja meg a  **&lt;Ctrl > D** billentyűzet kombinációja leválasztja az SSH-munkamenetet.

1. Hajt végre a fenti lépések a `ansible-inventory-test-vm2` virtuális gép, amelynek eredménye egy tájékoztató üzenet jelzi, amelyről Nginx (amely magában foglalja, hogy nincs telepítve ezen a ponton) tölthet le:

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Alapszintű virtuális gép létrehozása az Azure-ban Ansible](/azure/virtual-machines/linux/ansible-create-vm)
