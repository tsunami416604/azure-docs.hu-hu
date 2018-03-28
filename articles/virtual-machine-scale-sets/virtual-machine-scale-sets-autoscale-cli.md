---
title: Automatikus skálázási virtuálisgép-méretezési beállítja az Azure parancssori felülettel |} Microsoft Docs
description: Az Azure CLI 2.0 beállítja a virtuálisgép-méretezési automatikus skálázási szabályok létrehozása
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2018
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>A virtuálisgép-méretezési beállítása az Azure CLI 2.0 automatikus méretezése
A méretezési csoport létrehozásakor megadhatja a futtatni kívánt Virtuálisgép-példányok száma. Az alkalmazás igény szerinti változásával automatikusan növeli vagy csökkenti a Virtuálisgép-példányok számát. Automatikus skálázás teszi lehetővé teszi keresletének tartani, vagy az alkalmazás életciklusa során alkalmazás teljesítmény változásait.

Ez a cikk bemutatja, hogyan automatikus skálázási szabályok létrehozását az Azure CLI 2.0-s verziójával, amely a méretezési csoportban lévő Virtuálisgép-példányok teljesítményének figyeléséhez. Ezek a szabályok automatikus skálázás növelje, vagy csökkentse a metrikák válaszul Virtuálisgép-példányok száma. Ezeket a lépéseket is végre tudja [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) vagy a [Azure-portálon](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Előfeltételek
Automatikus skálázási szabályok létrehozásához szükséges egy meglévő virtuális gép méretezési készlet. Létrehozhat egy méretezési állítható be a [Azure-portálon](virtual-machine-scale-sets-create-portal.md), [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), vagy [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md).

Könnyebb az automatikus skálázási szabályok létrehozása, adja meg a bizonyos változókat a méretezési készlet. Az alábbi példa meghatározza a méretezési készletben elnevezett változói *myScaleSet* az erőforráscsoport neve *myResourceGroup* és a *eastus* régióban. Az előfizetés-azonosítója nem kapunk [az fiók megjelenítése](/cli/azure/account#az_account_show). Ha a fiókjához társított több előfizetéssel rendelkezik, csak az első előfizetés ad vissza. A nevek és előfizetés-azonosító beállítása a következőképpen:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Automatikus skálázási profilok
Automatikus skálázási szabályok (JavaScript Object Notation) JSON-t az Azure CLI 2.0 üzemelnek. A teljes JSON határozza meg, és az automatikus skálázási szabályok telepíti a cikk későbbi részében található. 

Az automatikus skálázási profil kezdetét határozza meg az alapértelmezett, minimális és maximális méretezési kapacitás. Az alábbi példa beállítja az alapértelmezett, és minimális, kapacitásának *2* VM példányok, és legfeljebb *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Hozzon létre egy szabályt, amely automatikusan horizontális felskálázása
Az alkalmazás igény szerinti egyenes arányban növekszik, ha a Virtuálisgép-példány a skála terhelését növeli állítsa be. Ha ez a megnövekedett terhelés egységes, ahelyett, hogy csak egy rövid igény szerinti, konfigurálhatja az automatikus skálázási szabályok a méretezési csoportban lévő Virtuálisgép-példányok számának növeléséhez. Ezek a Virtuálisgép-példányok jönnek létre, és az alkalmazások vannak telepítve, a méretezési hozzákezd terjesztése azokra a terheléselosztó forgalmát. Milyen metrikák figyeléséhez, mint a CPU vagy lemez, mennyi ideig alkalmazásterhelés meg kell felelnie a megadott küszöbértéket, és hány Virtuálisgép-példányok a skála hozzáadása set szabályozhatja.

Hozzon létre egy szabályt, amely növeli a terjedő skálán állítható be, ha a átlagos CPU-terhelést érték nagyobb, mint 70 % 10 perc alatt a Virtuálisgép-példányok számát. A szabály akkor váltja ki, ha Virtuálisgép-példányok száma 20 %-kal növekszik. A méretezési készlet egy Virtuálisgép-példányok kis mennyiségű, akkor állítsa a `type` való *ChangeCount* és növelje a `value` által *1* vagy *2* példányok. Virtuálisgép-példányok számos, a 10 % vagy 20 %-os növekedést méretezési készlet Virtuálisgép-példányok lehet jobban megfelelő.

A következő paramétert kell használni ehhez a szabályhoz:

| Paraméter         | Magyarázat                                                                                                         | Érték           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A teljesítmény metrika figyelésére és a skála alkalmazni az alábbi műveletek beállítani.                                                   | Százalékos processzorhasználat  |
| *timeGrain*       | Milyen gyakran a metrikák gyűjtése történt elemzéséhez.                                                                   | 1 perc        |
| *timeAggregation* | Határozza meg, hogy az összegyűjtött metrikák elemzések céljából összesíti kell-e.                                                | Átlag         |
| *timeWindow*      | Az időtartam a metrika és a küszöbértéket az összehasonlítás előtt figyeli.                                   | 10 perc      |
| *operator*        | Hasonlítsa össze a metrikaadatokat szemben a küszöbérték operátor.                                                     | Nagyobb, mint    |
| *Küszöbérték*       | Az érték, amely az automatikus skálázási szabály elindítani egy műveletet okoz.                                                      | 70%             |
| *direction*       | Meghatározza, hogy a méretezési kell méretezni felfelé vagy lefelé, amikor a szabály vonatkozik.                                             | Növelés        |
| *Típusa*            | Azt jelzi, hogy a Virtuálisgép-példányok száma a által százalékát módosítani kell-e.                                 | Készültségi módosítása  |
| *value*           | Hány Virtuálisgép-példányok kell méretezhető felfelé vagy lefelé a szabály.                                            | 20              |
| *cooldown*        | Mennyi ideig várjon a szabály alkalmazza újra, így az automatikus skálázási műveletek érvénybe lépéséhez idő kell. | 5 perc       |

Az alábbi példában a szabály a Virtuálisgép-példányok száma horizontális határozza meg. A *metricResourceUri* korábban már meg van adva az előfizetés-azonosító, erőforráscsoport-név és skálázási nevének beállítása a változókat használ:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Hozzon létre egy szabályt, amely az automatikus méretezése
Egy este vagy hétvégi az alkalmazás igény szerinti csökkenhet. Ha egy meghatározott időtartamra vonatkozóan ez csökkentheti a betöltés egységes, konfigurálhatja az automatikus skálázási szabályok segítségével csökkentheti a méretezési csoportban lévő Virtuálisgép-példányok számát. A skálázási művelet csökkenti a költségeket a méretezési készletben a futtatásakor csak az aktuális igény kielégítésére szükséges példányok futtatásához.

Hozzon létre egy másik szabály, amely csökken a Virtuálisgép-példány terjedő skálán állítható be, ha a átlagos CPU-terhelést, majd alá süllyed 30 % 10 perc alatt. Az alábbi példában a szabály a Virtuálisgép-példányok száma horizontális határozza meg. A *metricResourceUri* korábban már meg van adva az előfizetés-azonosító, erőforráscsoport-név és skálázási nevének beállítása a változókat használ:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>A méretezési automatikus skálázási szabályok alkalmazása
Az utolsó lépés, hogy az automatikus skálázási profil és a szabályok vonatkoznak a méretezési készlet. A skála elindulhatott automatikus méretezése a bejövő vagy kimenő az alkalmazás igény szerint. Az automatikus skálázási profil alkalmazása [létrehozása az automatikus skálázás-figyelőbeállítások](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) az alábbiak szerint. A teljes JSON profil és a korábbi szakaszokban leírtaknak szabályokat használja.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Méretezési csoportban lévő példányok száma figyelése
A szám és a Virtuálisgép-példányok állapotának megtekintéséhez példányok listájának megtekintéséhez állítsa a méretezési [az vmss-példányokat](/cli/azure/vmss#az_vmss_list_instances). Az állapot azt jelzi, ha a Virtuálisgép-példány a méretezési készletben automatikusan méretezi a kimenő, vagy van megszüntetés, mint a skála automatikusan méretezi a kiépítés-e. Az alábbi példa megtekinti a virtuális gép példány állapotát a méretezési készletben elnevezett *myScaleSet* az erőforráscsoport neve *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Ütemezett automatikus skálázás
Az előző példákban automatikusan átméretezi a bejövő vagy kimenő meg például a CPU-használat alapvető gazdagép metrikák méretezési. Ütemezés alapján automatikus skálázás szabályokat is létrehozhat. Ütemezésalapú szabályok lehetővé teszik automatikusan horizontális felskálázás előre várható növekedése alkalmazás igény szerinti core időpontokat a munkaidőhöz, például Virtuálisgép-példányok száma, és majd automatikus méretezése a példányok száma, amelyek várhatóan kisebb egyszerre igény szerint, például a hétvégi.

Ütemezés alapján automatikus skálázás szabályokat használ, hozzon létre egy JSON-profilt, amely meghatározza egy fix kezdési és befejezési idő ablakban fussanak Virtuálisgép-példányok száma. Az alábbi példa meghatározza egy szabályt, amely horizontálisan *10* a példányok *9* munkanapokon munkahelyi naponta (hétfőtől péntekig).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

A méretezési során az este, hozzon létre egy másik szabály, amely meghatározza a Virtuálisgép-példány és egy megfelelő kezdési ideje kevesebb.

A következő teljes példa kibővítési és a skála szabályokat határozza meg, majd alkalmazza az automatikus skálázási profil [létrehozása az automatikus skálázás-figyelőbeállítások](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Ez a példa felülírja az előző példákban létrehozott metrika-alapú automatikus skálázási szabályok. A *metricResourceUri* korábban már meg van adva az előfizetés-azonosító, erőforráscsoport-név és skálázási nevének beállítása a változókat használ:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használható az automatikus skálázási szabályok horizontálisan méretezhető és növeléséhez vagy csökkentéséhez a *szám* állítsa be a skálázási Virtuálisgép-példánya. Is méretezheti függőleges növelheti vagy csökkentheti a Virtuálisgép-példány *mérete*. További információkért lásd: [virtuálisgép-méretezési csoportok függőleges skálázva](virtual-machine-scale-sets-vertical-scale-reprovision.md).

A Virtuálisgép-példányok kezeléséről további információért lásd: [kezelése virtuálisgép-méretezési beállítja az Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Az automatikus skálázási szabályok eseményindító riasztást generáljon, lásd: [automatikus skálázási műveletek segítségével e-mailek és a webhook riasztási értesítések küldése az Azure-figyelő](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Emellett [használata naplókat küldeni e-mailek és a webhook riasztási értesítések az Azure-figyelő](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
