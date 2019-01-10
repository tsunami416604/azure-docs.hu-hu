---
title: Az Azure Analysis Services horizontális felskálázás |} A Microsoft Docs
description: Horizontális felskálázás az Azure Analysis Services-kiszolgálók replikálása
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 775de554f39df8359c3852a2d7fa876fd12199d2
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54190831"
---
# <a name="azure-analysis-services-scale-out"></a>Az Azure Analysis Services horizontális felskálázás

Horizontális felskálázás, az ügyfél lekérdezések elosztható több *lekérdezésreplikára* az adott lekérdezési készletben feladatok esetén a válaszidők csökkentése. A lekérdezési készletből feldolgozásához, biztosítva az ügyfél lekérdezések nem negatívan érinti feldolgozási műveletek is egymástól. Horizontális felskálázás az Azure Portalon vagy az Analysis Services REST API használatával konfigurálhatja.

## <a name="how-it-works"></a>Működés

Egy tipikus server központi telepítés esetén egy kiszolgáló feldolgozó kiszolgáló és a lekérdezés-kiszolgálóként is szolgál. Ha az ügyfél-lekérdezéseket futtassanak a kiszolgálón modellek száma meghaladja a lekérdezés feldolgozása egységek (QPU) csomagot a server, vagy a modell feldolgozása befejeződik, a lekérdezési számítási feladatok egy időben, a teljesítmény csökkenhet. 

Horizontális felskálázás létrehozhat egy lekérdezési készlet legfeljebb hét további lekérdezési replika erőforrásokkal (összesen nyolcat a kiszolgálóval együtt). Méretezheti a kritikus fontosságú időpontokban QPU figyelembevételével lekérdezési replikák száma és a lekérdezési készletből egy feldolgozó kiszolgáló elkülönítheti a tetszőleges időpontban. Minden lekérdezési replikák és a kiszolgáló ugyanabban a régióban jönnek létre.

Rendelkezik az adott lekérdezési készletben lekérdezési replikák számától függetlenül tranzakciófeldolgozási nem oszlanak meg lekérdezési replikák között. A feldolgozó kiszolgáló egyetlen kiszolgáló szolgál. Lekérdezési replikák szolgálják, csak a modellek között a lekérdezési készlet minden egyes lekérdezés replikát szinkronizálja a lekérdezéseket. 

Horizontális felskálázás, ha új lekérdezési replikák növekményes hozzáadódik a lekérdezési készletből. Új lekérdezés replika erőforrásokat, amelyeknek szerepelnie a lekérdezési készlet legfeljebb öt percet is igénybe vehet. Ha minden új lekérdezési replikák fel, és fut, az új ügyfélkapcsolatokat is lekérdezési készlet összes erőforrást kell osztani. Meglévő ügyfélkapcsolatok nem változnak, jelenleg csatlakoznak-erőforrásból.  Skálázás az, ha bármely meglévő ügyfélkapcsolatok éppen eltávolítják a lekérdezési készlet lekérdezési készlet erőforrás megszűnik. Ezek újracsatlakoztatását a fennmaradó lekérdezési készlet erőforrás befejeződésekor a horizontális leskálázási művelet, amely akár öt percet is igénybe vehet.

Modellek feldolgozásakor a feldolgozási műveletek után, a feldolgozó kiszolgáló és a lekérdezési replikák közötti szinkronizálás kell elvégezni. Feldolgozási műveletek automatizálása, esetén fontos, hogy konfigurálja a szinkronizálási művelet feldolgozási műveletek sikeres befejezése után. Szinkronizálás manuálisan hajtható végre a portálon, vagy a PowerShell vagy REST API használatával. 

### <a name="separate-processing-from-query-pool"></a>A lekérdezési készletből külön feldolgozása

Maximális teljesítményt az feldolgozási és lekérdezési műveleteket is beállíthatja a lekérdezési készletből a feldolgozó kiszolgáló külön. Során elválasztott, csak a lekérdezési készlet lekérdezési replikák meglévő és új kapcsolatok vannak hozzárendelve. Ha feldolgozási műveletek csak igénybe vehetnek egy rövid idő alatt, választhat, a feldolgozó kiszolgáló csak ennyi ideig tart feldolgozás és a szinkronizálási műveletek végrehajtására, majd adja hozzá újra üzembe a lekérdezési készletből, a lekérdezési készletből külön. 

