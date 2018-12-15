---
title: Az Azure-ban az Ansible beállítása egy virtuálisgép-méretezési csoport automatikus méretezése
description: Ismerje meg, hogyan skálázhatja állítsa be az automatikus méretezési funkció az Azure-beli virtuálisgép-méretezési csoportot az Ansible használatával
ms.service: ansible
keywords: az ansible, azure, devops, bash, forgatókönyv, méretezhető, automatikus skálázási, virtuális gép, virtuálisgép-méretezési csoportot, vmss
author: tomarcher
manager: jeconnoc
ms.author: yuwzho, kyliel
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: c6678d6df3a695d3a0471e5779bc3af4b6ba6c84
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410915"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Az Azure-ban az Ansible beállítása egy virtuálisgép-méretezési csoport automatikus méretezése
Az Ansible-lel automatizálhatja az erőforrások üzembe helyezését és konfigurálását a környezetében. Az Ansible segítségével ugyanúgy felügyelheti a virtuálisgép-méretezési csoportokat (VMSS) az Azure-ban, ahogy azt bármely más Azure-erőforrással tenné. 

Méretezési csoport létrehozásakor meghatározza a futtatni kívánt virtuálisgép-példányok számát. Az alkalmazás igényeihez igazodva automatikusan növelheti vagy csökkentheti a virtuálisgép-példányok számát. Az automatikus méretezésnek köszönhetően lépést tarthat az ügyfeleik igényeivel és az alkalmazás teljes élettartama alatt reagálhat az alkalmazás teljesítményében bekövetkezett változásokra. Ebben a cikkben létrehoz egy automatikus skálázási beállítás, és társítsa azt egy meglévő virtuálisgép-méretezési csoportot. Az automatikus skálázási beállítás konfigurálhatja egy szabályt, amely horizontálisan felskálázhatja vagy leskálázhatja az, ahogyan szeretné.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- Egy meglévő Azure-beli virtuálisgép-méretezési csoportot. – Ha nem rendelkezik egy, [hozzon létre virtuálisgép-méretezési csoportok az Azure-ban Ansible használatával](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss).

> [!Note]
> Az oktatóanyagban szereplő következő forgatókönyvek futtatásához az Ansible 2.7-es verziója szükséges. 

## <a name="auto-scale-based-on-a-schedule"></a>Ütemezés alapján automatikus skálázás   
Az automatikus skálázás méretezési csoportban történő engedélyezéséhez először határozza meg az automatikus skálázási profilt. Ez a profil határozza meg a méretezési csoport alapértelmezett, minimális és maximális kapacitását. Ezekkel a korlátokkal szabályozhatja a költségeket a virtuálisgép-példányok folyamatos létrehozásának mellőzésével, valamint megtalálhatja az egyensúlyt az elfogadható teljesítményt és a horizontális leskálázási események során fennmaradó példányok minimális száma között. 

Méretezhető és méretezheti az ismétlődő ütemezés szerint vagy egy adott dátum szerint a Virtual Machine Scale Sets ki. Ez a szakasz bemutatja egy minta az Ansible-forgatókönyv, amely létrehoz egy automatikus skálázási beállítás, amely növeli a három, a méretezési csoportokban, 10:00 csendes-óceáni időzóna minden hétfőn, a Virtuálisgép-példányok számát. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
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

Mentse a forgatókönyvet, *vmss-auto-scale.yml*. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>Automatikus méretezés, teljesítmény-adatok alapján
Ha az alkalmazás növekvő igényeivel párhuzamosan, a méretezési csoportban lévő Virtuálisgép-példányok terhelése növekszik állítja be. Ha a megnövekedett terhelés állandó, nem csak pillanatnyi igény, akkor megadhatja, hogy az automatikus skálázási szabály növelje meg a virtuálisgép-példányok számát a méretezési csoportban. Ezen virtuálisgép-példányok létrehozását és az alkalmazások telepítését követően a méretezési csoport megkezdi a forgalom elosztását közöttük a terheléselosztón keresztül. Ön határozza meg, hogy milyen metrikákat kíván monitorozni – például a processzort vagy a lemezt, meddig kell az alkalmazás terhelésének elérnie egy megadott küszöbértéket, hány virtuálisgép-példányt kell hozzáadni a méretezési csoporthoz.

Méretezhető és méretezheti a ki a Virtual Machine Scale Sets alapuló teljesítmény-mérőszám küszöbértékén, ismétlődő ütemezés szerint, vagy egy adott dátumot. Ez a szakasz bemutatja egy minta az Ansible-forgatókönyv, amely ellenőrzi a számítási feladatok az elmúlt 10 perc alatt a 18:00 csendes-óceáni időzóna minden hétfőn, és elvégzi a horizontális felskálázást négy a méretezési csoportokban lévő Virtuálisgép-példányok számát, vagy az egyik példányhoz, a processzorhasználat alapján méretezéssel metrikák. 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
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

Mentse a forgatókönyvet, *vmss-automatikus – méretezési csoport – metrics.yml*. Az Ansible-forgatókönyv futtatásához használja az **ansible-playbook** parancsot a következőképpen:

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>Meglévő automatikus skálázási beállítások adatainak beolvasása
Bármilyen automatikus skálázási beállítás részletes keresztül kap a *azure_rm_autoscale_facts* modulját és a forgatókönyv az alábbiak szerint:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>Tiltsa le az automatikus méretezési beállításokkal
Az automatikus skálázási beállítás módosításával letilthatja `enabled: true` való `enabled: false`, vagy az automatikus méretezési beállításokkal a forgatókönyv a következő törlése:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Az Ansible minta forgatókönyv esetében a virtuálisgép-méretezési csoportok](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)