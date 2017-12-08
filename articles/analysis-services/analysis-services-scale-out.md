---
title: "Az Azure Analysis Services kibővített |} Microsoft Docs"
description: "A kibővített Azure Analysis Services-kiszolgálók replikálása"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: a97f9648efef7f07659110d720c200dcd0a241a9
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services kibővített

Kibővített, az ügyfél lekérdezések elosztható több *replikák lekérdezése* a lekérdezés készletbe csökkentése során a lekérdezési munkaterhelések válaszidejét. A lekérdezés készletből feldolgozása, biztosítva az ügyfél lekérdezések nem messzemenően érinti feldolgozási műveletek is külön. Kibővített Azure-portálon, vagy az Analysis Services REST API használatával konfigurálható.

## <a name="how-it-works"></a>Működés

Egy tipikus kiszolgáló telepítése esetén egy kiszolgáló mind feldolgozási és lekérdezés kiszolgálóként szolgál. Ha a kiszolgálón modellekkel ügyfél lekérdezések száma meghaladja a lekérdezés feldolgozása egységek (QPU) a kiszolgáló terv, vagy a modell feldolgozása a lekérdezési munkaterhelések egy időben történik, a teljesítmény csökkenhet. 

Kibővített hozhat létre a lekérdezés-készletben legfeljebb hét további lekérdezési replikával (nyolc összesen, beleértve a kiszolgáló). Méretezheti a kritikus időpontokban QPU kielégítése érdekében lekérdezés replikák száma, és bármikor elkülönítheti a feldolgozási kiszolgáló, a lekérdezés készletből. 

A lekérdezés-készletben található lekérdezés replikák száma, függetlenül feldolgozási terheléshez nem osztják lekérdezés replikák között. A feldolgozó kiszolgáló egyetlen kiszolgáló szolgál. Lekérdezés replikák ellenőrizhető, hogy csak a lekérdezések a lekérdezés készletben található összes replikát szinkronizálja modellekkel. 

Feldolgozási műveletek befejezése után a feldolgozási kiszolgáló és a lekérdezés a replika kiszolgálók közötti szinkronizálás kell elvégezni. Feldolgozási műveletek automatizálása esetén fontos, hogy konfigurálja a szinkronizálási művelet feldolgozási műveletek sikeres befejezését követően. Szinkronizálás végezheti el manuálisan a portálon, vagy a PowerShell vagy a REST API használatával.

> [!NOTE]
> Kibővített kiszolgálók a standard tarifacsomag érhető el. Minden egyes lekérdezés replika számlázása a kiszolgálóval azonos ütemben történik.

> [!NOTE]
> Kibővített nem növeli a kiszolgáló rendelkezésre álló memória mennyisége. Memória növelése érdekében frissítenie kell a tervet.

## <a name="monitor-qpu-usage"></a>A figyelő QPU kihasználtsága

 Ha kibővített megállapításához a kiszolgálóra szükség, a kiszolgáló figyelése az Azure-portálon metrikák használatával. Ha a QPU rendszeresen maxes ki, az azt jelenti, a modellekkel lekérdezések száma meghaladja a terv QPU korlátozást. A lekérdezés készlet feladat várólista hossza metrika is növekszik, ha a lekérdezés szál-készlet várólistája lévő lekérdezések száma meghaladja a rendelkezésre álló QPU. További tudnivalókért lásd: [server metrikát](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Kibővített konfigurálása

### <a name="in-azure-portal"></a>Azure-portálon

1. Kattintson a portál **kibővített**. A csúszka segítségével válassza ki a lekérdezés a replika kiszolgálók számát. A replikák mellett dönt van mellett a meglévő kiszolgálóról.

2. A **a lekérdező készletből a feldolgozási kiszolgáló külön**, jelölje be a feldolgozási kiszolgáló kizárása lekérdezés kiszolgálók igen.

   ![Kibővített csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kattintson a **mentése** az új lekérdezés replika kiszolgálók. 

Az elsődleges kiszolgálón táblázatos modellek szinkronizálva legyenek a replikakiszolgálót. Szinkronizálás befejeződése után a lekérdezés készlet kezdődik, bejövő lekérdezések a replika kiszolgálók között terjeszti. 


## <a name="synchronization"></a>Szinkronizálás 

Amikor új lekérdezés replikákat, Azure Analysis Services automatikusan replikálja a modellek összes replika között. Manuális szinkronizálást a portálon vagy REST API használatával is elvégezheti. Amikor dolgozza fel a modellek, végre kell hajtania a szinkronizálás, a lekérdezés replikák között szinkronizálva.

### <a name="in-azure-portal"></a>Azure-portálon

A **áttekintése** > modell > **szinkronizálás modell**.

![Kibővített csúszka](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>A modell szinkronizálása   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapotának beolvasása  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
PowerShell, a szinkronizálási futtatásához [frissítése a legújabb](https://github.com/Azure/azure-powershell/releases) 5.01 vagy magasabb AzureRM modul. Használjon [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Kapcsolatok

A kiszolgáló – áttekintés lapon nincsenek két kiszolgáló nevét. Ha még kiszolgáló kibővített még nincs konfigurálva, akkor mindkét kiszolgáló neve azonos működik. Ha a kiszolgáló kibővített megfelelően konfigurált, szüksége lesz a kapcsolat típusától függően a megfelelő kiszolgáló nevének megadásához. 

Például a Power BI Desktop, az Excel és az egyéni alkalmazások használatát a végfelhasználói ügyfélkapcsolatok **kiszolgálónév**. 

Az SSMS, az SSDT és PowerShell kapcsolati karakterláncokat, Azure-függvény alkalmazások és az AMO-használata **felügyeleti kiszolgáló neve**. A felügyeleti kiszolgáló neve tartalmaz egy speciális `:rw` (írásra) minősítő jelöl. Minden feldolgozási műveletek fordulhat elő, a felügyeleti kiszolgálón.

![Kiszolgáló neve](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Kapcsolódó információk

[A figyelő kiszolgálói metrikák](analysis-services-monitor.md)   
[Az Azure Analysis Services kezelése](analysis-services-manage.md) 

