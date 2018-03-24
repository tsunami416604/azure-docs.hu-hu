---
title: Az Azure-erőforrások naplók integrálása a SIEM-rendszerekről |} Microsoft Docs
description: Tudnivalók Azure napló integrációs, annak főbb funkcióit és annak működéséről.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 6d91692a64a4d3def80990a439fe0a0898bf2f09
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="introduction-to-azure-log-integration"></a>Bevezetés az Azure Naplóelemzés integráció

Azure napló integráció az Azure-erőforrások nyers naplók integrálása a helyszíni biztonsági információkat és az esemény felügyeleti SIEM-rendszerek használhatja. Az Azure napló integrálást használja, csak ha az összekötő [Azure figyelő](../monitoring-and-diagnostics/monitoring-get-started.md) nem érhető el a meglévő SIEM forgalmazójától.

Az előnyben részesített integráló Azure naplók módja a SIEM gyártója által biztosított Azure figyelő-összekötő használatával. Az összekötő használatára, kövesse az utasításokat a [figyelési adatok az event hubs figyelő adatfolyam](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). 

Azonban ha a SIEM-szállító Azure figyelése nem biztosít egy összekötőt, akkor fogja tudni használni az Azure napló integrációs ideiglenes megoldásként mindaddig, amíg az összekötő érhető el. Azure napló-integráció lehetőség csak akkor, ha Azure napló integrációs támogatja a saját siem-Rendszerébe.

> [!IMPORTANT]
> Ha elsődleges érdeklődését van a virtuális gép naplók gyűjtésére, legtöbb SIEM-beszállítót a megoldás vegye fel ezt a beállítást. A SIEM gyártója által biztosított összekötő használata mindig az előnyben részesített megoldás.

Azure napló-integráció Windows-eseményeket gyűjti össze a Windows Eseménynapló-naplók [Azure tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center riasztásait](../security-center/security-center-intro.md), és [Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a Azure-erőforrások. Rendszer integrálása segít a SIEM megoldás, hogy az eszközök, adjon meg egy új, egységesített irányítópult a helyszínen vagy a felhőben. Irányítópult segítségével kap, összesíteni, összefüggéseket és biztonsági események riasztásai elemzése.

> [!NOTE]
> Azure napló integrációs jelenleg csak az Azure kereskedelmi és Azure Government felhők. Nem támogatja a többi felhőből.

![Az Azure napló integrációs folyamat diagramja][1]

## <a name="what-logs-can-i-integrate"></a>Milyen naplókat is integrálhatja

Azure hoz létre minden egyes Azure szolgáltatás kiterjedt naplózás. A naplók háromféle napló mutatják be:

* **Ellenőrzés/management-naplók**: betekintést nyújtanak a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) LÉTREHOZÁSI, frissítési és törlési műveletek. Egy Azure tevékenységnapló például a következő napló.
* **Adatok sík naplók**: Adjon meg egy Azure-erőforrás használatakor előállított események láthatósága. Példa az ilyen típusú napló: a Windows Eseménynapló **rendszer**, **biztonsági**, és **alkalmazás** csatornák Windows virtuális gépként. Egy másik példa: Azure Diagnostics-naplózás konfigurálása Azure figyelő keresztül.
* **A feldolgozott események**: nyújtson elemzett esemény és a feldolgozott riasztási információkat. Ez eseménytípusra példa, hogy Azure Security Center riasztásait. Az Azure Security Center feldolgozza, és adja meg a riasztásokat, amelyek szükségesek a jelenlegi biztonságot előfizetés elemzi.

Azure napló-integráció ArcSight, QRadar és Splunk támogatja. Ellenőrizze a SIEM forgalmazójával annak ellenőrzéséhez, hogy a szállító natív összekötő rendelkezik. Azure napló integrációs ne használjon, ha natív összekötő érhető el.

Ha nincs más lehetőségek állnak rendelkezésre, érdemes lehet Azure napló integráció. A következő táblázat a ajánlásokat tartalmaz:

|SIEM | Ügyfél már használja az Azure naplóelemzés integráló | Ügyfél vizsgálja a SIEM-integrációs beállítások|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Az áttelepítés megkezdése a [Splunk Azure figyelő bővítménye](https://splunkbase.splunk.com/app/3534/). | Használja a [Splunk összekötő](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Telepítse át, vagy az utolsó szakasza ismerteti QRadar összekötő használatának megkezdéséhez [figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel adatfolyam Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Az utolsó szakasza ismerteti QRadar összekötő használatára [figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel adatfolyam Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Továbbra is használhatja az Azure naplóelemzés integráló, amíg az összekötő érhető el. Ezután telepítse át az összekötő alapú megoldás.  | Érdemes lehet használni az Azure Naplóelemzés helyett. Nem bevezetésében Azure napló integrációhoz kivéve, ha a rendszer halad át az áttelepítési folyamat, amikor az összekötő elérhetővé válik. |

> [!NOTE]
> Bár Azure napló integrációs szabad megoldást, nincsenek társított napló fájl adatokat tároló Azure storage költségei.

Ha segítségre van szüksége, létrehozhat egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). A szolgáltatás, válassza ki a **napló integrációs**.

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, Azure napló integráció. Azure napló integrációs és a támogatott naplók típusait kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Ismerkedés az Azure napló integrációs](security-azure-log-integration-get-started.md). Ez az oktatóanyag végigvezeti Azure napló-integráció telepítése. Azt is bemutatja, hogyan integrálható a napló, a Windows Azure diagnosztikai (ÜVEGVATTA) tároló, Azure tevékenységi naplóit, Azure Security Center riasztásait és Azure Active Directory naplókat.
* [Azure napló-integráció gyakori kérdések (GYIK)](security-azure-log-integration-faq.md). Ez a GYIK kapcsolatos kérdésekre ad közös Azure napló integráció.
* További tudnivalók a [adatfolyam-figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
