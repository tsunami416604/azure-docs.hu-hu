---
title: A tárolók biztonságának figyelése Azure Security Center
description: Ismerje meg, hogyan ellenőrizhető a tárolók biztonsági helyzete Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 900398a701659bff593df042db16890792e5cffd
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744742"
---
# <a name="monitoring-the-security-of-your-containers"></a>A tárolók biztonságának figyelése

Ez az oldal ismerteti, hogyan használható a Container [Security című cikkben](container-security.md) ismertetett tároló biztonsági szolgáltatások a fogalmak szakaszban.

A Azure Security Center a tárolók biztonságának következő három aspektusát fedi le:

- **Biztonsági rések kezelése** – ha Security Center Standard díjszabási szinttel rendelkezik (lásd a [díjszabást](/azure/security-center/security-center-pricing)), akkor a ARM-alapú Azure Container Registry minden alkalommal beolvashatók, amikor új rendszerképet küldenek. A képolvasó (Qualys-alapú) Security Center javaslatként mutatja be az eredményeket.
    Részletes útmutatásért lásd: a tároló-beállításjegyzékek keresése az alábbi [biztonsági rések esetében](#scanning-your-arm-based-container-registries-for-vulnerabilities) .

- **A tárolók Docker-gazdagépek** megtartása – Security Center megkeresi a IaaS Linux rendszerű virtuális gépeken, illetve a Docker-t futtató más Linux-gépeken üzemeltetett nem felügyelt tárolókat, és folyamatosan összehasonlítja a tárolók konfigurációit az Internet Security (CIS) Docker-viszonyítási Security Center riasztást küld, ha a tárolók nem elégítik ki a vezérlők egyikét sem. A biztonsági kockázatok folyamatos figyelése a helytelen konfigurációk miatt a biztonsági programok kulcsfontosságú összetevője. 
    Részletes útmutatásért lásd: [a tárolók Docker-gazdagépek megkeményedése](#hardening-your-containers-docker-hosts) alább.

- **Az Azure Kubernetes Service-fürtök megerősítése** – a Security Center ajánlásokat nyújt, amikor megkeresi a biztonsági réseket az Azure Kubernetes Service-fürtök konfigurációjában. Az esetlegesen megjelenő javaslatok részleteiért tekintse meg a [Kubernetes szolgáltatási javaslatait](recommendations-reference.md#recs-containers).

- **Futtatókörnyezet védelme** – ha Security Center Standard díjszabási szintje, valós idejű veszélyforrások elleni védelmet kap a tároló környezetek számára. Security Center riasztásokat hoz létre a gyanús tevékenységekhez a gazdagép és az AK-fürt szintjén. Az esetlegesen megjelenő biztonsági riasztások részleteiért tekintse meg az [Azure Kubernetes Service Clusters szolgáltatáshoz tartozó riasztásokat](alerts-reference.md#alerts-akscluster) , valamint a [tárolók –](alerts-reference.md#alerts-containerhost) a riasztások című táblázat gazdagép szintű fejezeteinek riasztásait.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>A ARM-alapú tároló-beállításjegyzékek vizsgálata a biztonsági rések esetében 

1. A Azure Container Registry-lemezképek sebezhetőségi vizsgálatának engedélyezése:

    1. Győződjön meg arról, hogy a Azure Security Center Standard díjszabási szintje van.

    1. A **díjszabási & beállítások** lapon engedélyezze az előfizetéshez választható tároló-beállításjegyzékek köteget ![: a Container nyilvántartórendszer-köteg engedélyezése](media/monitor-container-security/enabling-container-registries-bundle.png)

        Security Center most már készen áll a beállításjegyzékbe leküldeni kívánt rendszerképek vizsgálatára. 

        >[!NOTE]
        >Ez a funkció rendszerkép alapján van felszámítva.


1. A rendszerkép vizsgálatának elindításához küldje le azt a beállításjegyzékbe. 

    Ha a vizsgálat befejeződik (általában körülbelül 10 percet követően), az eredmények Security Center javaslatokban érhetők el.
    

1. A megállapítások megtekintéséhez nyissa meg a **javaslatok** lapot. Ha probléma merült fel, a következő javaslat jelenik meg:

    ![Javaslatok a problémák megoldásához ](media/monitor-container-security/acr-finding.png)


1. Válassza ki a javaslatot. 
    Megnyílik a javaslat részletei lap, amely további információkat tartalmaz. Ez az információ tartalmazza a sebezhető rendszerképekkel ("érintett erőforrásokkal") és a Szervizelési lépésekkel rendelkező kibocsátásiegység-forgalmi jegyzékek listáját. 

1. Válasszon ki egy adott beállításjegyzéket, és tekintse meg a védett adattárakkal rendelkező adattárakat.

    ![Beállításjegyzék kiválasztása](media/monitor-container-security/acr-finding-select-registry.png)

    Megnyílik a beállításjegyzék részletei lap az érintett adattárak listájával.

1. Válasszon ki egy adott tárházat a sebezhető lemezképekkel rendelkező adattárak megtekintéséhez.

    ![Adattár kiválasztása](media/monitor-container-security/acr-finding-select-repository.png)

    Megnyílik az adattár részletei lap. Felsorolja a sebezhető lemezképeket, valamint az eredmények súlyosságának értékelését.

1. Válasszon ki egy adott képet a biztonsági rések megtekintéséhez.

    ![Képek kiválasztása](media/monitor-container-security/acr-finding-select-image.png)

    Megnyílik a kiválasztott rendszerkép megállapításainak listája.

    ![Megállapítások listája](media/monitor-container-security/acr-findings.png)

1. Ha többet szeretne megtudni a keresésről, válassza a keresés lehetőséget. 

    Megnyílik a megállapítások részletei ablaktábla.

    [![Megállapítások részletei ablaktábla](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Ez a panel a probléma részletes ismertetését és a külső erőforrásokra mutató hivatkozásokat tartalmaz a fenyegetések enyhítése érdekében.

1. Kövesse az ablaktábla szervizelés szakaszának lépéseit.

1. Ha elvégezte a biztonsági probléma megoldásához szükséges lépéseket, cserélje le a rendszerképet a beállításjegyzékbe:

    1. A frissített rendszerkép leküldése. Ez elindítja a vizsgálatot. 
    
    1. Tekintse meg a "biztonsági rések a Azure Container Registry lemezképekben" című részt a javaslatok oldalon. 
    
        Ha a javaslat továbbra is megjelenik, és a kezelt lemezkép továbbra is megjelenik a sebezhető lemezképek listájában, ellenőrizze újra a szervizelés lépéseit.

    1. Ha biztos abban, hogy a frissített rendszerkép le lett küldve, a vizsgálat megtörtént, és már nem jelenik meg a javaslatban, törölje a "régi" sebezhető rendszerképet a beállításjegyzékből.


## <a name="hardening-your-containers-docker-hosts"></a>A tárolók Docker-gazdagépének megerősítése

Security Center folyamatosan figyeli a Docker-gazdagépek konfigurációját, és biztonsági javaslatokat hoz létre az iparági szabványoknak megfelelően.

A Azure Security Center a tárolók Docker-gazdagépekre vonatkozó biztonsági javaslatainak megtekintéséhez:

1. A Security Center navigációs sávon nyissa meg a **számítási & alkalmazások** elemet, és válassza a **tárolók** fület.

1. Szükség esetén szűrheti a tároló-erőforrások listáját a tároló gazdagépei számára.

    ![Tároló erőforrásainak szűrője](media/monitor-container-security/container-resources-filter.png)

1. A Container Host Machines listájából válassza ki az egyiket a további vizsgálathoz.

    ![A Container Host-javaslatok](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Megnyílik a **tároló-gazdagép adatai lap** , amely a gazdagép adatait és a javaslatok listáját tartalmazza.

1. A javaslatok listából válasszon ki egy javaslatot a további vizsgálathoz.

    ![Container Host-javaslatok listája](media/monitor-container-security/container-host-rec.png)

1. Szükség esetén olvassa el a leírást, az információkat, a fenyegetéseket és a Szervizelési lépéseket. 

1. A lap alján válassza a **művelet elvégzése** elemet.

    [![Művelet elvégzése gomb](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    A Log Analytics egy egyéni művelettel nyílik meg, amely készen áll a futtatásra. Az alapértelmezett egyéni lekérdezés tartalmazza az összes észlelt szabály listáját, valamint útmutatást nyújt a problémák megoldásához.

    [![Log Analytics művelet](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Szükség esetén a lekérdezés paramétereinek csípése.

1. Ha biztos abban, hogy a parancs megfelelő, és készen áll a gazdagépre, válassza a **Futtatás**lehetőséget.



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a Security Center tároló biztonsági funkcióit. 

Más kapcsolódó anyagok esetében tekintse meg a következő lapokat: 

- [Security Centeri javaslatok a tárolók számára](recommendations-reference.md#recs-containers)
- [AK-fürt szintű riasztások](alerts-reference.md#alerts-akscluster)
- [A Container Host szintű riasztások](alerts-reference.md#alerts-containerhost)
