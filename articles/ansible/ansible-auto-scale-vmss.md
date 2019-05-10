---
title: Oktatóanyag – automatikus skálázási virtuálisgép-méretezési csoportok az Azure-ban használatával Ansible |} A Microsoft Docs
description: Ismerje meg, hogyan méretezheti a virtuális gép méretezési csoportok az automatikus méretezési funkció az Azure-ban az Ansible segítségével
keywords: az ansible, azure, devops, bash, forgatókönyv, méretezhető, automatikus skálázási, virtuális gép, virtuálisgép-méretezési csoportot, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 4f2cd66b7460fc6fe48cb55f45bf4bc309ae054c
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231281"
---
# <a name="tutorial-autoscale-virtual-machine-scale-sets-in-azure-using-ansible"></a>Oktatóanyag: Automatikus skálázási virtuálisgép-méretezési csoportok az Azure-ban az Ansible használatával

[!INCLUDE [ansible-27-note.md](../../includes/ansible-27-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

A Virtuálisgép-példányok száma automatikusan beállítja a szolgáltatás neve [automatikus skálázási](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview). Automatikus skálázás előnye, hogy csökkenti a kezelési terhelést figyelik és optimalizálják az alkalmazás teljesítményét. Az automatikus méretezés igény szerint vagy egy meghatározott ütemezés szerint konfigurálható. Az Ansible-lel is megadhat az automatikus méretezési szabályok, amelyek meghatározzák az elfogadható teljesítményt egy pozitív felhasználói élmény.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Automatikus skálázási profil meghatározása
> * Automatikus méretezés, ismétlődő ütemezésen alapuló
> * Az alkalmazás teljesítmény-alapú automatikus méretezés
> * Automatikus skálázási beállítási információk lekérése 
> * Az automatikus skálázási beállítás letiltása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)] 
[!INCLUDE [ansible-prereqs-vm-scale-set.md](../../includes/ansible-prereqs-vm-scale-set.md)]

## <a name="autoscale-based-on-a-schedule"></a>Az automatikus méretezés az ütemezés alapján

Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezek a korlátok lehetővé teszik a költségek Virtuálisgép-példányok nem folyamatosan létrehozásával, és elosztja a elfogadható teljesítményt, mely egy horizontális leskálázási esemény is példányok minimális száma. 

Az Ansible segítségével a méretezési csoportok egy adott dátumot vagy ismétlődő ütemezés szerint.

Ebben a szakaszban a forgatókönyv kód növeli a Virtuálisgép-példányok három, minden hétfőn 10:00-kor.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="autoscale-based-on-performance-data"></a>Automatikus méretezés, teljesítmény-adatok alapján

Ha az alkalmazás növekvő igényeivel párhuzamosan, a méretezési csoportban lévő Virtuálisgép-példányok terhelése növekszik állítja be. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Az Ansible segítségével szabályozhatja, hogy milyen metrikákat kíván monitorozni – például CPU-használat, a lemezhasználat és az alkalmazás-betöltési ideje. Skálázhatja a és a méretezési csoport horizontális felskálázási beállítása alapján teljesítmény mérőszám küszöbértékén ismétlődő ütemezés szerint vagy egy adott dátum szerint. 

A forgatókönyv kód ebben a szakaszban az előző 10 perc minden hétfőn 18:00-kor ellenőrzi a CPU-terhelés. 

A Processzorhasználat százalékos teljesítménymetrikák alapján, a forgatókönyv hajtja végre az alábbi műveletek egyikét:

- Méretezhető négy Virtuálisgép-példányok mennyiségét
- Egy Virtuálisgép-példányok számának méretezhető

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-autoscale-settings-information"></a>Automatikus skálázási beállítási információk lekérése 

A forgatókönyv kód ebben a szakaszban a `azure_rm_autoscale_facts` modul automatikus skálázási beállítás adatai olvashatók be.

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
ansible-playbook vmss-auto-scale-get-settings.yml
```

## <a name="disable-autoscale-settings"></a>Tiltsa le az automatikus méretezési beállításokkal

Tiltsa le az automatikus méretezési beállítások két módon lehet. Egyik módja az, hogy módosítsa a `enabled` kulcsának `true` való `false`. A második lehetőség, hogy törli a beállítást.

Ebben a szakaszban a forgatókönyv kód automatikus skálázási beállítás törlése. 

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

A forgatókönyv segítségével futtassa a `ansible-playbook` parancsot:

```bash
vmss-auto-scale-delete-setting.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Oktatóanyag: Egyéni rendszerkép frissítése az Azure virtuálisgép-méretezési csoport beállítja az Ansible-lel](./ansible-vmss-update-image.md)