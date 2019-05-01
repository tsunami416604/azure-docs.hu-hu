---
title: Oktatóanyag – az Ansible-lel az Azure-erőforrások dinamikus leltárának konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Ansible az Azure dinamikus leltárak kezelése
keywords: az ansible, azure, devops, bash, cloud Shell, dinamikus készlet
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdd78747505664c0824fffbd41a692818000193f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728792"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Oktatóanyag: Az Ansible-lel az Azure-erőforrások dinamikus leltárának konfigurálása

Az Ansible segítségével Hardverleltár-információk lekérése a különböző forrásokból (például az Azure felhőalapú forrásokból is beleértve) egy *dinamikus készlet*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Két teszt virtuális gép konfigurálása. 
> * A virtuális gépek egyik címke
> * A címkézett virtuális gépeket az Nginx telepítése
> * A dinamikus készlet, amely tartalmazza a konfigurált Azure-erőforrások konfigurálása

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>A teszt virtuális gépek létrehozása

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

## <a name="tag-a-vm"></a>Virtuális gép címkézése

Is [címkék használata az Azure-erőforrások rendszerezéséhez](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) felhasználó által definiált kategóriák szerint. 

Írja be a következő [az erőforrás-címke](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) parancsot a virtuális gép címkézése `ansible-inventory-test-vm1` kulccsal `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```
## <a name="generate-a-dynamic-inventory"></a>Dinamikus készítése

Ha létrehozta a virtuális gépek meghatározott (és a címkézett), a dinamikus készlet létrehozásához ideje.

### <a name="using-ansible-version--28"></a>Az Ansible-lel verzió < 2.8

Az Ansible nevű Python-szkriptet biztosít [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) , amely létrehoz egy dinamikus készlet az Azure-erőforrások. A következő lépések végigvezetik használatával a `azure_rm.py` szeretne csatlakozni a két parancsfájlt tesztelése az Azure virtual machines:

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

### <a name="ansible-version--28"></a>Az Ansible verzió > = 2.8

Az Ansible 2.8-as verziótól kezdődően az Ansible biztosít egy [Azure dinamikus-készlet beépülő modul](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). A következő lépések végigvezetik a beépülő modul használatával:

1. A készlet beépülő modul konfigurációs fájl szükséges. A konfigurációs fájl végződése `azure_rm` és a egy kiterjesztése `yml` vagy `yaml`. Az oktatóanyag példájában a Mentés, a következő forgatókönyv `myazure_rm.yml`:

    ```yml
    plugin: azure_rm
    include_vm_resource_groups:
    - ansible-inventory-test-rg
    auth_source: auto
    ```

1. Futtassa a következő parancsot az erőforráscsoportban lévő pingelni a virtuális gépek:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Az előző parancs futtatásakor sikerült a következő hibaüzenetet kapja:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Ha a "állomáskulcs ellenőrzés" hibaüzenetet kapja, adja hozzá a következő sort az Ansible-konfigurációs fájl. Az Ansible-konfigurációs fájl `/etc/ansible/ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Amikor futtatja a forgatókönyvet, a következő kimenethez hasonló eredmény jelenik meg:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>A virtuális gép címke engedélyezése
Miután beállította a címke, kell "Engedélyezés" címkét. Ahhoz, hogy egy címkét egy úgy, hogy a címke exportálása egy környezeti változóhoz `AZURE_TAGS` keresztül a `export` parancsot:

```azurecli-interactive
export AZURE_TAGS=nginx
```

- Az Ansible használata < 2.8-as, futtassa a következő parancsot:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

- Az Ansible használata > = 2.8-as, futtassa a következő parancsot:
  
    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

Csak egy virtuális gép most már láthatók (amelynek címke értéke megegyezik egy exportált a `AZURE_TAGS` környezeti változó):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>A címkézett virtuális Géphez az Nginx telepítése

A címkék célja, hogy gyorsan és egyszerűen dolgozhat a virtuális gépek alcsoportok lehetővé. Például tegyük fel, az Nginx telepítéséhez csak a virtuális gépek, amelyhez hozzárendelte a címke `nginx`. A következő lépések bemutatják, milyen egyszerűen ez elvégzéséhez:

1. Hozzon létre egy fájlt `nginx.yml`:

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
        apt: pkg=nginx state=installed
        notify:
        - start nginx

      handlers:
        - name: start nginx
          service: name=nginx state=started
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

1. A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

   - Az Ansible < 2.8:

    ```bash
    ansible-playbook -i azure_rm.py nginx.yml
    ```

   - Az Ansible > = 2.8:

    ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml
    ```

1. A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

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

1. Kattintson a `<Ctrl>D` billentyűzet kombináció leválasztja az SSH-munkamenetből.

1. Ezzel az előző lépések a `ansible-inventory-test-vm2` virtuális gép poskytne egy tájékoztató üzenet, amely azt jelzi, ahol megtekintheti az nginx-et (Ez azt jelenti, hogy még nincs telepítve ezen a ponton):

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
> [Rövid útmutató: Az Azure-ban az Ansible konfigurálása a Linux rendszerű virtuális gépek](/azure/virtual-machines/linux/ansible-create-vm)