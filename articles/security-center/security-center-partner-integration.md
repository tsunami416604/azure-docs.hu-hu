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
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: ecfb4bdbd523dd1514333a2185186eab0a7e4aaa
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032677"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Biztonsági megoldások integrálása az Azure Security Centerbe
Ez a dokumentum az Azure Security Centerhez már csatlakoztatott biztonsági megoldások kezelésében és újak hozzáadásában segít.

## <a name="integrated-azure-security-solutions"></a>Integrált Azure biztonsági megoldások
A Security Center használatával egyszerűen engedélyezhet integrált biztonsági megoldásokat az Azure-ban. Az előnyök:

- **Egyszerűsített üzembe helyezés**: A Security Center segítségével az integrált partnermegoldások egy optimalizált folyamat mentén helyezhetőek üzembe. Az olyan megoldások esetében, mint például az antimalware és a sebezhetőségi felmérés, a Security Center képes kiépíteni az ügynököt a virtuális gépekre. A tűzfalak esetében Security Center a szükséges hálózati konfiguráció nagy részét is elvégezheti.
- **Integrált észlelések**: a partneri megoldások biztonsági eseményeit a rendszer automatikusan összegyűjti, összesíti és megjeleníti Security Center riasztások és incidensek részeként. Ezek az események más forrásoktól érkező észlelésekhez is kapcsolódnak, ami fejlett fenyegetésészlelési képességeket biztosít.
- **Egyesített állapotmonitorozás és -kezelés**: Az integrált állapotesemények lehetővé teszik az összes partnermegoldás gyors monitorozását. Az alapszintű felügyeletből könnyen elérhető a speciális beállítás a partnermegoldás használatával.

Az integrált biztonsági megoldások jelenleg a [Qualys](https://www.qualys.com/public-cloud/#azure) és a [Rapid7](https://www.rapid7.com/products/insightvm/) , valamint [az Application Gateway Azure-beli webalkalmazási tűzfalon Microsoft Azure a](../web-application-firewall/ag/ag-overview.md)biztonsági rések felmérését is.

> [!NOTE]
> Security Center nem telepíti a Log Analytics ügynököt a partner virtuális készülékekre, mert a legtöbb biztonsági gyártó tiltja a készülékeken futó külső ügynököket.

Ha többet szeretne megtudni a sebezhetőségi vizsgálat eszközeinek integrálásáról a Qualys-ből, beleértve az Azure Defender-ügyfelek számára elérhető beépített szkennert, tekintse meg az [Azure-Virtual Machines sebezhetőségi felméréseit](deploy-vulnerability-assessment-vm.md).

A Security Center a következő biztonsági rések elemzését is biztosítja:

* SQL-adatbázisok – lásd: [sebezhetőségi felmérési jelentések feltárása a sebezhetőségi felmérés irányítópultján](defender-for-sql-usage.md#explore-vulnerability-assessment-reports)
* Azure Container Registry lemezképek – lásd: az [Azure Defender használata a tárolók beállításjegyzékében a lemezképek vizsgálatához a biztonsági rések](defender-for-container-registries-usage.md) felderítéséhez

## <a name="how-security-solutions-are-integrated"></a>A biztonsági megoldások integrálása
A Security Centerből üzembe helyezett Azure biztonsági megoldások automatikusan csatlakoztatva vannak. Más biztonsági adatforrásokat is összekapcsolhat, beleértve a helyszínen vagy más felhőkben futó számítógépeket is.

[![Partnermegoldások integrációja](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Integrált Azure biztonsági megoldások és egyéb adatforrások kezelése

1. A [Azure Portal](https://azure.microsoft.com/features/azure-portal/)nyissa meg **Security Center**.

1. A Security Center menüjében válassza a **biztonsági megoldások** elemet.

A **biztonsági megoldások** lapon megtekintheti az integrált Azure biztonsági megoldások állapotát, és alapszintű felügyeleti feladatokat futtathat.

### <a name="connected-solutions"></a>Csatlakoztatott megoldások

A **csatlakoztatott megoldások** szakasz olyan biztonsági megoldásokat tartalmaz, amelyek jelenleg Security Centerhoz vannak csatlakoztatva. Az egyes megoldások állapotát is megjeleníti.  

![Csatlakoztatott megoldások](./media/security-center-partner-integration/connected-solutions.png)

A partneri megoldások állapota a következőket teheti:

* **Kifogástalan** (zöld) – nincsenek állapotbeli problémák.
* **Sérült (piros** ) – probléma merül fel, ami azonnali beavatkozást igényel.
* **Leállított jelentéskészítés** (narancssárga) – a megoldás leállította az állapotáról szóló jelentést.
* **Nem jelentett** (szürke) – a megoldás még nem jelentett semmit, és nem érhető el állapotadatok. Előfordulhat, hogy A megoldás állapota nem jelenthető le, ha a közelmúltban csatlakozott, és még mindig települ.

> [!NOTE]
> Ha az állapotadatok nem érhetők el, Security Center az utolsó fogadott esemény dátumát és időpontját jeleníti meg, amely jelzi, hogy a megoldás jelentéskészítési vagy nem. Ha nem állnak rendelkezésre állapotadatok, és az elmúlt 14 napban nem érkeztek riasztások, Security Center azt jelzi, hogy a megoldás sérült, vagy nem jelent jelentést.
>
>

Válassza a **nézet** lehetőséget További információk és beállítások megadásához, például:

   - **Megoldás-konzol** – megnyitja a megoldás kezelési élményét.
   - **Virtuális gép csatolása** – megnyitja az alkalmazások összekapcsolása lapot. Itt erőforrásokat kapcsolhat hozzá a partnermegoldáshoz.
   - **Megoldás törlése**
   - **Konfigurálás**

   ![Partneri megoldás részletei](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Felderített megoldások

Security Center automatikusan felderíti az Azure-ban futó biztonsági megoldásokat, de nem kapcsolódik Security Centerhoz, és megjeleníti a megoldásokat a **felderített megoldások** szakaszban. Ezen megoldások közé tartoznak az Azure-megoldások, például a [Azure ad Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)és a partneri megoldások.

> [!NOTE]
> Az **Azure Defender** előfizetési szintjének engedélyezése a felderített megoldások szolgáltatáshoz. A díjszabással kapcsolatos további információkért tekintse meg a [díjszabást](security-center-pricing.md) .
>

Válassza a **Kapcsolódás** a megoldás alatt lehetőséget a Security Center való integráláshoz és a biztonsági riasztások értesítéséhez.

### <a name="add-data-sources"></a>Adatforrások hozzáadása

Az **Adatforrások hozzáadása** szakasz sorolja fel az egyéb csatlakoztatható adatforrásokat. Az ezekből a forrásokból származó adatok hozzáadásával kapcsolatos utasításokért kattintson a **HOZZÁADÁS** gombra.

![Adatforrások](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megismerkedett a partnermegoldások Security Centerrel való integrálásával. A kapcsolódó információk a következő cikkekben találhatók:

* [Security Center-adatfeldolgozás folyamatos exportálása](continuous-export.md). Ismerje meg, hogyan telepítheti az integrációt az Azure Sentinel vagy más SIEM használatával.
* [Biztonsági állapot monitorozása a Security Centerben](security-center-monitoring.md). Az Azure-erőforrások állapotának figyelését ismertető útmutató.