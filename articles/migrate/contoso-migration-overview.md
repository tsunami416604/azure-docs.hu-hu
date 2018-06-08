---
title: Az Azure-bA Contoso áttelepítésének áttekintése |} Microsoft Docs
description: Az áttelepítési stratégia és a helyszíni adatközpontját át Azure Contoso által használt forgatókönyvek áttekintése.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: raynew
ms.openlocfilehash: e4ba0bd639be96830a75c195c3f998157070121c
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839044"
---
# <a name="contoso-migration-overview"></a>Contoso áttelepítési: áttekintés


Ez az az első cikk a azt mutatja be, hogyan a fiktív szervezetre Contoso végzi az áttelepítést a helyszíni infrastruktúrát a [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) felhő. 

A cikk adatsorozat minta példa a központi telepítésekre, amelyek segítenek megérteni és más beállítások áttelepítése az Azure-ba, valamint a hibrid környezetben kipróbálásához biztosít. Cikkek bemutatják, hogyan a Contoso vállalat befejeződött áttelepítési feladatát, de általános olvasásra és konkrét utasításokat mutatók teljes rendelkeznek.

## <a name="introduction"></a>Bevezetés

Azure cloud services csomag átfogó gyűjteményét hozzáférést biztosít. A fejlesztők és informatikai szakemberek számára ezek a szolgáltatások segítségével létre, telepítheti és kezelheti az alkalmazásokat különféle eszközöket és keretrendszerek adatközpontok globális hálózaton keresztül. Ahogy a digitális átállás különféle kihívások elé állítja vállalkozását, az Azure-felhő segít az erőforrások és műveletek optimalizálásának, az ügyfelekkel és alkalmazottakkal való kapcsolatteremtésnek és a termékek átalakításának a megtervezésében.

A felhő számos előnyt nyújt a rugalmasság, a költségcsökkentés, a teljesítmény és a megbízhatóság terén, de az Azure tisztában van azzal, hogy mindezek ellenére számos cégnek egy ideig még helyszíni adatközpontokat is futtatnia kell. A felhőre való átállás akadályainak elhárítása érdekében az Azure egy hibrid felhőstratégiát biztosít, amely áthidalja a helyszíni adatközpontok és az Azure-beli nyilvános felhő közötti távolságot. Ilyen például az Azure-felhő erőforrásainak, például a biztonsági mentésnek a használata a helyszíni erőforrások védelméhez, illetve az Azure Analytics használata a helyszíni számítási feladatok elemzéséhez. 

A hibrid felhő stratégia részeként Azure növekvő megoldást nyújt áttelepítése a helyszíni alkalmazások és munkafolyamatok a felhőbe. Egyszerű lépésekkel átfogó értékelést készítheti a helyszíni erőforrásairól, és kiszámíthatja, hogyan fognak működni az Azure-felhőben. Ezután a részletes értékeléssel már magabiztosan migrálhatja erőforrásait az Azure-ba. Ha az erőforrások megfelelően működnek az Azure-ban, optimalizálhatja őket a hozzáférés, a rugalmasság, a biztonság és a megbízhatóság fenntartása és javítása érdekében.

## <a name="migration-strategies"></a>Migrálási stratégiák

A felhőre való áttérést kapcsolatos olyan stratégiák sorolhatók négy kategóriába sorolhatók: áthelyezési, azonosítóterületen, rearchitect vagy építse újra. Ön elfogadja a stratégiát a stratégiai célok és áttelepítési célok függ. Előfordulhat, hogy bevezessék az néhány stratégiát. Például akkor sikerült áthelyezési egyszerű alkalmazások (növekedési-és-shift), vagy a nem kritikus – az üzleti alkalmazások mellett dönt, de rearchitect azokat, amelyeket az összetettebb és üzleti szempontból kritikus fontosságú. A stratégiák vizsgáljuk meg.


