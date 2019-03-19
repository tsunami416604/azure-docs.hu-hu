---
title: Az Azure Virtual Machines, fejlett automatikus méretezést
description: Használja a Resource Manager és a Virtuálisgép-méretezési csoportok több szabályokkal és profilokat, amely e-mailt, és hívja meg a webhook URL-címet, a skálázási műveletek.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080777"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Automatikus méretezés speciális konfigurálása Resource Manager-sablonok használata Virtuálisgép-méretezési csoportok
Horizontális le- és a Virtual Machine Scale Sets alapuló teljesítmény-mérőszám küszöbértékén, ismétlődő ütemezés szerint, vagy egy adott dátumot kibővített is. Skálázási műveletek értesítései e-mailt és webhookot is konfigurálhatja. Ez az útmutató bemutatja egy példa az összes ezeket az objektumokat a Virtuálisgép-méretezési Resource Manager-sablon használatával.

> [!NOTE]
> Bár ez az útmutató ismerteti a Virtuálisgép-méretezési csoportok, ugyanazokat az információkat vonatkozik-e az automatikus skálázás [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts) Egyszerű méretezési csoport és horizontális a Virtuálisgép-méretezési beállítás alapján egy egyszerű teljesítmény-mérőszám, például a CPU, tekintse meg a [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) és [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) dokumentumok
>
>