> [!NOTE]
> Horizontális felskálázás kiszolgálók a standard tarifacsomagban érhető el. Minden egyes lekérdezés replika költsége megegyezik a kiszolgáló számoljuk fel.

> [!NOTE]
> Horizontális felskálázás nem növeli a kiszolgáló rendelkezésre álló memória mennyisége. Növelje a memória, váltson magasabb szintű csomagra kell.

## <a name="region-limits"></a>Régió korlátok

Az a régió, a kiszolgáló konfigurálható lekérdezési replikák száma korlátozott. További tudnivalókért lásd: [rendelkezésre állása régiónként](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>QPU-használat monitorozása

 Annak megállapításához, ha horizontális felskálázás az a kiszolgálóra szükség, figyeli a kiszolgáló Azure Portalon metrikák használatával. A QPU rendszeresen lefoglalja ki, ha az azt jelenti,-lekérdezéseket futtassanak a modellek száma meghaladja a QPU-korlát a terv. A lekérdezési készlet feladat várólista hossza metrika is nő, ha a lekérdezési szál készlet várólistában lévő lekérdezések száma meghaladja a rendelkezésre álló QPU. További tudnivalókért lásd: [A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Horizontális felskálázás konfigurálása

### <a name="in-azure-portal"></a>Az Azure Portalon

1. Kattintson a portál **kibővített**. A csúszka segítségével válassza ki a lekérdezés replika kiszolgálók számát. Replikák úgy dönt, az a szám, a meglévő kiszolgáló mellett.

2. A **a lekérdezési készlettől a feldolgozó kiszolgáló elkülönítése**, jelölje be a feldolgozó kiszolgáló kizárása lekérdezés kiszolgálók igen. Az alapértelmezett kapcsolati karakterlánc használatával Ügyfélkapcsolatok (nélkül: rw) a lekérdezési készletből a replikákat a rendszer átirányítja. 

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

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell

PowerShell-lel, mielőtt [telepítése vagy frissítése a legújabb AzureRM-modul](https://github.com/Azure/azure-powershell/releases). 

Lekérdezési replikák száma beállításához használja [Set-azurermanalysisservicesserver parancsmagban](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Adja meg a választható `-ReadonlyReplicaCount` paraméter.

Szinkronizálási futtatásához használja [Sync-AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Kapcsolatok

A kiszolgáló áttekintés oldalán a rendszer két kiszolgáló nevét. Ha még nem konfigurálta a kiszolgáló kibővített, mindkét kiszolgáló neve azonos működik. Miután konfigurálja a kiszolgáló kibővített, kell a kapcsolat típusától függően a megfelelő kiszolgáló nevének megadásához. 

Például a Power BI Desktop, Excel és egyéni alkalmazások használatát a végfelhasználói ügyfélkapcsolatok **kiszolgálónév**. 

Az ssms-ben, az SSDT és kapcsolati karakterláncokat a PowerShell, Azure-függvényalkalmazás és az AMO, használjon **felügyeleti kiszolgáló neve**. A felügyeleti kiszolgáló nevét tartalmazza, egy speciális `:rw` (olvasás / írás) minősítője. Az összes feldolgozási műveletei fordulhat elő, a felügyeleti kiszolgálón.

![Kiszolgáló neve](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Hibaelhárítás

**A probléma leírása:** Felhasználók hibaüzenet **-kiszolgáló nem található "\<a kiszolgáló nevét >" példány "ReadOnly" kapcsolati módban.**

**Megoldás:** Amikor kiválasztja a **a lekérdezési készlettől a feldolgozó kiszolgáló elkülönítése** beállítás, az alapértelmezett kapcsolati karakterlánc használatával Ügyfélkapcsolatok (nélkül: rw) lekérdezési készlet replikákat a rendszer átirányítja. A lekérdezési készlet replikák vannak nem még online hogy a szinkronizálás még nem fejeződtek be, ha az átirányított ügyfélkapcsolatok sikertelen lehet. Sikertelen csatlakozás tiltása, hogy nem szeretné a feldolgozó kiszolgáló, a lekérdezési készlettől külön addig, amíg a horizontális felskálázást és a szinkronizálási művelet befejeződött. A memória és a QPU mérőszámok segítségével szinkronizálási állapotának figyelése.

## <a name="related-information"></a>Kapcsolódó információk

[A kiszolgáló metrikáinak monitorozása](analysis-services-monitor.md)   
[Azure Analysis Services kezelése](analysis-services-manage.md) 

