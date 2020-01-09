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
ms.openlocfilehash: 7174003485d51cf582c798c4b18404b1b72de0fb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530952"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

> [!NOTE]
> A biztonsági megoldások egy részhalmaza a 2019. július 31-én megszűnt. További információ és alternatív szolgáltatások: [Security Center szolgáltatások kivonása (július 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center segítségével az integrált partnermegoldások egy optimalizált folyamat mentén helyezhetőek üzembe. Az olyan megoldások esetében, mint például az antimalware és a sebezhetőségi felmérés, a Security Center képes kiépíteni az ügynököt a virtuális gépekre. A tűzfalak esetében Security Center a szükséges hálózati konfiguráció nagy részét is elvégezheti.
- **Integrált észlelések**: A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Az integrált biztonsági megoldások jelenleg a [Qualys](https://www.qualys.com/public-cloud/#azure) és a [Rapid7](https://www.rapid7.com/products/insightvm/) , valamint a Microsoft Application Gateway webalkalmazási tűzfal általi sebezhetőségi felmérést is tartalmaznak.

> [!NOTE]
> Security Center nem telepíti a Microsoft monitoring agentet a partner virtuális készülékekre, mert a legtöbb biztonsági gyártó tiltja a készülékeken futó külső ügynököket.
>
>

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Más biztonsági adatforrásokat is összekapcsolhat, beleértve a helyszínen vagy más felhőkben futó számítógépeket is.

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. Jelentkezzen be az [Azure portálra](https://azure.microsoft.com/features/azure-portal/).

2. A **Microsoft Azure menüben** válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

3. A Security Center menüjében válassza a **Biztonsági megoldások** elemet.

   ![Security Center – Áttekintés](./media/security-center-partner-integration/overview.png)

A **biztonsági megoldásokban**megtekintheti az integrált Azure biztonsági megoldások állapotát, és alapszintű felügyeleti feladatokat is futtathat.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **csatlakoztatott megoldások** szakasz olyan biztonsági megoldásokat tartalmaz, amelyek jelenleg Security Centerhoz vannak csatlakoztatva. Az egyes megoldások állapotát is megjeleníti.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

A partneri megoldások állapota a következőket teheti:

* Kifogástalan (zöld) – nincsenek állapotbeli problémák.
* Sérült (piros) – probléma merül fel, ami azonnali beavatkozást igényel.
* Állapottal kapcsolatos problémák (narancssárga) – a megoldás leállította az állapotáról szóló jelentést.
* Nem jelentett (szürke) – a megoldás még nem jelentett semmit, és nem érhető el állapotadatok. Előfordulhat, hogy A megoldás állapota nem jelenthető le, ha a közelmúltban csatlakozott, és még mindig települ.

> [!NOTE]
> Ha az állapotadatok nem érhetők el, Security Center az utolsó fogadott esemény dátumát és időpontját jeleníti meg, amely jelzi, hogy a megoldás jelentéskészítési vagy nem. Ha nem állnak rendelkezésre állapotadatok, és az elmúlt 14 napban nem érkeztek riasztások, Security Center azt jelzi, hogy a megoldás sérült, vagy nem jelent jelentést.
>
>

1. Válassza a **nézet** lehetőséget További információk és beállítások megadásához, például:

   - **Megoldás konzolja**. Megnyitja a megoldás kezelési élményét.
   - **Virtuális gép csatolása**. Megnyitja az alkalmazások összekapcsolása lapot. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **Megoldás törlése**.
   - **Konfigurálás**.

   ![Partneri megoldás részletei](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Felderített megoldások

Security Center automatikusan felderíti az Azure-ban futó biztonsági megoldásokat, de nem kapcsolódik Security Centerhoz, és megjeleníti a megoldásokat a **felderített megoldások** szakaszban. Ezen megoldások közé tartoznak az Azure-megoldások, például a [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)és a partneri megoldások.

> [!NOTE]
> A Felderített megoldások funkcióhoz a Standard szintű Security Centerre az előfizetés szintjén van szükség. A díjszabással kapcsolatos további információkért tekintse meg a [díjszabást](security-center-pricing.md) .
>
>

Válassza a **Kapcsolódás** a megoldás alatt lehetőséget a Security Center való integráláshoz és a biztonsági riasztások értesítéséhez.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Az adatexportálás SIEM-be

> [!NOTE]
> Az adatok SIEM-re való exportálásához szükséges egyszerűbb (jelenleg előzetes verzióban elérhető) módszer részleteiért lásd: [biztonsági riasztások és javaslatok exportálása (előzetes verzió)](continuous-export.md). Az új metódus nem használja a műveletnapló közvetítőként való használatát, és lehetővé teszi a közvetlen exportálást Security Centerról Event Hubsra (majd a SIEM-re), továbbá támogatja a biztonsági javaslatok exportálását is.


A SIEM-ket és más figyelési eszközöket úgy is konfigurálhatja, hogy Azure Security Center eseményeket kapjon.

Azure Security Center összes eseménye Azure Monitor Azure- [tevékenység naplójában](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)lesz közzétéve. A Azure Monitor [összevont folyamattal](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) továbbítja az adatstreamet egy Event hub-ba, ahol azután lehívható a figyelési eszközbe.

A következő szakaszok azt ismertetik, hogyan konfigurálható az Event hub adatfolyamként való továbbítása. A lépések azt feltételezik, hogy már van Azure Security Center konfigurálva az Azure-előfizetésében.

### <a name="high-level-overview"></a>Áttekintés

![Magas szintű áttekintés](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi az a SIEM számára elérhető Azure biztonsági rendszer?

Ebben a verzióban elérhetővé tesszük a [biztonsági riasztásokat.](../security-center/security-center-managing-and-responding-alerts.md) A következő kiadásokban a biztonsági javaslatokkal gazdagítjuk az adathalmazt.

### <a name="how-to-set-up-the-pipeline"></a>A folyamat beállítása

#### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Mielőtt elkezdené, [hozzon létre egy Event Hubs névteret](../event-hubs/event-hubs-create.md) – ez az összes megfigyelési adattal kapcsolatos cél.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Azure-tevékenység naplójának továbbítása Event Hubs

Tekintse meg a következő cikk [stream Activity naplóját Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Partner SIEM-összekötő telepítése 

A monitorozási adatait az Azure Monitor segítségével könnyedén integrálhatja a partner SIEM-és figyelési eszközeivel.

A [támogatott Siem](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-platform-logs-sent-to-an-event-hub) -listák listáját a következő cikkben tekintheti meg

### <a name="example-for-querying-data"></a>Példa az adatlekérdezésre 

Íme néhány splunk-lekérdezés, amellyel lehívhatja a riasztási adatforrásokat:

| **A lekérdezés leírása** | **Lekérdezés** |
|----|----|
| Minden riasztás| index = fő Microsoft. Security/Locations/riasztások|
| Műveletek számának összefoglalása a nevük alapján| index = Main forrás típusa = "Adorján: biztonság" \| Table operationName \| stats Count by operationName|
| Riasztások információinak beolvasása: idő, név, állapot, azonosító és előfizetés | index = fő Microsoft. Security/Locations/riasztások \| tábla \_idő, tulajdonságok. eventName, állapot, tulajdonságok. operationId, am_subscriptionId |


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
