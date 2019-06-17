---
title: Biztonsági megoldások integrálása az Azure Security Centerbe | Microsoft Docs
description: Megtudhatja, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: d94567800a9fd020784c9cb07b2c6824cd032509
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064279"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

> [!NOTE]
> Biztonsági megoldások egy részét a 2019. július 31-én megszűnik. További információkat és más szolgáltatások: [a Security Center nyugdíjazási szolgáltatásokat (július 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezési**: A Security Center kínál az integrált partneri megoldások fejlett jogosultságkiosztási. A kártevőirtó, sebezhetőségfelmérő és hasonló megoldások esetében a Security Center képes biztosítani a szükséges ügynököt a virtuális gépeken, a tűzfalberendezések esetében pedig elintézi a szükséges hálózati konfigurációs feladatok nagy részét.
- **Integrált észlelések**: A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Jelenleg, integrált biztonsági megoldások érhetők el a biztonságirés-értékelési [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) és [Rapid7](https://www.rapid7.com/products/insightvm/) és a Microsoft Application Gateway webalkalmazási tűzfal.

> [!NOTE]
> A Security Center nem telepíti a Microsoft Monitoring Agentet a partner virtuális berendezésekre, mert a legtöbb biztonsági megoldásokkal foglalkozó forgalmazó tiltja a külső ügynökök futtatását a saját berendezésein.
>
>

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Többek között a számítógépek a helyszínen futó egyéb biztonsági adatforrásokat is csatlakoztathat vagy más felhőkben.

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

2. A **Microsoft Azure menüben** válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

3. A Security Center menüjében válassza a **Biztonsági megoldások** elemet.

   ![Security Center – Áttekintés](./media/security-center-partner-integration/overview.png)

A **Biztonsági megoldások** területen megtekintheti az Azure integrált biztonsági megoldásainak állapotinformációit, valamint alapszintű felügyeleti feladatokat hajthat végre. Emellett egyéb típusú biztonsági adatforrásokat is csatlakoztathat, például Common Event Format (CEF) formátumú Azure Active Directory Identity Protection-riasztásokat és tűzfalnaplókat.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz a Security Centerhez jelenleg csatlakozó biztonsági megoldásokat, valamint az egyes megoldások állapotával kapcsolatos adatokat tartalmazza.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Egy partneri megoldás állapota lehet:

* Kifogástalan (zöld) – nincs az állapottal kapcsolatos probléma.
* Nem megfelelő (piros) – azonnali figyelmet igénylő állapottal kapcsolatos probléma.
* Állapotbeli problémák (narancs) – a megoldás már le van állítva állapotára.
* Nem jelentett (szürke) – a megoldás még nem jelentett semmit, mégis megoldás állapota lehet nem jelentett, ha azt a nemrégiben csatlakoztatott és még telepítés, vagy nem egészségügyi érhetők el adatok.

> [!NOTE]
> Állapotadatok nem érhető el, ha a Security Center a dátum és idő az utolsó esemény jelzi, hogy a megoldás e jelentést vagy sem fogadott jeleníti meg. Ha nem egészségügyi érhetők el adatok és riasztások az elmúlt 14 napon belül érkezik, a Security Center azt jelzi, hogy a megoldás nem megfelelőnek vagy nem jelentéskészítési.
>
>

1. Válassza ki **NÉZET** további információért és a beállítások, amely tartalmazza:

   - **Megoldáskonzol**. Megnyílik a kezelést biztosít a megoldáshoz.
   - **Virtuális Géphez csatolni**. Az alkalmazások összekapcsolása panel megnyílik. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **Megoldás törlése**.
   - **Konfigurálása**.

   ![Partneri megoldás részletei](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Felderített megoldások

A Security Center automatikusan felderíti az Azure-ban futó, azonban a Security Centerhez nem csatlakoztatott biztonsági megoldásokat, és azokat a **Felderített megoldások** szakaszban jeleníti meg. Ez az Azure-beli megoldásokat, például az [Azure AD Identity Protectiont](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), valamint a partnermegoldásokat is tartalmazza.

> [!NOTE]
> A Felderített megoldások funkcióhoz a Standard szintű Security Centerre az előfizetés szintjén van szükség. A Security tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

Az egyes megoldások alatt a **CSATLAKOZTATÁS** gombra kattintva integrálhatja azokat a Security Centerbe, és értesülhet a biztonsági riasztásokról.

![Felderített megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

A Security Center az előfizetésben üzembe helyezett azon megoldásokat is felderíti, amelyek képesek Common Event Format (CEF) formátumú naplókat továbbítani. Ismerje meg, hogyan [csatlakoztathat CEF-naplókat használó biztonsági megoldásokat](quick-security-solutions.md) a Security Centerhez.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Adatok exportálása siem-mel

Az Azure Security Center által feldolgozott események az Azure-ban közzétett [tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), a napló egyik Azure monitoron keresztül elérhető típusokat. Az Azure Monitor egy konszolidált folyamatot a figyelési adatokat egy SIEM-eszközével történő útválasztáshoz kínál. Ez történik, a streamelési adatokat egy eseményközpontba, ahol azt is majd le kell kérnie egy partner eszközt.

A függőleges vonal használja a [egyetlen folyamat Azure Monitoring](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) hozzáférés a monitorozási adatok lekérése az Azure-környezet számára. Ez lehetővé teszi, hogy könnyedén létrehozhasson siem-EK és figyelési eszközök az adatok felhasználásához.

A következő szakaszok ismertetik, hogyan konfigurálható egy eseményközpontba is streamelhetők adatokat. A lépések feltételezik, hogy már rendelkezik az Azure Security Center az Azure-előfizetéshez konfigurált.

Áttekintés

![Magas szintű áttekintése](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi az az Azure biztonsági adatok siem-be közzétett?

Ebben a verzióban elérhetővé tesszük a [biztonsági riasztásokat.](../security-center/security-center-managing-and-responding-alerts.md) A jövőbeli kiadásokban az adatkészlet, a biztonsági javaslatok azt fogja bővítését.

### <a name="how-to-setup-the-pipeline"></a>A folyamat beállítása

#### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Mielőtt elkezdené, kell [Event Hubs-névtér létrehozása](../event-hubs/event-hubs-create.md). A névtér és Eseményközpont az összes monitorozási adatot célját.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Event hubs Azure tevékenységnapló Stream

Tekintse meg a következő cikkben [tevékenységnapló streamelése az Event hubs szolgáltatásba](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Partneri SIEM-összekötő telepítése 

A figyelési adatok útválasztást egy eseményközpontba, és az Azure Monitor lehetővé teszi könnyen integrálhatja a partneri SIEM és figyelési eszközöket.

Tekintse meg a következő hivatkozásra kattintva megtekintheti [támogatott siem-ektől](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Például az adatok lekérdezése 

Íme néhány Splunk lekérdezések, amelyek segítségével kérje le a riasztási adatokat:

| **Lekérdezés leírása** | **Lekérdezés** |
|----|----|
| Az összes riasztás| index=main Microsoft.Security/locations/alerts|
| Műveletek száma összefoglalója neve szerint| index = fő forrás típusa = "amal: biztonsági" \| tábla operationName \| statisztikák szerint operationName száma|
| Riasztások adatainak beolvasása: Idő, neve, állam, ID és az előfizetés | index = fő Microsoft.Security/locations/alerts \| tábla \_idő, properties.eventName, állam, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
