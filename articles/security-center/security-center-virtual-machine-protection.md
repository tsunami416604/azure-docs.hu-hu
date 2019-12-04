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
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 4a6d733b490edd892136f6febcc90c29a5a865e1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766803"
---
# <a name="protect-your-machines-and-applications"></a>A gépek és az alkalmazások elleni védelem
Ha a Security Center felismeri a lehetséges biztonsági réseket, javaslatokat hoz létre, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán. 

Ez a cikk a Azure Security Center erőforrás-biztonsági szakaszának **számítási és alkalmazások** lapját ismerteti. Emellett ismerteti azokat a javaslatokat, amelyeket itt láthat.

A számítási és az App Services-javaslatok teljes listáját a [számítás és az alkalmazás javaslataiban](recommendations-compute-and-apps.md)találhatja meg.

## <a name="view-the-security-of-your-compute-and-apps-resources"></a>A számítási és alkalmazási erőforrások biztonságának megtekintése

![A Security Center irányítópultja](./media/security-center-virtual-machine-recommendations/overview.png)

A számítási és alkalmazási erőforrások állapotának megtekintéséhez válassza a Security Center Sidebarban az **erőforrások** területen a **számítási & alkalmazások** elemet. A következő lapok érhetők el:

* **Áttekintés**: az összes számítási és alkalmazási erőforrásra, valamint az aktuális biztonsági állapotra vonatkozó javaslatok felsorolása 

* [**Virtuális gépek és számítógépek**](#vms-and-computers): felsorolja a virtuális gépekre, a számítógépekre és a jelenlegi biztonsági állapotra vonatkozó javaslatokat

* [**VM Scale sets**](#vmscale-sets): a méretezési csoportokra vonatkozó javaslatok felsorolása, 

* [**Cloud Services**](#cloud-services): a Security Center által megfigyelt webes és feldolgozói szerepkörökre vonatkozó ajánlások felsorolása

* [**App Services**](#app-services): felsorolja az App Service-környezetekre vonatkozó javaslatokat és az egyes szolgáltatások aktuális biztonsági állapotát

* **Tárolók**: a tárolók javaslatainak és a konfigurációk biztonsági értékelésének felsorolása

* **Számítási erőforrások**: a számítási erőforrások, például a Service Fabric-fürtök és az esemény-hubok javaslatainak felsorolása

### <a name="whats-in-each-tab"></a>Mi az egyes lapok?

Mindegyik lapon több szakasz található, és mindegyik szakaszban részletesen megtekintheti a megjelenített elem további részleteit.

Az egyes lapokon a figyelt környezetben található megfelelő erőforrásokra vonatkozó javaslatokat is látni fogja. Az első oszlop felsorolja a javaslatot, a második pedig az érintett erőforrások teljes számát, a harmadik pedig a probléma súlyosságát mutatja.

Mindegyik javaslat olyan műveleteket tartalmaz, amelyeket a kiválasztása után elvégezhet. Ha például a **hiányzó rendszerfrissítések**lehetőséget választja, akkor a virtuális gépek és a hiányzó javításokat tartalmazó számítógépek száma, valamint a hiányzó frissítés súlyossága jelenik meg.

> [!NOTE]
> A biztonsági javaslatok ugyanazok, mint a **javaslatok** lapon, de itt vannak szűrve a kiválasztott erőforrás-típusra. A javaslatok megoldásával kapcsolatos további információkért lásd: [biztonsági javaslatok megvalósítása Azure Security Centerban](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>Virtuális gépek és számítógépek
A virtuális gépek és számítógépek szakasz áttekintést nyújt a virtuális gépek és számítógépek biztonsági javaslatairól. A rendszer négyféle típusú gépet tartalmaz:

![Nem Azure-beli számítógép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Nem Azure-alapú számítógép.

![Azure Resource Manager virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager virtuális gép.

![Klasszikus Azure-beli virtuális gép](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klasszikus Azure-beli virtuális gép.

![A munkaterületről azonosított virtuális gépek](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Azok a virtuális gépek, amelyek csak a megtekintett előfizetés részét képező munkaterület alapján azonosít a rendszer. Ebbe beletartoznak az előfizetésben szereplő munkaterületre vonatkozó más előfizetések virtuális gépei, valamint Operations Manager közvetlen ügynökkel telepített virtuális gépek és nem rendelkeznek erőforrás-AZONOSÍTÓval.

Az egyes javaslatok alatt megjelenő ikon segíti a virtuális gép és a beavatkozást igénylő számítógép gyors azonosítását, valamint a javaslat típusát. A szűrőket használhatja **Erőforrás típusa** és **Súlyosság**szerint is a listában való kereséshez.

Az egyes virtuális gépek biztonsági javaslatainak részletezéséhez kattintson a virtuális gépre.
Itt láthatja a virtuális gép vagy a számítógép biztonsági részleteit. Alul a javasolt művelet és az egyes problémák súlyossága látható.
![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Cloud Services
A Cloud Services esetében egy javaslat jön létre, ha az operációs rendszer verziója elavult.

![Felhőszolgáltatások](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Egy olyan forgatókönyvben, amelyben javaslata van, kövesse a javaslat lépéseit az operációs rendszer frissítéséhez. Ha egy frissítés elérhetővé válik, a probléma súlyossága függvényében riasztás jelenik meg (vörös vagy narancssárga). A javaslat részletes ismertetéséhez kattintson az **operációs rendszer verziójának frissítése** elemre a **Leírás** oszlopban.

### <a name="app-services"></a>App Services
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

### <a name="vmscale-sets"></a>Virtuálisgép-méretezési csoportok
Security Center automatikusan felfedi, hogy rendelkezik-e méretezési csoportokkal, és javasolja, hogy telepítse a Microsoft monitoring agentet.

A Microsoft monitoring Agent telepítése: 

1. Válassza a **figyelési ügynök telepítése virtuálisgép-méretezési csoportra**lehetőséget. A nem figyelt méretezési csoportok listáját kapja meg.

1. Válasszon egy nem kifogástalan állapotú méretezési készletet. Kövesse az utasításokat, és telepítse a figyelési ügynököt egy meglévő feltöltött munkaterületen, vagy hozzon létre egy újat. Ha nincs beállítva, ügyeljen arra, hogy a munkaterület [árképzési szintjét](security-center-pricing.md) állítsa be.

   ![Az MMS telepítése](./media/security-center-virtual-machine-recommendations/install-mms.png)

Új méretezési csoportok beállítása a Microsoft monitoring Agent Automatikus telepítéséhez:
1. Nyissa meg Azure Policy és kattintson a **definíciók**elemre.

1. Keresse meg a házirendet a **Windows rendszerű virtuálisgép-méretezési csoportok log Analytics ügynökének üzembe helyezéséhez** , és kattintson rá.

1. Kattintson a **Hozzárendelés** gombra.

1. Állítsa be a **hatókör** és **log Analytics munkaterületet** , és kattintson a **hozzárendelés**elemre.

Ha az összes meglévő méretezési csoportot be szeretné állítani a Microsoft monitoring Agent telepítésére, akkor a Azure Policyban lépjen a **szervizelés** elemre, és alkalmazza a meglévő házirendet a meglévő méretezési csoportokra.


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következő cikkeket:

* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme Azure Security Center](security-center-sql-service-recommendations.md)
