---
title: "Automatikus skálázási a Microsoft Azure virtuális gépek, a Cloud Services és a webalkalmazások áttekintése |} Microsoft Docs"
description: "A Microsoft Azure-ban automatikus skálázás áttekintése. Virtuális gépek, a Felhőszolgáltatások és a webalkalmazások vonatkozik."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 74bf03be-e658-4239-a214-c12424b53e4c
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2016
ms.author: robb
ms.openlocfilehash: 413828d79d79c181c662bc7cfb4114345de57f90
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>A Microsoft Azure virtuális gépek, a Cloud Services és a Web Apps automatikus skálázás áttekintése
Ez a cikk ismerteti, milyen Microsoft Azure automatikus skálázás, előnye, valamint Ismerkedés használja azt.  

Az Azure a figyelő automatikus skálázás vonatkozik csak a [virtuálisgép-méretezési csoportok](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services/), és [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/).

> [!NOTE]
> Azure automatikus skálázás két módszer van. Automatikus skálázás régebbi verzióját a virtuális gépek (rendelkezésre állási készletek) vonatkozik. Ez a szolgáltatás korlátozott mértékben támogatja, és ajánlott az áttelepítés a virtuálisgép-méretezési csoportok gyorsabb és megbízhatóbb automatikus skálázás támogatásához. A régebbi technológia használatával hivatkozásra kattintva szerepel ebben a cikkben.  
>
>

## <a name="what-is-autoscale"></a>Mi az az automatikus skálázás?
Automatikus skálázás lehetővé teszi, hogy rendelkezik a megfelelő mennyiségű erőforrást fut az alkalmazás a terhelés kezelésére. Lehetővé teszi a terhelés növekszik, és is költségtakarékosabb munkavégzésben erőforrásokat, amelyek ül eltávolításával erőforrások hozzáadása tétlen. A minimális és maximális mennyiségű példánnyal futtatásához, és vegye fel vagy távolítsa el a virtuális gépek meghatározott szabályok alapján automatikusan megadja. A minimális elérhetővé válnak meg arról, hogy az alkalmazás mindig fut még nincs terhelés alatt. A legfeljebb korlátozza a teljes lehetséges óránkénti költsége. Automatikus méretezése hoz létre szabályokkal e két érték között.

 ![Automatikus skálázás ismertetése. Hozzá és távolíthat el a virtuális gépek](./media/monitoring-overview-autoscale/AutoscaleConcept.png)

Ha a szabály feltételek teljesülnek, egy vagy több automatikus skálázási művelet aktiválódnak. Hozzáadása és eltávolítása a virtuális gépek vagy egyéb műveleteket hajthat végre. A következő fogalmi diagramja jeleníti meg ezt a folyamatot.  

 ![Automatikus skálázás folyamatábrája](./media/monitoring-overview-autoscale/Autoscale_Overview_v4.png)

A következő magyarázatot az előző ábrán a eszközökre vonatkozik.   

