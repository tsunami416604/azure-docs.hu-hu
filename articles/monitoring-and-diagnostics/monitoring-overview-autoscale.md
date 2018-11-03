---
title: Az automatikus méretezés a Virtual Machines, Cloud Services és a Web Apps áttekintése
description: Az automatikus méretezés a Microsoft Azure-ban. Virtuális gépek, a virtuálisgép-méretezési csoportok, a Cloud Services és a Web Apps vonatkozik.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.component: autoscale
ms.openlocfilehash: 7a3083bd757a7aa5625799724d601f99fd7e2b14
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961296"
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Az automatikus méretezés a Microsoft Azure Virtual Machines, Cloud Services és a Web Apps áttekintése
Ez a cikk azt ismerteti, milyen a Microsoft Azure automatikus méretezési funkciójával, annak előnyeit, és hogyan kezdheti el használni.  

Az Azure Monitor automatikus skálázása csak érvényes [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

> [!NOTE]
> Az Azure automatikus méretezési kétféleképpen rendelkezik. Az automatikus méretezés egy régebbi verzióját (rendelkezésre állási csoportok) virtuális gépekre vonatkozik. Ez a szolgáltatás korlátozott támogatással rendelkezik, és azt javasoljuk, hogy a gyorsabb és megbízhatóbb automatikus méretezés támogatása a virtuális gép méretezési-ba való migrálás. Ez a cikk tartalmazza egy hivatkozást a régebbi technológia használatával.  
>
>

## <a name="what-is-autoscale"></a>Mi az automatikus méretezés?
Az automatikus méretezés lehetővé teszi a megfelelő mennyiségű erőforrást fut az alkalmazás terhelés kezelésére. Lehetővé teszi a terhelés növekedését kezelni és is pénzt takaríthat meg, amelyek úgy vannak erőforrások eltávolítása erőforrások hozzáadásához inaktív. Azt adja meg a futtassa, és adja hozzá, vagy távolítsa el a virtuális gépek szabályok alapján automatikusan-példányok minimális és maximális számát. Egy minimális teszi meg arról, hogy az alkalmazás mindig fut még nincs terhelés alatt. A legfeljebb korlátozza az összes lehetséges óránkénti költség. Automatikus méretezése létrehozott szabályok használatával e két érték között.

 ![Automatikus skálázási ismertetése. Virtuális gépek hozzáadása és eltávolítása](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Ha a szabály feltételei teljesülnek, egy vagy több automatikus skálázási műveletek aktiválódnak. Adja hozzá, és távolítsa el a virtuális gépeket, vagy egyéb műveleteket hajthat végre. A következő fogalmi ábrán ez a folyamat.  

 ![Automatikus skálázási folyamatábrája](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

A következő magyarázatot az előző ábrán az eszközökre vonatkozik.   

## <a name="resource-metrics"></a>Erőforrás-metrikák
Erőforrás szolgáltat metrikák, ezek a metrikák később dolgozza fel a szabályokat. Metrikák származnak, különböző módszerekkel keresztül.
A Virtual machine scale sets használ az Azure diagnostics-ügynököktől származó telemetriai adatok, míg a Web apps és a Cloud services telemetriája származik, közvetlenül az Azure-infrastruktúra. Néhány gyakran használt statisztikai közé tartozik a CPU-használat, memóriahasználat, szálak számát, várólista hossza és lemezek használata terén. Milyen telemetriai adatokat használhatja listáját lásd: [gyakori metrikák az automatikus méretezés](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Egyéni metrikák
Saját egyéni metrikákat, amely az alkalmazás is lehet úgy is használhatja. Ha konfigurálta a mérőszámok küldése az Application Insightsba alkalmazás(ok) kihasználhatja követhesse döntést e skálázásra vagy nem található.

## <a name="time"></a>Time
Ütemezésalapú szabályok UTC alapulnak. Be kell állítani az időzóna megfelelően a szabályok beállítása során.  

## <a name="rules"></a>Szabályok
Az ábrán csak egy automatikus skálázási szabály, de ezek közül számos rendelkezhet. Összetett átfedésben lévő szabályok az adott helyzetben igény szerint hozhat létre.  A szabály típusok:  

* **A mérőszám-alapú** – például a művelet végrehajtására, amikor a CPU-használat 50 % fölött van.
* **Időalapú** – például a trigger minden 8 -kor egy adott időzóna szombatján webhook.

A mérőszám-alapú szabályok mérheti az alkalmazások terhelésének, és adja hozzá, vagy távolítsa el a virtuális gépeket, hogy a terhelés alapján. Ütemezésalapú szabályok lehetővé teszik méretezhető idő jelenik meg a terhelését, és a egy lehetséges terhelés megnövekedése előtt méretezésére vagy csökkenése történik.  

## <a name="actions-and-automation"></a>Műveletek és automatizálás
Szabályok is indíthat egy vagy több típusú műveleteket.

* **Méretezési csoport** – méretezési csoport virtuális gépek és leskálázása
* **E-mailek** – e-mail küldése az előfizetés-adminisztrátorok, társrendszergazdák, illetve megadhat további e-mail-cím
* **Webhookokkal automatizálása** -hívás webhookokkal, melyek több összetett művelet belül, vagy az Azure-on kívülről is aktiválhatja. Azure,-on belül elkezdheti egy Azure Automation runbook, az Azure-függvény vagy az Azure Logic Apps. Példa külső URL-cím kívül az Azure-szolgáltatások, például Slack és a Twilio tartalmazza.

## <a name="autoscale-settings"></a>Automatikus méretezési beállítások
Az automatikus méretezés használata a következő terminológia és struktúra.

- Egy **automatikus skálázási beállítás** annak megállapításához, hogy a kisebbre vagy nagyobbra méretezhetők az automatikus skálázási motor által olvasható. Egy vagy több profilok, a célként megadott erőforrás, és az értesítési beállítások kapcsolatos információkat tartalmaz.

    - Egy **automatikus skálázási profil** v: kombinációja

        - **kapacitás beállítás**, ami azt jelenti, hogy a minimális, maximális és alapértelmezett értékei a példányok számát.
        - **Szabálykészlet**, amelyek mindegyike tartalmaz egy eseményindító (idő vagy metrika) és a egy skálázási műveletet (feljebb vagy lejjebb).
        - **ismétlődési**, ami azt jelenti, hogy az automatikus méretezés kell üzembe helyezés a profil lép érvénybe.

        Több profilt, melyek lehetővé teszik a különböző egymást átfedő követelmények irányuló rendelkezhet. Különböző automatikus méretezési profilok például is rendelkezhet a napot vagy a hét azon napjai, különböző időpontokban.

    - A **értesítési beállítás** határozza meg, milyen értesítéseket alapján a profilok az automatikus skálázási beállítás egyik feltételeknek eleget tevő automatikus skálázási esemény bekövetkezésekor kerül sor. Automatikus skálázási értesítése egy vagy több e-mail címet vagy egy vagy több webhook hívásokat is.


![Az Azure automatikus skálázási beállítás, a profil és a szabály struktúra](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

Konfigurálható mezőket és -leírások teljes listája megtalálható a [automatikus skálázási REST API-val](https://msdn.microsoft.com/library/dn931928.aspx).

Hitelesítésikód-példák lásd:

* [Resource Manager-sablonok használata Virtuálisgép-méretezési csoportok speciális automatikus skálázási konfiguráció](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Az automatikus méretezés – REST API](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vízszintes és függőleges skálázás
Az automatikus méretezés csak méretezéssel vízszintesen, ("ki") növekedése vagy csökkenése ("") a Virtuálisgép-példányok számát.  Rugalmas felhőalapú helyzetben, mert Ön futtatásához potenciálisan több ezer virtuális gép terhelés kezeléséhez, a vízszintes.

Ezzel szemben a vertikális skálázás eltér. Az azonos számú virtuális gépek megtartja, hanem lehetővé teszi a virtuális gépek ("be") több vagy kevesebb, ("üzemen kívül") hatékony. Energiagazdálkodási mérik a memória, CPU-sebesség, lemezterület stb.  Függőleges méretezés további korlátozások vonatkoznak. Célszerű a nagyobb hardverekhez, ami gyors találatok száma a felső korlát és régiónként rendelkezésre állásának függ. Vertikális skálázás is általában egy virtuális gép leállítása és újraindítása van szükség.

További információkért lásd: [vertikális skálázása az Azure virtuális gépen az Azure Automation](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Hozzáférési módok
Beállíthat automatikus skálázási keresztül

* [Azure Portal](monitoring-autoscale-get-started.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Platformfüggetlen parancssori felület (CLI)](insights-cli-samples.md#autoscale)
* [Az Azure Monitor REST API-val](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Támogatott szolgáltatások, az automatikus méretezéshez
| Szolgáltatás | Séma és a dokumentumok |
| --- | --- |
| Web Apps |[Webes alkalmazások skálázása](monitoring-autoscale-get-started.md) |
| Cloud Services |[Automatikus skálázási Felhőszolgáltatás](../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtuális gépek: klasszikus |[Klasszikus virtuális gép rendelkezésre állási csoportok méretezése](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtual Machines: Windows méretezési csoportok |[A Windows virtuálisgép-méretezési csoport skálázási beállítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-powershell.md) |
| Virtuális gépek: Linux rendszerű méretezési csoportok |[A Linux virtuálisgép-méretezési csoport skálázási beállítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md) |
| Virtual Machines: Windows-példa |[Resource Manager-sablonok használata Virtuálisgép-méretezési csoportok speciális automatikus skálázási konfiguráció](insights-advanced-autoscale-virtual-machine-scale-sets.md) |
| API Management-szolgáltatás|[Automatikus skálázása az Azure API Management-példány](https://docs.microsoft.com/azure/api-management/api-management-howto-autoscale)

## <a name="next-steps"></a>További lépések
További információ az automatikus méretezés, használja az automatikus skálázási útmutatók korábban felsorolt, vagy tekintse meg a következő:

* [Gyakori metrikák az Azure Monitor automatikus méretezés](insights-autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure Monitor automatikus skálázása](insights-autoscale-best-practices.md)
* [Automatikus skálázási műveletek használatával küldjön e-mailt és webhookot riasztási értesítések](insights-autoscale-to-webhook-email.md)
* [Az automatikus méretezés – REST API](https://msdn.microsoft.com/library/dn931953.aspx)
* [Hibaelhárítási virtuális gép méretezési csoportok automatikus skálázási](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
