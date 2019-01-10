---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fe995535cd42571ad96f192883e48f4b6ea1eb0e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160403"
---
1. Hozzon létre egy `rg.yml` nevű fájlt a Cloud Shellben.

    ```bash
    vi rg.yml
    ```

1. Az **I** billentyű lenyomásával lépjen beszúrási módba.

1. Másolja az alábbi kódot a szerkesztőbe:

   ```yaml
   ---
   - hosts: localhost
     connection: local
     tasks:
       - name: Create resource group
         azure_rm_resourcegroup:
           name: ansible-rg
           location: eastus
         register: rg
       - debug:
           var: rg
   ```

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. A forgatókönyv futtatása `rg.yml`:

   ```bash
   ansible-playbook rg.yml
   ```

Az ansible parancs futtatásának eredményeit az alábbi kimenet hasonlóan kell kinéznie:

```output
PLAY [localhost] *********************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [localhost]

TASK [Create resource group] *********************************************************************
changed: [localhost]

TASK [debug] *************************************************************************************
ok: [localhost] => {
    "rg": {
        "changed": true,
        "contains_resources": false,
        "failed": false,
        "state": {
            "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/ansible-rg",
            "location": "eastus",
            "name": "ansible-rg",
            "provisioning_state": "Succeeded",
            "tags": null
        }
    }
}

PLAY RECAP ***************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```