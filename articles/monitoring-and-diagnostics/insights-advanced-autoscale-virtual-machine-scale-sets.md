---
title: Speciális használó Azure virtuális gépek automatikus skálázás |} Microsoft Docs
description: Erőforrás-kezelő és a Virtuálisgép-méretezési készlet használ, több szabályok és profilok, amely e-mailek küldése, és hívja meg a webhook URL-címet a skálázási művelet.
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 7e3576e2-4a2b-4736-b5ae-98c4689cdd2b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2016
ms.author: ancav
ms.openlocfilehash: c1ac5c4c44386fc05e3ee87ccdbbc4f652a94a1c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Speciális automatikus skálázás konfigurációját a Resource Manager-sablonok segítségével a Virtuálisgép-méretezési készlet
Méretezés és a virtuálisgép-méretezési csoportok alapján a teljesítmény-küszöbértékeinek metrika, ismétlődő ütemezés szerint, vagy egy adott dátumot kibővített is. A skálázási műveletek értesítések e-mailek és a webhook is konfigurálhatja. Ez a bemutató ismerteti egy példa a Resource Manager-sablon használatával egy Virtuálisgép-méretezési csoportban lévő összes objektum konfigurálására.

> [!NOTE]
> Amíg ez a bemutató lépéseit ismerteti a Virtuálisgép-méretezési készlet, ugyanazokat az információkat vonatkozik-e automatikus skálázás [Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services/), és [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/).
> Egy egyszerű teljesítmény metrika például CPU-alapú egy egyszerű méretezési be- / kimeneti beállítást egy Virtuálisgép-méretezési csoportban, tekintse meg a [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) és [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) dokumentumok
>
>

