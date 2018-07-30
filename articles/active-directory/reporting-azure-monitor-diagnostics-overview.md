---
title: Azure Active Directory-tevékenységnaplók az Azure Monitorban (előzetes verzió) | Microsoft Docs
description: Azure Active Directory-tevékenységnaplók az Azure Monitorban (előzetes verzió) – Áttekintés
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9b594360bc760fa9eec8ab9900c3aadcb10e9db6
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240108"
---
# <a name="azure-active-directory-activity-logs-in-azure-monitor-preview"></a>Azure Active Directory-tevékenységnaplók az Azure Monitorban (előzetes verzió)

Mostantól az Azure AD-tevékenységnaplókat az Azure Monitor használatával átirányíthatja saját tárfiókjába vagy az Event Hubsba is. Az Azure Monitorban nyilvános előzetes verzióban elérhető Azure Active Directory-naplókezelési szolgáltatás a következő képességeket biztosítja:

* Az auditnaplóit egy Azure-tárfiókba archiválhatja, így hosszabb ideig őrizheti meg az adatokat.
* Az auditnaplókat átstreamelheti egy Azure Event Hubba, ahol népszerű SIEM-eszközök (például a Splunk vagy a QRadar) használatával elemezheti őket.
* Az auditnaplókat eseményközpontba streamelve integrálhatja azokat saját egyéni naplókezelő megoldásaival is.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="supported-reports"></a>Támogatott jelentések

Ezzel a szolgáltatással a tevékenységnaplókat és a bejelentkezési tevékenységnaplókat átirányíthatja Azure-tárfiókjába, az Event Hubsba vagy valamilyen egyéni megoldásba. 

* **Naplók**: A [naplók tevékenységjelentés](active-directory-reporting-activity-audit-logs.md) hozzáférést nyújt a bérlőn elvégzett összes feladat előzményeihez.
* **Bejelentkezések**: A [bejelentkezések tevékenységjelentéssel](active-directory-reporting-activity-sign-ins.md) meghatározhatja, hogy ki hajtotta végre a naplók jelentés által jelentett feladatokat.

> [!NOTE]
> A B2C-hez kapcsolódó audit- és bejelentkezési tevékenységnaplók jelenleg nem támogatottak.
>

## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

