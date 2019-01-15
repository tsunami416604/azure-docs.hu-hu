---
title: Azure-erőforrások naplóinak integrálása a SIEM-rendszerekkel |} A Microsoft Docs
description: Tudnivalók az Azure Log Integration, annak főbb funkcióit és működését.
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
ms.date: 01/14/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 93ed7620636535d45791a657d012a9c7056be09d
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303190"
---
# <a name="introduction-to-azure-log-integration"></a>Bevezetés az Azure-naplók integrációja

>[!IMPORTANT]
> Az Azure Log integration szolgáltatás 06/01/2019 elavulttá válik. 2018. június 27. AzLog letöltések letiltottuk. Mi a teendő mozgatása előre tekintse át a hozzászólás útmutatást [SIEM-eszközök integrálása az Azure monitor](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Az Azure Log Integration volt elérhetővé egyszerűsítheti a integrálása az Azure naplói a helyszíni biztonságiadat- és eseménykezelés (SIEM) rendszerbe.

 Az ajánlott módszer az Azure-naplók integráló, hogy a SIEM gyártója által biztosított összekötőkkel. Az Azure Monitor lehetővé teszi a naplók streamelése az event hubsba, és a SIEM-beszállítót írhat összekötők további integrálásához az eseményközpontból származó naplókat a siem-be.  Hogyan is működik mindez leírását, kövesse a [figyelési adatok az event hubs figyelő-adatfolyam](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). A cikk felsorolja az siem-EK, amelyhez közvetlen Azure-összekötők már rendelkezésre állnak.  

> [!IMPORTANT]
> Ha elsődleges érdeklődését gyűjti a virtuális gépek naplóinak, a legtöbb SIEM-beszállítót adni ezt a lehetőséget a megoldásban. Az SIEM gyártója által biztosított összekötő használata mindig az előnyben részesített megoldás.

A dokumentáció az Azure Log Integration szolgáltatást még mindig folyamatban változatlan marad mindaddig, amíg a funkció elavult.

További információ az Azure Log Integration szolgáltatás érdekében olvasható:

Az Azure Log Integration Windows eseménynaplók, Windows-eseményeket gyűjt [Azure-Tevékenységnaplók](../azure-monitor/platform/activity-logs-overview.md), [Azure Security Center riasztásainak](../security-center/security-center-intro.md), és [Azure Diagnostics-naplók](../azure-monitor/platform/diagnostic-logs-overview.md) a Azure-erőforrások. Integráció az SIEM-megoldástól, hogy az eszközök, adja meg az egységesített irányítópult segít a helyszíni vagy a felhőben. Egy irányítópult segítségével kapni, összesítése, összekapcsolását és elemzését a biztonsági eseményekkel kapcsolatos riasztások.

> [!NOTE]
> Azure Log Integration jelenleg csak az Azure kereskedelmi és az Azure Government felhőben. Más felhőkben nem támogatottak.

![Az Azure Log Integration folyamat][1]

## <a name="what-logs-can-i-integrate"></a>Milyen naplók integrálása?

Az Azure széles körű naplózás az egyes Azure szolgáltatások eredményez. A naplók mutatják be három napló típusa:

* **Ellenőrzés/felügyeleti naplók**: Nyújtanak betekintést a [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) LÉTREHOZÁSI, frissítési és törlési műveleteket. Egy Azure-tevékenységnapló, amelyek az ilyen típusú naplót.
* **Adatok adatsík naplók**: Betekintést az eseményeket, amelyek akkor aktiválódnak, ha egy Azure-erőforrás használja. Napló az ilyen például a Windows Eseménynapló **rendszer**, **biztonsági**, és **alkalmazás** csatornák Windows virtuális gépen. Egy másik példa, az Azure diagnosztikai naplózás, amelyet az Azure monitoron keresztül.
* **Események feldolgozása**: Adja meg az elemzett esemény- és riasztási információkat, amelyek feldolgozása az Ön számára. Egy ilyen típusú események példája az Azure Security Center riasztásait. Az Azure Security Center feldolgozza, és elemzi az előfizetést, amely az aktuális biztonsági állapotát a szolgáltatás riasztásokat nyújt.

Az Azure Log Integration ArcSight QRadar és Splunk támogatja. Ellenőrizze a SIEM gyártójával annak ellenőrzéséhez, hogy a szállító rendelkezik egy natív összekötőt. Azure Log Integration ne használjon, ha egy natív összekötő érhető el.

Ha nincsenek más lehetőségek állnak rendelkezésre, fontolja meg az Azure Log Integration. Az alábbi táblázat a javaslatokat tartalmaz:

|SIEM | Ügyfél már használja az Azure-naplók rendszerintegrátor | Ügyfél vizsgálja a SIEM-integráció beállításai|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | -BA való migrálás megkezdéséhez a [Splunk bővítménye az Azure Monitor](https://splunkbase.splunk.com/app/3534/). | Használja a [Splunk összekötő](https://splunkbase.splunk.com/app/3534/). |
|**QRadar** | Telepíthető át, vagy az utolsó szakaszában az ismertetett QRadar összekötő használatának megkezdéséhez [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). | A dokumentált QRadar-összekötő használata a utolsó szakaszában [Stream Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). |
|**ArcSight** | Továbbra is használhatja az Azure-naplók integráló mindaddig, amíg az összekötő érhető el, majd utána áttelepíteni az összekötő-alapú megoldáshoz.  | Vegye figyelembe, alternatív megoldásként az Azure Log Analytics szolgáltatást. Ne készítse elő az Azure Log Integration, kivéve, ha a rendszer fogadja el az áttelepítési folyamatot, amikor elérhetővé válik az összekötőt. |

> [!NOTE]
> Bár az Azure Log Integration ingyenes megoldása, nincsenek társított log fájl adatokat tároló Azure storage költségeit.

Ha segítségre van szüksége, létrehozhat egy [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki a szolgáltatás **Naplóintegráció**.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhetett az Azure Log Integration. Azure Log Integration, és a naplók által támogatott típusú kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Ismerkedés az Azure Log Integration](security-azure-log-integration-get-started.md). Ez az oktatóanyag végigvezeti az Azure Log Integration telepítése. Emellett bemutatja, hogyan lehet Windows Azure Diagnostics (WAD) storage, Azure-Tevékenységnaplók, az Azure Security Center riasztásainak és az Azure Active Directory naplóinak naplóinak integrálása.
* [Az Azure Log Integration – gyakori kérdések (GYIK)](security-azure-log-integration-faq.md). Ez a GYIK az Azure Log Integration kapcsolatos általános kérdéseket válaszol.
* Ismerje meg, hogyan [streamelése az Azure monitorozási adatok felhasználásra egy eseményközpontba egy külső eszközzel](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).

<!--Image references-->
[1]: ./media/security-azure-log-integration-overview/azure-log-integration.png
