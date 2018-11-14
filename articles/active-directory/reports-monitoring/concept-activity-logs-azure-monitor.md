---
title: Azure Active Directory-tevékenységnaplók az Azure Monitorban (előzetes verzió) | Microsoft Docs
description: Bevezetés az Azure Active Directory-tevékenység naplók az Azure Monitor (előzetes verzió)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: concept
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 760110d0ac359f6b7f135bf869e2520b8028ba6e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51625436"
---
# <a name="azure-ad-activity-logs-in-azure-monitor-preview"></a>Azure AD-tevékenységnaplók az Azure Monitorban (előzetes verzió)

Az Azure Active Directory (Azure AD) tevékenységeket tartalmazó naplók mostantól átirányítása hosszú távú megőrzési és az adatok insights több végpontot is. A nyilvános előzetes verziója az Azure AD-naplók az Azure Monitor lehetővé teszi:

* A Tevékenységnaplók archiválása az Azure AD az Azure storage-fiókkal, megőrizni az adatokat hosszú ideig.
* A Stream az Azure AD-Tevékenységnaplók az Azure event hub Analytics népszerű biztonságiadat- és eseménykezelés (SIEM) eszközökkel, például a Splunk és QRadar.
* Az Azure AD integrálása tevékenységeket tartalmazó naplók a saját egyéni napló megoldásokkal folyamatos őket egy eseményközpontba.
* Tevékenység naplók küldése az Azure AD a Log Analytics látványos vizualizációkkal, monitorozási és riasztási a csatlakoztatott adatok engedélyezéséhez.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Támogatott jelentések

Irányíthatja az Azure AD naplózási naplókat és az Azure storage-fiókot, event hub, a Log Analytics vagy egyéni megoldás bejelentkezési naplók a szolgáltatás használatával. 

