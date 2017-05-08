---
title: "Operations Management Suite biztonsági és auditálási megoldás webes alapkonfigurációja | Microsoft Docs"
description: "Ez a dokumentum ismerteti, hogyan lehet használni az OMS biztonsági és auditálási megoldást az összes figyelt webkiszolgáló webes alapkonfigurációjának megfelelőségi és biztonsági célú értékelésére."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: hu-hu
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>A webes alapkonfiguráció értékelése az Operations Management Suite biztonsági és auditálási megoldásában
Ez a dokumentum segít az [Operations Management Suite (OMS) biztonsági és auditálási megoldás](operations-management-suite-overview.md) webes alapkonfiguráció-értékelési képességeinek használatában, hogy hozzáférhessen a figyelt erőforrások biztonsági állapotához.

## <a name="what-is-web-baseline-assessment"></a>Mi az a webes alapkonfiguráció-értékelés?
Jelenleg az OMS biztonsági megoldása biztosít alapkonfiguráció-értékelést az operációs rendszerekhez. A kiszolgálók operációsrendszer-beállításait 24 óránként ellenőrzi, és lehetővé teszi a potenciálisan sebezhető beállítások áttekintését. Ezzel kapcsolatban további információkat olvashat [az alapkonfiguráció az Operations Management Suite biztonsági és auditálási megoldásában történő értékelését](oms-security-baseline.md) ismertető cikkben.

A webes alapkonfiguráció-értékelés célja a potenciálisan sebezhető webkiszolgáló-beállítások megkeresése. A három elsődleges forrás a webes alapkonfigurációkhoz a .NET-, az ASP.NET- és az IIS-konfiguráció.  Az operációs rendszer alapkonfigurációjának értékeléséhez hasonlóan az OMS biztonsági megoldása 24 óránként fogja ellenőrizni a webkiszolgálóit, majd lehetővé teszi azok biztonsági állapotának áttekintését.  Az Internet Information Services-ben (IIS) a konfigurációk nagy mértékben testreszabhatók, ez pedig lehetővé teszi számos hely- és alkalmazásszint felülbírálását. A vizsgáló az alapértelmezett gyökérszinten felül minden alkalmazás-/helyszinten ellenőrzi a beállításokat. Ez segít a potenciálisan sebezhető beállítások helyét azonosítani, valamint azokat gyorsan kijavítani.


## <a name="web-security-baseline-assessment"></a>A webes biztonsági alapkonfiguráció értékelése
Ehhez az előzetes verzióhoz ez a szolgáltatás az OMS keresési szolgáltatásán keresztül érhető el. A megfelelő lekérdezés végrehajtásához kövesse az alábbi lépéseket:

1. A **Microsoft Operations Management Suite** fő irányítópultján kattintson a **Biztonság és auditálás** csempére.
2. A **Biztonság és auditálás** irányítópulton kattintson a **Naplók keresése** gombra.
3. Az első használható lekérdezés a **Webes alapkonfiguráció-értékelés összegzése**. A **Kezdje itt a keresést** mezőbe írja be a következő lekérdezést: Type*=SecurityBaselineSummary BaselineType=web*. A következő képernyő egy kimeneti mintát jelenít meg:

![Webes alapkonfiguráció-értékelés összegzése](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> Ebben a lekérdezésben minden rekord egy kiszolgáló értékelési összegzését jelöli.

Ha már a **Naplók keresése** területen van, különböző lekérdezéseket megadva több információt szerezhet be a webes alapkonfiguráció-értékelésről. Az előző lekérdezésen túl ebben az előzetes verzióban az alábbiakat is használhatja.

**Webes alapkonfigurációsszabály-értékelés**: Minden rekord egy kiszolgáló webes alapkonfigurációsszabály-értékelését jelöli. Tartalmazza a szabály összes adatát, a helyét, a várt eredményt és az aktuális eredményt.

**Lekérdezés**: Type*=SecurityBaseline BaselineType=web*

![Webes alapkonfigurációsszabály-értékelés](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Az összes eredmény megjelenítése egy adott kiszolgálóhoz**:

**Lekérdezés**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Minden eredmény](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

E rekordokkal és lekérdezésekkel létrehozhatja a saját irányítópultjait, jelentéseit vagy riasztásait. Az alábbi képernyőn egy mintát láthat egy felhasználói felületi vezérlőre, amelyet hozzáadhat az irányítópulthoz. [Itt](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/) megtudhatja, hogyan készíthet vizualizációt az adataiból az OMS Nézettervezőjével. Az alábbi képernyő mintául szolgál arra, hogyan fog kinézni a csempe a testreszabás végrehajtása után.

![Mintául szolgáló felhasználói felület](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Ha szeretné megismerni az alapkonfiguráció-értékelés során ellenőrzött beállításokat, letöltheti [ezt az Excel-táblázatot](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690), amely tartalmazza a beállításokat.

## <a name="see-also"></a>Lásd még:
Ebben a dokumentumban az OMS biztonsági és auditálási megoldásának webes alapkonfiguráció-értékeléséről olvashatott. Az OMS által nyújtott védelemmel kapcsolatos további információkat a következő cikkek tartalmaznak:

* [Az Operations Management Suite (OMS) áttekintése](operations-management-suite-overview.md)
* [A biztonsági riasztások figyelése és megválaszolása az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-responding-alerts.md)
* [Az erőforrások figyelése az Operations Management Suite biztonsági és auditálási megoldásban](oms-security-monitoring-resources.md)


