---
title: Védje gépeit és alkalmazásait
description: Ez a dokumentum a Security Center ben található javaslatokkal foglalkozik, amelyek segítenek a virtuális gépek és számítógépek, valamint a webalkalmazások és az App Service-környezetek védelmében.
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
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282639"
---
# <a name="protect-your-machines-and-applications"></a>Védje gépeit és alkalmazásait
Amikor az Azure Security Center azonosítja a potenciális biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán az erőforrások megerősítéséhez és védelméhez.

Ez a cikk a Security Center erőforrás-biztonsági **szakaszának Számítási és alkalmazások** lapját ismerteti.

Az ezen az oldalon látható javaslatok teljes listáját a [Számítási és alkalmazásjavaslatok című témakörben találja.](recommendations-reference.md#recs-computeapp)


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>A számítási és alkalmazáserőforrások biztonságának megtekintése

[![A Security Center irányítópultja](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

A számítási és alkalmazáserőforrások állapotának megtekintéséhez a Biztonsági központ bal oldali ablaktáblájában válassza a **Számítási & az alkalmazások lehetőséget.** A következő lapok érhetők el:

* **Áttekintés**: felsorolja az összes számítási és alkalmazáserőforrásra vonatkozó javaslatokat, valamint azok aktuális biztonsági állapotát 

* [**Virtuális gépek és kiszolgálók:**](#vms-and-computers)felsorolja a virtuális gépekre, számítógépekre és az egyes számítógépek aktuális biztonsági állapotára vonatkozó javaslatokat

* [**Virtuálisgép-méretezési csoportok:**](#vmscale-sets)felsorolja a méretezési csoportokra vonatkozó javaslatokat, 

* [**Felhőszolgáltatások:**](#cloud-services)felsorolja a Security Center által figyelt webes és feldolgozói szerepkörökre vonatkozó javaslatokat

* [**Alkalmazásszolgáltatások:**](#app-services)felsorolja az alkalmazásszolgáltatási környezetekre vonatkozó javaslatokat és az egyes

* [**Tárolók:**](#containers)felsorolja a tárolókra vonatkozó ajánlásokat és konfigurációik biztonsági értékelését

* **Számítási erőforrások:** felsorolja a számítási erőforrásokra vonatkozó javaslatokat, például a Service Fabric-fürtöket és az Eseményközpontokat

### <a name="whats-in-each-tab"></a>Mi van az egyes lapokon?

Minden lapnak több szakasza van, és az egyes szakaszokban leáshat, hogy további részleteket jelenítsen meg az elemről.

Az egyes lapokon a figyelt környezetben a megfelelő erőforrásokra vonatkozó javaslatokat is látni fogja. Az első oszlop ban a javaslat, a második az érintett erőforrások teljes számát, a harmadik pedig a probléma súlyosságát mutatja.

Minden javaslat rendelkezik egy műveletkészletet, amely et a kijelölés után is elvégezheti. Ha például a Hiányzó rendszerfrissítések lehetőséget **választja,** a hiányzó javításokat tartalmazó virtuális gépek és számítógépek száma, valamint a hiányzó frissítés súlyossága jelenik meg.

> [!NOTE]
> A biztonsági javaslatok megegyeznek a Javaslatok lapon található **abból,** de itt a kiválasztott erőforrástípusra vannak szűrve. A javaslatok megoldásáról a [Biztonsági javaslatok megvalósítása az Azure Security Centerben](security-center-recommendations.md)című témakörben talál további információt.
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>Virtuális gépek és kiszolgálók
A virtuális gépek és számítógépek szakasz áttekintést nyújt a virtuális gépek és számítógépek összes biztonsági javaslatáról. A gépek négy típusa szerepel:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Azure Resource Manager Virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Erőforrás-kezelő virtuális gép.

![Azure Classic Virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure Classic virtuális gép.

![A munkaterületről azonosított virtuális gépek](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ez magában foglalja a virtuális gépek más előfizetések, amelyek jelentést a munkaterület ebben az előfizetésben, és a virtuális gépek, amelyek telepítettek az Operations Manager közvetlen ügynök, és nincs erőforrás-azonosító.

Az egyes javaslatok alatt megjelenő ikon segítségével gyorsan azonosíthatja a figyelmet igényelő virtuális gépet és számítógépet, valamint a javaslat típusát. A szűrők segítségével **erőforrástípus** és **súlyosság**i. kifejezés szerint is kereshet a listában.

Az egyes virtuális gépek biztonsági javaslatainak részletezéséhez kattintson a virtuális gépre.
Itt láthatja a virtuális gép vagy a számítógép biztonsági adatait. Az alján láthatja az ajánlott műveletet és az egyes probléma súlyosságát.

[![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Virtuálisgép-méretezési készletek
A Security Center automatikusan felderíti, hogy rendelkezik-e méretezési készletekkel, és javasolja, hogy telepítse rájuk a Microsoft Monitoring Agent-et.

A Microsoft Monitoring Agent telepítése: 

1. Válassza ki a javaslat **Telepítse a figyelési ügynök a virtuálisgép-méretezési csoport.** A nem figyelt méretezési csoportok listáját kapja.

1. Jelöljön ki egy nem megfelelő méretezési készletet. Kövesse az utasításokat a figyelési ügynök meglévő lakott munkaterület használatával történő telepítéséhez, vagy hozzon létre egy újat. Győződjön meg arról, hogy állítsa be a munkaterület [tarifacsomag,](security-center-pricing.md) ha nincs beállítva.

   ![MMS telepítése](./media/security-center-virtual-machine-recommendations/install-mms.png)

Új méretezési csoportok beállítása a Microsoft Monitoring Agent automatikus telepítéséhez:
1. Nyissa meg az Azure-szabályzatot, és kattintson **a Definíciók gombra.**

1. Keresse meg a házirend **Et Deploy Log Analytics ügynök Windows virtuálisgép-méretezési készletek,** és kattintson rá.

1. Kattintson a **Hozzárendelés** gombra.

1. Állítsa be a **Hatókör** és **a Log Analytics munkaterületet,** és kattintson a Hozzárendelés **gombra.**

Ha azt szeretné, hogy az összes meglévő méretezési csoportok a Microsoft Monitoring Agent telepítéséhez, az Azure-szabályzat, lépjen **a szervizelés,** és alkalmazza a meglévő szabályzatot a meglévő méretezési csoportok.





### <a name="cloud-services"></a><a name="cloud-services"></a>Felhőszolgáltatások
A felhőszolgáltatások esetében egy javaslat akkor jön létre, amikor az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Olyan esetben, ha van egy javaslat, kövesse az operációs rendszer frissítésére vonatkozó javaslat lépéseit. Ha egy frissítés elérhető, a probléma súlyosságától függően (piros vagy narancssárga) riasztás jelenik meg. A javaslat teljes magyarázatáért kattintson az **operációs rendszer verziójának frissítése** a **LEÍRÁS** oszlopban.






### <a name="app-services"></a><a name="app-services"></a>Alkalmazásszolgáltatások
Az App Service-adatok megtekintéséhez a Security Center standard díjszabási csomagján kell lennie, és engedélyeznie kell az App Service-t az előfizetésében. A szolgáltatás engedélyezésével kapcsolatos tudnivalókért olvassa el az App Service védelme az Azure Security Centerrel című [témakört.](security-center-app-services.md)

Az **App Services**csoportban megtalálja az Alkalmazásszolgáltatás-környezetek listáját és az állapotösszefoglalót a Biztonsági központ által végzett értékelés alapján.

![Alkalmazásszolgáltatások](./media/security-center-virtual-machine-recommendations/app-services.png)

Az alkalmazásszolgáltatásoknak három típusa látható:

![Alkalmazásszolgáltatási környezet](./media/security-center-virtual-machine-recommendations/ase.png) Alkalmazásszolgáltatási környezet

![Webalkalmazás](./media/security-center-virtual-machine-recommendations/web-app.png) Webalkalmazás

![Függvényalkalmazás](./media/security-center-virtual-machine-recommendations/function-app.png) Függvényalkalmazás

Ha kiválaszt egy webalkalmazást, egy három lapból áll az összesítő nézet:

   - **Javaslatok:** a Security Center által végzett sikertelen értékelések alapján.
   - **Átadott értékelések:** a Security Center által végzett, átadott értékelések listája.
   - **Nem elérhető értékelések:** azoknak az értékeléseknek a listája, amelyek hiba miatt nem futottak, vagy az ajánlás nem releváns az adott alkalmazásszolgáltatás szempontjából

   Ajánlások **Recommendations** a kiválasztott webes alkalmazás ajánlásainak listáját és az egyes javaslatok súlyosságát tartalmazza.

   ![Az App Services javaslatai](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Válasszon ki egy javaslatot a javaslat leírásának megtekintéséhez, valamint a nem megfelelő állapotú erőforrások, kifogástalan állapotú erőforrások és a be nem vizsgált erőforrások listájának megtekintéséhez.

   - Az **Átadott értékelések** oszlop az átadott értékelések listáját mutatja. Ezeknek az értékeléseknek a súlyossága mindig zöld.

   - Válasszon ki egy átadott értékelést a listából az értékelés leírásához, a nem megfelelő és kifogástalan állapotú erőforrások listájához, valamint a be nem olvasott erőforrások listájához. Van egy lap a nem megfelelő állapotú erőforrások, de ez a lista mindig üres, mivel az értékelés telt el.





### <a name="containers"></a><a name="containers"></a>Tárolók

A **Tárolók** lap megnyitásakor a környezettől függően három féle erőforrás jelenhet meg:

![Tárológazda](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Tárolóállomások – dockert futtató virtuális gépek 

![Kubernetes szolgáltatás](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Az Azure Kubernetes-szolgáltatás (AKS) fürtjei

![Tárolóregisztrációs adatbázis](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Azure Container Registry (ACR) beállításjegyzékek – Csak akkor jelenik meg, ha a standard díjszabási szinten, és ha az Azure Container Registry csomag engedélyezve van.

A tárolóbiztonsági szolgáltatások használatáról a [Tárolók biztonságának figyelése című](monitor-container-security.md)témakörben talál.

Az Azure Container Registry csomag előnyeit [itt](azure-container-registry-integration.md) ismertetik.

A Kubernetes-szolgáltatások csomag előnyei [itt](azure-kubernetes-service-integration.md) kerülnek magyarázatra

[![Tárolók lap](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Ha meg szeretné tekinteni egy adott erőforrásra vonatkozó javaslatokat a listában, kattintson az adott erőforrásra.

#### <a name="visibility-into-container-registries"></a>A tárolónyilvántartások láthatósága

Ha például a fenti ábrán látható listában az asc-demo ACR beállításjegyzékre kattint, akkor ez a részletoldal jelenik meg:

[![Ajánlások egy adott ACR-nyilvántartóhoz](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Az IaaS Linux-gépeken tárolt tárolók láthatósága

Ha rákattint az egyik futó virtuális gépek docker, látni fogja a részleteket oldal kapcsolatos információkat a tárolók a gépen, például a Docker-verzió és a gazdagépen futó képek száma.

![Javaslatok a docker-t futtató virtuális géphez](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>A Docker CIS-referenciaértékén alapuló biztonsági javaslatok

A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. A Security Center útmutatást nyújt a problémák gyors megoldásához és az időmegtakarításhoz. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.

![tároló lap](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrástípusokra vonatkozó javaslatokról, olvassa el az alábbi cikkeket:

* [Az Azure Security Center biztonsági javaslatainak teljes hivatkozási listája](recommendations-reference.md)
* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme az Azure Security Centerben](security-center-sql-service-recommendations.md)