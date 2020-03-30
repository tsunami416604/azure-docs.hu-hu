---
title: Biztonsági megoldások integrálása az Azure Security Centerbe | Microsoft Docs
description: Megtudhatja, hogy az Azure Security Center hogyan integrálható a partnerekkel az Azure-erőforrások általános biztonságának növelése érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245381"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

> [!NOTE]
> 2019. július 31-én a biztonsági megoldások egy részét megszüntették. További információ és alternatív szolgáltatások: [Retirement of Security Center features (July 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center segítségével az integrált partnermegoldások egy optimalizált folyamat mentén helyezhetőek üzembe. Olyan megoldások, mint a kártevőirtó és a biztonsági rés felmérése, a Security Center kiépítheti az ügynököt a virtuális gépeken. Tűzfalak esetében a Security Center gondoskodik a szükséges hálózati konfiguráció nagy részéről.
- **Integrált észlelések:** A partnermegoldások biztonsági eseményei automatikusan összegyűjtésre, összesítésre kerülnek, és a Security Center riasztásainak és incidenseinek részeként jelennek meg. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Jelenleg az integrált biztonsági megoldások közé tartozik a [Qualys,](https://www.qualys.com/public-cloud/#azure) valamint a [Rapid7](https://www.rapid7.com/products/insightvm/) és a Microsoft Application Gateway webalkalmazás tűzfalának biztonsági résfelmérése.

> [!NOTE]
> A Security Center nem telepíti a Microsoft Monitoring Agent-et a partner virtuális készülékekre, mert a legtöbb biztonsági szállító tiltja a készülékeiken futó külső ügynököket.
>
>

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Más biztonsági adatforrásokat is csatlakoztathat, beleértve a helyszíni vagy más felhőkön futó számítógépeket is.

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. Jelentkezzen be az [Azure Portalra.](https://azure.microsoft.com/features/azure-portal/)

2. A **Microsoft Azure menüben**válassza a **Security Center (Biztonsági központ)** lehetőséget. Megnyílik a **Security Center – Áttekintés** képernyő.

3. A Security Center menüjében válassza a **Biztonsági megoldások** elemet.

   ![Security Center – Áttekintés](./media/security-center-partner-integration/overview.png)

A **Biztonsági megoldások**ban megtekintheti az integrált Azure biztonsági megoldások állapotát, és alapvető felügyeleti feladatokat futtathat.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz olyan biztonsági megoldásokat tartalmaz, amelyek jelenleg a Security Centerhez csatlakoznak. Azt is mutatja, az egyes megoldások állapotát.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

A partneri megoldás állapota a következő lehet:

* Egészséges (zöld) - nincs egészségügyi probléma.
* Egészségtelen (piros) - van egy egészségügyi probléma, amely azonnali figyelmet igényel.
* Egészségügyi problémák (narancssárga) - a megoldás leállt az egészségi állapotának jelentésével.
* Nem jelentett (szürke) – a megoldás még nem jelentett semmit, és nem állnak rendelkezésre egészségügyi adatok. Előfordulhat, hogy a megoldás állapota nem lesz bejelentve, ha nemrég csatlakozott, és még mindig telepítve van.

> [!NOTE]
> Ha az állapotadatok nem állnak rendelkezésre, a Security Center megjeleníti az utolsó kapott esemény dátumát és időpontját, jelezve, hogy a megoldás jelentést tesz-e vagy sem. Ha nem állnak rendelkezésre állapotadatok, és az elmúlt 14 napban nem érkezett riasztás, a Security Center azt jelzi, hogy a megoldás nem kifogástalan, vagy nem jelent jelentést.
>
>

1. Válassza a **NÉZET** lehetőséget a további információkért és a következő beállításokért:

   - **Megoldáskonzol**. Megnyitja a megoldás felügyeleti élményét.
   - **Vm csatolása**. Megnyitja az Alkalmazások csatolása lapot. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **A megoldás törlése**.
   - **Konfigurálja**a beállítást.

   ![Partneri megoldás részletei](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Felderített megoldások

A Security Center automatikusan felderíti az Azure-ban futó, de a Security Centerhez nem kapcsolódó biztonsági megoldásokat, és megjeleníti a megoldásokat a **Felderített megoldások** szakaszban. Ezek a megoldások közé tartoznak az Azure-megoldások, például [az Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)és a partnermegoldások.

> [!NOTE]
> A Felderített megoldások funkcióhoz a Standard szintű Security Centerre az előfizetés szintjén van szükség. További információ a [díjszabási](security-center-pricing.md) szintekről.
>
>

Válassza a **CONNECT** lehetőséget a Biztonsági központtal integrálható megoldás alatt, és értesítést kapjon a biztonsági riasztásokról.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Adatok exportálása SIEM-be

> [!NOTE]
> Az adatok SIEM-be történő exportálásának egyszerűbb (jelenleg előzetes verzióban elérhető) módszeréről a [Biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md)című témakörben talál részleteket. Az új módszer nem használja a tevékenységnaplót közvetítőként, és lehetővé teszi a biztonsági központból az Eseményközpontokba (majd a SIEM-be) történő közvetlen exportálást, támogatja a biztonsági javaslatok exportálását is.


Beállíthatja a SIEMs vagy más figyelési eszközök fogadására Azure Security Center események.

Az Azure Security Center összes eseménye megjelenik az Azure Monitor [Azure-tevékenységnaplójában.](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) Az Azure Monitor [egy konszolidált folyamat](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) ot használ az adatok streameléséhez egy Event Hub, ahol ezután belehet vonni a figyelési eszköz.

A következő szakaszok azt ismertetik, hogyan konfigurálhatja az eseményközpontba adatfolyamként továbbítandó adatokat. A lépések feltételezik, hogy már rendelkezik az Azure Security Center konfigurálva az Azure-előfizetésben.

### <a name="high-level-overview"></a>Magas szintű áttekintés

![Magas szintű áttekintés](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi kvanazd az Azure biztonsági adatai a SIEM számára?

Ebben a verzióban elérhetővé tesszük a [biztonsági riasztásokat.](../security-center/security-center-managing-and-responding-alerts.md) A következő kiadásokban biztonsági javaslatokkal gazdagítjuk az adatkészletet.

### <a name="how-to-set-up-the-pipeline"></a>A csővezeték beállítása

#### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Mielőtt elkezdené, [hozzon létre egy Event Hubs névteret](../event-hubs/event-hubs-create.md) - az összes figyelési adat célját.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Azure-tevékenységnapló streamelése az eseményközpontokba

Tekintse meg a következő [cikkstream tevékenységnaplóját az Eseményközpontokba.](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Partner SIEM-csatlakozó telepítése 

A figyelési adatok egy Eseményközpontba való átirányítása az Azure Monitor segítségével könnyedén integrálható a partner SIEM-mel és a figyelési eszközökkel.

A [támogatott siem-ek](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)listáját az alábbi cikktartalmazza.

### <a name="example-for-querying-data"></a>Példa adatok lekérdezésére 

Íme néhány splunk lekérdezés, amelysegítségével riasztási adatokat lehet lehallgatni:

| **A lekérdezés leírása** | **Lekérdezés** |
|----|----|
| All Alerts| index=fő Microsoft.Security/locations/alerts|
| Műveletek számának összegzése a nevük alapján| index=main sourcetype="amal:security" \| tábla \| operationName stats count by operationName|
| Értesítések beszerezése információk: Idő, név, állam, azonosító és előfizetés | index=main \| Microsoft.Security/locations/alerts \_table time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Center ről az alábbi cikkben olvashat bővebben:

* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.