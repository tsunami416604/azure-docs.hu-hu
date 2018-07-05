---
title: Az Azure Analysis Services horizontális felskálázás |} A Microsoft Docs
description: Horizontális felskálázás az Azure Analysis Services-kiszolgálók replikálása
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4cb7b165311f57fadd63770646907ddfc0378844
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445020"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázás

Horizontális felskálázás, az ügyfél lekérdezések elosztható több *lekérdezésreplikára* az adott lekérdezési készletben feladatok esetén a válaszidők csökkentése. A lekérdezési készletből feldolgozásához, biztosítva az ügyfél lekérdezések nem negatívan érinti feldolgozási műveletek is egymástól. Horizontális felskálázás az Azure Portalon vagy az Analysis Services REST API használatával konfigurálhatja.

## <a name="how-it-works"></a>Működés

Egy tipikus server központi telepítés esetén egy kiszolgáló feldolgozó kiszolgáló és a lekérdezés-kiszolgálóként is szolgál. Ha az ügyfél-lekérdezéseket futtassanak a kiszolgálón modellek száma meghaladja a lekérdezés feldolgozása egységek (QPU) csomagot a server, vagy a modell feldolgozása befejeződik, a lekérdezési számítási feladatok egy időben, a teljesítmény csökkenhet. 

Horizontális felskálázás létrehozhat egy lekérdezési készlet legfeljebb hét további lekérdezésreplikát (összesen nyolcat a kiszolgálóval együtt). Méretezheti a kritikus fontosságú időpontokban QPU figyelembevételével lekérdezési replikák száma és a lekérdezési készletből egy feldolgozó kiszolgáló elkülönítheti a tetszőleges időpontban. Minden lekérdezési replikák és a kiszolgáló ugyanabban a régióban jönnek létre.

Rendelkezik az adott lekérdezési készletben lekérdezési replikák számától függetlenül tranzakciófeldolgozási nem oszlanak meg lekérdezési replikák között. A feldolgozó kiszolgáló egyetlen kiszolgáló szolgál. Lekérdezési replikák szolgálják, csak a modellek között a lekérdezési készlet minden egyes replikát szinkronizálja a lekérdezéseket. 

Feldolgozási műveletek befejezése után a feldolgozó kiszolgáló és a lekérdezés replika kiszolgálók közötti szinkronizálás kell elvégezni. Feldolgozási műveletek automatizálása, esetén fontos, hogy konfigurálja a szinkronizálási művelet feldolgozási műveletek sikeres befejezése után. Szinkronizálás manuálisan hajtható végre a portálon, vagy a PowerShell vagy REST API használatával.

> [!NOTE]
> Horizontális felskálázás kiszolgálók a standard tarifacsomagban érhető el. Minden egyes lekérdezés replika költsége megegyezik a kiszolgáló számoljuk fel.

> [!NOTE]
> Horizontális felskálázás nem növeli a kiszolgáló rendelkezésre álló memória mennyisége. Növelje a memória, váltson magasabb szintű csomagra kell.

## <a name="region-limits"></a>Régió korlátok

Az a régió, a kiszolgáló konfigurálható lekérdezési replikák száma korlátozott. Az alábbi korlátozások érvényesek:

|Régió  |Replikák maximális száma  |
|---------|---------|
|USA 2. keleti régiója    |    7     |
|USA nyugati középső régiója     |    7     |
|Nyugat-Európa     |    7     |
|USA nyugati régiója     |     7    |
|USA középső régiója     |     3    |
|Délkelet-Ázsia    |     3    |
|Minden más régiókban  |   1    |



## <a name="monitor-qpu-usage"></a>QPU-használat monitorozása

 Annak megállapításához, ha horizontális felskálázás az a kiszolgálóra szükség, figyeli a kiszolgáló Azure Portalon metrikák használatával. A QPU rendszeresen lefoglalja ki, ha az azt jelenti,-lekérdezéseket futtassanak a modellek száma meghaladja a QPU-korlát a terv. A lekérdezési készlet feladat várólista hossza metrika is nő, ha a lekérdezési szál készlet várólistában lévő lekérdezések száma meghaladja a rendelkezésre álló QPU. További tudnivalókért lásd: [A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>Az Azure Portalon

1. Kattintson a portál **kibővített**. A csúszka segítségével válassza ki a lekérdezés replika kiszolgálók számát. Replikák úgy dönt, az a szám, a meglévő kiszolgáló mellett.

2. A **a lekérdezési készlettől a feldolgozó kiszolgáló elkülönítése**, jelölje be a feldolgozó kiszolgáló kizárása lekérdezés kiszolgálók igen.

   ![Horizontális felskálázás csúszka](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kattintson a **mentése** az új lekérdezés replika kiszolgálók. 

A replikakiszolgáló az elsődleges kiszolgálón a táblázatos modellek szinkronizálva legyenek. Szinkronizálás befejeződése után a lekérdezési készletből kezdődik, a replikakiszolgáló közötti bejövő lekérdezések terjesztése. 


## <a name="synchronization"></a>Szinkronizálás 

Amikor üzembe helyezi az új lekérdezési replikák, az Azure Analysis Services automatikusan replikálja a modellek összes replika között. Manuális szinkronizálást a portálon vagy REST API használatával is elvégezheti. A modellek dolgozza fel, ha szinkronizálva legyenek a lekérdezési replikák között, a szinkronizálást kell végezni.

### <a name="in-azure-portal"></a>Az Azure Portalon

A **áttekintése** > modell > **Synchronize modell**.

![Horizontális felskálázás csúszka](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
Használja a **szinkronizálási** műveletet.

#### <a name="synchronize-a-model"></a>A modell szinkronizálása   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Szinkronizálási állapotának beolvasása  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
PowerShell-lel, mielőtt [telepítése vagy frissítése a legújabb AzureRM-modul](https://github.com/Azure/azure-powershell/releases). 

Lekérdezési replikák száma beállításához használja [Set-azurermanalysisservicesserver parancsmagban](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Adja meg a választható `-ReadonlyReplicaCount` paraméter.

Szinkronizálási futtatásához használja [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>Kapcsolatok

A kiszolgáló áttekintés oldalán a rendszer két kiszolgáló nevét. Ha még nem konfigurálta a kiszolgáló kibővített, mindkét kiszolgáló neve azonos működik. Miután konfigurálja a kiszolgáló kibővített, kell a kapcsolat típusától függően a megfelelő kiszolgáló nevének megadásához. 

Például a Power BI Desktop, Excel és egyéni alkalmazások használatát a végfelhasználói ügyfélkapcsolatok **kiszolgálónév**. 

Az ssms-ben, az SSDT és kapcsolati karakterláncokat a PowerShell, Azure-függvényalkalmazás és az AMO, használjon **felügyeleti kiszolgáló neve**. A felügyeleti kiszolgáló nevét tartalmazza, egy speciális `:rw` (olvasás / írás) minősítője. Az összes feldolgozási műveletei fordulhat elő, a felügyeleti kiszolgálón.

![Kiszolgáló neve](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Kapcsolódó információk

[A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md)   
[Azure Analysis Services kezelése](analysis-services-manage.md) 