**Stratégia** | **Definíció** | **Mikor érdemes használni** 
--- | --- | --- 
**Áthelyezési** | Gyakran nevezik "növekedési-és-shift" áttelepítés. Ez a beállítás nincs szükség a kód módosítására, és most telepíti át a meglévő alkalmazások Azure gyorsan. Egyes alkalmazások áttelepítése, hogy a felhő, kockázat és kódmódosításokat társított költség nélkül előnyeinek. | Ha szeretné alkalmazások gyors áthelyezése a felhőbe.<br/><br/> Ha szeretne egy alkalmazást áthelyezése nélkül módosításához.<br/><br/> Ha vannak tervezett az alkalmazásokat, így is kihasználja [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) méretezhetőség, az áttelepítés után.<br/><br/> Amikor az üzleti alkalmazások fontosak, de képességeit azonnali módosításai nem kell.
**Azonosítóterületen** | Gyakran nevezik "újból létre kell hozni", újrabontása módosítását igényli az minimális alkalmazások esetén, hogy csatlakozhassanak [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/), és felhőajánlatokat használja.<br/><br/> Például tudta áttelepíteni meglévő alkalmazások Azure App Service- vagy Azure Kubernetes szolgáltatás (AKS). Vagy a beállításokat, például az Azure SQL adatbázis felügyelt példány, a MySQL az Azure-adatbázis, az Azure-adatbázis az PostgreSQL és Azure Cosmos DB sikerült refactor a relációs és nem relációs adatbázisok. | Ha az alkalmazás könnyen képes újracsomagolva működéséhez az Azure-ban.<br/><br/> Újrabontása is használható, ha kíván alkalmazni az Azure által biztosított innovatív DevOps eljárásokat, vagy éppen a számítógép egy tároló stratégia használata munkaterhelések DevOps gondolat.<br/><br/> Újrabontása, gondolja át a meglévő kódbázisra és elérhető a fejlesztési ismeretekkel hordozhatóságát kell.
**Rearchitect** | Az áttelepítéshez amit arra készül, módosítása és alkalmazások működését és a felhő méretezhetőségére app architektúrája optimalizálása kódbázis kiterjesztése.<br/><br/> Például érvénytelenítheti le egy egységes alkalmazás mikroszolgáltatások létrehozására, amelyek együttműködése és a méret böngéssze csoportjára. Vagy sikerült rearchitect relációs és nem relációs adatbázisok teljes körűen felügyelt típusú megoldások, például az Azure SQL Database felügyelt-példányt, MySQL az Azure-adatbázis, az Azure Database PostgreSQL és Azure Cosmos DB. | Ha az alkalmazások kell fő verziók átfogó új képességeket, vagy a felhőplatform hatékonyan dolgozhatnak.<br/><br/> Rearchitecture akkor beállítást, ha szeretné használni az alkalmazás már meglévő befejtetések, méretezhetőségi követelményeinek költséghatékony, Azure által biztosított innovatív DevOps gyakorlatot és virtuális gépek használatának csökkentése.
**Építse újra** | Építse újra dolgot a lépés újraépítése használata az Azure felhőalapú technológiái által nyújtott teljesen új alkalmazás tovább tart. Például, hogy sikerült zöld mező olyan alkalmazásokat hozhat létre felhő natív technológiákkal, például a kiszolgáló nélküli, Azure AI, Azure SQL Database felügyelt-példányt és Azure Cosmos DB. | Ha azt szeretné, hogy a gyors fejlesztést, és a meglévő alkalmazások korlátozott funkciók és élettartamot. Rearchitect építésekor (beleértve az Azure által biztosított DevOps eljárások) üzleti innováció gyorsítása készen a natív felhőalapú technológiái új alkalmazásokat, és fejlesztés AI blockchain és IoT előnyeit.

## <a name="migration-articles"></a>Áttelepítési cikkek

A cikkek a Contoso áttelepítési adatsorozat alkotó az alábbi táblázat foglalja össze.  

- A forgatókönyvek növekvő összetettségét, és azt adja hozzá azokat az adott idő alatt.
- Minden áttelepítési forgatókönyv célja a némileg eltérő üzleti célokat kitűzni, az áttelepítési stratégia meghatározása.
- Az egyes központi telepítési forgatókönyvek esetében azt adja meg a stratégiai célok és célok, javasolt architektúra, a lépéseket hajtsa végre az áttelepítést, és javaslatot tisztítás és a következő lépéseket az áttelepítés befejezése után.

