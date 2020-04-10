---
title: Az Azure Active Directory tevékenységnaplói az Azure Monitorban | Microsoft dokumentumok
description: Bevezetés az Azure Active Directory-tevékenységnaplókba az Azure Monitorban
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/09/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 595c87d2b8182c9044baeb2662e34871d9e52c52
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991245"
---
# <a name="azure-ad-activity-logs-in-azure-monitor"></a>Az Azure AD-tevékenységnaplók az Azure Monitorban

Az Azure Active Directory (Azure AD) tevékenységnaplók at több végpontra irányíthatja a hosszú távú megőrzésés adatelemzésérdekében. Ez a funkció lehetővé teszi, hogy:

* Archiválja az Azure AD-tevékenységnaplókat egy Azure-tárfiókba, hogy hosszú ideig megőrizze az adatokat.
* Az Azure AD-tevékenységnaplók streamelése egy Azure-eseményközpontba elemzés céljából, a népszerű biztonsági információk és eseménykezelési (SIEM) eszközök, például a Splunk és a QRadar használatával.
* Integrálja az Azure AD-tevékenységnaplókat a saját egyéni naplómegoldásaival, és streamelje őket egy eseményközpontba.
* Az Azure AD-tevékenységnaplók at küldhet az Azure Monitor-naplókba, hogy gazdag vizualizációkat, figyelést és riasztást kapjon a csatlakoztatott adatokon.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="supported-reports"></a>Támogatott jelentések

Az Azure AD naplózási naplóit és bejelentkezési naplóit az Azure storage-fiókjába, az eseményközpontba, az Azure Monitor-naplókba vagy az egyéni megoldásba irányíthatja ezzel a funkcióval. 

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
* Az Azure Log Analytics-munkaterület a naplók küldéséhez az Azure Monitor naplók.

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok

Ha már rendelkezik Azure AD-licenccel, egy Azure-előfizetésre lesz szüksége a tárfiók és az eseményközpont beállításához. Az Azure-előfizetés ingyenesen érhető el, az Azure-erőforrások használatáért azonban fizetnie kell, beleértve az archiváláshoz használt tárfiókot és a streameléshez használt eseményközpontot. Az adatok mennyisége és ezáltal a vonatkozó költségek is a bérlő méretétől függően jelentős mértékben változhatnak. 

### <a name="storage-size-for-activity-logs"></a>Tevékenységnaplók tárterületmérete

Minden auditnapló-esemény körülbelül 2 KB adattárhelyet foglal el. A bejelentkezési eseménynaplók körülbelül 4 KB adattárolást jelentenek. Egy 100 000 felhasználót számláló bérlőhöz, amely naponta körülbelül 1,5 millió eseményt hoz létre, hozzávetőleg napi 3 GB adattárhelyre lesz szüksége. Mivel az írási műveletek hozzávetőleg ötperces kötegekben történnek, havonta várhatóan körülbelül 9000 írási művelettel számolhat. 


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

### <a name="azure-monitor-logs-cost-considerations"></a>Az Azure Monitor naplózza a költséggel kapcsolatos szempontokat



| Naplókategória       | Felhasználók száma | Napi események | Események havonta (30 nap) | Havi költség USD-ben (est) |
| :--                | ---             | ---            | ---                        | --:                          |
| Naplózás és bejelentkezések | 100 000         | 16,500,000     | 495,000,000                |  $1093.00                       |
| Naplózás              | 100 000         | 1,500,000      | 45,000,000                 |  $246.66                     |
| Bejelentkezések           | 100 000         | 15,000,000     | 450,000,000                |  $847.28                     |










Az Azure Monitor-naplók kezelésével kapcsolatos költségek áttekintéséhez olvassa el a Költségek kezelése az [adatok mennyiségének és megőrzésének szabályozásával az Azure Monitor naplóiban című témakört.](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-cost-storage)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a szakasz az Azure AD-naplók az Azure Monitorban való kezelésével kapcsolatos gyakori kérdéseket válaszolja meg, és ismerteti az ismert problémákat.

**K: Melyik naplókat kezeli a rendszer?**

**V**: A bejelentkezési tevékenységnaplókat és az auditnaplókat egyaránt át lehet irányítani a szolgáltatás használatával, azonban ez a B2C-vel kapcsolatos auditeseményekre jelenleg még nem érvényes. Ha szeretné megtudni, hogy jelenleg milyen naplótípusok és mely szolgáltatásalapú naplók támogatottak, olvassa el [az auditnaplók sémáját](reference-azure-monitor-audit-log-schema.md) és [a bejelentkezési naplók sémáját](reference-azure-monitor-sign-ins-log-schema.md) ismertető cikkeket. 

---

**K: Milyen hamar jelennek meg a megfelelő naplók az eseményközpontban a művelet után?**

**V**: A naplóknak körülbelül két-öt percen belül kell megjelenniük az eseményközpontban a műveletek végrehajtása után. Az Event Hubsról a [Mi az Azure Event Hubs?](../../event-hubs/event-hubs-about.md) című cikkben talál további információt.

---

**K: Milyen hamar jelennek meg a megfelelő naplók a tárfiókomban?**

**V**: Az Azure Storage-fiókok esetében a késés 5–15 perc az egyes műveletek végrehajtása után.

---

**K: Mi történik, ha egy rendszergazda módosítja a diagnosztikai beállítás megőrzési időszakát?**

**V**: Az új adatmegőrzési szabály a módosítás után gyűjtött naplókra lesz alkalmazva. A házirend módosítása előtt gyűjtött naplókat ez nem érinti.

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

**V**: **A**: Jelenleg az Azure Monitor t támogatja [Splunk](tutorial-integrate-activity-logs-with-splunk.md), IBM QRadar, [Sumo Logic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory), [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight), LogRhythm, és Logz.io. Az összekötők működéséről [az Azure monitorozási adatok egy eseményközpontba külső eszközökben való használat céljából való streamelését](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ismertető cikkben talál további információt.

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat a Splunk-példányommal?**

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md), majd a vonatkozó lépéseket követve [integrálja a tevékenységnaplókat a Splunkkal](tutorial-integrate-activity-logs-with-splunk.md).

---

**K: Hogyan integrálhatom az Azure AD-tevékenységnaplókat és a Sumo Logicot?** 

**V**: Először [irányítsa át az Azure AD-tevékenységnaplókat egy eseményközpontba](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory), majd a vonatkozó lépéseket követve [telepítse az Azure AD-alkalmazást, és tekintse át az irányítópultokat a SumoLogicban](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards).

---

**K: Elérhetem az adatokat egy eseményközpontban külső SIEM-eszköz használata nélkül is?** 

**A:** Igen. Az [Event Hubs API](../../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) használatával is elérheti a naplókat az egyéni alkalmazásokban. 

---


## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók archiválása egy Storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Tevékenységnaplók irányítása egy eseményközpontba](quickstart-azure-monitor-stream-logs-to-event-hub.md)
* [Tevékenységnaplók integrálása az Azure Monitorral](howto-integrate-activity-logs-with-log-analytics.md)
