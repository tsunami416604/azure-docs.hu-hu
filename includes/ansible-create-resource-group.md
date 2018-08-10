---
author: tomarcher
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fa1f7fe0b4b70aae4f9165197d5d1463df1f2e3b
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40026962"
---
1. A Cloud Shellben hozzon létre egy fájlt `rg.yml`.

    ```bash
    vi rg.yml
    ```

1. Adja meg a Beszúrás mód kiválasztásával a **I** kulcsot.

1. Illessze be a következő kódot a szerkesztőbe:

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

1. Kilépés insert módból a **Esc** kulcsot.

1. Mentse a fájlt, és zárja be a vi szerkesztőt a következő parancs beírásával:

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