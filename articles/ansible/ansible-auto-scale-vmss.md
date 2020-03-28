---
title: Oktatóanyag – Automatikus skálázású virtuálisgép-méretezési készletek az Azure-ban az Ansible használatával
description: Megtudhatja, hogyan méretezhető keleszthető k e-műveletek automatikus skálázási funkcióval az Ansible segítségével az Azure-ban
keywords: ansible, azúr, devops, bash, ötletekbõl, skála, automatikus skálázás, virtuális gép, virtuális gép méretezési készlet, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74156813"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: Automatikus skálázású virtuálisgép-méretezési csoportok az Azure-ban az Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

A virtuálisgép-példányok számának automatikus beállítását [automatikus skálázásnak](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)nevezzük. Az automatikus skálázás előnye, hogy csökkenti a felügyeleti terhelést az alkalmazás teljesítményének figyeléséhez és optimalizálásához. Az automatikus skálázás konfigurálható az igényekre adott válaszként vagy egy meghatározott ütemezés szerint. Az Ansible használatával megadhatja az automatikus skálázási szabályokat, amelyek meghatározzák a pozitív felhasználói élmény elfogadható teljesítményét.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Automatikus skálázási profil meghatározása
> * Automatikus méretezés ismétlődő ütemezés alapján
> * Automatikus méretezés az alkalmazás teljesítménye alapján
> * Automatikus skálázási beállítások adatainak beolvasása 
> * Automatikus skálázási beállítás letiltása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Automatikus méretezés ütemezés alapján

Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezek a korlátok lehetővé teszik a költségek szabályozását azáltal, hogy nem folyamatosan hozza létre a virtuálisgép-példányokat, és egyensúlyt teremt az elfogadható teljesítmény és a horizontális felskálázási eseményben maradó példányok minimális számával. 

Az Ansible lehetővé teszi a méretezési csoportok méretezését egy adott dátumra vagy ismétlődő ütemezésre.

Ebben a szakaszban a forgatókönyv-kód növeli a virtuálisgép-példányok számát három ra 10:00 minden hétfőn.

Mentse a következő forgatókönyvet `vmss-auto-scale.yml` néven:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks: 
    - name: Create autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatikus skálázás teljesítményadatok alapján

Ha az alkalmazás igénye nő, a méretezési csoportokban a virtuálisgép-példányok terhelése nő. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Az Ansible lehetővé teszi, hogy szabályozhatja, hogy milyen metrikákat figyeljen, például a PROCESSZOR-használatot, a lemezhasználatot és az alkalmazás betöltési idejét. Skálázási csoportokban skálázhatja és skálázhatja a teljesítménymetrika küszöbértékei, egy ismétlődő ütemezés vagy egy adott dátum alapján. 

Ebben a szakaszban a forgatókönyv-kód ellenőrzi a CPU számítási az előző 10 perc 18:00 minden hétfőn. 

A PROCESSZOR százalékos mérőszámai alapján a forgatókönyv az alábbi műveletek egyikét végzi:

- A virtuálisgép-példányok számát négyre méretezi
- A virtuálisgép-példányok számát egyre méretezi

Mentse a következő forgatókönyvet `vmss-auto-scale-metrics.yml` néven:

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myScaleSet
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get scale set resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create autoscaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Automatikus skálázási beállítások adatainak beszereznie 

Ebben a szakaszban a `azure_rm_autoscale_facts` forgatókönyv-kód a modul segítségével lekéri az automatikus skálázási beállítás részleteit.

Mentse a következő forgatókönyvet `vmss-auto-scale-get-settings.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve autoscale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Automatikus skálázási beállítások letiltása

Az automatikus skálázási beállítások kétféleképpen tiltható le. Ennek egyik módja, `enabled` hogy `true` `false`a kulcsot a -ra változtassa. A második módszer a beállítás törlése.

Az ebben a szakaszban található forgatókönyvkód törli az automatikus skálázási beállítást. 

Mentse a következő forgatókönyvet `vmss-auto-scale-delete-setting.yml` néven:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete autoscaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

Futtassa a `ansible-playbook` forgatókönyvet a következő paranccsal:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Az Azure virtuálisgép-méretezési készletek egyéni lemezképének frissítése az Ansible használatával](./ansible-vmss-update-image.md)