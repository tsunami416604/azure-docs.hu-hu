---
title: Oktatóanyag – Az Azure-erőforrások dinamikus készleteinek konfigurálása az Ansible használatával
description: Ismerje meg, hogyan kezelheti az Ansible-t az Azure dinamikus készleteinek kezelésére
keywords: ansible, azúr, devops, bash, cloudshell, dinamikus leltár
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247852"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Oktatóanyag: Konfigurálja az Azure-erőforrások dinamikus készleteit az Ansible használatával

Az Ansible segítségével különböző forrásokból (például a felhőforrásokból, például az Azure-ból) származó készletadatokat lehet egy *dinamikus készletbe*bevonni. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfiguráljon két teszt virtuális gépet. 
> * Az egyik virtuális gép megjelölése
> * Telepítse a Nginx-et a címkézett virtuális gépekre
> * Konfiguráljon egy dinamikus készletet, amely tartalmazza a konfigurált Azure-erőforrásokat

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>A tesztvirtuális gépek létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)

1. Nyissa meg a [Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Hozzon létre egy Azure-erőforráscsoportot az oktatóanyag virtuális gépei tárolására.

    > [!IMPORTANT]  
    > Az ebben a lépésben létrehozott Azure-erőforráscsoportnak teljesen kisbetűs névvel kell rendelkeznie. Ellenkező esetben a dinamikus készlet létrehozása sikertelen lesz.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Hozzon létre két Linuxos virtuális gépet az Azure-ban az alábbi technikák egyikével:

    - **Ansible forgatókönyv** – A cikk, [hozzon létre egy alapvető virtuális gép az Azure-ban Ansible](./ansible-create-vm.md) bemutatja, hogyan hozhat létre egy virtuális gépet egy Ansible forgatókönyv. Ha egy forgatókönyv segítségével határozza meg az egyik vagy mindkét virtuális gép, győződjön meg arról, hogy az SSH-kapcsolat használata jelszó helyett.

    - **Azure CLI** – Adja ki a következő parancsokat a Cloud Shellben a két virtuális gép létrehozásához:

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

Címkék [használatával rendszerezheti az Azure-erőforrásokat](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) a felhasználó által definiált kategóriák szerint. 

### <a name="using-ansible-version--28"></a>Az Ansible 2.8 < használata
Írja be a következő [az erőforráscímke](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) parancsot a virtuális gép `ansible-inventory-test-vm1` kulcsával `nginx`való megjelölni:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Az Ansible verzió használata >= 2,8
Írja be a következő [az erőforráscímke](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) parancsot a virtuális gép `ansible-inventory-test-vm1` kulcsával `Ansible=nginx`való megjelölni:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Dinamikus készlet létrehozása

Miután a virtuális gépek definiálva (és címkézett), itt az ideje, hogy létrehoz a dinamikus leltár.

### <a name="using-ansible-version--28"></a>Az Ansible 2.8 < használata

Az Ansible egy [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) nevű Python-parancsfájlt biztosít, amely dinamikus leltárt készít az Azure-erőforrásokról. A következő lépések végigvezetik a `azure_rm.py` parancsfájl használatával a két teszt Azure virtuális géphez való csatlakozáshoz:

1. A parancsfájl `wget` beolvasásához `azure_rm.py` használja a GNU parancsot:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. A `chmod` paranccsal módosíthatja a `azure_rm.py` parancsfájl hozzáférési engedélyeit. A következő parancs `+x` a paramétert használja a megadott fájl`azure_rm.py`( ) végrehajtásának (futtatásához) történő engedélyezéséhez:

    ```python
    chmod +x azure_rm.py
    ```

1. Az [ansible paranccsal](https://docs.ansible.com/ansible/2.4/ansible.html) csatlakozhat az erőforráscsoporthoz: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. A csatlakozás után a következő kimenethez hasonló eredmények jelennek meg:

    ```output
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

### <a name="ansible-version--28"></a>Ansible változat >= 2,8

Az Ansible 2.8-as tól kezdve az Ansible egy Dinamikus Készlet Beépülő modult biztosít az [Azure dinamikus készletbeépülő moduljával.](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py) A következő lépések végigvezetik a beépülő modul használatán:

1. A készletbeépülő modulhoz konfigurációs fájl szükséges. A konfigurációs fájlnak be `azure_rm` kell `yml` érnie, és meg kell hosszabbítania a vagy `yaml`a . Ehhez a bemutató példához mentse `myazure_rm.yml`a következő forgatókönyvet:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Futtassa a következő parancsot az erőforráscsoportban lévő virtuális gépek pingeléséhez:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Az előző parancs futtatásakor a következő hibaüzenet jelenhet meg:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Ha a "gazdakulcs-ellenőrzés" hibaüzenet jelenik meg, adja hozzá a következő sort az Ansible konfigurációs fájlhoz. Az Ansible konfigurációs `/etc/ansible/ansible.cfg` fájl `~/.ansible.cfg`a vagyban található.

    ```bash
    host_key_checking = False
    ```

1. A forgatókönyv futtatásakor a következő kimenethez hasonló eredmények jelennek meg:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>A virtuális gép címke engedélyezése

### <a name="if-youre-using-ansible--28"></a>Ha ansible < 2.8-at használ,

- Miután beállítottegy címkét, "engedélyeznie kell" a címkét. A címke engedélyezésének egyik módja a címke exportálása egy környezeti változóba `AZURE_TAGS` a `export` parancs segítségével:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Futtassa az alábbi parancsot:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Most már csak egy virtuális gép jelenik meg (amelynek a címkéje megegyezik a `AZURE_TAGS` környezeti változóba exportált értékkel):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Ha Ansible >= 2,8

- Futtassa `ansible-inventory -i myazure_rm.yml --graph` a parancsot a következő kimenet levételéhez:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- A nginx-i virtuális géphez való csatlakozás teszteléséhez a következő parancsot is futtathatja:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>A Nginx beállítása a címkézett virtuális gépen

A címkék célja, hogy lehetővé tegye a virtuális gépek alcsoportjaival való gyors és egyszerű munkát. Tegyük fel például, hogy a Nginx-et csak olyan virtuális gépekre `nginx`szeretné telepíteni, amelyekhez hozzárendelte a címkét. A következő lépések azt mutatják be, hogy ez milyen egyszerű:

1. Hozzon létre `nginx.yml`egy nevű fájlt:

   ```console
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

1. Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

   - Ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. A forgatókönyv futtatása után a következő eredményekhez hasonló kimenet jelenik meg:

    ```output
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

## <a name="test-nginx-installation"></a>Nginx telepítésének tesztelése

Ez a szakasz egy olyan technikát mutat be, amely a Nginx virtuális gépen való telepítését mutatja be.

1. Használja az [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) parancsot `ansible-inventory-test-vm1` a virtuális gép IP-címének lekéréséhez. A visszaadott érték (a virtuális gép IP-címe) ezután az SSH parancs paramétereként szolgál a virtuális géphez való csatlakozáshoz.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Miközben a `ansible-inventory-test-vm1` virtuális géphez csatlakozik, futtassa a [nginx -v](https://nginx.org/en/docs/switches.html) parancsot annak megállapítására, hogy a Nginx telepítve van-e.

    ```console
    nginx -v
    ```

1. Miután futtatta a `nginx -v` parancsot, megjelenik a Nginx verzió (második sor), amely azt jelzi, hogy a Nginx telepítve van.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Az `<Ctrl>D` SSH-munkamenet leválasztásához kattintson a billentyűzet kombinációra.

1. A `ansible-inventory-test-vm2` virtuális gép előző lépései egy tájékoztató üzenetet eredményeznek, amely jelzi, hogy hol szerezhető be a Nginx (ami azt jelenti, hogy ezen a ponton nincs telepítve):

    ```output
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
> [Rövid útmutató: Linuxos virtuális gépek konfigurálása az Azure-ban az Ansible használatával](./ansible-create-vm.md)