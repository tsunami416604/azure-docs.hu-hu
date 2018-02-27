---
title: "Az Azure-erőforrások naplók integrálja a SIEM-rendszerekről |} Microsoft Docs"
description: "Tudnivalók az Azure naplóelemzés integrációs, annak főbb funkcióit és annak működéséről."
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
ms.openlocfilehash: e427e2f7dafb6db9bc5c15d841fbbf83a02fc0e1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-microsoft-azure-log-integration"></a>Bevezetés a Microsoft Azure napló integráció

Azure naplóelemzés integrációja lehetővé teszi, hogy az Azure-erőforrások származó nyers naplók integrálása a helyszíni biztonsági információkat és az esemény felügyeleti SIEM-rendszerek abban az esetben az összekötő [Azure figyelő](../monitoring-and-diagnostics/monitoring-get-started.md) még nem érhető el a SIEM forgalmazójával.

Az előnyben részesített módszere integrálása az Azure naplókat, hogy a SIEM gyártója által biztosított Azure figyelő összekötővel, és ezeket a következő [utasításokat](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Azonban ha a SIEM-szállító Azure figyelése nem biztosít egy összekötőt, akkor előfordulhat, hogy fogja tudni használni Azure napló integrációs ideiglenes megoldásként (Ha a SIEM Azure napló integrációs támogatja) addig, amíg az ilyen összekötő nem érhető el.

>[!IMPORTANT]
>Ha elsődleges érdeklődését virtuális gép naplók gyűjtésére, legtöbb SIEM-beszállítót vegye fel ezt a megoldást. A SIEM használatával gyártója által biztosított összekötő mindig kell lennie az előnyben részesített megoldás.

Azure naplóelemzés integrációs Windows-eseményeket gyűjti össze a Windows Eseménynapló-naplók [Azure tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center riasztásait](../security-center/security-center-intro.md), és [Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a Azure-erőforrások. Ez az integráció a SIEM-megoldás adjon meg egy új, egységesített irányítópult minden eszköz segítségével, a helyszíni vagy a felhőben, így meg tudja-e összesíteni, összefüggéseket, elemzése és biztonsági eseményeket a riasztás.

>[!NOTE]
Jelenleg az egyetlen támogatott felhő hozzá Azure kereskedelmi és Azure Government. Nem támogatja a többi felhőből.

![Azure-naplók integrációja][1]

## <a name="what-logs-can-i-integrate"></a>Milyen naplókat is integrálhatja?

Azure kiterjedt naplózás minden Azure Service eredményez. Ezek a naplók naplók három típusú mutatják be:

* **Ellenőrzés/management-naplók** betekintést nyújtanak a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) LÉTREHOZÁSI, frissítési és törlési műveletek. Az Azure tevékenységi naplóit például a következő napló.
* **Adatok sík naplók** adja meg az Azure-Erőforrás kihasználtsága részeként kiváltott események láthatósága. Példa az ilyen típusú napló: a Windows Eseménynapló **rendszer**, **biztonsági**, és **alkalmazás** csatornák Windows virtuális gépként. Egy másik példa a diagnosztikai naplózás Azure figyelő konfigurálva
* **A feldolgozott események** elemzett esemény és az Ön nevében feldolgozott riasztási adatokat. Például a következő esemény az Azure Security Center riasztásait, ahol az Azure Security Center feldolgozása és elemzése a adja meg a jelenlegi biztonságot vonatkozó riasztás-előfizetés.

Azure napló-integráció ArcSight, QRadar és Splunk támogatja. Minden esetben ellenőrizze a SIEM-forgalmazójával annak ellenőrzéséhez, hogy azok rendelkeznek-e egy natív összekötő. Ne használjon Azure napló integrációs, ha natív összekötők nem érhető el.

Ha nincsenek egyéb beállítások, Azure napló integrációs tekinthetők. A következő táblázat a ajánlásokat tartalmaz.

|**SIEM** | **Az ügyfél már használja a napló integráló** | **Ügyfél vizsgálja a SIEM-integrációs beállítások**|
|---------|--------------------------|-------------------------------------------|
|**SPLUNK** | Az áttelepítés megkezdése a [Splunk Azure figyelő bővítménye](https://splunkbase.splunk.com/app/3534/) | Használjon [SPLUNK összekötő](https://splunkbase.splunk.com/app/3534/) |
|**AZ IBM QRADAR** | Telepítse át, vagy a dokumentált http://aka.ms/azmoneventhub végén QRadar összekötő használatának megkezdése | A dokumentált http://aka.ms/azmoneventhub végén QRadar összekötő használatára  |
|**ARCSIGHT** | A napló integráló használhatja összekötő érhető el, majd telepítse át az összekötő alapú megoldás.  | Fontolja meg az Azure Naplóelemzés helyett. Tegye nem érheti el a Azure napló integrációs kivéve, ha a rendszer halad át az áttelepítési folyamat, amikor az összekötő elérhetővé válik. |

>[!NOTE]
>Míg Azure napló integrációs szabad megoldást, nincsenek eredő a napló fájl adatokat tároló Azure storage költségei.

Ha segítségre van szüksége, nyissa meg a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Ehhez az szükséges, válassza ki a **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megismerhette Azure naplóelemzés integráció. Azure naplóelemzés integráció és támogatott naplók típusait kapcsolatos további tudnivalókért olvassa el a következőket:

* [Ismerkedés az Azure naplóelemzés integrációs](security-azure-log-integration-get-started.md) – Ez az oktatóanyag végigvezeti az Azure naplóelemzés integráció telepítése, és az Azure ÜVEGVATTA storage, Azure tevékenységi naplóit, naplók integrálása az Azure Security Center riasztások és az Azure Active Directory naplók.
* [Gyakori kérdések (GYIK) integrációs Azure naplóelemzés](security-azure-log-integration-faq.md) -Ez gyakran ismételt kérdések Azure naplóelemzés integrációs kapcsolatos kérdésekre ad választ.
* [A figyelés egy eseményközpontba felhasználásra adatok külső eszköz adatfolyam Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md)

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
