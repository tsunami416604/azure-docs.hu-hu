---
title: Helyszíni adatközpont migrálása az Azure-ba | Microsoft Docs
description: Olvassa el a helyszíni adatközpontok az Azure-ba történő migrálásáról szóló áttekintő tanulmányt.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Helyszíni számítási feladatok migrálása az Azure-ba


A Microsoft Azure a felhőszolgáltatások átfogó gyűjteményéhez biztosít hozzáférést, amelyekkel a fejlesztők és informatikai szakemberek különféle eszközökön és keretrendszereken hozhatnak létre, helyezhetnek üzembe és kezelhetnek alkalmazásokat egy globális adatközpont-hálózaton keresztül. Ahogy a digitális átállás különféle kihívások elé állítja vállalkozását, az Azure-felhő segít az erőforrások és műveletek optimalizálásának, az ügyfelekkel és alkalmazottakkal való kapcsolatteremtésnek és a termékek átalakításának a megtervezésében.

A felhő számos előnyt nyújt a rugalmasság, a költségcsökkentés, a teljesítmény és a megbízhatóság terén, de az Azure tisztában van azzal, hogy mindezek ellenére számos cégnek egy ideig még helyszíni adatközpontokat is futtatnia kell. A felhőre való átállás akadályainak elhárítása érdekében az Azure egy hibrid felhőstratégiát biztosít, amely áthidalja a helyszíni adatközpontok és az Azure-beli nyilvános felhő közötti távolságot. Ilyen például az Azure-felhő erőforrásainak, például a biztonsági mentésnek a használata a helyszíni erőforrások védelméhez, illetve az Azure Analytics használata a helyszíni számítási feladatok elemzéséhez. 

A hibrid felhőstratégia részenként az Azure egyre több és jobb megoldást kínál a helyszíni alkalmazások és számítási feladatok felhőbe történő migrálására. Egyszerű lépésekkel átfogó értékelést készítheti a helyszíni erőforrásairól, és kiszámíthatja, hogyan fognak működni az Azure-felhőben. Ezután a részletes értékeléssel már magabiztosan migrálhatja erőforrásait az Azure-ba. Ha az erőforrások megfelelően működnek az Azure-ban, optimalizálhatja őket a hozzáférés, a rugalmasság, a biztonság és a megbízhatóság fenntartása és javítása érdekében.

Ez a migrálásról szóló cikksorozat bemutatja, hogyan tervezhet és állíthat össze migrálási stratégiát vállalata számára. A cikkek több, egyre összetettebb forgatókönyvet mutatnak be, amelyeket a későbbiek során adunk hozzá. Ezeket a fogatókönyveket a következő táblázat foglalja össze. Az egyes forgatókönyvekhez tartozik egy áttekintés, egy migrálási architektúra, valamint a migrálási folyamat lépéseinek bemutatása. 

**Forgatókönyv** | **Megoldás** | **Szolgáltatások** | **Cikk** 
--- | --- | --- | ---
[1. forgatókönyv: Felderítés és értékelés](migrate-scenarios-assessment.md) | Fedezze fel és mérje fel a helyszíni alkalmazások, adatok, és az áttelepítéshez az Azure infrastruktúra | Data Migration Assistant, Azure Migrate szolgáltatás  | Már elérhető
**[2. forgatókönyv: Áthelyezési alkalmazás](migrate-scenarios-lift-and-shift.md)** | Növekedési és shift alkalmazások az Azure-bA. | Azure Site Recovery, Azure Database Migration Service, Azure SQL Managed Instance | Már elérhető
**3. forgatókönyv: Azonosítóterületen alkalmazás** | Refactor alkalmazások az Azure-bA az áttelepítés során. | Tervezés alatt | Tervezve
**4. forgatókönyv: Rearchitect alkalmazás** | Rearchitect alkalmazások az Azure-bA az áttelepítés során. | Tervezés alatt | Tervezve
**5. forgatókönyv: Rebuild alkalmazás** |Építse újra az alkalmazások az Azure-bA az áttelepítés során | Tervezés alatt | Tervezve