## <a name="walkthrough"></a>Útmutatás
Ez az útmutató használjuk [Azure erőforrás-kezelő](https://resources.azure.com/) konfigurálásához és a méretezési csoportok automatikus skálázási beállítás frissítése. Az Azure Resource Explorer egyszerű módja Resource Manager-sablonok segítségével az Azure-erőforrások kezeléséhez. Ha most ismerkedik az Azure Resource Explorer eszköz, olvassa el [Ez a bevezető](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Egy alapszintű automatikus méretezési beállítás új méretezési csoport üzembe helyezése. Ebben a cikkben az Azure katalógusából a rövid útmutató, amely rendelkezik egy Windows egy méretezési csoport egy alapszintű automatikus méretezési sablonnal. Linux méretezési csoportok ugyanúgy működnek.
2. A méretezési készlet létrehozása után keresse meg a méretezési csoport erőforrás az Azure Resource Explorerben. A következő Microsoft.Insights csomópont alatt láthatja.

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    A sablon végrehajtása hozott létre egy alapértelmezett automatikus skálázási beállítás neve **"autoscalewad"**. A jobb oldalon tekintheti meg az automatikus méretezési beállítás a teljes meghatározását. Ebben az esetben az alapértelmezett automatikus skálázási beállítás együttműködik a CPU-alapú % horizontális felskálázást és a horizontális leskálázási szabály.  

3. Most már hozzáadhat további profilok és szabályokat azt az ütemezést, vagy konkrét követelmények alapján. Három profilt az automatikus skálázási beállítás hozunk létre. Profilok és az automatikus skálázási szabályok kapcsolatban tekintse át [automatikus méretezés ajánlott eljárásairól](autoscale-best-practices.md).  

    | Profilok és szabályok | Leírás |
    |--- | --- |
    | **Profil** |**Teljesítmény/metrika alapján** |
    | Szabály |Service Bus Üzenetsorbeli üzenetek száma > x |
    | Szabály |Service Bus Üzenetsorbeli üzenetek száma < y |
    | Szabály |CPU: % > n |
    | Szabály |CPU: % < p |
    | **Profil** |**Hét napja reggel óra (nincs szabály)** |
    | **Profil** |**A termék indítási nap (nincs szabály)** |

4. Íme egy elméleti méretezési forgatókönyv, amely ebben az útmutatóban a használjuk.

   * **A terhelés alapján** – azt szeretném, hogy méretezzen horizontálisan vagy a saját méretezési set.* lévő üzemeltetett alkalmazás terhelése alapján
   * **Üzenet-várólista mérete** -egy Service Bus-üzenetsor bejövő üzenetekhez hozzáférek az alkalmazáshoz használni. Tudom a várólista üzenetek száma és százalékos processzorhasználatról, és konfigurálja a egy alapértelmezett profilt egy skálázási műveletet aktiválásához, ha bármelyik üzenetek száma vagy a Processzor eléri a küszöbértéket.\*
   * **Heti és napi** -szeretnék egy "Hét napja reggel Hours" nevű hetente ismétlődő "idő a nap"-alapú profilt. A korábbi adatok alapján, tudom jobb bizonyos számú Virtuálisgép-példányok ebben az időszakban az alkalmazásom által terhelés kezeléséhez.\*
   * **Speciális dátumok** -Hozzáadtam egy "Termék indítsa el a Day" profilt. Szeretném előre az adott dátumok az alkalmazásom kezelni a terhelés miatt marketing bejelentések és tárgyaljuk, egy új terméket az alkalmazás készen álljon.\*
   * *Az utolsó két profilt is lehet egyéb metrika alapján teljesítményszabályok rajtuk. Ebben az esetben kifejezetten nem rendelkezik ilyennel, és inkább az alapértelmezett teljesítmény-mérőszám támaszkodnia szabályok alapján. Szabályok megadása nem kötelező a ismétlődő és a dátum-alapú profilokhoz.*

     A profilok és a szabályok automatikus skálázási motor rangsorolási is bekerül az a [automatikus méretezés ajánlott eljárásai](autoscale-best-practices.md) cikk.
     Gyakori metrikák az automatikus skálázási listájáért tekintse meg a [gyakori metrikák az automatikus méretezéshez](autoscale-common-metrics.md)

5. Ellenőrizze, hogy van a **olvasási/írási** mód az erőforrás-kezelőben

    ![Autoscalewad, alapértelmezett automatikus skálázási beállítás](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Kattintson a Szerkesztés gombra. **Cserélje le** a "profilok" elem az automatikus skálázási beállítás a következő beállításokkal:

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
    Támogatott mezőket és azok értékeit: [az automatikus méretezés – REST API-dokumentáció](https://msdn.microsoft.com/library/azure/dn931928.aspx). Most már az automatikus skálázási beállítás tartalmazza a korábban ismertetett három profilt.

7. Végül tekintse meg az automatikus skálázási **értesítési** szakaszban. Automatikus skálázási értesítések lehetővé teszik, hogy ha egy kibővített, három dolgot, vagy a művelet sikeresen elindítva.
   - Értesítse a felügyeleti és az előfizetés társadminisztrátorai
   - E-mail-felhasználók
   - Indítson egy webhook hívása. Aktivált, ha ezt a webhookot az automatikus skálázási feltétel kapcsolatos metaadatokat továbbítja-e, és a méretezési csoport erőforrás. Az automatikus méretezés a webhook hasznos kapcsolatos további információkért lásd: [konfigurálása Webhook & E-mail értesítések az automatikus skálázás](autoscale-webhook-email.md).

   Adja hozzá a következő automatikus skálázási beállítás cseréje a **értesítési** elem, amelynek az értéke null

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

   Találati **Put** az erőforrás-kezelőben az automatikus skálázási beállítás frissítése gombra.

Egy Virtuálisgép-méretezési csoport több méretezési profil tartalmazza, és skálázhatja a értesítések beállítása az automatikus méretezési beállítások frissítése.

## <a name="next-steps"></a>További lépések
Ezek a hivatkozások segítségével további információ az automatikus skálázás.

[Virtuálisgép-méretezési csoportok automatikus skálázás hibáinak elhárítása](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Gyakori metrikák az automatikus méretezéshez](autoscale-common-metrics.md)

[Az Azure automatikus méretezés ajánlott eljárásai](autoscale-best-practices.md)

[Automatikus skálázás a PowerShell használatával kezelheti.](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Automatikus skálázási parancssori felület használata kezelheti](cli-samples.md#autoscale)

[Webhook és az automatikus skálázás E-mail-értesítések konfigurálása](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings) sablon hivatkozása