* Azure-előfizetés. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Azure AD Ingyenes, Alapszintű, Prémium 1 vagy Prémium 2 [licenc](https://azure.microsoft.com/pricing/details/active-directory/) az Azure AD-naplók eléréséhez az Azure Portalon. 

Attól függően, hová szeretné irányítani a naplózási adatokat, a következők valamelyikére is szüksége lesz:

* Egy Azure-tárfiók, amelyen *ListKeys* jogosultsággal rendelkezik. Azt javasoljuk, hogy általános tárfiókot használjon, ne blobtárfiókot. A tárfiók díjszabásával kapcsolatban lásd az [Azure Storage-díjkalkulátort](https://azure.microsoft.com/pricing/calculator/?service=storage). 
* Egy Azure Event Hubs-névtér a külső megoldásokkal való integrációhoz.

> [!NOTE]
> Az Azure AD-bérlő *globális* vagy *biztonsági rendszergazdájának* kell lennie ahhoz, hogy hozzáférhessen az Azure AD-tevékenységnaplókhoz.
>

## <a name="cost-considerations"></a>Költségekkel kapcsolatos szempontok

Ha már rendelkezik Azure AD-licenccel, egy Azure-előfizetésre lesz szüksége a tárfiók és az eseményközpont beállításához. Az Azure-előfizetés ingyenesen érhető el, az Azure-erőforrások használatáért azonban fizetnie kell, beleértve az archiváláshoz használt tárfiókot és a streameléshez használt eseményközpontot. Az adatok mennyisége és ezáltal a vonatkozó költségek is a bérlő méretétől függően jelentős mértékben változhatnak. 

### <a name="storage-size-for-activity-logs"></a>Tevékenységnaplók tárterületmérete

Minden auditnapló-esemény körülbelül 2 KB adattárhelyet foglal el. Tehát egy 100 000 felhasználót számláló bérlőhöz, amely naponta körülbelül 1,5 millió eseményt hoz létre, hozzávetőleg napi 3 GB adattárhelyre lesz szüksége. Mivel az írási műveletek hozzávetőleg 5 perces kötegekben történnek, havonta várhatóan körülbelül 9000 írási művelettel számolhat. Az alábbi táblázat egy hozzávetőleges költségbecslést tartalmaz a bérlő méretének függvényében egy általános célú v2-es tárfiókra az USA nyugati régiójában, legalább egyéves megőrzéssel. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/details/storage/blobs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről. Minden auditnapló-esemény körülbelül 2 KB adattárhelyet foglal el. Tehát egy 100 000 felhasználót számláló bérlőhöz, amely naponta körülbelül 1,5 millió eseményt hoz létre, hozzávetőleg napi 3 GB adattárhelyre lesz szüksége. Mivel az írási műveletek hozzávetőleg 5 perces kötegekben történnek, havonta várhatóan körülbelül 9000 írási művelettel számolhat. Az alábbi táblázat egy hozzávetőleges költségbecslést tartalmaz a bérlő méretének függvényében egy általános célú v2-es tárfiókra az USA nyugati régiójában, legalább egyéves megőrzéssel. Az [Azure Storage-díjkalkulátor](https://azure.microsoft.com/pricing/details/storage/blobs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről. 

| Naplókategória | Felhasználók száma | Események száma naponta | Hozzávetőleges havi adatmennyiség | Hozzávetőleges havi költség | Hozzávetőleges éves költség |
|--------------|-----------------|----------------------|--------------------------------------|----------------------------|---------------------------|
| Naplózás | 100 000 | 1,5 millió | 90 GB | 1,93 dollár | 23,12 dollár |
| Naplózás | 1000 | 15 000 | 900 MB | 0,02 dollár | 0,24 dollár |
| Bejelentkezések | 1000 | 34 800 | 4 GB | 0,13 dollár | 1,56 dollár |
| Bejelentkezések | 100 000 | 15 millió | 1,7 TB | 35,41 dollár | 424,92 dollár | 


### <a name="event-hub-messages-for-activity-logs"></a>Tevékenységnaplók eseményközpont-üzenetei

A rendszer az eseményeket körülbelül ötperces intervallumokba kötegeli, majd egyetlen üzenetben küldi el, amely az adott időablakba tartozó összes eseményt tartalmazza. Az eseményközpontban az egyes üzenetek mérete legfeljebb 256 KB lehet, és ha az adott időablakba tartozó üzenetek összesített mérete meghaladja ezt a mennyiséget, a rendszer több üzenetet küld. 

Például egy több mint 100 000 felhasználót számláló bérlőn általában körülbelül 18 esemény történik másodpercenként, ami 5 percenként 5400 eseményt jelent. Mivel az auditnaplók eseményenként nagyjából 2 KB méretűek, ez összesen 10,8 MB adatot jelent, tehát az adott 5 perces időablakban a rendszer 43 üzenetet küld az eseményközpontra. Az alábbi táblázat egy alapszintű eseményközpont hozzávetőleges költségét tartalmazza az eseményadatok mennyiségének függvényében az USA nyugati régiójában. Az [Event Hubs-díjkalkulátor](https://azure.microsoft.com/pricing/details/event-hubs/) használatával ennél pontosabb becslést is készíthet az alkalmazása várható adatmennyiségéről.

| Naplókategória | Felhasználók száma | Események száma másodpercenként | Események száma az 5 perces időablakokban | Adatmennyiség az egyes időablakokban | Üzenetek száma az egyes időablakokban | Üzenetek száma havonta | Hozzávetőleges havi költség |
|--------------|-----------------|-------------------------|----------------------------------------|---------------------|---------------------------------|------------------------------|----------------------------|
| Naplózás | 100 000 | 18 | 5400 | 10,8 MB | 43 | 371 520 | 10,83 dollár |
| Naplózás | 1000 | 0,1 | 52 | 104 KB | 1 | 8640 | 10,8 dollár |
| Bejelentkezések | 1000 | 178 | 53 400 | 106,8 MB | 418 | 3 611 520 | 11,06 dollár |  


## <a name="frequently-asked-questions"></a>Gyakori kérdések

Ez a szakasz az Azure Active Directory-naplók az Azure Monitorban való kezelésével kapcsolatos gyakori kérdéseket tartalmazza.

**K: Hol érdemes elkezdenem?** 

**V:** Kezdje az [Áttekintés](reporting-azure-monitor-diagnostics-overview.md) szakasszal, ahol megtudhatja, mire lesz szüksége a szolgáltatás telepítéséhez. Miután megismerte az előfeltételeket, tekintse át az oktatóanyagokat, amelyek segítenek konfigurálni a rendszert és átirányítani a naplókat az Event Hubsba.

---

**K: Melyik naplókat kezeli a rendszer?**

**V:** A bejelentkezési és az auditnaplókat egyaránt át lehet irányítani a szolgáltatás használatával, azonban ez a B2C-vel kapcsolatos auditeseményekre jelenleg még nem érvényes. Olvassa el [az auditnaplók](reporting-azure-monitor-diagnostics-audit-log-schema.md) és [a bejelentkezési naplók sémáját](reporting-azure-monitor-diagnostics-sign-in-log-schema.md) ismertető cikkeket, amelyekből megtudhatja, jelenleg a naplók mely típusai és mely szolgáltatásalapú naplók támogatottak. 

---

**K: Az egyes műveletek után milyen hamar jelennek meg a vonatkozó naplók az Event Hubsban?**

**V:** A naplóknak körülbelül kettő–öt percen belül kell megjelennie az Event Hubsban a műveletek végrehajtását követően. Az Event Hubsszal kapcsolatos további információkat a [Mi az Event Hubs?](/azure/event-hubs/event-hubs-what-is-event-hubs.md) című cikk tartalmazza.

---

**K: Az egyes műveletek után milyen hamar jelennek meg a vonatkozó naplók a tárfiókokban?**

**V:** Az Azure-tárfiókok esetében a késés 5–15 perc az egyes műveletek végrehajtását követően.

---

**K: Mennyibe kerül az adataim tárolása?**

**V:** A tárolás díja a naplók méretének, valamint a választott megőrzési időnek a függvénye. A bérlők a naplók mennyiségétől függő hozzávetőleges becsült költségeiért lásd az [Áttekintés](reporting-azure-monitor-diagnostics-overview.md) szakaszt.

---

**K: Mennyibe kerül az adataim átstreamelése az Event Hubsba?**

**V:** A streamelés költsége a percenként kapott üzenetmennyiségtől függ. A költségek számításának módjával kapcsolatos információkért, valamint az üzenetek számától függő hozzávetőleges becslésért lásd az [Áttekintés](reporting-azure-monitor-diagnostics-overview.md) szakaszt. 

---

**K: Mely SIEM-eszközök támogatottak jelenleg?** 

**V:** Jelenleg az Azure Monitort a Splunk, a QRadar és a Sumologic támogatja. Azonban a Splunk az egyetlen SIEM-eszköz, amely az Azure Active Directory-naplók esetében is támogatott. Az összekötők működésével kapcsolatos további információkért lásd [az Azure monitorozási adatok egy eseményközpontba külső eszközökben való használat céljából való streamelését](/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs) ismertető cikket.

---

**K: Elérhetem az adatokat az Event Hubsban külső SIEM-eszköz használata nélkül is?** 

**V:** Igen, az [Event Hubs API](/azure/event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph) használatával is elérheti a naplókat az egyéni alkalmazásokban. 

---


## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók archiválása egy tárfiókba](reporting-azure-monitor-diagnostics-azure-storage-account.md)
* [Tevékenységnaplók irányítása egy Event Hubs-eseményközpontba](reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [További információk az Azure Diagnostics-naplókról](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)