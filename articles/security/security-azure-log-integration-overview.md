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
ms.date: 06/06/2018
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 3c7ea4f3c081734a77f5ebe44c9fba167ddc6d67
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839390"
---
# <a name="introduction-to-azure-log-integration"></a>Bevezetés az Azure Naplóelemzés integráció


>[!IMPORTANT]
> Az Azure Naplóelemzés integrációs szolgáltatás 06/01/2019 elavulttá válik. AzLog letöltések 2018 jún 27 letiltásra kerül. Mi a teendő áthelyezése előre tekintse át a feladás egy vagy több útmutatót [integrálható a meglévő SIEM-eszközök használata Azure-figyelő](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/preview/?cdn=disable) 

Azure napló-integráció lett elérhetővé tenni az leegyszerűsítik a Azure integrálása a helyszíni biztonsági adatai és az esemény felügyeleti SIEM-rendszer naplókat.

 Az ajánlott módszer integráló Azure naplókat, hogy a SIEM gyártója által biztosított összekötők használja. Az Azure a figyelő lehetővé teszi a adatfolyamként küldje el a naplókat az event hubsban, és a SIEM-beszállítót írhat napló, az event hubs további integrálja a SIEM-összekötőt.  Ennek működéséről leírását, kövesse az utasításokat a [figyelési adatok az event hubs figyelő adatfolyam](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). A cikk is megjeleníti a siem-ektől, amelynek közvetlen Azure összekötők már rendelkezésre állnak.  

> [!IMPORTANT]
> Ha elsődleges érdeklődését van a virtuális gép naplók gyűjtésére, legtöbb SIEM-beszállítót a megoldás vegye fel ezt a beállítást. A SIEM gyártója által biztosított összekötő használata mindig az előnyben részesített megoldás.

A dokumentáció az Azure-napló integrációs szolgáltatás továbbra is folyamatban megmarad, amíg a funkció elavult.

Olvassa el a további található további információ az Azure napló integrációs szolgáltatás számára:

Azure napló-integráció Windows-eseményeket gyűjti össze a Windows Eseménynapló-naplók [Azure tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), [Azure Security Center riasztásait](../security-center/security-center-intro.md), és [Azure diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) a Azure-erőforrások. Rendszer integrálása segít a SIEM megoldás, hogy az eszközök, adjon meg egy új, egységesített irányítópult a helyszínen vagy a felhőben. Irányítópult segítségével kap, összesíteni, összefüggéseket és biztonsági események riasztásai elemzése.

> [!NOTE]
> Azure napló integrációs jelenleg csak az Azure kereskedelmi és Azure Government felhők. Nem támogatja a többi felhőből.

![Az Azure napló integrációs folyamatban][1]

## <a name="what-logs-can-i-integrate"></a>Milyen naplókat is integrálhatja?

Azure hoz létre minden egyes Azure szolgáltatás kiterjedt naplózás. A naplók háromféle napló mutatják be:

* **Ellenőrzés/management-naplók**: betekintést nyújtanak a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) LÉTREHOZÁSI, frissítési és törlési műveletek. Egy Azure tevékenységnapló például a következő napló.
* **Adatok sík naplók**: Adjon meg egy Azure-erőforrás használatakor előállított események láthatósága. Példa az ilyen típusú napló: a Windows Eseménynapló **rendszer**, **biztonsági**, és **alkalmazás** csatornák Windows virtuális gépként. Egy másik példa: Azure Diagnostics-naplózást is, amelyet az Azure-figyelő keresztül.
* **A feldolgozott események**: nyújtson elemzett esemény és a feldolgozott riasztási információkat. Ez eseménytípusra példa, hogy Azure Security Center riasztásait. Az Azure Security Center feldolgozza, és adja meg a riasztásokat, amelyek szükségesek a jelenlegi biztonságot előfizetés elemzi.

Azure napló-integráció ArcSight, QRadar és Splunk támogatja. Ellenőrizze a SIEM forgalmazójával annak ellenőrzéséhez, hogy a szállító natív összekötő rendelkezik. Azure napló integrációs ne használjon, ha natív összekötő érhető el.

Ha nincs más lehetőségek állnak rendelkezésre, érdemes lehet Azure napló integráció. A következő táblázat a ajánlásokat tartalmaz:

|SIEM | Ügyfél már használja az Azure naplóelemzés integráló | Ügyfél vizsgálja a SIEM-integrációs beállítások|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Az áttelepítés megkezdése a [Splunk Azure figyelő bővítménye](https://splunkbase.splunk.com/app/3534/). | Használja a [Splunk összekötő](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Telepítse át, vagy utolsó szakaszában az QRadar összekötő dokumentált használatának megkezdése [figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel adatfolyam Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). | Használja a QRadar összekötő dokumentált utolsó szakaszában [figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel adatfolyam Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Továbbra is használhatja az Azure naplóelemzés integráló, amíg az összekötő érhető el, és telepítse át az összekötő alapú megoldás.  | Érdemes lehet használni az Azure Naplóelemzés helyett. Nem bevezetésében Azure napló integrációhoz kivéve, ha a rendszer halad át az áttelepítési folyamat, amikor az összekötő elérhetővé válik. |

> [!NOTE]
> Bár Azure napló integrációs szabad megoldást, nincsenek társított napló fájl adatokat tároló Azure storage költségei.

Ha segítségre van szüksége, létrehozhat egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). A szolgáltatás, válassza ki a **napló integrációs**.

## <a name="next-steps"></a>További lépések

Ez a cikk bevezetett Azure napló integráció. Azure napló integrációs és a támogatott naplók típusait kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Ismerkedés az Azure napló integrációs](security-azure-log-integration-get-started.md). Ez az oktatóanyag végigvezeti Azure napló-integráció telepítése. Azt is bemutatja, hogyan integrálható a napló, a Windows Azure diagnosztikai (ÜVEGVATTA) tároló, Azure tevékenységi naplóit, Azure Security Center riasztásait és Azure Active Directory naplókat.
* [Azure napló-integráció gyakori kérdések (GYIK)](security-azure-log-integration-faq.md). Ez a GYIK kapcsolatos kérdésekre ad közös Azure napló integráció.
* További tudnivalók a [adatfolyam-figyelési adatok a felhasználásához az eseményközpontba egy külső eszközzel Azure](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
