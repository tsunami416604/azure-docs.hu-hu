---
title: Oktatóanyag – Azure-erőforrások dinamikus leltározásának konfigurálása az Ansible használatával
description: Ismerje meg, hogyan kezelheti Azure-beli dinamikus leltározásait a Ansible használatával
keywords: Ansible, Azure, devops, bash, cloudshellben, dinamikus leltár
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: d2ebf202cfc9f94b28fc7a512e1fea452401aec6
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193599"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Oktatóanyag: Azure-erőforrások dinamikus leltározásának konfigurálása az Ansible használatával

A Ansible használatával különböző forrásokból (például az Azure-ból) származó leltári adatokat lehet lekérni *dinamikus leltárba*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két teszt virtuális gép konfigurálása. 
> * A virtuális gépek egyikének címkézése
> * Az Nginx telepítése a címkézett virtuális gépeken
> * Konfigurált Azure-erőforrásokat tartalmazó dinamikus leltár konfigurálása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>A teszt virtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Hozzon létre egy Azure-erőforráscsoportot az oktatóanyaghoz tartozó virtuális gépek tárolásához.

    > [!IMPORTANT]  
    > Az ebben a lépésben létrehozott Azure-erőforráscsoport olyan névvel kell rendelkeznie, amely teljesen kisbetűs. Ellenkező esetben a dinamikus leltár létrehozása sikertelen lesz.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Hozzon létre két linuxos virtuális gépet az Azure-ban az alábbi módszerek egyikével:

    - **Ansible** forgatókönyv – a cikk, [alapszintű virtuális gép létrehozása az Azure-ban a Ansible](./ansible-create-vm.md) bemutatja, hogyan hozhat létre virtuális gépet egy Ansible forgatókönyvből. Ha a virtuális gépek egyikét vagy mindkettőt egy vagy több virtuális gép definiálására használja, ügyeljen arra, hogy jelszó helyett az SSH-kapcsolatok legyenek használatban.

    - **Azure CLI** – a Cloud Shell a következő parancsok mindegyikét kiadja a két virtuális gép létrehozásához:

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

## <a name="tag-a-vm"></a>Virtuális gép címkézése

A címkék használatával a felhasználó által definiált kategóriák szerint [rendezheti az Azure-erőforrásokat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) . 

### <a name="using-ansible-version--28"></a>A Ansible < 2,8-es verziójának használata
Adja meg a következő az [Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) paranccsal a virtuális gép `ansible-inventory-test-vm1` a kulcs `nginx`ával:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>A Ansible > = 2,8-es verziójának használata
Adja meg a következő az [Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) paranccsal a virtuális gép `ansible-inventory-test-vm1` a kulcs `Ansible=nginx`ával:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Dinamikus leltár létrehozása

Ha a virtuális gépek definiálva vannak (és címkézve), ideje, hogy létrehozza a dinamikus leltárt.

### <a name="using-ansible-version--28"></a>A Ansible < 2,8-es verziójának használata

A Ansible egy [azure_rm.](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) -es nevű Python-szkriptet biztosít, amely az Azure-erőforrások dinamikus leltárát hozza létre. Az alábbi lépések végigvezetik a két Azure-beli Azure-beli virtuális gépekhez való kapcsolódáshoz szükséges `azure_rm.py` szkript használatával:

