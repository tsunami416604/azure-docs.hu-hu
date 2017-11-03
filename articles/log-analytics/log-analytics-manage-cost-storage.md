---
title: "Az Azure Naplóelemzés az adatmegőrzés költségét kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan árképzési terv és az adatok megőrzési szabályzatának módosítása az Azure portálon Naplóelemzési munkaterületet."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>A Naplóelemzési munkaterület az adatok megőrzése költségét kezelése
Feliratkozás a Naplóelemzési, attól függően, hogy a terv választja, nincs a megadott korlát a csatlakoztatott adatforrások által létrehozott adatokat mennyi ideig tárolja a munkaterületen.  Ebben a cikkben ismertetett szempontok alapján, amelyek hatással lehetnek arra, hogy ezek az adatok megőrzése és ezt a határértéket konfigurálása különböző időszakokra vonatkozó költségek mutatja be.   

Naplóelemzési nagy mennyiségű adat a helyszíni adatforrások is felhasználhatnak, mert cloud és hibrid környezetekben, a költség, hogy az adatok tárolása egy ideig lehet jelentős attól függően, hogy a következő tényezőket:

* Rendszerek, infrastruktúra-összetevőihez, felhőalapú erőforrásokat, stb. a gyűjtött száma
* A forrás, például az üzenetsorok, a naplók, a események, a biztonsági adatok vagy a teljesítménymutatók által létrehozott adatok
* A források által generált adatmennyiséget 
* Felügyeleti megoldás engedélyezett száma, az adatforrás és gyűjtemény gyakorisága

> [!NOTE]
> Tekintse meg az egyes megoldások dokumentációjában mennyi adatot összegyűjti az becsült biztosít.   

Ha a *szabad* terv, adatok korlátozódik megőrzési hét nap.  Az a *önálló* vagy *fizetve* réteg, gyűjtött adatok érhető el az elmúlt 31 napra.  

Használatakor a *szabad* tervezze meg, ha folyamatosan túllépi az engedélyezett összegek, keresztül tudja módosítani a munkaterület egy fizetős csomagra meghaladja ezt a határt adatok gyűjtéséért felelős ügyfélfeladatot. 

> [!NOTE]
> Díjak vonatkoznak, ha úgy dönt, hogy válassza ki a fizetős réteg hosszabb megőrzési időtartamot. A terv típusának módosítása, tetszőleges időpontban, és az árakkal kapcsolatos további információt, lásd: [díjszabása](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Módosítsa az Adatmegőrzés időtartama 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Kattintson a **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**.
3. A Naplóelemzési előfizetések ablaktáblában jelölje ki a munkaterület módosítani a listából.
4. A munkaterület lapján kattintson a **megőrzési** a bal oldali ablaktáblán.
5. A munkaterület megőrzési ablaktábla a csúszkával növelheti vagy csökkentheti a napok számát, és kattintson a **mentése**.  Ha a *szabad* réteget, nem tudják módosítani az Adatmegőrzés időtartama, és frissítenie kell a fizetős csomagra ezzel a beállítással szabályozása érdekében.<br><br> ![Munkaterület adatmegőrzési beállításának módosítása](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Következő lépések  

Annak eldöntéséhez, hogy mennyi adatot gyűjt, mely források és a használat és a költséghatékonyság kezeléséhez küldött adatok különböző típusú üzenetet küld, lásd: [Naplóelemzési a használati adatok elemzése](log-analytics-usage.md)