## <a name="walkthrough"></a>Útmutatás
Ebben a bemutatóban használjuk [Azure erőforrás-kezelő](https://resources.azure.com/) konfigurálásához és az automatikus skálázási beállítás skálázási készletének frissítéséhez. Az Azure erőforrás-kezelő egyszerű módja Resource Manager-sablonok segítségével az Azure erőforrások kezeléséhez. Ha most ismerkedik az Azure erőforrás-kezelő eszköz, olvassa el a [a bevezetés](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Telepítsen egy új méretezési készletben egy alapszintű automatikus skálázási beállítás. Ez a cikk az használja az Azure katalógusából gyors üzembe helyezés, amely rendelkezik egy Windows méretezési alapvető automatikus skálázás sablonnal. Linux-méretezési csoportok azonos módon működnek.
2. A méretezési készlet létrehozása után nyissa meg a méretezési készlet erőforrást az Azure erőforrás-kezelővel. A következő Microsoft.Insights csomópont alatt megjelenik.

    ![Az Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

    A sablon végrehajtási hozott létre egy alapértelmezett automatikus skálázási beállítás nevű **"autoscalewad"**. A jobb oldalon megtekintheti az automatikus skálázási beállítás teljes definíciója. Ebben az esetben az alapértelmezett automatikus skálázási beállítás CPU-alapú % kibővített és skálázási szabály tartalmaz.  

3. Hozzáadhat további profilok és az ütemezést vagy a meghatározott követelmények alapján. Az automatikus skálázási beállítás nem létrehozni három profilok. Ha szeretné megtudni, profilok és az automatikus skálázás szabályai, tekintse át a [automatikus skálázás gyakorlati tanácsok](insights-autoscale-best-practices.md).  

    | Profilok és szabályok | Leírás |
    |--- | --- |
    | **Profil** |**Teljesítmény/metrika alapján** |
    | Szabály |Service Bus várólista-üzenetek száma > x |
    | Szabály |Service Bus várólista-üzenetek száma < y |
    | Szabály |CPU % > n |
    | Szabály |CPU % < p |
    | **Profil** |**Milyen napra esik reggel óra (nincs szabály)** |
    | **Profil** |**A termék indítási nap (nincs szabály)** |

4. Íme egy elméleti méretezési forgatókönyv, hogy ez az útmutató használjuk.

    * **A terhelés alapján** -szeretnék vertikális fel- vagy a skála set.* tárolt saját-alkalmazás terhelése alapján
    * **Üzenet-várólista mérete** – a bejövő üzenetek alkalmazás használom a Service Bus-üzenetsorba. Szeretnék használni a várólista üzenet számán és CPU % és tartozó skálázási műveletek indítás, ha a threshold.* találatok üzenetek száma vagy a CPU, vagy az alapértelmezett profil konfigurálása
    * **Heti és napi** -szeretném "Hétköznap reggel Hours" nevű hetente ismétlődő "időt a napi" alapú profil. A korábbi adatok alapján, tudható, hogy jobb Virtuálisgép-példányok a time.* során az alkalmazás terhelés kezelése érdekében bizonyos számú
    * **Adott dátumok** -hozzáadott "Termék indítása Day" profil. I tervezzen előre az adott dátumok, az alkalmazás készen álljon a terhelés miatt marketing közlemények, és egy új terméken be azt a application.* kezelésére
    * *Az utolsó két profilok is más metrika alapján teljesítményszabályok rajtuk. Ebben az esetben I úgy döntött, hogy nem rendelkezik ilyennel, és helyette az alapértelmezett teljesítmény metrika támaszkodni szabályok alapján. Szabályok megadása nem kötelező, az ismétlődő és a dátum-alapú profilokhoz.*

    A profilok és a szabályok automatikus skálázás motor rangsorolási is bekerül az a [automatikus skálázás gyakorlati tanácsok](insights-autoscale-best-practices.md) cikk.
    Az automatikus skálázás közös metrikáját listája, [közös metrikáját automatikus skálázás](insights-autoscale-common-metrics.md)

5. Győződjön meg arról, hogy sikerült megnyitni a **olvasási/írási** mód az erőforrás-kezelőben

    ![Autoscalewad, alapértelmezett automatikus skálázási beállítás](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Kattintson a Szerkesztés gombra. **Cserélje le** az "profilok" elem, az automatikus skálázási beállítás a következő beállításokkal:

    ![Profilok](./media/insights-advanced-autoscale-vmss/profiles.png)

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
    Támogatott mezőket és azok értékeit: [automatikus skálázás REST API-dokumentáció](https://msdn.microsoft.com/library/azure/dn931928.aspx). Az automatikus skálázási beállítás most a korábban ismertetett három profilokat tartalmazza.

7. Végül tekintse meg az automatikus skálázás **értesítési** szakasz. Automatikus skálázás értesítések lehetővé teszik a három műveleteket, ha egy kibővített, vagy a művelet sikeresen elindítva.
   - A rendszergazda és az előfizetés társadminisztrátoroknak értesítése
   - E-mail-felhasználók
   - Indítás, webhook hívásakor. Következik be, amikor a webhook elküldi a metaadatokat az automatikus skálázás feltételről, és a méretezési erőforrás. Az automatikus skálázás webhook hasznos kapcsolatos további információkért lásd: [konfigurálja Webhook & automatikus skálázási az értesítő e-mailek](insights-autoscale-to-webhook-email.md).

   Adja hozzá a következőket az automatikus skálázási beállítás cseréje a **értesítési** elem, amelynek az értéke null

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

Az automatikus skálázási beállítás egy Virtuálisgép-méretezési több skálázási profil tartalmazza, a méretezés értesítések beállítása a frissítése befejeződött.

## <a name="next-steps"></a>További lépések
Ezek a hivatkozások segítségével további információ az automatikus skálázást.

[Virtuálisgép-méretezési csoportok automatikusan skálázva hibaelhárítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Az automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)

[Ajánlott eljárások az Azure automatikus skálázás](insights-autoscale-best-practices.md)

[Kezelheti az automatikus skálázás a PowerShell használatával](insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Parancssori felület használatával automatikus skálázás kezelése](insights-cli-samples.md#autoscale)

[Webhook & értesítő e-mailek az automatikus skálázás konfigurálása](insights-autoscale-to-webhook-email.md)
