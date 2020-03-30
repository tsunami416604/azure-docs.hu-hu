---
title: Integrálja az Azure Monitor ta-leképezése az Operations Managerrel | Microsoft dokumentumok
description: Az Azure Monitor virtuális gépek automatikusan felderíti az alkalmazás-összetevőket a Windows és linuxos rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ez a cikk a Map szolgáltatás használatával automatikusan létrehozza az elosztott alkalmazásdiagramokat az Operations Managerben.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663453"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>A System Center Operations Manager integrálása az Azure Monitor for VMs Map szolgáltatással

A virtuális gépek hez az Azure-figyelőben megtekintheti az Azure-ban vagy a környezetben futó Windows és Linux virtuális gépeken (VM-ek). Ezzel az integrációval a Map szolgáltatás és a System Center Operations Manager, automatikusan létrehozhat elosztott alkalmazásdiagramok operations manager, amelyek alapján a dinamikus függőségi leképezések az Azure Monitor virtuális gépekhez. Ez a cikk azt ismerteti, hogy miként konfigurálható a System Center Operations Manager felügyeleti csoport a szolgáltatás támogatására.

>[!NOTE]
>Ha már telepítette a Szolgáltatástérképet, megtekintheti a leképezéseket az Azure Monitor virtuális gépekhez szolgáltatásában, amely további funkciókat tartalmaz a virtuális gépek állapotának és teljesítményének figyeléséhez. Az Azure Monitor virtuális gépekhez szolgáltatás ának leképezési szolgáltatása az önálló szolgáltatástérkép-megoldás lecserélésére szolgál. További információ: [Azure Monitor for VMs overview](vminsights-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* A System Center Operations Manager felügyeleti csoport (2012 R2 vagy újabb).
* A Log Analytics munkaterület, amely az Azure Monitor virtuális gépek támogatására konfigurálva van.
* Egy vagy több Windows- és Linux-os virtuális gép vagy fizikai számítógép, amelyet az Operations Manager figyel, és adatokat küld a Log Analytics-munkaterületre. Az Operations Manager felügyeleti csoportnak jelentést tevő Linux-kiszolgálókat úgy kell konfigurálni, hogy közvetlenül csatlakozzanak az Azure Monitorhoz. További információért tekintse át a [Naplóadatok gyűjtése a Log Analytics-ügynökkel](../platform/log-analytics-agent.md)című témaköráttekintését.
* Az Azure-előfizetéshez való hozzáféréssel rendelkező egyszerű szolgáltatás, amely a Log Analytics-munkaterülethez van társítva. További információért nyissa meg [az Egyszerű szolgáltatás létrehozása](#create-a-service-principal)című területet.

## <a name="install-the-service-map-management-pack"></a>A Szervizcsomag felügyeleti csomag jának telepítése

Az Operations Manager és a Map szolgáltatás közötti integrációt a Microsoft.SystemCenter.ServiceMap felügyeleti csomag (Microsoft.SystemCenter.ServiceMap.mpb) importálásával engedélyezheti. A felügyeleti csomag a [Microsoft letöltőközpontjából](https://www.microsoft.com/download/details.aspx?id=55763)tölthető le. A csomag a következő felügyeleti csomagokat tartalmazza:

* Microsoft szolgáltatástérkép alkalmazásnézetei
* Microsoft System Center szolgáltatástérkép belső
* A Microsoft System Center szolgáltatástérkép felülbírálása
* Microsoft System Center szolgáltatástérkép

## <a name="configure-integration"></a>Integráció konfigurálása

A Szolgáltatástérkép felügyeleti csomag telepítése után egy új csomópont, **a Szolgáltatástérkép**jelenik meg az **Operations Management Suite** csoportban az Operations Manager Operations konzol **Felügyeleti** ablaktáblájában.

>[!NOTE]
>[Az Operations Management Suite olyan szolgáltatások gyűjteménye volt,](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) amelyek tartalmazzák a Log Analytics szolgáltatást, és most már az [Azure Monitor](../overview.md)része.

Az Azure Monitor virtuális gépek leképezési integrációjának konfigurálásához tegye a következőket:

1. A konfigurációs varázsló megnyitásához kattintson a **Szolgáltatástérkép áttekintése** ablaktáblán a **Munkaterület hozzáadása gombra.**  

    ![Szolgáltatásleképezés – áttekintés ablaktábla](media/service-map-scom/scom-configuration.png)

2. A **Kapcsolat konfigurációja** ablakban adja meg a bérlő nevét vagy azonosítóját, az alkalmazásazonosítót (más néven felhasználónevet vagy ügyfélazonosítót) és a szolgáltatásnév jelszavát, majd kattintson a **Tovább**gombra. További információért nyissa meg az Egyszerű szolgáltatás létrehozása című területet.

    ![A Kapcsolat konfigurációja ablak](media/service-map-scom/scom-config-spn.png)

3. Az **Előfizetés kiválasztása** ablakban válassza ki az Azure-előfizetést, az Azure erőforráscsoportot (a Log Analytics-munkaterületet tartalmazót) és a Log Analytics-munkaterületet, majd kattintson a **Tovább**gombra.

    ![Az Operations Manager konfigurációs munkaterülete](media/service-map-scom/scom-config-workspace.png)

4. A **Gépcsoport kiválasztása** ablakban kiválaszthatja, hogy mely szervizleképező gépcsoportokat szeretné szinkronizálni az Operations Manager rel. Kattintson **a Gépcsoportok hozzáadása/eltávolítása**gombra, válasszon csoportokat az **Elérhető számítógépcsoportok**listából, majd kattintson a **Hozzáadás**gombra.  Ha végzett a csoportok kijelölése, kattintson **az Ok gombra** a befejezéshez.

    ![Az Operations Manager konfigurációs gépcsoportjai](media/service-map-scom/scom-config-machine-groups.png)

5. A **Kiszolgálóválasztás** ablakban a Szolgáltatásleképezési kiszolgálók csoportot az Operations Manager és a Térkép szolgáltatás között szinkronizálni kívánt kiszolgálókkal konfigurálhatja. Kattintson **a Kiszolgálók hozzáadása/eltávolítása gombra.**

    Ahhoz, hogy az integráció elosztott alkalmazásdiagramot hozzon létre egy kiszolgálóhoz, a kiszolgálónak a következőnek kell lennie:

   * Az Operations Manager figyeli
   * Konfigurálva az Azure-figyelő virtuális gépekhez konfigurált Log Analytics-munkaterületének való jelentésre
   * A Szolgáltatásleképezési kiszolgálók csoportban szerepel

     ![Az Operations Manager konfigurációs csoportja](media/service-map-scom/scom-config-group.png)

6. Nem kötelező: Válassza ki a Minden felügyeleti kiszolgáló erőforráskészletet a Log Analytics szolgáltatással való kommunikációhoz, majd kattintson **a Munkaterület hozzáadása gombra.**

    ![Az Operations Manager konfigurációs erőforráskészlete](media/service-map-scom/scom-config-pool.png)

    A Log Analytics-munkaterület konfigurálása és regisztrálása eltarthat egy percig. Konfigurálása után az Operations Manager elindítja az első térképszinkronizálást.

    ![Az Operations Manager konfigurációs erőforráskészlete](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Az integráció figyelése

A Log Analytics munkaterület csatlakoztatása után egy új mappa, a Szolgáltatástérkép jelenik meg az Operations Manager műveleti konzol **Figyelés** ablaktábláján.

![Az Operations Manager figyelési ablaktáblája](media/service-map-scom/scom-monitoring.png)

A Szolgáltatástérkép mappa négy csomódból áll:

* **Aktív riasztások:** Felsorolja az Operations Manager és az Azure Monitor közötti kommunikációra vonatkozó összes aktív riasztást.  

  >[!NOTE]
  >Ezek a riasztások nem az Operations Managerrel szinkronizált Log Analytics-riasztások, hanem a felügyeleti csoportban jönnek létre a Service Map felügyeleti csomagban definiált munkafolyamatok alapján.

* **Kiszolgálók**: Felsorolja azokat a figyelt kiszolgálókat, amelyek úgy vannak konfigurálva, hogy szinkronizáljanak az Azure Monitor szolgáltatásból a virtuális gépek leképezése szolgáltatásból.

    ![Az Operations Manager figyelőkiszolgálóinak ablaktáblája](media/service-map-scom/scom-monitoring-servers.png)

* **Gépcsoport-függőségi nézetek**: A Térkép szolgáltatásból szinkronizált összes számítógépcsoport listája. Bármelyik csoportra kattintva megtekintheti az elosztott alkalmazásdiagramot.

    ![Az Operations Manager elosztott alkalmazásdiagramja](media/service-map-scom/scom-group-dad.png)

* **Kiszolgálófüggőségi nézetek**: A Térkép szolgáltatásból szinkronizált összes kiszolgáló listázása. Bármelyik kiszolgálóra kattintva megtekintheti az elosztott alkalmazásdiagramot.

    ![Az Operations Manager elosztott alkalmazásdiagramja](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Munkaterület szerkesztése vagy törlése

A konfigurált munkaterületet a Szolgáltatástérkép áttekintése ablaktáblán **(A** **szolgáltatásleképezési** panelen > **Operations Management Suite** > Service Map) szerkesztheti vagy**törölheti.**

>[!NOTE]
>[Az Operations Management Suite olyan szolgáltatások gyűjteménye volt,](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) amelyek tartalmazzák a Log Analytics szolgáltatást, amely jelenleg az [Azure Monitor](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)része.

Ebben a kiadásban csak egy Log Analytics-munkaterületkonfigurálható.

![Az Operations Manager Munkaterület szerkesztése ablaktábla](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Szabályok és felülbírálások konfigurálása

Egy szabály, *a Microsoft.SystemCenter.ServiceMapImport.Rule*, rendszeresidőközönként lekéri az adatokat az Azure Monitor a virtuális gépek map szolgáltatás. A szinkronizálási időköz módosításához felülbírálhatja a szabályt, és módosíthatja az **IntervalMinutes**paraméter értékét.

![Az Operations Manager felülbírálja a tulajdonságokablakot](media/service-map-scom/scom-overrides.png)

* **Engedélyezve**: Az automatikus frissítések engedélyezése vagy letiltása.
* **IntervalMinutes**: A frissítések közötti időt adja meg. Az alapértelmezett időköz egy óra. Ha gyakrabban szeretné szinkronizálni a térképeket, módosíthatja az értéket.
* **TimeoutSeconds**: Megadja a kérelem időmúlásának idejét megelőző időtartamot.
* **TimeWindowMinutes**: Az adatok lekérdezésének időablakát adja meg. Az alapértelmezett érték 60 perc, amely a maximálisan engedélyezett időköz.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az aktuális kialakítás a következő problémákat és korlátozásokat mutatja be:

* Csak egyetlen Log Analytics-munkaterülethez csatlakozhat.
* Bár a Szolgáltatástérkép-kiszolgálók csoporthoz manuálisan is hozzáadhat **kiszolgálókat** a Szerzői ablaktáblán keresztül, a rendszer nem szinkronizálja azonnal a kiszolgálók leképezéseit. A következő szinkronizálási ciklus során az Azure Monitor szolgáltatás a virtuális gépek leképezési funkciójából lesz szinkronizálva.
* Ha módosítja a felügyeleti csomag által létrehozott elosztott alkalmazásdiagramokat, ezek a módosítások valószínűleg felüllesznek írva a következő szinkronizáláskor az Azure Monitor szolgáltatással a virtuális gépekhez.

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Az egyszerű szolgáltatás létrehozásáról szóló hivatalos Azure-dokumentációa:

* [Egyszerű szolgáltatás létrehozása a PowerShell használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Egyszerű szolgáltatás létrehozása az Azure CLI használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Egyszerű szolgáltatás létrehozása az Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Visszajelzés
Van-e visszajelzése az Azure Monitor szolgáltatásmap funkciójával való integrációról, vagy ez a dokumentáció? Látogasson el [a User Voice oldalra,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)ahol funkciókat javasolhat, vagy szavazhat a meglévő javaslatokról.
