---
title: Élő adatok megtekintése (előzetes verzió) az Azure Monitor tárolókhoz való megtekintéséhez | Microsoft dokumentumok
description: Ez a cikk ismerteti a Kubernetes naplók, események és pod metrikák valós idejű nézetét kubectl használata nélkül az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216541"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>A Kubernetes-naplók, események és pod-metrikák megtekintése valós időben

Az Azure Monitor tárolók tartalmazza a Live Data (előzetes verzió) funkciót, amely egy speciális diagnosztikai funkció, amely lehetővé teszi az Azure Kubernetes-szolgáltatás (AKS) tárolónaplók (stdout/stderror), események és pod metrikák közvetlen hozzáférést biztosít. Ez kiteszi a `kubectl logs -c` `kubectl get` közvetlen hozzáférést, események, és `kubectl top pods`. A konzolablaktábla a tárolómotor által létrehozott naplókat, eseményeket és metrikákat jeleníti meg, hogy valós időben segítse a problémák elhárítását.

Ez a cikk részletes áttekintést nyújt, és segít megérteni, hogyan használhatja ezt a funkciót. 

>[!NOTE]
>Ez a szolgáltatás nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AKS-fürtöket. Ez a szolgáltatás a Kubernetes API közvetlen elérésére támaszkodik a böngészőből származó proxykiszolgálón keresztül. Ha engedélyezi a hálózati biztonságot a Kubernetes API letiltásának a proxyról, az blokkolja ezt a forgalmat. 

>[!NOTE]
>Ez a funkció minden Azure-régióban elérhető, beleértve az Azure China-t is. Jelenleg nem érhető el az Azure US Government.

