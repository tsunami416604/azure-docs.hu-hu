---
title: Az Azure Defender és a rendelkezésre álló csomagok áttekintése
description: Ismerje meg az Azure Defender terveit, védelmét és riasztásait. Ezután engedélyezze az Azure Defender számára a fokozott biztonságú előfizetéseket.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bfff96666981a522cd6d91828604696a12ecad56
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91576854"
---
# <a name="introduction-to-azure-defender"></a>Az Azure Defender bemutatása

Azure Security Center funkciói a Felhőbeli biztonság két fő pillérét fedik le:

- **Cloud Security testtartás-felügyelet (CSPM)** – Security Center **ingyenesen** elérhető az összes Azure-felhasználó számára. Az ingyenes élmény olyan CSPM funkciókat tartalmaz, mint például a biztonságos pontszám, az Azure-gépek biztonsági konfigurációs beállításainak észlelése, az eszközök leltára stb. Ezekkel a CSPM funkciókkal erősítheti a hibrid Felhőbeli testtartást, és nyomon követheti a beépített szabályzatoknak való megfelelést.

- **Cloud munkaterhelés Protection (CWP)** – a Security Center integrált felhőalapú munkaterhelés-védelmi platformja (CWPP), az **Azure Defender**, fejlett, intelligens, Azure-és hibrid erőforrások és számítási feladatok védelmét teszi lehetővé. Az Azure Defender engedélyezésének számos további biztonsági funkciója van, az ezen a lapon leírtak szerint. A beépített szabályzatok mellett, ha engedélyezte valamelyik Azure Defender-csomagot, egyéni szabályzatokat és kezdeményezéseket adhat hozzá. Hozzáadhat szabályozási szabványokat – például a NIST és az Azure CIS –, valamint az Azure biztonsági teljesítménytesztet a megfelelőségének valóban testreszabott nézetéhez.

Az Azure Defender irányítópultja Security Center a környezethez tartozó CWP-funkciók láthatóságát és felügyeletét teszi lehetővé:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Példa az Azure Defender irányítópultra" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Milyen típusú erőforrástípusok biztonságosak az Azure Defender?

Az Azure Defender biztonsági riasztásokat és komplex veszélyforrások elleni védelmet biztosít a virtuális gépek, az SQL-adatbázisok, a tárolók, a webalkalmazások, a hálózat és egyebek tekintetében.

Amikor engedélyezi az Azure Defender számára a Azure Security Center **díjszabási és beállítási** területét, a következő Defender-csomagok egyidejűleg engedélyezve vannak, és átfogó védelmet biztosítanak a környezet számítási, adatés szolgáltatási rétegei számára:

- [Azure Defender kiszolgálókhoz](defender-for-servers-introduction.md)
- [Azure Defender App Service-hez](defender-for-app-service-introduction.md)
- [Azure Defender tároláshoz](defender-for-storage-introduction.md)
- [Azure Defender SQL-hez](defender-for-sql-introduction.md)
- [Azure Defender IoT-hez](defender-for-iot-introduction.md)
- [Azure Defender Kuberneteshez](defender-for-kubernetes-introduction.md)
- [Azure Defender tárolóregisztrációs adatbázisokhoz](defender-for-container-registries-introduction.md)
- [Azure Defender Key Vaulthoz](defender-for-key-vault-introduction.md)

Ezeket a csomagokat a Security Center dokumentációjában külön ismertetjük.


## <a name="hybrid-cloud-protection"></a>Hibrid Felhőbeli védelem

Az Azure-környezet védelme mellett hozzáadhat Azure Defender-képességeket is a hibrid felhőalapú környezethez:

- A nem Azure-beli kiszolgálók elleni védelem
- A virtuális gépeket más felhőkben (például AWS és GCP) is védetté teheti
- IoT-eszközök megóvása

Az adott környezetnek megfelelően testre szabott fenyegetési intelligenciát és rangsorolt riasztásokat kap, így a leginkább a legfontosabb dolgokra koncentrálhat.

A virtuális gépek és a más Felhőbeli vagy helyszíni SQL-adatbázisok védelmének kiterjesztéséhez telepítse az [Azure-ívet](https://azure.microsoft.com/services/azure-arc/) , és engedélyezze az Azure Defendert. Az Azure arc for Servers egy ingyenes szolgáltatás, de az ív használatára képes kiszolgálókon használt szolgáltatások, például az Azure Defender a szolgáltatás díjszabása szerint lesznek felszámítva. További információ: [nem Azure-beli gépek hozzáadása az Azure arc szolgáltatással](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Az AWS natív összekötője transzparens módon kezeli az Azure arc üzembe helyezését. További információ az [AWS-fiókok Azure Security Center való összekapcsolásáról](quickstart-onboard-aws.md).



## <a name="azure-defender-alerts"></a>Azure Defender-riasztások 

Ha az Azure Defender fenyegetést észlel a környezet bármely területén, riasztást hoz létre. Ezek a riasztások ismertetik az érintett erőforrások részleteit, a javasolt szervizelési lépéseket, valamint bizonyos esetekben a logikai alkalmazások válaszként való aktiválásának lehetőségét.

Azt jelzi, hogy a riasztást egy Security Center hozza-e létre, vagy a Security Center egy integrált biztonsági terméktől fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.

> [!NOTE]
> A különböző forrásokból származó riasztások eltérő mennyiségű időt vehetnek igénybe. Előfordulhat például, hogy a hálózati forgalom elemzését igénylő riasztások hosszabb ideig tartanak, mint a virtuális gépeken futó gyanús folyamatokkal kapcsolatos riasztások.


## <a name="azure-defender-advanced-protection-capabilities"></a>Az Azure Defender speciális védelmi képességei

Az Azure Defender fejlett analitikai megoldásokkal rendelkezik az erőforrásokkal kapcsolatos személyre szabott javaslatokhoz. 

A védelem magában foglalja a virtuális gépek felügyeleti portjainak az igény szerinti hozzáféréssel és az adaptív alkalmazás-vezérlőkkel való védelmét, amelyek lehetővé teszik, hogy a számítógépeken milyen alkalmazásokat kell és ne futtasson. 

Az Azure Defender irányítópultjának speciális védelmi csempéi segítségével figyelheti és konfigurálhatja ezeket a védelmi lépéseket. 

## <a name="vulnerability-assessment-and-management"></a>Sebezhetőségi felmérés és felügyelet

Az Azure Defender külön díj nélkül tartalmazza a virtuális gépek és a tároló-beállításjegyzékek sebezhetőségi vizsgálatát. A képolvasókat a Qualys működteti, de nincs szüksége Qualys-licencre, vagy akár Qualys-fiókra is – minden a Security Center-ben zökkenőmentesen kezelhető. 

Tekintse át a biztonsági rések képolvasóinak eredményeit, és válaszoljon rájuk mind Security Center belülről. Ennek köszönhetően a Felhőbeli biztonsági erőfeszítések mindegyikének egyetlen panelje Security Center.

További információ a következő lapokon:

- [Security Center az Azure Virtual Machines szolgáltatáshoz készült integrált sebezhetőségi felmérési megoldás](deploy-vulnerability-assessment-vm.md)
- [Biztonsági rések azonosítása az Azure Container nyilvántartóban található lemezképekben](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta az Azure Defender előnyeit. 

> [!div class="nextstepaction"]
> [Az Azure Defender engedélyezése](security-center-pricing.md)