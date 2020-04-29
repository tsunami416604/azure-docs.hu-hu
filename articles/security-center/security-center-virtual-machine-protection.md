---
title: A gépek és az alkalmazások elleni védelem
description: Ez a dokumentum olyan Security Center javaslatokat tartalmaz, amelyek segítenek a virtuális gépek és számítógépek, valamint a webalkalmazások és a App Service környezetek védelmében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 93dde2e873a00303dcb8563caed4d56dbf11cc12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80435629"
---
# <a name="protect-your-machines-and-applications"></a>A gépek és az alkalmazások elleni védelem
Ha Azure Security Center észleli a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának lépésein az erőforrások megerősítéséhez és védelméhez.

Ez a cikk a Security Center erőforrás-biztonsági szakaszának **számítási és alkalmazások** lapját ismerteti.

Az ezen a lapon megjelenő javaslatok teljes listáját lásd: [számítási és alkalmazási javaslatok](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>A számítási és alkalmazási erőforrások biztonságának megtekintése

[![A Security Center irányítópultja](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

A számítási és alkalmazási erőforrások állapotának megtekintéséhez Security Center bal oldali ablaktábláján válassza a **számítási & alkalmazások**lehetőséget. A következő lapok érhetők el:

* **Áttekintés**: az összes számítási és alkalmazási erőforrásra, valamint az aktuális biztonsági állapotra vonatkozó javaslatok felsorolása 

* [**Virtuális gépek és kiszolgálók**](#vms-and-computers): felsorolja a virtuális gépek, számítógépek és aktuális biztonsági állapotokra vonatkozó ajánlásokat

* Virtuálisgép- [**méretezési**](#vmscale-sets)csoportok: felsorolja a méretezési csoportokra vonatkozó javaslatokat, 

* [**Cloud Services**](#cloud-services): a Security Center által figyelt webes és feldolgozói szerepkörökre vonatkozó ajánlások felsorolása

* [**App Services**](#app-services): felsorolja az App Service-környezetekre vonatkozó javaslatokat és az egyes szolgáltatások aktuális biztonsági állapotát

* [**Tárolók**](#containers): a tárolók javaslatainak és a konfigurációk biztonsági értékelésének felsorolása

* **Számítási erőforrások**: a számítási erőforrások, például a Service Fabric-fürtök és az esemény-hubok javaslatainak felsorolása

### <a name="whats-in-each-tab"></a>Mi az egyes lapok?

Mindegyik lapon több szakasz található, és mindegyik szakaszban részletesen megtekintheti a megjelenített elem további részleteit.

Az egyes lapokon a figyelt környezetben található megfelelő erőforrásokra vonatkozó javaslatokat is látni fogja. Az első oszlop felsorolja a javaslatot, a második pedig az érintett erőforrások teljes számát, a harmadik pedig a probléma súlyosságát mutatja.

Mindegyik javaslat olyan műveleteket tartalmaz, amelyeket a kiválasztása után elvégezhet. Ha például a **hiányzó rendszerfrissítések**lehetőséget választja, akkor a virtuális gépek és a hiányzó javításokat tartalmazó számítógépek száma, valamint a hiányzó frissítés súlyossága jelenik meg.

> [!NOTE]
> A biztonsági javaslatok ugyanazok, mint a **javaslatok** lapon, de itt vannak szűrve a kiválasztott erőforrás-típusra. A javaslatok megoldásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Virtuális gépek és kiszolgálók
A virtuális gépek és számítógépek szakasz áttekintést nyújt a virtuális gépek és számítógépek biztonsági javaslatairól. A rendszer négyféle típusú gépet tartalmaz:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Azure Resource Manager virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager virtuális gép.

![Klasszikus Azure-beli virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasszikus Azure-beli virtuális gép.

![A munkaterületről azonosított virtuális gépek](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ebbe beletartoznak az előfizetésben szereplő munkaterületre vonatkozó más előfizetések virtuális gépei, valamint Operations Manager közvetlen ügynökkel telepített virtuális gépek és nem rendelkeznek erőforrás-AZONOSÍTÓval.

Az egyes javaslatok alatt megjelenő ikon segíti a virtuális gép és a beavatkozást igénylő számítógép gyors azonosítását, valamint a javaslat típusát. A szűrőket használhatja **Erőforrás típusa** és **Súlyosság**szerint is a listában való kereséshez.

Az egyes virtuális gépek biztonsági javaslatainak részletezéséhez kattintson a virtuális gépre.
Itt láthatja a virtuális gép vagy a számítógép biztonsági részleteit. Alul a javasolt művelet és az egyes problémák súlyossága látható.

[![Cloud Services](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Virtuálisgép-méretezési csoportok
Security Center automatikusan felfedi, hogy rendelkezik-e méretezési csoportokkal, és javasolja, hogy telepítse a Log Analytics ügynököt.

A Log Analytics-ügynök telepítése: 

1. Válassza a **figyelési ügynök telepítése virtuálisgép-méretezési csoportra**lehetőséget. A nem figyelt méretezési csoportok listáját kapja meg.

1. Válasszon egy nem kifogástalan állapotú méretezési készletet. Kövesse az utasításokat, és telepítse a figyelési ügynököt egy meglévő feltöltött munkaterületen, vagy hozzon létre egy újat. Ha nincs beállítva, ügyeljen arra, hogy a munkaterület [árképzési szintjét](security-center-pricing.md) állítsa be.

   ![Az MMS telepítése](./media/security-center-virtual-machine-recommendations/install-mms.png)

Új méretezési csoportok beállítása az Log Analytics-ügynök automatikus telepítéséhez:
1. Nyissa meg Azure Policy és kattintson a **definíciók**elemre.

1. Keresse meg a házirendet a **Windows rendszerű virtuálisgép-méretezési csoportok log Analytics ügynökének üzembe helyezéséhez** , és kattintson rá.

1. Kattintson a **Hozzárendelés** gombra.

1. Állítsa be a **hatókör** és **log Analytics munkaterületet** , és kattintson a **hozzárendelés**elemre.

Ha az Log Analytics ügynök telepítésére szeretné beállítani az összes meglévő méretezési csoportot, akkor a Azure Policyban lépjen a **szervizelés** elemre, és alkalmazza a meglévő házirendet a meglévő méretezési csoportokra.





### <a name="cloud-services"></a><a name="cloud-services"></a>Cloud Services
A Cloud Services esetében egy javaslat jön létre, ha az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Egy olyan forgatókönyvben, amelyben javaslata van, kövesse a javaslat lépéseit az operációs rendszer frissítéséhez. Ha egy frissítés elérhetővé válik, a probléma súlyossága függvényében riasztás jelenik meg (vörös vagy narancssárga). A javaslat részletes ismertetéséhez kattintson az **operációs rendszer verziójának frissítése** elemre a **Leírás** oszlopban.






### <a name="app-services"></a><a name="app-services"></a>App Services
A App Service információk megtekintéséhez Security Center Standard díjszabási szintnek kell lennie, és engedélyeznie kell a App Service az előfizetésében. A szolgáltatás engedélyezésével kapcsolatos útmutatásért lásd: [App Service Azure Security Centerekkel való védelemmel](security-center-app-services.md).

Az **app Services**alatt megtalálhatja az App Service-környezetek listáját, valamint az állapot összegzését az értékelés Security Center elvégzése alapján.

![App Services](./media/security-center-virtual-machine-recommendations/app-services.png)

Az Application Services három típusa látható:

![App Services-környezet](./media/security-center-virtual-machine-recommendations/ase.png) App Services-környezet

![Webalkalmazás](./media/security-center-virtual-machine-recommendations/web-app.png) Webalkalmazás

![Function alkalmazás](./media/security-center-virtual-machine-recommendations/function-app.png) Function alkalmazás

Ha kijelöl egy webalkalmazást, megnyílik egy összefoglaló nézet három lapból:

   - **Javaslatok**: a sikertelen Security Center által végrehajtott értékelések alapján.
   - **Átadott értékelések**: az átadott Security Center által elvégzett értékelések listája.
   - Nem **elérhető értékelések**: azon értékelések listája, amelyeket egy hiba miatt nem sikerült futtatni, vagy ha a javaslat nem vonatkozik az adott app Service-re

   A **javaslatok** területen a kiválasztott webalkalmazásra és az egyes javaslatok súlyosságára vonatkozó javaslatok szerepelnek.

   ![Javaslatok App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Válasszon egy javaslatot a javaslat leírásának megtekintéséhez, valamint a nem megfelelő állapotú erőforrások, az egészséges erőforrások és a nem ellenőrzött erőforrások listájához.

   - Az **átadott értékelések** oszlop az átadott értékelések listáját jeleníti meg. Az értékelések súlyossága mindig zöld.

   - Válasszon ki egy átadott értékelést a listából az értékelés leírásához, a nem megfelelő állapotú és kifogástalan erőforrások listáját, valamint a nem ellenőrzött erőforrások listáját. A nem megfelelő állapotú erőforrásokhoz tartozó lap szerepel, de ez a lista mindig üres, mert az értékelés sikeres volt.





### <a name="containers"></a><a name="containers"></a>Containers

Amikor megnyitja a **tárolók** fület a környezettől függően, a következő három típusú erőforrás jelenhet meg:

![Tároló gazdagépe](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Tároló gazdagépek – Docker-t futtató virtuális gépek 

![Kubernetes szolgáltatás](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes szolgáltatás (ak) fürtök

![Tárolóregisztrációs adatbázis](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry (ACR) jegyzékek – csak akkor jelenik meg, ha a standard árképzési szinttel rendelkezik, és ha engedélyezve van a Azure Container Registry csomag.

A tároló biztonsági funkcióinak használatáról a [tárolók biztonságának figyelését](monitor-container-security.md)ismertető cikkben olvashat bővebben.

A Azure Container Registry Bundle előnyeit [itt](azure-container-registry-integration.md) találja

A Kubernetes Services csomag előnyeit [itt](azure-kubernetes-service-integration.md) találja

[![Tárolók lap](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Ha meg szeretné tekinteni a listában szereplő adott erőforrásra vonatkozó javaslatokat, kattintson az adott erőforrásra.

#### <a name="visibility-into-container-registries"></a>Tároló-nyilvántartások láthatósága

Ha például az ASC-bemutató ACR-beállításjegyzékre kattint a fenti ábrán látható listában, akkor az a részleteket megjelenítő oldalon jelenik meg:

[![Javaslatok egy adott ACR-beállításjegyzékhez](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>IaaS Linux rendszerű gépeken üzemeltetett tárolók láthatósága

Ha a Docker-t futtató virtuális gépek egyikére kattint, a Részletek lap a gépen lévő tárolókkal kapcsolatos információkkal, például a Docker-verzióval és a gazdagépen futó rendszerképek számával jelenik meg.

![A Docker-t futtató virtuális gépekre vonatkozó javaslatok](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Biztonsági javaslatok a Docker-hez készült CIS-teljesítményteszt alapján

A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. Security Center útmutatást nyújt a problémák gyors megoldásához és az idő megtakarításához. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.

![tároló lap](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikkeket:

* [Azure Security Center biztonsági javaslatainak teljes listája](recommendations-reference.md)
* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme Azure Security Center](security-center-sql-service-recommendations.md)