A Live Data (előzetes verzió) funkció beállításával vagy hibaelhárításával kapcsolatos segítségért olvassa el [a telepítési útmutatót.](container-insights-livedata-setup.md) Ez a funkció közvetlenül hozzáfér a Kubernetes API-hoz, és további információkat talál a hitelesítési modellről [itt.](https://kubernetes.io/docs/concepts/overview/kubernetes-api/) 

## <a name="live-data-preview-functionality-overview"></a>Élő adatok (előzetes verzió) funkció áttekintése

### <a name="search"></a>Keresés

![Példa a Live Data konzol ablaktáblájának szűrőjére](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

A Live Data (előzetes verzió) funkció keresési funkciókat tartalmaz. A **Keresés** mezőben egy kulcsszó vagy kifejezés beírásával szűrheti az eredményeket, és a program kiemeli az egyező eredményeket a gyors ellenőrzés lehetővé teszi. Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **Szűrő** tabletta segítségével is korlátozhatja. Attól függően, hogy milyen erőforrást választott, a tabletta egy podot, névteret vagy fürtöt sorol fel.  

### <a name="scroll-lock-and-pause"></a>Görgetés zárolása és szüneteltetése 

Az automatikus görgetés felfüggesztéséhez és az ablaktábla viselkedésének szabályozásához, amely lehetővé teszi az új adatok olvasásának manuális görgetését, használhatja a **Görgetés** lehetőséget. Az automatikus görgetés újbóli engedélyezéséhez egyszerűen válassza újra a **Görgetés** lehetőséget. A napló- vagy eseményadatok lekérését a **Szüneteltetés** lehetőség kiválasztásával is szüneteltetheti, és ha készen áll a folytatásra, egyszerűen válassza a **Lejátszás**lehetőséget.  

![A Live Data konzol ablaktáblájának szüneteltetése élő nézetben](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Azt javasoljuk, hogy csak a probléma elhárítása közben függessze fel vagy függessze fel az automatikus görgetést rövid ideig. Ezek a kérések hatással lehetnek a Kubernetes API rendelkezésre állására és szabályozására a fürtön. 

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárol véglegesen adatokat. A munkamenet során rögzített összes információ törlődik, amikor bezárja a böngészőt, vagy elnavigál a böngészőből. Az adatok csak a metrikák funkció öt perces ablakán belüli vizualizációhoz maradnak meg; az öt percnél régebbi mérőszámok is törlődnek. A Live Data (előzetes) pufferlekérdezések ésszerű memóriahasználati korlátokon belül (pontosabban kell itt, mi ésszerű?). 

## <a name="view-logs"></a>Naplók megtekintése

Megtekintheti a valós idejű naplóadatokat, ahogy **azokat**a tárolómotor hozza létre a Csomópontok , **Vezérlők**és **Tárolók** nézetből. A naplóadatok megtekintéséhez hajtsa végre a következő lépéseket.

1. Az Azure Portalon keresse meg az AKS-fürt erőforráscsoportot, és válassza ki az AKS-erőforrást.

2. Az AKS-fürt irányítópultján a **Figyelés** a bal oldalon válassza az **Insights**lehetőséget. 

3. Válassza a **Csomópontok**, Vezérlők vagy **Tárolók**lapot. **Containers**

4. Jelöljön ki egy objektumot a teljesítményrácsból, és a jobb oldalon található tulajdonságok ablaktáblán válassza az **Élő adatok megtekintése (előnézet)** lehetőséget. Ha az AKS-fürt az Azure AD használatával egyszeri bejelentkezéssel van konfigurálva, a rendszer kéri, hogy a böngészőmunkamenet során az első használatkor hitelesítse magát. Válassza ki a fiókját, és teljes körű hitelesítést az Azure-ral.  

    >[!NOTE]
    >Amikor a Log Analytics-munkaterületről tekinti meg az adatokat a Tulajdonságok ablaktáblán a **Megtekintés elemzésben** lehetőség kiválasztásával, a naplókeresési eredmények potenciálisan **csomópontokat,** **démonkészleteket, replikakészleteket,** **feladatokat,** **cron-feladatokat,** **podokat**és **tárolókat** jelenítenek meg, amelyek már nem léteznek. **Replica Sets** A nem elérhető tárolók keresésére tett kísérlet `kubectl` itt is sikertelen lesz. Tekintse át a [Nézet elemzéskor](container-insights-log-search.md#search-logs-to-analyze-data) funkciót, ha többet szeretne megtudni a korábbi naplók, események és mérőszámok megtekintéséről.  

A sikeres hitelesítés t követően a Live Data (előzetes verzió) konzolablaktábla a teljesítményadatrács alatt jelenik meg, ahol a naplóadatokat folyamatos adatfolyamban tekintheti meg. Ha a lehívási állapot jelzőzöld pipát jelenít meg, amely az ablaktábla jobb szélén található, az azt jelenti, hogy az adatok lehívhatók, és a konzolra való streamelés megkezdődik.  

![Csomópont tulajdonságai ablaktábla nézet adatai beállítás](./media/container-insights-livedata-overview/node-properties-pane.png)  

Az ablaktábla címe annak a podnak a nevét jeleníti meg, amelyhez a tároló van csoportosítva.

## <a name="view-events"></a>Események megtekintése

Megtekintheti a valós idejű eseményadatokat, ahogy azok a tárolómotor által generált a **csomópontok,** vezérlők , **tárolók**, és **a központi telepítések (előzetes verzió)** nézetben, ha egy tároló, pod, csomópont, ReplicaSet, DaemonSet, feladat, CronJob vagy központi telepítés van kiválasztva. **Containers** Az események megtekintéséhez hajtsa végre az alábbi lépéseket.

1. Az Azure Portalon keresse meg az AKS-fürt erőforráscsoportot, és válassza ki az AKS-erőforrást.

2. Az AKS-fürt irányítópultján a **Figyelés** a bal oldalon válassza az **Insights**lehetőséget. 

3. Válassza a **Csomópontok**, Vezérlők , **Tárolók**vagy Központi **Containers** **telepítések (előzetes verzió)** lapot.

4. Jelöljön ki egy objektumot a teljesítményrácsból, és a jobb oldalon található tulajdonságok ablaktáblán válassza az **Élő adatok megtekintése (előnézet)** lehetőséget. Ha az AKS-fürt az Azure AD használatával egyszeri bejelentkezéssel van konfigurálva, a rendszer kéri, hogy a böngészőmunkamenet során az első használatkor hitelesítse magát. Válassza ki a fiókját, és teljes körű hitelesítést az Azure-ral.  

    >[!NOTE]
    >Amikor a Log Analytics-munkaterületről tekinti meg az adatokat a Tulajdonságok ablaktáblán a **Megtekintés elemzésben** lehetőség kiválasztásával, a naplókeresési eredmények potenciálisan **csomópontokat,** **démonkészleteket, replikakészleteket,** **feladatokat,** **cron-feladatokat,** **podokat**és **tárolókat** jelenítenek meg, amelyek már nem léteznek. **Replica Sets** A nem elérhető tárolók keresésére tett kísérlet `kubectl` itt is sikertelen lesz. Tekintse át a [Nézet elemzéskor](container-insights-log-search.md#search-logs-to-analyze-data) funkciót, ha többet szeretne megtudni a korábbi naplók, események és mérőszámok megtekintéséről.  

A sikeres hitelesítés t követően a Live Data (előzetes verzió) konzolablaktábla megjelenik a teljesítményadatrács alatt. Ha a lehívási állapot jelzőzöld pipát jelenít meg, amely az ablaktábla jobb szélén található, az azt jelenti, hogy az adatok lehívhatók, és a konzolra való streamelés megkezdődik. 
    
Ha a kijelölt objektum tároló volt, válassza az **Események** lehetőséget az ablaktáblában. Ha csomópontot, podot vagy vezérlőt választott, a rendszer automatikusan kiválasztja az események megtekintését. 

![Vezérlő tulajdonságainak ablaktáblájának eseményei](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

Az ablaktábla címe a tárolóhoz csoportosított pod nevét jeleníti meg.

### <a name="filter-events"></a>Események szűrése 

Az események megtekintése közben az eredményeket a keresősáv jobb oldalán található **Szűrő** tabletta segítségével is korlátozhatja. Attól függően, hogy milyen erőforrást választott, a tabletta egy podot, névteret vagy fürtöt sorol fel.  

## <a name="view-metrics"></a>Metrikák megtekintése 

Valós idejű metrikaadatokat tekinthet meg, ahogy azok a tárolómotor által a **csomópontok** vagy **vezérlők** nézetben csak akkor, ha a **Pod** van kiválasztva. A mérőszámok megtekintéséhez hajtsa végre a következő lépéseket.

1. Az Azure Portalon keresse meg az AKS-fürt erőforráscsoportot, és válassza ki az AKS-erőforrást.

2. Az AKS-fürt irányítópultján a **Figyelés** a bal oldalon válassza az **Insights**lehetőséget. 

3. Válassza a **Csomópontok** vagy a **Vezérlők** lapot.

4. Jelöljön ki egy **Pod** objektumot a teljesítményrácsból, és a jobb oldalon található tulajdonságok ablaktáblán válassza az **Élő adatok megtekintése (előnézet)** lehetőséget. Ha az AKS-fürt az Azure AD használatával egyszeri bejelentkezéssel van konfigurálva, a rendszer kéri, hogy a böngészőmunkamenet során az első használatkor hitelesítse magát. Válassza ki a fiókját, és teljes körű hitelesítést az Azure-ral.  

    >[!NOTE]
    >Amikor a Log Analytics-munkaterületről tekinti meg az adatokat a Tulajdonságok ablaktáblán a **Megtekintés elemzésben** lehetőség kiválasztásával, a naplókeresési eredmények potenciálisan **csomópontokat,** **démonkészleteket, replikakészleteket,** **feladatokat,** **cron-feladatokat,** **podokat**és **tárolókat** jelenítenek meg, amelyek már nem léteznek. **Replica Sets** A nem elérhető tárolók keresésére tett kísérlet `kubectl` itt is sikertelen lesz. Tekintse át a [Nézet elemzéskor](container-insights-log-search.md#search-logs-to-analyze-data) funkciót, ha többet szeretne megtudni a korábbi naplók, események és mérőszámok megtekintéséről.  

A sikeres hitelesítés t követően a Live Data (előzetes verzió) konzolablaktábla megjelenik a teljesítményadatrács alatt. A mérőszámok lekérése és a konzolra való streamelés megkezdődik a két diagramon való megjelenítéshez. Az ablaktábla címe annak a podnak a nevét jeleníti meg, amelyhez a tároló van csoportosítva.

![Pod-metrikák megtekintése példa](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>További lépések

- Az Azure [Kubernetes-szolgáltatás állapotának megtekintése.](container-insights-analyze.md)

- Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez riasztások, vizualizációk létrehozásához vagy a fürtök további elemzéséhez.