* **Auditnaplók**: Az [auditnaplók tevékenységjelentés](concept-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.
* **Bejelentkezési naplók**: A [bejelentkezések tevékenységjelentéssel](concept-sign-ins.md) meghatározhatja, hogy ki hajtotta végre az auditnaplók által jelentett feladatokat.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Azure AD Ingyenes, Alapszintű, Prémium 1 vagy Prémium 2 [licenc](https://azure.microsoft.com/pricing/details/active-directory/) az Azure AD-auditnaplók eléréséhez az Azure Portalon. 
* Egy Azure AD-bérlő.
* Egy felhasználó, aki az adott Azure AD-bérlő **globális** vagy **biztonsági rendszergazdája**.
* Azure AD Prémium 1 vagy Prémium 2 [licenc](https://azure.microsoft.com/pricing/details/active-directory/) az Azure AD bejelentkezési naplók eléréséhez az Azure Portalon. 

Attól függően, hogy hová szeretné irányítani a naplózási adatokat, a következők valamelyikére is szüksége lesz:

* Egy Azure Storage-fiók, amelyen *ListKeys* jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne Blob Storage-fiókot. A tárolás díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Azure Event Hubs-névtér a külső megoldásokkal való integrációhoz.
* Az Azure Log Analytics-munkaterület naplók elküldése a Log Analytics szolgáltatásba.

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok

Ha már rendelkezik Azure AD-licenccel, egy Azure-előfizetésre lesz szüksége a tárfiók és az eseményközpont beállításához. Az Azure-előfizetés ingyenesen érhető el, az Azure-erőforrások használatáért azonban fizetnie kell, beleértve az archiváláshoz használt tárfiókot és a streameléshez használt eseményközpontot. Az adatok mennyisége és ezáltal a vonatkozó költségek is a bérlő méretétől függően jelentős mértékben változhatnak. 

### <a name="storage-size-for-activity-logs"></a>Tevékenységnaplók tárterületmérete

Minden auditnapló-esemény körülbelül 2 KB adattárhelyet foglal el. Egy 100 000 felhasználót számláló bérlőhöz, amely naponta körülbelül 1,5 millió eseményt hoz létre, hozzávetőleg napi 3 GB adattárhelyre lesz szüksége. Mivel az írási műveletek hozzávetőleg ötperces kötegekben történnek, havonta várhatóan körülbelül 9000 írási művelettel számolhat. 

Az alábbi táblázat tartalmaz egy költségbecslést a bérlő méretének függvényében egy általános célú v2-es tárfiókra az USA nyugati régiójában, legalább egyéves megőrzéssel. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/details/storage/blobs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről. 

| Naplókategória | Felhasználók száma | Napi események | Havi adatmennyiség (becsült) | Havi költség (becsült) | Éves költség (becsült) |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Naplózás | 100 000 | 1,5&nbsp;millió | 90 GB | 1,93 dollár | 23,12 dollár |
| Naplózás | 1,000 | 15 000 | 900 MB | 0,02 dollár | 0,24 dollár |
| Bejelentkezések | 1,000 | 34 800 | 4 GB | 0,13 dollár | 1,56 dollár |
| Bejelentkezések | 100 000 | 15&nbsp;millió | 1,7 TB | 35,41 dollár | 424,92 dollár | 


### <a name="event-hub-messages-for-activity-logs"></a>Tevékenységnaplók eseményközpont-üzenetei

A rendszer az eseményeket körülbelül ötperces intervallumokba kötegeli, majd egyetlen üzenetben küldi el, amely az adott időablakba tartozó összes eseményt tartalmazza. Az eseményközpontban az egyes üzenetek mérete legfeljebb 256 KB lehet, és ha az adott időablakba tartozó üzenetek összesített mérete meghaladja ezt a mennyiséget, a rendszer több üzenetet küld. 

Például egy több mint 100 000 felhasználót számláló bérlőn általában körülbelül 18 esemény történik másodpercenként, ez ötpercenként 5400 eseményt jelent. Mivel az auditnaplók eseményenként nagyjából 2 KB méretűek, ez összesen 10,8 MB adatot jelent. Így az adott ötperces időablakban a rendszer 43 üzenetet küld az eseményközpontra. 

Az alábbi táblázat egy alapszintű eseményközpont becsült havi költségét tartalmazza az eseményadatok mennyiségének függvényében az USA nyugati régiójában. Az [Event Hubs-díjkalkulátor](https://azure.microsoft.com/pricing/details/event-hubs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről.

| Naplókategória | Felhasználók száma | Események száma másodpercenként | Események száma ötperces időközönként | Adatmennyiség az egyes időablakokban | Üzenetek száma időközönként | Üzenetek száma havonta | Havi költség (becsült) |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Naplózás | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 dollár |
| Naplózás | 1,000 | 0,1 | 52 | 104 KB | 1 | 8640 | 10,80 dollár |
| Bejelentkezések | 1,000 | 178 | 53 400 | 106,8&nbsp;MB | 418 | 3 611 520 | 11,06 dollár |  

### <a name="log-analytics-cost-considerations"></a>A log Analytics költségvetési szempontok

A Log Analytics-munkaterület felügyeletével kapcsolatos költségek áttekintéséhez lásd: [költségek kezelése a Log Analytics és az adatmennyiség szabályozásával](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage).

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a szakasz az Azure AD-naplók az Azure Monitorban való kezelésével kapcsolatos gyakori kérdéseket válaszolja meg, és ismerteti az ismert problémákat.

**K: Melyik naplókat kezeli a rendszer?**

**V**: A bejelentkezési tevékenységnaplókat és az auditnaplókat egyaránt át lehet irányítani a szolgáltatás használatával, azonban ez a B2C-vel kapcsolatos auditeseményekre jelenleg még nem érvényes. Ha szeretné megtudni, hogy jelenleg milyen naplótípusok és mely szolgáltatásalapú naplók támogatottak, olvassa el [az auditnaplók sémáját](reference-azure-monitor-audit-log-schema.md) és [a bejelentkezési naplók sémáját](reference-azure-monitor-sign-ins-log-schema.md) ismertető cikkeket. 

---

**K: hogyan hamarosan után egy műveletet a hozzá tartozó naplók jelennek meg az eseményközpont?**

**V**: A naplóknak körülbelül két-öt percen belül kell megjelenniük az eseményközpontban a műveletek végrehajtása után. Az Event Hubsról a [Mi az Azure Event Hubs?](../../event-hubs/event-hubs-about.md) című cikkben talál további információt.

---

**K: hogyan hamarosan után egy műveletet a hozzá tartozó naplók megjelennek a storage-fiókomat?**

**V**: Az Azure Storage-fiókok esetében a késés 5–15 perc az egyes műveletek végrehajtása után.

---

**K: Mennyibe kerül az adataim tárolása?**

**V**: A tárolás díja a naplók méretétől és a választott megőrzési időtől függ. A bérlőkhöz tartozó, a létrehozott naplók mennyiségétől függő hozzávetőleges becsült költségekért lásd a [Tevékenységnaplók tárterületméretéről](#storage-size-for-activity-logs) szóló szakaszt.

---

**K: Mennyibe kerül az adataim eseményközpontba való streamelése?**

**V**: A streamelés költsége a percenként kapott üzenetmennyiségtől függ. Ez a cikk ismerteti a költségek kiszámításának módját, és felsorolja az üzenetek száma alapján kiszámított költségbecsléseket. 

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat az SIEM-rendszeremmel?**

**V**: Ezt kétféleképpen teheti meg:

- Az Azure Monitor és az Event Hubs együttes használatával streamelje a naplókat az SIEM-rendszerbe. Először [streamelje a naplókat egy eseményközpontba](tutorial-azure-monitor-stream-logs-to-event-hub.md), majd [állítsa be az SIEM-eszközt](tutorial-azure-monitor-stream-logs-to-event-hub.md#access-data-from-your-event-hub) a konfigurált eseményközponttal. 

- A [Reporting Graph API](concept-reporting-api.md) használatával elérheti az adatokat, amelyeket aztán saját szkriptjeivel leküldhet az SIEM-rendszerbe.

---

**K: Mely SIEM-eszközök támogatottak jelenleg?** 

**V**: Az Azure Monitort jelenleg a [Splunk](tutorial-integrate-activity-logs-with-splunk.md), a QRadar és a [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory) támogatja. Az összekötők működéséről [az Azure monitorozási adatok egy eseményközpontba külső eszközökben való használat céljából való streamelését](../../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md) ismertető cikkben talál további információt.

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat a Splunk-példányommal?**

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md), majd a vonatkozó lépéseket követve [integrálja a tevékenységnaplókat a Splunkkal](tutorial-integrate-activity-logs-with-splunk.md).

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat és a Sumo Logicot?** 

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), majd a vonatkozó lépéseket követve [telepítse az Azure AD-alkalmazást, és tekintse át az irányítópultokat a SumoLogicban](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**K: Elérhetem az adatokat egy eseményközpontban külső SIEM-eszköz használata nélkül is?** 

**V**: Igen. Az [Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) használatával is elérheti a naplókat az egyéni alkalmazásokban. 

---


## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók archiválása egy Storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Tevékenységnaplók irányítása egy eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Tevékenység-naplók integrálása a Log Analytics használatával](howto-integrate-activity-logs-with-log-analytics.md)
