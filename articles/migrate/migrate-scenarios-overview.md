---
title: Helyszíni adatközpont migrálása az Azure-ba | Microsoft Docs
description: Olvassa el a helyszíni adatközpontok az Azure-ba történő migrálásáról szóló áttekintő tanulmányt.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: be322596da0c3e5ba18aa64285c437cdb823fc4b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>Helyszíni számítási feladatok migrálása az Azure-ba


A Microsoft Azure a felhőszolgáltatások átfogó gyűjteményéhez biztosít hozzáférést, amelyekkel a fejlesztők és informatikai szakemberek különféle eszközökön és keretrendszereken hozhatnak létre, helyezhetnek üzembe és kezelhetnek alkalmazásokat egy globális adatközpont-hálózaton keresztül. Ahogy a digitális átállás különféle kihívások elé állítja vállalkozását, az Azure-felhő segít az erőforrások és műveletek optimalizálásának, az ügyfelekkel és alkalmazottakkal való kapcsolatteremtésnek és a termékek átalakításának a megtervezésében.

A felhő számos előnyt nyújt a rugalmasság, a költségcsökkentés, a teljesítmény és a megbízhatóság terén, de az Azure tisztában van azzal, hogy mindezek ellenére számos cégnek egy ideig még helyszíni adatközpontokat is futtatnia kell. A felhőre való átállás akadályainak elhárítása érdekében az Azure egy hibrid felhőstratégiát biztosít, amely áthidalja a helyszíni adatközpontok és az Azure-beli nyilvános felhő közötti távolságot. Ilyen például az Azure-felhő erőforrásainak, például a biztonsági mentésnek a használata a helyszíni erőforrások védelméhez, illetve az Azure Analytics használata a helyszíni számítási feladatok elemzéséhez. 

A hibrid felhőstratégia részenként az Azure egyre több és jobb megoldást kínál a helyszíni alkalmazások és számítási feladatok felhőbe történő migrálására. Egyszerű lépésekkel átfogó értékelést készítheti a helyszíni erőforrásairól, és kiszámíthatja, hogyan fognak működni az Azure-felhőben. Ezután a részletes értékeléssel már magabiztosan migrálhatja erőforrásait az Azure-ba. Ha az erőforrások megfelelően működnek az Azure-ban, optimalizálhatja őket a hozzáférés, a rugalmasság, a biztonság és a megbízhatóság fenntartása és javítása érdekében.

Ez a migrálásról szóló cikksorozat bemutatja, hogyan tervezhet és állíthat össze migrálási stratégiát vállalata számára. A cikkek több, egyre összetettebb forgatókönyvet mutatnak be, amelyeket a későbbiek során adunk hozzá. Ezeket a fogatókönyveket a következő táblázat foglalja össze. Az egyes forgatókönyvekhez tartozik egy áttekintés, egy migrálási architektúra, valamint a migrálási folyamat lépéseinek bemutatása. 

**Forgatókönyv** | **Megoldás** | **Szolgáltatások** | **Cikk** 
--- | --- | --- | ---
[1. forgatókönyv: Felderítés és értékelés](migrate-scenarios-assessment.md) | Helyszíni alkalmazások és adatok felderítése és kiértékelése az Azure-ba való migráláshoz | Data Migration Assistant, Azure Migrate szolgáltatás  | Már elérhető
**2. forgatókönyv: Átemeléses migrálás** | Belső alkalmazások üzemeltetésének áthelyezése az Azure-ba. Optimalizálás az Azure-ban migrálás után. | Azure Site Recovery, Azure Database Migration Service, Azure SQL Managed Instance | Már elérhető
**3. forgatókönyv: Újrabontás és migrálás** | Helyszíni ügyfélalkalmazások modernizálása és újrabontása az Azure-ba történő migrálás közben. | Tervezés alatt | Tervezve
**4. forgatókönyv: Újratervezés és migrálás** | Ügyféltranzakciós webhelyek újratervezése és migrálása az Azure-ba történő migrálás közben. | Tervezés alatt | Tervezve
**5. forgatókönyv: Újraépítés** |Ügyfélalkalmazás és adatok újraépítése és migrálása az Azure-ba | Tervezés alatt | Tervezve




