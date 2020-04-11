---
title: Automatikus skálázás a virtuális gépeken, a felhőszolgáltatásokban és a webalkalmazásokban
description: Automatikus skálázás a Microsoft Azure-ban. A virtuális gépekre, a virtuálisgép-méretezési csoportokra, a felhőszolgáltatásokra és a webalkalmazásokra vonatkozik.
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: eeb8b301bf087efa164a7864cdce3a04952f45ed
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114137"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Az automatikus skálázás áttekintése a Microsoft Azure Virtual Machines, a Cloud Services és a Web Apps szolgáltatásokban
Ez a cikk ismerteti, mi a Microsoft Azure automatikus skálázás, annak előnyeit, és hogyan kell kezdeni annak használatát.  

Az Azure Monitor automatikus skálázása csak [a virtuális gép méretezési készleteire,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az appszolgáltatásra – webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/)és [az API-kezelési szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

> [!NOTE]
> Az Azure két automatikus skálázási módszerrel rendelkezik. Az automatikus skálázás régebbi verziója a virtuális gépekre (rendelkezésre állási csoportokra) vonatkozik. Ez a funkció korlátozott támogatást, és azt javasoljuk, hogy a gyorsabb és megbízhatóbb automatikus skálázás támogatása virtuálisgép-méretezési készletek. A cikk egy linket, hogyan kell használni a régebbi technológia tartalmazza ezt a cikket.  
>
>

## <a name="what-is-autoscale"></a>Mi az automatikus skálázás?
Automatikus skálázás lehetővé teszi, hogy a megfelelő mennyiségű erőforrás fut az alkalmazás terhelésének kezeléséhez. Ez lehetővé teszi, hogy adjunk forrásokat kezelni növeli a terhelést, és pénzt takarít meg az erőforrások eltávolításával, amelyek tétlenül. Megadhatja a futtatandó példányok minimális és maximális számát, és szabályok készlete alapján automatikusan hozzáadja vagy eltávolítja a virtuális gépeket. A minimum biztosítja, hogy az alkalmazás mindig terhelés nélkül is fut. A maximális limitek a teljes lehetséges óránkénti költséget. A két szélsőség között automatikusan skálázható a létrehozott szabályok használatával.

 ![Autoscale magyarázta. Virtuális gépek hozzáadása és eltávolítása](./media/autoscale-overview/AutoscaleConcept.png)

Ha a szabály feltételek teljesülnek, egy vagy több automatikus skálázási műveletek aktiválódnak. Hozzáadhat és eltávolíthat virtuális gépeket, vagy egyéb műveleteket hajthat végre. Az alábbi koncepcionális ábra ezt a folyamatot mutatja be.  

 ![Automatikus méretezési folyamatábra](./media/autoscale-overview/Autoscale_Overview_v4.png)

A következő magyarázat az előző ábra darabjaira vonatkozik.   

## <a name="resource-metrics"></a>Erőforrás-metrikák
Az erőforrások metrikákat bocsátanak ki, ezeket a mutatókat később szabályok dolgozzák fel. A mérőszámok különböző módszerekkel érkeznek.
A virtuálisgép-méretezési csoportok telemetriai adatokat használnak az Azure diagnosztikai ügynökeitől, míg a webalkalmazások és a felhőszolgáltatások telemetriája közvetlenül az Azure-infrastruktúrából származik. Néhány általánosan használt statisztika a PROCESSZOR-használat, a memóriahasználat, a szálak száma, a várólista hossza és a lemezhasználat. A telemetriai adatok at a [Közös metrikák automatikus skálázása (Autoscale Common Metrics) (Közös metrikák automatikus skálázása) (Közös metrikák) (Közös metrikák) (Közös metrikák automatikus skálázása) (Auto](../../azure-monitor/platform/autoscale-common-metrics.md)

## <a name="custom-metrics"></a>Egyéni mutatók
Használhatja a saját egyéni metrikák, amelyek az alkalmazás(ok) lehet kibocsátó. Ha úgy állította be az alkalmazást(oka)t, hogy metrikákat küldjön az Application Insightsnak, akkor ezeket a mutatókat kihasználva hozhat döntéseket a méretezésről vagy sem.

## <a name="time"></a>Time
Az ütemezésen alapuló szabályok utc-en alapulnak. A szabályok beállításakor megfelelően kell beállítania az időzónát.  

## <a name="rules"></a>Szabályok
Az ábrán csak egy automatikus skálázási szabály látható, de sok lehet belőlük. A helyzetnek szüksége szerint összetett, egymást átfedő szabályokat hozhat létre.  A szabálytípusok közé tartoznak a következők:  

* **Metrika alapú** – Például ezt a műveletet, ha a CPU-használat meghaladja az 50%-ot.
* **Időalapú** – Például egy webhook aktiválása minden 8:00 szombaton egy adott időzónában.

Metrika alapú szabályok intézkedés alkalmazás terhelését, és adja hozzá vagy távolítsa el a virtuális gépek alapján, hogy a terhelés. Az ütemezésalapú szabályok lehetővé teszik a méretezést, amikor időmintákat lát a terhelésben, és szeretné, hogy a méretezés, mielőtt egy esetleges terhelés növekedése vagy csökkenése történik.  

## <a name="actions-and-automation"></a>Műveletek és automatizálás
A szabályok egy vagy több művelettípust válthatnak ki.

* **Méretezés** – virtuális gépek méretezése be- és kiskálázása
* **E-mail** – E-mail küldése az előfizetés-rendszergazdáknak, társadminisztrátoroknak és/vagy további e-mail-címre, amelyet ön ad meg
* **Webhookok on-call** webhooks, amely az Azure-on belül vagy kívül több összetett műveletet is indíthat. Az Azure-on belül elindíthatja az Azure Automation-runbookot, az Azure-függvényt vagy az Azure Logic Alkalmazást. Példa az Azure-on kívüli harmadik fél től származó URL-címre, például a Slack és a Twilio.

## <a name="autoscale-settings"></a>Automatikus méretezési beállítások
Az automatikus skálázás a következő terminológiát és struktúrát használja.

- Az **automatikus skálázási beállításokat** az automatikus skálázási motor olvassa be annak meghatározásához, hogy fel- vagy leskálázásra van-e szükség. Egy vagy több profilt, a célerőforrással kapcsolatos információkat és értesítési beállításokat tartalmaz.

  - Az **automatikus skálázási profil** a következők kombinációja:

    - **kapacitásbeállítás**, amely a példányok számának minimális, maximális és alapértelmezett értékeit jelzi.
    - **szabálykészlet,** amelyek mindegyike tartalmaz egy eseményindítót (idő vagy metrika) és egy léptékműveletet (felvagy le).
    - **ismétlődése**, amely azt jelzi, hogy az automatikus skálázás mikor lépteti életbe ezt a profilt.

      Több profillal is rendelkezhet, amelyek lehetővé teszik a különböző, egymást átfedő követelmények teljesítését. Különböző automatikus skálázási profilokkal rendelkezhet például a nap különböző időpontjaihoz vagy a hét napjaihoz.

  - Az **értesítési beállítás** határozza meg, hogy milyen értesítések történjenek, ha automatikus skálázási esemény következik be az automatikus skálázási beállítás egyik profiljának feltételei alapján. Az automatikus skálázás egy vagy több e-mail címet értesíthet, vagy hívásokat kezdeményezhet egy vagy több webhooknak.


![Az Azure automatikus skálázási beállítása, profilja és szabályszerkezete](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

A konfigurálható mezők és leírások teljes listája elérhető az [Automatikus skálázásrest API-ban.](https://msdn.microsoft.com/library/dn931928.aspx)

A kódra vonatkozó példákat lásd:

* [Speciális automatikus skálázási konfiguráció a Virtuálisgép-méretezési csoportok Erőforrás-kezelő sablonjaival](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md)  
* [REST AUTOMATIKUS SKÁLÁZÁSI REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vízszintes és függőleges méretezés
Az automatikus skálázás csak vízszintesen skálázódik, ami a virtuálisgép-példányok számának növekedése ("out") vagy csökkenése ("in").  Vízszintes rugalmasabb felhőbeli helyzetben, mivel lehetővé teszi, hogy potenciálisan több ezer virtuális gépek kezelésére terhelés.

Ezzel szemben a függőleges méretezés eltérő. Megtartja az azonos számú virtuális gépek, de teszi a virtuális gépek több ("fel") vagy kevesebb ("le") hatékony. A tápellátás mérése a memóriában, a processzor sebességében, a lemezterületen stb.  A függőleges skálázásnak több korlátozása van. Ez anagyobb hardverek rendelkezésre állásától függ, amelyek gyorsan elérik a felső korlátot, és régiónként változhatnak. Függőleges skálázás is általában egy virtuális gép leállításához és újraindításához.


## <a name="methods-of-access"></a>A hozzáférés módszerei
Az automatikus skálázást a

* [Azure Portal](../../azure-monitor/platform/autoscale-get-started.md)
* [PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)
* [Platformfüggetlen parancssori felület (CLI)](../../azure-monitor/platform/cli-samples.md#autoscale)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Támogatott szolgáltatások automatikus skálázáshoz
| Szolgáltatás | Séma & dokumentumok |
| --- | --- |
| Web Apps |[Webalkalmazások méretezése](../../azure-monitor/platform/autoscale-get-started.md) |
| Cloud Services |[Felhőalapú szolgáltatás automatikus skálázása](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuális gépek: Klasszikus |[Klasszikus virtuálisgép-rendelkezésre állási készletek méretezése](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuális gépek: Windows méretezési készletek |[Virtuálisgép-méretezési készletek méretezése a Windows rendszerben](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtuális gépek: Linux méretezési készletek |[Virtuálisgép-méretezési készletek méretezése Linux alatt](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtuális gépek: Windows példa |[Speciális automatikus skálázási konfiguráció a Virtuálisgép-méretezési csoportok Erőforrás-kezelő sablonjaival](../../azure-monitor/platform/autoscale-virtual-machine-scale-sets.md) |
| API-felügyeleti szolgáltatás|[Az Azure API Management-példány automatikus skálázása](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az automatikus skálázásról, használja a korábban felsorolt automatikus skálázási forgatókönyveket, vagy olvassa el a következő erőforrásokat:

* [Az Azure Monitor automatikus skálázási gyakori metrikák](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor automatikus skálázásához](../../azure-monitor/platform/autoscale-best-practices.md)
* [Automatikus skálázási műveletek használata e-mailek és webhook-értesítési értesítések küldéséhez](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST AUTOMATIKUS SKÁLÁZÁSI REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [A virtuális gép méretezési skálájának hibáinak elhárítása automatikus skálázás](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

