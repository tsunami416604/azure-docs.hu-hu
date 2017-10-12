---
title: "A webes alapkonfiguráció értékelése az Operations Management Suite biztonsági és auditálási megoldásának alapkonfigurációjában | Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogyan lehet használni a webes alapkonfiguráció értékelését az OMS biztonsági és auditálási megoldásban az összes monitorozott webkiszolgáló alapkonfigurációjának megfelelőségi és biztonsági célú értékelésére."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: yurid
ms.openlocfilehash: 40b0c6ca933ea02ac9f5fe3bfaaf87a310542a8d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>A webes alapkonfiguráció értékelése az Operations Management Suite biztonsági és auditálási megoldásában
Ez a dokumentum segít az OMS biztonsági és auditálási megoldás webes alapkonfiguráció-értékelési képességeinek használatában, hogy hozzáférhessen a monitorozott erőforrások biztonsági állapotához.

## <a name="what-is-web-baseline-assessment"></a>Mi az a webes alapkonfiguráció-értékelés?
Jelenleg az OMS biztonsági megoldása biztosít alapkonfiguráció-értékelést az operációs rendszerekhez. 24 óránként ellenőrzi a kiszolgálók operációsrendszer-beállításait, és lehetővé teszi a potenciálisan sebezhető beállítások áttekintését. Ezzel kapcsolatban további információkat olvashat [az alapkonfiguráció az Operations Management Suite biztonsági és auditálási megoldásában történő értékelését](https://docs.microsoft.com/azure/operations-management-suite/oms-security-baseline) ismertető cikkben.

A webes alapkonfiguráció-értékelés célja a potenciálisan sebezhető webkiszolgáló-beállítások megkeresése. A három elsődleges forrás a webes alapkonfigurációkhoz a .NET-, az ASP.NET- és az IIS-konfiguráció.  Az operációs rendszer alapkonfigurációjának értékeléséhez hasonlóan az OMS biztonsági megoldása 24 óránként fogja ellenőrizni a webkiszolgálóit, majd lehetővé teszi azok biztonsági állapotának áttekintését.  Az Internet Information Services-ben (IIS) a konfigurációk nagy mértékben testreszabhatók, ez pedig lehetővé teszi számos hely- és alkalmazásszint felülbírálását. A vizsgáló az alapértelmezett gyökérszinten felül minden alkalmazás-/helyszinten ellenőrzi a beállításokat. Ez segít a potenciálisan sebezhető beállítások azonosításában és gyors javításában, valamint javaslatokat tesz ezekre a beállításokra.

>[!NOTE] 
>Az OMS Security által használt gyakori konfigurációs azonosítók és alapkonfigurációs szabályok [ezen az oldalon](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335?redir=0) tölthetők le.


## <a name="web-security-baseline-assessment"></a>A webes biztonsági alapkonfiguráció értékelése

Ebben az előzetes verzióban ez a funkció az OMS keresési szolgáltatásán, valamint az OMS biztonsági és auditálási irányítópultján keresztül érhető el. A megfelelő lekérdezés végrehajtásához kövesse az alábbi lépéseket:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
2. A **Security and Audit** (Biztonság és auditálás) irányítópulton a webes alapkonfiguráció perspektívája az operációs rendszer alapkonfigurációjának perspektívája mellett látható.
   
    ![A webes biztonsági alapkonfiguráció értékelése az OMS biztonsági és auditálási irányítópultján](./media/oms-security-web-baseline/oms-security-web-baseline-fig5.png)

3. A bal oldali panelen a webkiszolgálók alapkonfigurációhoz viszonyított száma, az összes kiértékelt kiszolgálón sikeresen vizsgált szabályok átlagos százalékos aránya, valamint a kiértékelt kiszolgálók száma látható.
4. A jobb oldali panelen az egyes meghiúsult szabályok láthatóak *Súlyosság* és *RuleType* szerint. A jobb oldali panelen az egyes szabályokra kattintva megjelenítheti a részleteiket. Az alábbi képen látható erre egy példa. A kiértékelt szabály a *Szabály beállítása* területen látható. Az *AzId* mező a Microsoft által az egyes alapkonfigurációs szabályok követéséhez használt egyedi azonosítót tartalmazza. Emellett a felhasználó a *Várt eredményt* (a Microsoft által ajánlott értéket), valamint a szabály biztonsági hatásaival kapcsolatos egyéb részleteket is megtekintheti.
    
    ![Lekérdezés](./media/oms-security-web-baseline/oms-security-web-baseline-fig6.png)

5. Az eredmények áttekintéséhez létrehozhat saját lekérdezéseket is. 

Az első használható lekérdezés a **Webes alapkonfiguráció-értékelés összegzése**. A **Kezdje itt a keresést** mezőbe írja be a következő lekérdezést: *Type=SecurityBaselineSummary BaselineType=Web*. Az alábbi példa egy lehetséges kimenetet mutat be:

![Lekérdezés eredménye](./media/oms-security-web-baseline/oms-security-web-baseline-fig7.png)

>[!NOTE] 
>Ebben a lekérdezésben minden rekord egy kiszolgáló értékelési összegzését jelöli.

Ha már a **Naplók keresése** területen van, különböző lekérdezéseket megadva több információt szerezhet be a webes alapkonfiguráció-értékelésről. Az előző lekérdezésen túl ebben az előzetes verzióban az alábbiakat is használhatja.

**Webes alapkonfigurációsszabály-értékelés**: Minden rekord egy kiszolgáló webes alapkonfigurációsszabály-értékelését jelöli. Tartalmazza a sikertelen szabályok összes adatát, a szabály kiértékelése során az eléréshez használt *SitePath* útvonalat, a *Várt eredményt* és a *Tényleges eredményt*.

Lekérdezés: *Type=SecurityBaseline BaselineType=Web AnalyzeResult=Failed*

![2. lekérdezés eredménye](./media/oms-security-web-baseline/oms-security-web-baseline-fig8.png)

**Egy adott kiszolgálóra vonatkozó összes eredmény megjelenítése**: Ez a lekérdezés az egy adott kiszolgálóhoz tartozó összes eredményt megjeleníti: Lekérdezés: *Type=SecurityBaseline BaselineType=Web Computer=BaselineTestVM1*

![3. lekérdezés eredménye](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Ezekkel a rekordokkal és lekérdezésekkel létrehozhatja a saját irányítópultjait, jelentéseit vagy riasztásait. Alább egy mintát láthat egy felhasználói felületi vezérlőre, amelyet hozzáadhat az irányítópulthoz. [Itt](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/) megtudhatja, hogyan készíthet vizualizációt az adataiból az OMS Nézettervezőjével. Az alábbi képernyő mintául szolgál arra, hogyan fog kinézni a csempe a testreszabás végrehajtása után.

![irányítópult](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban az OMS biztonsági és auditálási megoldásának webes alapkonfiguráció-értékeléséről olvashatott. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)

