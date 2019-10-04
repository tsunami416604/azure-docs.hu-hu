---
title: Naplók integrálása az Azure-erőforrásokból az SIEM-rendszerekkel | Microsoft Docs
description: Ismerje meg a Azure Log Integrationt, annak főbb képességeit és működését.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TerryLanfear
ms.assetid: 9c1346e1-baf8-4975-b2f2-42ae05b2dc0a
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/28/2019
ms.author: TomSh
ms.custom: azlog
ms.openlocfilehash: 9ba4a64268bcc57f04e92be83edb2ac71f18bcaf
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727595"
---
# <a name="introduction-to-azure-log-integration"></a>Bevezetés a Azure Log Integrationba

>[!IMPORTANT]
> Az Azure log-integrációs szolgáltatást a 06/15/2019-as rendszer elavulttá teszi. A AzLog letöltése a 2018. június 27-én le lett tiltva. Útmutatás a további lépések áttekintéséhez: az [Azure monitor használata az Siem-eszközökkel való integráláshoz](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integrationt elérhetővé tettük az Azure-naplók helyszíni biztonsági információkkal és eseménykezelő (SIEM) rendszerbe való integrálásának feladatának leegyszerűsítése érdekében.

 Az Azure-naplók integrálásának ajánlott módszere az SIEM-szállítói összekötők használata. A Azure Monitor lehetővé teszi a naplók továbbítását az Event hubokba, és az SIEM-szállítók olyan összekötőket is írhatnak, amelyek további integrálják a naplókat az Event hub-ből a SIEM-be.  A működésének leírását a stream monitorozásának figyelése az [adatesemény-hubok esetében](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs)című témakörben talál. A cikk felsorolja azokat a SIEM-ket is, amelyek közvetlen Azure-összekötők már elérhetők.  

> [!IMPORTANT]
> Ha az elsődleges érdek a virtuálisgép-naplók gyűjtése, akkor a legtöbb SIEM-szállító a megoldásában ezt a lehetőséget is tartalmazza. Az SIEM-szállító összekötője mindig az előnyben részesített alternatíva.

A Azure Log Integration szolgáltatás dokumentációja továbbra is érvényben marad, amíg a szolgáltatás elavult nem lesz.

További információ a Azure Log Integration funkcióról:

A Azure Log Integration Windows-eseményeket gyűjt a Windows Eseménynapló naplóiból, az [Azure-tevékenységek naplóiból](/azure/azure-monitor/platform/activity-logs-overview), a [Azure Security Center riasztásokból](/azure/security-center/security-center-intro)és az Azure-erőforrások [Azure Diagnostics naplóiból](/azure/azure-monitor/platform/diagnostic-logs-overview) . Az integráció révén a SIEM-megoldás egységes irányítópultot biztosít az összes eszközhöz, akár a helyszínen, akár a felhőben. A biztonsági eseményekhez tartozó riasztások fogadásához, összesítéséhez, összekapcsolásához és elemzéséhez irányítópultot használhat.

> [!NOTE]
> A Azure Log Integration jelenleg csak az Azure kereskedelmi és Azure Government felhők használatát támogatja. Más felhők nem támogatottak.

![A Azure Log Integration folyamat](media/azure-log-integration-overview/azure-log-integration.png)

## <a name="what-logs-can-i-integrate"></a>Milyen naplókba integrálható a szolgáltatás?

Az Azure részletes naplózást készít minden egyes Azure-szolgáltatáshoz. A naplók három naplózási típust képviselnek:

* **Vezérlési/felügyeleti naplók**: Adja meg a [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) létrehozási, frissítési és törlési műveleteinek láthatóságát. Az Azure-tevékenység naplója az ilyen típusú naplókra mutat példát.
* **Adatsíkok naplói**: Az Azure-erőforrások használatakor kiváltott események láthatóságának biztosítása. Ilyen típusú napló például a Windows Eseménynapló **rendszer**-, **Biztonság**-és **alkalmazás** -csatornái egy Windows virtuális gépen. Egy másik példa Azure Diagnostics naplózásra, amelyet a Azure Monitor használatával konfigurálhat.
* **Feldolgozott események**: Az elemzett események és a riasztások adatainak megadása. Ilyen típusú esemény például Azure Security Center riasztás. Azure Security Center dolgozza fel és elemzi az előfizetését, hogy olyan riasztásokat szolgáltasson, amelyek relevánsak az aktuális biztonsági testhelyzethez.

Azure Log Integration támogatja a ArcSight, a QRadar és a splunk. Érdeklődjön a SIEM-szállítónál annak felméréséhez, hogy a szállító natív összekötővel rendelkezik-e. Ne használja a Azure Log Integration, ha van elérhető natív összekötő.

Ha nem áll rendelkezésre más lehetőség, érdemes lehet Azure Log Integration használni. A következő táblázat a javaslatait tartalmazza:

|SIEM | Az ügyfél már használja az Azure log integrátort | Az ügyfél a SIEM-integrációs lehetőségeket vizsgálja|
|---------|--------------------------|-------------------------------------------|
|**Splunk** | Az áttelepítés megkezdése a [Splunk Azure monitor-bővítményéhez](https://splunkbase.splunk.com/app/3534/). | Használja az [splunk](https://splunkbase.splunk.com/app/3534/)-összekötőt. |
|**QRadar** | Váltson át vagy kezdjen el használni a QRadar-összekötőt, amely az Azure monitoring-adatainak az Event hubhoz való továbbításának utolsó szakaszában van dokumentálva [külső eszköz általi felhasználás céljából](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). | Használja a QRadar-összekötőt, amely az Azure monitoring-adatstreamek az Event hubhoz való továbbításának utolsó szakaszában van dokumentálva [külső eszköz általi felhasználás céljából](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). |
|**ArcSight** | Továbbra is használhatja az Azure log integrátort, amíg nincs elérhető összekötő, majd Migrálás az összekötő-alapú megoldásba.  | Vegye fontolóra Azure Monitor naplók használatát Alternatív megoldásként. Ha az összekötő elérhetővé válik, nem kell Azure Log Integrationa, hacsak nem szeretné áthelyezni az áttelepítési folyamatot. |

> [!NOTE]
> Bár a Azure Log Integration ingyenes megoldás, a naplófájlok információinak tárolásával kapcsolatos Azure Storage-költségek is rendelkezésre állnak.

Ha segítségre van szüksége, hozzon létre egy [támogatási kérést](/azure/azure-supportability/how-to-create-azure-support-request). A szolgáltatás esetében válassza a **naplózás integráció**lehetőséget.

## <a name="next-steps"></a>További lépések

Ez a cikk a Azure Log Integration. Ha többet szeretne megtudni a Azure Log Integrationekről és a támogatott naplók típusairól, tekintse meg a következő cikkeket:

* [A Azure log Integration első lépései](azure-log-integration-get-started.md). Ez az oktatóanyag végigvezeti a Azure Log Integration telepítésén. Azt is leírja, hogyan integrálhatja a naplókat a Windows Azure Diagnostics (WAD) tárolóból, az Azure-tevékenységek naplóiból, Azure Security Center riasztásokból és Azure Active Directory naplókból.
* [Azure log Integration gyakori kérdések (GYIK)](azure-log-integration-faq.md). Ez a gyakori kérdések a Azure Log Integrationával kapcsolatos gyakori kérdésekre adnak választ.
* További információ arról, hogyan [továbbíthatja az Azure monitoring-adatait egy Event hubhoz külső eszköz általi felhasználás céljából](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs).