**Cikk** | **Részletek** | **Állapot**
--- | --- | ---
1. cikk: Áttekintése (Ez a cikk) | Contoso-áttelepítési stratégia, a cikk adatsorozat és a mintaalkalmazások használt áttekintést nyújt. | Elérhető
[2. cikk: Az Azure-infrastruktúra telepítése](contoso-migration-overview.md) | Ismerteti, hogyan Contoso előkészíti a helyszíni és az Azure-infrastruktúra az áttelepítéshez. Ugyanabban az infrastruktúrában található összes Contoso áttelepítési forgatókönyvek szolgál. | Elérhető
[3. cikk: A helyszíni erőforrások felmérése](contoso-migration-assessment.md) | Bemutatja, hogyan Contoso fut-e a helyszíni kétrétegű SmartHotel alkalmazásuk VMware futó értékelését. Ezek értékeléséhez app virtuális gépek a [Azure áttelepítése](migrate-overview.md) szolgáltatás, és az alkalmazás SQL Server-adatbázis a [Azure adatbázis áttelepítési Segéd](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Elérhető
[4. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és a kezelt SQL-példány](contoso-migration-rehost-vm-sql-managed-instance.md) | Bemutatja, hogyan Contoso áttelepíti az Azure-bA a SmartHotel alkalmazást. Áttelepítés után az alkalmazás előtér VM használatával [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), és az alkalmazás adatbázis használata a [Azure-adatbázis áttelepítése](https://docs.microsoft.com/azure/dms/dms-overview) szolgáltatás kezelt SQL-példány át. | Elérhető
[5. cikk: Azonosítóterületen (növekedési-és-shift) az Azure virtuális gépen](contoso-migration-rehost-vm.md) | Bemutatja, hogyan Contoso át SmartHotel alkalmazásuk csak a Site Recovery segítségével virtuális gépeket.
[Cikk 6: Azonosítóterületen (növekedési-és-shift) és az Azure virtuális gépeken futó SQL Server rendelkezésre állási csoportok](contoso-migration-rehost-vm-sql-ag.md) | Bemutatja, hogyan Contoso áttelepíti a SmartHotel alkalmazást. Azok a Site Recovery segítségével telepíti át az alkalmazás virtuális gépek és az adatbázis áttelepítési szolgáltatás áttelepítése az app-adatbázist egy SQL Server AlwaysOn rendelkezésre állási csoport. | Elérhető
7. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és az Azure-beli MySQL kiszolgáló | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux virtuális gépek használata a Site Recovery alkalmazást. | Tervezve
8. cikk: Azonosítóterületen (növekedési-és-shift) Azure virtuális gépek és az Azure-beli MySQL kiszolgáló | Bemutatja, hogyan Contoso áttelepíti a osTicket Linux app virtuális gépek áttelepítéséhez a Site Recovery és MySQL munkaterület használatával (biztonsági mentése és visszaállítása) az Azure-beli MySQL Server-példány. | Tervezve



### <a name="demo-apps"></a>Bemutató alkalmazások

A cikk két bemutató apps - SmartHotel és osTicket használja.

- SmartHotel360: Az alkalmazás fejlesztette ki Microsoft használható az Azure használatakor teszt alkalmazásként. Való nyílt forráskódú, és letöltheti a [GitHub](https://github.com/Microsoft/SmartHotel360). Egy SQL Server-adatbázishoz kapcsolódó ASP.NET-alkalmazás is. Az alkalmazás jelenleg a Windows Server 2008 R2 és az SQL Server 2008 R2 rendszert futtató két VMware virtuális gépeken. Az alkalmazás virtuális gépek üzemeltethető a helyszínen és vCenter-kiszolgáló felügyeli.
- osTicket egy nyílt forráskódú szolgáltatás segélyszolgálati jegykezelési Linux rendszeren futó alkalmazást. Letöltheti a [GitHub](https://github.com/osTicket/osTicket). Aktuális az alkalmazás megtalálható Ubuntu 16.04LTS, Apache 2, a PHP 7.0 és a MySQL 5.7-es verzióját futtató két VMware virtuális gépek
    

## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan](contoso-migration-infrastructure.md) Contoso állít be a helyszíni és az Azure-infrastruktúra, mivel azok Felkészülés az áttelepítésre.