## <a name="resource-metrics"></a>Erőforrás metrikáit
Erőforrások kibocsátás metrikákat, ezeket a mérési szabályok később dolgoznak. Metrikák keresztül különböző módszereket származnak.
Virtuálisgép-méretezési csoportok telemetriai adatokat az Azure diagnostics ügynökök használja, mivel a webes alkalmazások és a felhőszolgáltatások telemetriai származik, közvetlenül az Azure infrastruktúra. Néhány gyakran használt statisztikai közé tartozik a CPU-használat, a memória használata, a szál száma, a várólista hossza és a lemezek használata terén. Milyen telemetriai adatok használható listájáért lásd: [automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Egyéni metrikák
Kihasználhatja a saját egyéni metrikákat, amelyek az alkalmazás esetleg kibocsátó. Ha konfigurálta a metrikák küldhet az Application Insights alkalmazás(ok) használhatja ezeket metrikákat, hogy méretezhető-e a vonatkozó döntések meghozatalában. 

## <a name="time"></a>Time
Ütemezésalapú szabályok UTC alapulnak. Meg kell adni az időzónájának megfelelően a szabályok beállítása során.  

## <a name="rules"></a>Szabályok
Az ábrán csak egy automatikus skálázási szabály, de ezek is. Létrehozhat összetett átfedő szabályokat a helyzetnek igény szerint.  Szabály típusa  

* **A metrika-alapú** – például a művelet végrehajtására, 50 % feletti CPU-használat esetén.
* **Időalapú** – például olyan webhook minden szombaton adott időzónában 8 am eseményindító.

Metrika-alapú szabályok mérésére alkalmazásterhelés, és adja hozzá, vagy távolítsa el a virtuális gépek terhelés alapján. Ütemezésalapú szabályok engedélyezik, hogy ha a betöltési idő szabályszerűségek és méretezésére előtt a lehetséges terhelés növelését, vagy csökkentse történik.  

## <a name="actions-and-automation"></a>Műveletek és automatizálás
Szabályok is elindíthatja egy vagy több típusú műveleteket.

* **Skála** -skálázási virtuális gépek bejövő vagy kimenő
* **E-mailek** – e-mail küldése az előfizetés rendszergazdái, a társadminisztrátoroknak és/vagy a megadott további e-mail cím
* **Webhook segítségével automatizálhatja** -hívás webhookokkal, melyek több összetett műveletek belül vagy kívül Azure elindítható. Azure-ban belül egy Azure Automation forgatókönyv, az Azure-függvény vagy az Azure Logic App is elindítható. Példa külső URL-cím kívül Azure-szolgáltatásokat, mint a Slackhez és Twilio tartalmaznak.

## <a name="autoscale-settings"></a>Automatikus skálázási beállításokat
Automatikus skálázás használja a következő terminológia és struktúra.

- Egy **automatikus skálázási beállítás** az automatikus skálázás motor határozza meg, hogy felfelé vagy lefelé méretezési olvasható. Egy vagy több profilok, a cél erőforráson, és az értesítési beállítások kapcsolatos információkat tartalmaz.

    - Egy **automatikus skálázási profil** a: kombinációja

        - **kapacitás beállítás**, ami azt jelenti, hogy a minimális, maximális és alapértelmezett értékei a példányok számát.
        - **szabályok**, amelyek mindegyike tartalmaz egy eseményindító (idő vagy metrika) és a méretezési művelet (felfelé vagy lefelé).
        - **Ismétlődés**, ami azt jelzi, ha automatikus skálázás kell a profil hatályba léptetni.

        Több profilok, amelyek lehetővé teszik különböző átfedő követelmények irányuló lehet. Különböző automatikus skálázási profilok például is rendelkezik különböző időpontokban, vagy a hét napjait az.

    - A **beállítása** határozza meg, milyen értesítéseket megtörténik az automatikus skálázási beállítás profilok egyik feltételeknek eleget tevő alapján automatikus skálázás esemény bekövetkezésekor. Automatikus skálázás értesítés egy vagy több e-mail címet, vagy egy vagy több webhook meghíváshoz.


![Az Azure automatikus skálázási beállítás, a profil és a szabály struktúra](./media/monitoring-overview-autoscale/AzureResourceManagerRuleStructure3.png)

A konfigurálható mezőket és leírások teljes listája megtalálható a [automatikus skálázás REST API](https://msdn.microsoft.com/library/dn931928.aspx).

Kódminták lásd:

* [Speciális automatikus skálázás konfigurációs Resource Manager sablonok Virtuálisgép-méretezési készlet használata](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [Automatikus skálázás REST API-n](https://msdn.microsoft.com/library/dn931953.aspx)

## <a name="horizontal-vs-vertical-scaling"></a>Vízszintes vagy függőleges skálázás
Automatikus skálázás csak arányosan vízszintesen, ez ("out") növelését vagy csökkenését ("") a Virtuálisgép-példányok száma.  Vízszintes rugalmasabb felhő helyzetben, lehetővé teszi több ezer virtuális gépek terhelés kezelése érdekében potenciálisan futtatásához.

Ezzel szemben függőleges skálázás nem azonos. A virtuális gépek azonos számú tartja, de lehetővé teszi a virtuális gépek ("Mentés") több vagy kevesebb ("le") hatékony. A memória, Processzor sebessége, lemezterület stb mérik.  További korlátozások függőleges skálázás rendelkezik. Elérhetőségétől függ nagyobb hardver, amely gyorsan találatok száma a felső korlátja, és régiónként eltérőek lehetnek. Általában is függőleges skálázáshoz kell a virtuális gépek leállítása és újraindítása.

További információkért lásd: [függőleges skálázása az Azure virtuális gép az Azure Automation szolgáltatásban](../virtual-machines/linux/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="methods-of-access"></a>Hozzáférési módok
Automatikus skálázás keresztül állíthat be

* [Azure Portal](insights-how-to-scale.md)
* [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
* [Többplatformos parancssori felület (CLI)](insights-cli-samples.md#autoscale)
* [Az Azure figyelő REST API-n](https://msdn.microsoft.com/library/azure/dn931953.aspx)

## <a name="supported-services-for-autoscale"></a>Az automatikus skálázás támogatott szolgáltatások
| Szolgáltatás | Séma & Docs |
| --- | --- |
| Web Apps |[Webalkalmazások skálázás](insights-how-to-scale.md) |
| Cloud Services |[Automatikus skálázás egy felhőalapú szolgáltatás](../cloud-services/cloud-services-how-to-scale.md) |
| Virtuális gépek: klasszikus |[Méretezési klasszikus virtuális gép rendelkezésre állási csoportok](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Virtuális gépek: Windows méretezési csoportok |[A Windows virtuálisgép-méretezési skálázás beállítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md) |
| Virtuális gépek: Linux-skálázási készletekben |[A Linux virtuálisgép-méretezési skálázás beállítása](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Virtuális gépek: Windows – példa |[Speciális automatikus skálázás konfigurációs Resource Manager sablonok Virtuálisgép-méretezési készlet használata](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Következő lépések
Automatikus méretezéssel kapcsolatos további tudnivalókért használja az automatikus skálázás forgatókönyvek, előzőleg felsorolt, vagy tekintse meg a következőket:

* [Az Azure a figyelő automatikus skálázás közös metrikák](insights-autoscale-common-metrics.md)
* [Ajánlott eljárások az Azure a figyelő automatikus skálázás](insights-autoscale-best-practices.md)
* [A automatikus skálázási műveletek is elküldhetik e-mailek és a webhook riasztási értesítésekre](insights-autoscale-to-webhook-email.md)
* [Automatikus skálázás REST API-n](https://msdn.microsoft.com/library/dn931953.aspx)
* [Hibaelhárítási virtuális gép méretezési készlet automatikus skálázás](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
