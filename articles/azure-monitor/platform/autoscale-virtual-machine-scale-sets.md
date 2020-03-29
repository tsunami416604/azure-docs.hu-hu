---
title: Speciális automatikus skálázás azure-beli virtuális gépek használatával
description: Erőforrás-kezelő és virtuálisgép-méretezési készletek több szabályok és profilok, amelyek e-mail küldése és hívás webhook URL-címeket méretezési műveleteket.
ms.topic: conceptual
ms.date: 02/22/2016
ms.subservice: autoscale
ms.openlocfilehash: e22806ff94ce2eb830bb6918bfc7f80e5ad3ba0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75364220"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Speciális automatikus skálázási konfiguráció a Virtuálisgép-méretezési csoportok Erőforrás-kezelő sablonjaival
A virtuálisgép-méretezési csoportokban teljesítménymetrikális küszöbértékek, ismétlődő ütemezés vagy egy adott dátum alapján skálázható és kibővített. A levelezési és webhook-értesítések méretezési műveletekhez is konfigurálhatók. Ez a forgatókönyv egy példát mutat be az összes objektum konfigurálása egy Erőforrás-kezelő sablon használatával egy virtuális gép méretezési csoport.

> [!NOTE]
> Bár ez a forgatókönyv ismerteti a virtuális gép méretezési készleteinek lépéseit, ugyanazok az információk vonatkoznak az automatikus [skálázásfelhő-szolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az App Services - Web Apps](https://azure.microsoft.com/services/app-service/web/)és az API Management [szolgáltatásokra:](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) A virtuális gép méretezési készletén egy egyszerű teljesítménymérő-készleten, például a CPU-n alapuló egyszerű méretezési/kitöltési beállításért tekintse meg a [Linux-](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) és [Windows-dokumentumokat.](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)
>
>

## <a name="walkthrough"></a>Útmutatás
Ebben a forgatókönyvben az [Azure Resource Explorer](https://resources.azure.com/) használatával konfiguráljuk és frissítjük a méretezési csoport automatikus méretezési beállítását. Az Azure Resource Explorer segítségével egyszerűen kezelheti az Azure-erőforrásokat a Resource Manager-sablonokon keresztül. Ha most jön az Azure Resource Explorer eszköz, olvassa el [ezt a bevezetést](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Telepítsen egy új méretezési készletet egy alapszintű automatikus skálázási beállítással. Ez a cikk az Azure QuickStart Gallery, amely egy Windows-méretezési készlet egy alapszintű automatikus skálázási sablont használja. A Linux méretezési készletek ugyanúgy működnek.
2. A méretezési csoport létrehozása után keresse meg a méretezési csoport erőforrás az Azure Resource Explorer. A microsoft.insights-csomópontban a következők et láthatja.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A sablon végrehajtása alapértelmezett automatikus skálázási beállítást hozott létre **"autoscalewad"** névvel. A jobb oldalon megtekintheti az automatikus skálázási beállítás teljes definícióját. Ebben az esetben az alapértelmezett automatikus skálázási beállítás egy CPU-alapú horizontális felskálázási és horizontális felskálázási szabályt tartalmazza.  

3. Mostantól további profilokat és szabályokat adhat hozzá az ütemezés vagy a konkrét követelmények alapján. Három profillal automatikus skálázási beállítást hozunk létre. Az automatikus skálázásban szereplő profilok és szabályok megértéséhez tekintse át [az automatikus skálázás gyakorlati tanácsait.](autoscale-best-practices.md)  

    | Profilok & szabályok | Leírás |
    |--- | --- |
    | **Profil** |**Teljesítmény/metrikus** |
    | Szabály |A szolgáltatásbusz üzeneteinek száma > x |
    | Szabály |A szolgáltatásbusz üzeneteinek száma < y |
    | Szabály |CPU%> n |
    | Szabály |CPU< p |
    | **Profil** |**Hétköznap reggeli órákban (nincs enek szabály)** |
    | **Profil** |**A termék megjelenésének napja (nincsenek szabályok)** |

4. Íme egy hipotetikus skálázási forgatókönyv, amelyet ehhez a forgatókönyvhöz használunk.

   * **Terhelés alapú** - szeretnék horizontális felskálázás vagy a terhelés alapján az én alkalmazás házigazdája az én scale set.*
   * **Üzenetsor mérete** – Az alkalmazásomba érkező üzenetekhez szolgáltatásbusz-várólistát használok. A várólista üzenetszámát és a CPU%-t használom, és egy alapértelmezett profilt konfigurálok egy méretezési művelet elindításához, ha az üzenetek száma vagy a PROCESSZOR eléri a küszöbértéket.\*
   * **A hét ideje és a nap** - Szeretnék egy heti ismétlődő "napszak" alapú profil úgynevezett "Hétköznap Morning Hours". A korábbi adatok alapján, tudom, hogy jobb, ha bizonyos számú virtuálisgép-példányok kezelésére az alkalmazás terhelése ez idő alatt.\*
   * **Különleges dátumok** - Hozzáadtam egy "Termékbevezetés napja" profilt. Azt tervezem, előre konkrét időpontokban, így az alkalmazás készen áll, hogy kezelje a terhelés miatt marketing bejelentések és amikor egy új terméket az alkalmazásba.\*
   * *Az utolsó két profil ban más teljesítménymérőmetrika-alapú szabályok is szerepelhetnek. Ebben az esetben úgy döntöttem, hogy nem rendelkezem ilyenvel, és ehelyett az alapértelmezett teljesítménymérő-metrika alapú szabályokra támaszkodom. Az ismétlődő és dátumalapú profilok esetében a szabályok nem kötelezőek.*

     Automatikus skálázási motor rangsorolása a profilok és szabályok is rögzíti az [automatikus skálázás ajánlott eljárások](autoscale-best-practices.md) cikkben.
     Az automatikus skálázás gyakori metrikáinak listáját az [automatikus skálázás gyakori metrikáiban](autoscale-common-metrics.md) olvassa el.

5. Ellenőrizze, hogy **olvasási/írási** módban van-e az Erőforrás-kezelőben

    ![Automatikus skálázás, alapértelmezett automatikus skálázási beállítás](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kattintson a Szerkesztés gombra. **Cserélje le** a "profilok" elemet az automatikus skálázási beállításban a következő konfigurációra:

    ![Profilok](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
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
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    A támogatott mezőket és azok értékeit az [Autoscale REST API dokumentációjában](https://msdn.microsoft.com/library/azure/dn931928.aspx)találja. Most az automatikus skálázási beállítás tartalmazza a korábban ismertetett három profilt.

7. Végül tekintse meg az automatikus **skálázásértesítési** szakaszt. Az automatikus skálázási értesítések lehetővé teszik, hogy három dolgot, ha egy horizontális felskálázás vagy működés közben sikeresen aktiválódik.
   - Az előfizetés rendszergazdájának és társadminisztrátorainak értesítése
   - Felhasználók e-mail ben
   - Webhook-hívás aktiválása. Amikor a webhook, ez a webhook metaadatokat küld az automatikus skálázási feltétel és a méretezési csoport erőforrás. Az automatikus skálázási webhook hasznos terheléséről a [Webhook & e-mail értesítések konfigurálása automatikus skálázáshoz](autoscale-webhook-email.md)című témakörben olvashat bővebben.

   Adja hozzá a következőket az automatikus skálázás beállításhoz, amely az **értesítési** elemet váltja fel, amelynek értéke null

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Az automatikus skálázási beállítás frissítéséhez nyomja meg az **Elhelyezés** gombot az Erőforrás-kezelőben.

Frissítette az automatikus skálázási beállítást egy virtuálisgép-méretezési készleten, hogy több méretezési profilt és méretezési értesítéseket tartalmazzon.

## <a name="next-steps"></a>Következő lépések
Ezekkel a hivatkozásokkal többet is megtudhat az automatikus skálázásról.

[TroubleShoot automatikus skálázás virtuálisgép-méretezési készletekkel](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Az automatikus skálázás közös metrikája](autoscale-common-metrics.md)

[Gyakorlati tanácsok az Azure automatikus skálázásához](autoscale-best-practices.md)

[Automatikus skálázás kezelése a PowerShell használatával](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Automatikus skálázás kezelése a CLI használatával](cli-samples.md#autoscale)

[Webhook & e-mail értesítések konfigurálása automatikus skálázáshoz](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) sablon – referencia