1. A `azure_rm.py` parancsfájl beolvasásához használja a GNU `wget` parancsot:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. A `chmod` parancs használatával módosíthatja a `azure_rm.py` parancsfájlhoz való hozzáférési engedélyeket. A következő parancs a `+x` paramétert használja a megadott fájl (`azure_rm.py`) futtatásának engedélyezéséhez:

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Az erőforráscsoporthoz való kapcsolódáshoz használja a [Ansible parancsot](https://docs.ansible.com/ansible/2.4/ansible.html) : 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. A csatlakozás után a következő kimenethez hasonló eredmények jelennek meg:

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

### <a name="ansible-version--28"></a>Ansible-verzió > = 2,8

A Ansible 2,8-es verziótól kezdődően a Ansible egy [Azure Dynamic-Inventory beépülő modult](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py)biztosít. A következő lépések végigvezetik a beépülő modul használatának lépésein:

1. A leltár beépülő modulhoz konfigurációs fájl szükséges. A konfigurációs fájlnak `azure_rm` kell végződnie, és `yml` vagy `yaml`kiterjesztéssel kell rendelkeznie. Ebben az oktatóanyagban például a következő forgatókönyvet mentse `myazure_rm.yml`ként:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. A következő parancs futtatásával pingelheti a virtuális gépeket az erőforráscsoporthoz:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Az előző parancs futtatásakor a következő hibaüzenetet kaphatja:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Ha a "gazdagép-kulcs ellenőrzése" hibaüzenetet kapja, adja hozzá a következő sort a Ansible konfigurációs fájlhoz. A Ansible konfigurációs fájlja `/etc/ansible/ansible.cfg` vagy `~/.ansible.cfg`található.

    ```bash
    host_key_checking = False
    ```

1. A forgatókönyv futtatásakor a következő kimenethez hasonló eredmények jelennek meg:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>VM-címke engedélyezése

### <a name="if-youre-using-ansible--28"></a>Ha a Ansible < 2,8-et használja,

- Miután beállított egy címkét, "engedélyeznie" kell a címkét. A címke engedélyezésének egyik módja, ha a címkét egy környezeti változóba exportálja `AZURE_TAGS` a `export` parancs használatával:

    ```azurecli-interactive
    export AZURE_TAGS=nginx
    ```
    
- Futtassa az alábbi parancsot:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Most már csak egy virtuális gép látható (amelynek a címkéje megegyezik a `AZURE_TAGS` környezeti változóba exportált értékkel):

    ```Output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Ha a Ansible > = 2,8

- A következő kimenet beszerzéséhez futtassa a `ansible-inventory -i myazure_rm.yml --graph` parancsot:

    ```Output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- A következő parancs futtatásával is tesztelheti az Nginx virtuális géphez való kapcsolódást:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Nginx beállítása a címkézett virtuális gépen

A címkék célja, hogy lehetővé tegye a virtuális gépek alcsoportjaival való gyors és egyszerű munkát. Tegyük fel például, hogy csak olyan virtuális gépeken szeretné telepíteni az Nginx-t, amelyekhez `nginx`címkét rendelt. Az alábbi lépések bemutatják, milyen egyszerű a megvalósítás:

1. Hozzon létre egy `nginx.yml`nevű fájlt:

   ```azurecli-interactive
   code nginx.yml
   ```

1. Másolja az alábbi kódmintát a szerkesztőbe:

    ```yml
        ---
        - name: Install and start Nginx on an Azure virtual machine
          hosts: all
          become: yes
          tasks:
          - name: install nginx
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

   - Ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible > = 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Az Nginx-Telepítés tesztelése

Ez a szakasz egy olyan technikát mutat be, amely azt teszteli, hogy az Nginx telepítve van-e a virtuális gépen.

1. A `ansible-inventory-test-vm1` virtuális gép IP-címének lekéréséhez használja az az [VM List-IP-Addresss](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) parancsot. A visszaadott érték (a virtuális gép IP-címe) az SSH-parancs paramétereként szolgál a virtuális géphez való kapcsolódáshoz.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Ha csatlakozik a `ansible-inventory-test-vm1` virtuális géphez, futtassa az [Nginx-v](https://nginx.org/en/docs/switches.html) parancsot annak megállapításához, hogy telepítve van-e az Nginx.

    ```azurecli-interactive
    nginx -v
    ```

1. Az `nginx -v` parancs futtatása után megjelenik az Nginx-verzió (második sor), amely azt jelzi, hogy az Nginx telepítve van.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Az SSH-munkamenet leválasztásához kattintson a `<Ctrl>D` billentyűzet kombinációra.

1. A `ansible-inventory-test-vm2` virtuális gép előző lépéseinek eredménye egy tájékoztató üzenet, amely azt jelzi, hogy honnan szerezhető be Nginx (amely azt jelenti, hogy ezen a ponton nincs telepítve):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Gyors útmutató: Linux rendszerű virtuális gépek konfigurálása az Azure-ban a Ansible használatával](./ansible-create-vm.md)