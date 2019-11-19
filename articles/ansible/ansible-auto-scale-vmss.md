---
title: Oktatóanyag – virtuálisgép-méretezési csoportok kiosztása az Azure-ban a Ansible használatával
description: Ismerje meg, hogyan méretezheti a Ansible a virtuálisgép-méretezési csoportok méretezésére az Azure-ban az autoscale használatával
keywords: Ansible, Azure, devops, bash, ötletekbõl, skála, automata méretezés, virtuális gép, virtuálisgép-méretezési csoport, vmss
ms.topic: tutorial
ms.date: 04/30/2019
ms.openlocfilehash: fb8d2a4bfca32be4575ca8f11018e5cab17cd9a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156813"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: virtuálisgép-méretezési csoportok autoskálázása az Azure-ban a Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

A virtuálisgép-példányok számának automatikus [méretezése](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)automatikusan történik. Az autoscale előnye, hogy csökkenti a felügyeleti terhelést az alkalmazás teljesítményének monitorozásához és optimalizálásához. Az autoskálázás konfigurálható igény szerint vagy meghatározott ütemezés szerint. A Ansible használatával megadhatja az autoskálázási szabályokat, amelyek meghatározzák a pozitív felhasználói élmény elfogadható teljesítményét.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Automatikus skálázási profil meghatározása
> * Az autoskálázás ismétlődő ütemterv alapján
> * Az alkalmazások teljesítményén alapuló autoskálázás
> * Az automatikusan méretezhető beállítások adatainak beolvasása 
> * Egy autoskálázási beállítás letiltása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Ütemterv alapján történő autoskálázás

Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezek a korlátok lehetővé teszik a költségeket a virtuálisgép-példányok folyamatos létrehozásával, valamint az elfogadható teljesítmény elosztásával, amely egy méretezési eseményen maradó példányok minimális száma. 

A Ansible lehetővé teszi a méretezési csoportok méretezését egy adott dátumra vagy ismétlődő ütemtervre.

Az ebben a szakaszban szereplő forgatókönyv-kód a virtuálisgép-példányok számát a hétfő 10:00-kor növeli.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Az autoskálázás a teljesítményadatok alapján

Ha az alkalmazás igénye növekszik, a méretezési csoportokban lévő virtuálisgép-példányok terhelése növekszik. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. A Ansible lehetővé teszi a figyelni kívánt mérőszámok, például a CPU-használat, a lemezhasználat és az alkalmazás-betöltési idő szabályozását. A méretezési csoportok méretezése és méretezése a teljesítmény mérőszámának küszöbértékei, ismétlődő ütemterv vagy egy adott dátum alapján végezhető el. 

Az ebben a szakaszban szereplő forgatókönyv-kód az előző 10 percben, 18:00 minden hétfőn ellenőrzi a processzor munkaterhelését. 

A CPU-százalékos mérőszámok alapján a forgatókönyv a következő műveletek egyikét hajtja végre:

- A VM-példányok számának kiméretezése négyre
- A virtuálisgép-példányok számának méretezése egy értékre

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Az autoskálázási beállítások adatainak beolvasása 

Az ebben a szakaszban található forgatókönyv-kód a `azure_rm_autoscale_facts` modul használatával kéri le az autoskálázási beállítás részleteit.

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Az autoskálázási beállítások letiltása

Az autoscale-beállítások letiltása kétféleképpen lehetséges. Az egyik módszer a `enabled` kulcs módosítása `true`ról `false`ra. A második módszer a beállítás törlése.

Az ebben a szakaszban található forgatókönyv-kód törli az autoskálázási beállítást. 

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

Futtassa a forgatókönyvet a `ansible-playbook` parancs használatával:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Azure-beli virtuálisgép-méretezési csoportok egyéni rendszerképének frissítése a Ansible használatával](./ansible-vmss-update-image.md)