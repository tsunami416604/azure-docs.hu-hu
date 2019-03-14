---
title: Az Ansible segítségével az Azure dinamikus leltárak kezelése
description: Ismerje meg, hogyan használhatja az Ansible az Azure dinamikus leltárak kezelése
ms.service: azure
keywords: az ansible, azure, devops, bash, cloud Shell, dinamikus készlet
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: b4dd1788291198926d620e406edb568d486be8b5
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792378"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Az Ansible segítségével az Azure dinamikus leltárak kezelése
Az Ansible segítségével Hardverleltár-információk lekérése a különböző forrásokból (például az Azure felhőalapú forrásokból is beleértve) egy *dinamikus készlet*. Ebben a cikkben fogja használni a [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) konfigurálása az Ansible az Azure dinamikus készlet két virtuális gépet hoz létre, ezeket a virtuális gépeket egyik címkét, és az Nginx telepítése a címkézett virtuális gépen.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- **Azure-beli hitelesítő** - [létrehozása Azure hitelesítő adatait, és az Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Hozzon létre egy Azure-erőforráscsoportot, amely tárolja a virtuális gépek ehhez az oktatóanyaghoz.

    > [!IMPORTANT]  
    > Az ebben a lépésben létrehozott Azure-erőforráscsoportot, amely kizárólag kisbetűket névvel kell rendelkeznie. Ellenkező esetben a dinamikus készlet generációja sikertelen lesz.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Hozzon létre két Linux rendszerű virtuális gépek az Azure-ban a következő módszerek egyikével:

    - **Az Ansible-forgatókönyvek** – a cikk [alapszintű virtuális gép létrehozása az Azure-ban, az ansible segítségével](/azure/virtual-machines/linux/ansible-create-vm) azt ábrázolja, hogyan hozhat létre egy virtuális gépet az Ansible-forgatókönyvek. Egy forgatókönyv meghatározása egyikét vagy mindkettőt a virtuális gépek használatakor győződjön meg arról, hogy az SSH-kapcsolatot a rendszer jelszó helyett használja.

    - **Az Azure CLI** -probléma a következő parancsokat a Cloud shellben a két virtuális gép létrehozásához:

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

## <a name="tag-a-virtual-machine"></a>Virtuális gép címkézése
Is [címkék használata az Azure-erőforrások rendszerezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) felhasználó által definiált kategóriák szerint. 

Írja be a következő [az erőforrás-címke](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) parancsot a virtuális gép címkézése `ansible-inventory-test-vm1` kulccsal `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Dinamikus készítése
Ha létrehozta a virtuális gépek meghatározott (és a címkézett), a dinamikus készlet létrehozásához ideje. Az Ansible nevű Python-szkriptet biztosít [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) az Azure-erőforrások dinamikus leltára, amely létrehoz által az Azure Resource Manager API-kérelem indítására. A következő lépések végigvezetik használatával a `azure_rm.py` szeretne csatlakozni a két parancsfájlt tesztelése az Azure virtual machines:

1. Használja a GNU `wget` parancs használatával kérje le a `azure_rm.py` parancsfájlt:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Használja a `chmod` parancs használatával váltson a hozzáférési engedélyeket a `azure_rm.py` parancsfájlt. A következő parancsot használja a `+x` paramétert, hogy a megadott fájl (fut) végrehajtásához (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Használja a [ansible parancs](https://docs.ansible.com/ansible/2.4/ansible.html) csatlakozni az erőforráscsoportban: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. A csatlakozás után a következő kimenethez hasonló eredmény jelenik meg:

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

## <a name="enable-the-virtual-machine-tag"></a>A virtuális gép címke engedélyezése
Miután beállította a kívánt címke, "Engedélyezés" címke van szükség. Ahhoz, hogy egy címke egyik módja egy környezeti változóhoz a címke exportálásával nevezzük `AZURE_TAGS` keresztül a **exportálása** parancsot:

```azurecli-interactive
export AZURE_TAGS=nginx
```

A címke exportálása után próbálja meg a `ansible` újra a parancsot:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Csak egy virtuális gép most már láthatók (amelynek címke értéke megegyezik egy exportált a **AZURE_TAGS** környezeti változó):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>A címkézett virtuális Géphez az Nginx telepítése
A címkék célja, hogy gyorsan és egyszerűen dolgozhat a virtuális gépek alcsoportok lehetővé. Például tegyük fel, az Nginx telepítéséhez csak a virtuális gépek, amelyhez hozzárendelte a címke `nginx`. A következő lépések bemutatják, milyen egyszerűen ez elvégzéséhez:

1. Hozzon létre egy fájlt (a tartalmaz a forgatókönyv) `nginx.yml` módon:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Az alábbi kód beszúrása az újonnan létrehozott `nginx.yml` fájlt:

    ```yml
    ---
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

1. A forgatókönyv futtatása után a következő kimenethez hasonló eredményeket jelenik meg:

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

## <a name="test-nginx-installation"></a>Az Nginx-telepítés tesztelése
Ez a szakasz azt mutatja be, tesztelheti, hogy az Nginx telepítve van-e a virtuális gép egyik módszer.

1. Használja a [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) parancs használatával kérje le az IP-címét a `ansible-inventory-test-vm1` virtuális gépet. A visszaadott érték (a virtuális gép IP-cím) majd a virtuális géphez való kapcsolódáshoz használt SSH-parancs paramétereként.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Csatlakozva a `ansible-inventory-test-vm1` virtuális gép futtatása az [nginx - v](https://nginx.org/en/docs/switches.html) parancsot annak meghatározásához, hogy az Nginx telepítve van-e.

    ```azurecli-interactive
    nginx -v
    ```

1. Futtatása után a `nginx -v` parancsot, láthatja, hogy az Nginx verzióját (a második sor), amely azt jelzi, hogy az Nginx telepítve van-e.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Nyomja le az  **&lt;Ctrl > D** billentyűzet kombináció leválasztja az SSH-munkamenetből.

1. Végez az előző lépések a `ansible-inventory-test-vm2` virtuális gép poskytne egy tájékoztató üzenet, amely azt jelzi, ahol megtekintheti az nginx-et (Ez azt jelenti, hogy még nincs telepítve ezen a ponton):

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
> [Alapszintű virtuális gép létrehozása az Azure-ban, az ansible segítségével](/azure/virtual-machines/linux/ansible-create-vm)
