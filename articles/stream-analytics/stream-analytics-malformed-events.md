---
title: Az Azure Stream Analytics helytelen formátumú bemeneti eseményeket hibaelhárítása |} Microsoft Docs
description: Honnan tudhatom, hogy melyik esemény szerepel a bemeneti adatok okozza a problémát a Stream Analytics-feladatok
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/19/2018
ms.author: sngun
ms.openlocfilehash: 2b4f82bae20c3cb398848a89715bfdc1316e1ba1
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
ms.locfileid: "29766397"
---
# <a name="troubleshoot-for-malformed-input-events"></a>Helytelen formátumú bemeneti események hibaelhárítása

Ha a bemeneti adatfolyam a Stream Analytics-feladat nem megfelelő alakú üzenetek tartalmaz, szerializálási problémákat okoz. Nem megfelelő alakú üzenetek közé tartoznak például a hiányzó zárójel helytelen szerializációs JSON-objektumból, vagy helytelen időformátum stamp A Stream Analytics-feladat helytelenül formázott üzenetet kap, amikor elutasítja azokat az üzenetet, és értesíti a felhasználót egy figyelmeztetést. Figyelmeztetés szimbólum jelenik meg a **bemenetek** csempe a Stream Analytics-feladat:

![Bemenetek csempe](media/stream-analytics-malformed-events/inputs_tile.png)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

1. Keresse meg a bemeneti csempe, és kattintson ide a figyelmeztetéseket.
2. A bemeneti részletek csempe információkhoz juthat a problémáról a figyelmeztetések megjelenítése. Az alábbiakban látható egy példa figyelmeztető üzenet, a figyelmeztető üzenetet jeleníti meg, a partíció, az eltolás és a sorozatszámok nem megfelelően formázott JSON-adatok esetén. 

   ![Figyelmeztető üzenet eltolású](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Ahhoz, hogy a JSON-adatokat, amelyek formátuma nem megfelelő, futtassa a következő kódrészletet. A kódblokk a partíció azonosítója, eltolásnál, és kiírja az adatok beolvasása. Kaphat a teljes minta a [minták GitHub-tárházban](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Ha olvasni az adatokat, elemezheti és javítsa ki a szerializálási formátum.

```csharp
static void PrintMessages(string partitionId, long offset, int numberOfEvents)
        {
            EventHubReceiver receiver;
            
            try
            {
                foreach (var e in receiver.Receive(numberOfEvents, TimeSpan.FromMinutes(1)))
                {
                    Console.WriteLine(Encoding.UTF8.GetString(e.GetBytes()));
                    Console.WriteLine("----");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.ToString());
                return;
            }

            receiver.Close();
        }
```
## <a name="next-steps"></a>További lépések

* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
