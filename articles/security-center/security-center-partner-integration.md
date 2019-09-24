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
ms.openlocfilehash: 25e7262842de2bcf3823a51dfe6d97b5558b16fe
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201743"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

> [!NOTE]
> A biztonsági megoldások egy részhalmaza a 2019. július 31-én megszűnt. További információ és alternatív szolgáltatások: [Security Center szolgáltatások kivonása (július 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center az integrált partneri megoldások egyszerűsített üzembe helyezését kínálja. A kártevőirtó, sebezhetőségfelmérő és hasonló megoldások esetében a Security Center képes biztosítani a szükséges ügynököt a virtuális gépeken, a tűzfalberendezések esetében pedig elintézi a szükséges hálózati konfigurációs feladatok nagy részét.
- **Integrált észlelések**: A partnermegoldásoktól érkező biztonsági eseményeket a rendszer automatikusan összegyűjti, összesíti és megjeleníti a Security Center riasztásainak és incidenseinek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egységes állapot figyelése és kezelése**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Az integrált biztonsági megoldások jelenleg a [Qualys](https://www.qualys.com/public-cloud/#azure) és a [Rapid7](https://www.rapid7.com/products/insightvm/) , valamint a Microsoft Application Gateway webalkalmazási tűzfal általi sebezhetőségi felmérést is tartalmaznak.

> [!NOTE]
> A Security Center nem telepíti a Microsoft Monitoring Agentet a partner virtuális berendezésekre, mert a legtöbb biztonsági megoldásokkal foglalkozó forgalmazó tiltja a külső ügynökök futtatását a saját berendezésein.
>
>

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Más biztonsági adatforrásokat is összekapcsolhat, beleértve a helyszínen vagy más felhőkben futó számítógépeket is.

![Partnermegoldások integrációja](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).

2. A **Microsoft Azure menüben** válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

3. A Security Center menüjében válassza a **Biztonsági megoldások** elemet.

   ![Security Center – Áttekintés](./media/security-center-partner-integration/overview.png)

A **Biztonsági megoldások** területen megtekintheti az Azure integrált biztonsági megoldásainak állapotinformációit, valamint alapszintű felügyeleti feladatokat hajthat végre.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **Csatlakoztatott megoldások** szakasz a Security Centerhez jelenleg csatlakozó biztonsági megoldásokat, valamint az egyes megoldások állapotával kapcsolatos adatokat tartalmazza.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

A partneri megoldások állapota a következőket teheti:

* Kifogástalan (zöld) – nincs állapottal kapcsolatos probléma.
* Nem megfelelő (piros) – azonnali figyelmet igénylő állapottal kapcsolatos probléma.
* Állapottal kapcsolatos problémák (narancssárga) – a megoldás leállította az állapotáról szóló jelentést.
* Nem jelentett (szürke) – a megoldás még nem jelentett semmit, a megoldás állapota nem jelenthető le, ha a közelmúltban csatlakozott, és még mindig üzembe helyezi, vagy nem áll rendelkezésre Health adat.

> [!NOTE]
> Ha az állapotadatok nem érhetők el, Security Center az utolsó fogadott esemény dátumát és időpontját jeleníti meg, amely jelzi, hogy a megoldás jelentéskészítési vagy nem. Ha nem állnak rendelkezésre állapotadatok, és az elmúlt 14 napban nem érkeznek riasztások, Security Center azt jelzi, hogy a megoldás sérült, vagy nem jelent jelentést.
>
>

1. A további információk és lehetőségek megtekintéséhez válassza a **nézet** lehetőséget, amely a következőket tartalmazza:

   - **Megoldás konzolja**. Megnyitja a megoldás kezelési élményét.
   - **Virtuális gép csatolása**. Megnyitja az alkalmazások csatolása panelt. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **Megoldás törlése**.
   - **Konfigurálás**.

   ![Partneri megoldás részletei](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Felderített megoldások

A Security Center automatikusan felderíti az Azure-ban futó, azonban a Security Centerhez nem csatlakoztatott biztonsági megoldásokat, és azokat a **Felderített megoldások** szakaszban jeleníti meg. Ez az Azure-beli megoldásokat, például az [Azure AD Identity Protectiont](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), valamint a partnermegoldásokat is tartalmazza.

> [!NOTE]
> A Felderített megoldások funkcióhoz a Standard szintű Security Centerre az előfizetés szintjén van szükség. A Security tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
>
>

Az egyes megoldások alatt a **CSATLAKOZTATÁS** gombra kattintva integrálhatja azokat a Security Centerbe, és értesülhet a biztonsági riasztásokról.

![Felderített megoldások](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Az adatexportálás SIEM-be

A Azure Security Center által létrehozott feldolgozott események közzé lesznek téve az Azure- [tevékenység naplójában](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), amely az Azure monitoron keresztül elérhető naplózási típusok egyike. A Azure Monitor összevont folyamatot biztosít a figyelési adatainak egy SIEM-eszközre történő átirányításához. Ezt úgy teheti meg, hogy az adott Event hub-ba küldi az adatátvitelt, ahol aztán egy partneri eszközbe lehet húzni.

Ez a cső az [Azure monitoring egyetlen folyamatát](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) használja az Azure-környezetből származó figyelési adatok eléréséhez. Ez lehetővé teszi, hogy könnyedén beállítsa a SIEM és a monitoring eszközöket az adatfelhasználáshoz.

A következő szakaszok azt ismertetik, hogyan konfigurálható az Event hub adatfolyamként való továbbítása. A lépések azt feltételezik, hogy már van Azure Security Center konfigurálva az Azure-előfizetésében.

Áttekintés

![Magas szintű áttekintés](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Mi az a SIEM számára elérhető Azure biztonsági rendszer?

Ebben a verzióban a [biztonsági riasztásokat](../security-center/security-center-managing-and-responding-alerts.md) tesszük elérhetővé. A következő kiadásokban a biztonsági javaslatokkal gazdagítjuk az adathalmazt.

### <a name="how-to-setup-the-pipeline"></a>A folyamat beállítása

#### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Mielőtt elkezdené, [létre kell hoznia egy Event Hubs névteret](../event-hubs/event-hubs-create.md). Ez a névtér és az Event hub az összes megfigyelési adattal kapcsolatos cél.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Az Azure-tevékenység naplójának továbbítása Event Hubs

Tekintse át a következő cikk [stream-tevékenység naplóját Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Partner SIEM-összekötő telepítése 

A monitorozási adatait az Azure Monitor segítségével könnyedén integrálhatja a partner SIEM-és figyelési eszközeivel.

A [támogatott Siem](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub) -lista megtekintéséhez tekintse meg a következő hivatkozást:

### <a name="example-for-querying-data"></a>Példa az adatlekérdezésre 

Íme néhány olyan splunk-lekérdezés, amely a riasztási adatmennyiség lekérésére használható:

| **A lekérdezés leírása** | **Lekérdezés** |
|----|----|
| Minden riasztás| index=main Microsoft.Security/locations/alerts|
| Műveletek számának összefoglalása a nevük alapján| index = Main forrás típusa = "Adorján: biztonság" \| tábla operationName \| statisztikáinak száma operationName szerint|
| Riasztások információinak beolvasása: Idő, név, állapot, azonosító és előfizetés | index = fő Microsoft. Security/Locations/ \| riasztások táblázat \_ideje, tulajdonságok. eventName, állapot, tulajdonságok. operationId, am_subscriptionId |


## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Azure Security Center – gyakori kérdések](security-center-faq.md) Választ találhat a Security Center használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/). Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
