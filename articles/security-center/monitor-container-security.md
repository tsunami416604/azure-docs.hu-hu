---
title: A tárolók biztonságának figyelése az Azure Security Centerben
description: Ismerje meg, hogyan ellenőrizheti a tárolók biztonsági állapotát az Azure Security Centerből
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919532"
---
# <a name="monitoring-the-security-of-your-containers"></a>A tárolók biztonságának figyelése

Ez az oldal bemutatja, hogyan használhatja a tároló biztonsági funkciók a [tároló biztonsági cikkben](container-security.md) leírt fogalmak szakaszban.

Az Azure Security Center a tárolóbiztonság alábbi három aspektusát fedi le:

- **Biztonsági rés kezelése** – Ha a Security Center szabványos tarifacsomagját (lásd: [díjszabási)](/azure/security-center/security-center-pricing)látja el, minden alkalommal beszkatolta az ARM-alapú Azure Container Registry-t, amikor új rendszerképet lenyom. A lapolvasó (qualys-i működtetés) a megállapításokat a Security Center ajánlásaként mutatja be.
    A részletes utasításokat az [alábbi, a tárolójegyzékek beolvasása](#scanning-your-arm-based-container-registries-for-vulnerabilities) című témakörben találja.

- **A tárolók Docker-gazdagépeinek megerősítésé** – A Security Center megtalálja az IaaS Linux-virtuális gépeken vagy más, Docker-t futtató Linux-gépeken tárolt nem felügyelt tárolókat, és folyamatosan összehasonlítja a tárolók konfigurációit a Center for Internet Security (CIS) Docker Benchmark szolgáltatással. A Security Center figyelmezteti, ha a tárolók nem felelnek meg a vezérlők egyikének sem. A helytelen konfigurációk ból eredő biztonsági kockázatok folyamatos figyelése minden biztonsági program kulcsfontosságú eleme. 
    Részletes utasításokat lásd: [A tárolók Docker-gazdagépeinek megerősítéséről.](#hardening-your-containers-docker-hosts)

- **Az Azure Kubernetes service-fürtök megerősítése** – A Biztonsági központ javaslatokat tartalmaz, ha az Azure Kubernetes-szolgáltatásfürtjainak konfigurációjában biztonsági réseket talál. A megjelenő konkrét javaslatok ról a [Kubernetes-szolgáltatás ajánlásai ban talál részleteket.](recommendations-reference.md#recs-containers)

- **Futásidejű védelem** – Ha a Security Center szabványos tarifacsomagját biztosítja, valós idejű fenyegetésvédelmet kap a tárolóba helyezett környezetekben. A Security Center riasztásokat hoz létre a gyanús tevékenységekről a gazdagép és az AKS-fürt szintjén. A vonatkozó biztonsági riasztások, amelyek megjelenhetnek, tekintse meg az [Azure Kubernetes szolgáltatás fürtjei](alerts-reference.md#alerts-akscluster) és [riasztások a tárolók – állomás szintű](alerts-reference.md#alerts-containerhost) szakaszok a riasztások referenciatábla.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>Az ARM-alapú tárolójegyzékek biztonsági résének vizsgálata 

1. Az Azure Container Registry rendszerképek biztonsági résének engedélyezése:

    1. Győződjön meg arról, hogy az Azure Security Center szabványos tarifacsomagját támogatja.

    1. Az **Árképzési & beállítások** lapon engedélyezze az előfizetéshez szükséges ![opcionális tárolójegyzék-csomagot: A tárolójegyzék-csomag engedélyezése](media/monitor-container-security/enabling-container-registries-bundle.png)

        A Security Center most már készen áll a rendszerleíró adatbázisba kerülő képek beszamártására. 

        >[!NOTE]
        >Ez a funkció képenként kerül felszámolásra.


1. A lemezkép vizsgálatának elindításához nyomja le a képazonosítóba. 

    Amikor a vizsgálat befejeződik (általában körülbelül 10 perc után), a megállapítások elérhetők a Security Center ajánlásaiban.
    

1. Az eredmények megtekintéséhez lépjen a **Javaslatok** lapra. Ha problémákat talál, a következő javaslat jelenik meg:

    ![Ajánlás a problémák elhárítására ](media/monitor-container-security/acr-finding.png)


1. Válassza ki a javaslatot. 
    A javaslat részletei lap további információkkal jelenik meg. Ez az információ tartalmazza a sebezhető képekkel rendelkező nyilvántartások listáját ("Érintett erőforrások") és a javítási lépéseket. 

1. Válasszon ki egy adott beállításjegyzéket a sebezhető adattárakat tartalmazó tárolók megtekintéséhez.

    ![Rendszerleíró adatbázis kiválasztása](media/monitor-container-security/acr-finding-select-registry.png)

    Megnyílik a rendszerleíró adatbázis részletei lap az érintett adattárak listájával.

1. Válasszon ki egy adott tárházat a benne lévő, sebezhető rendszerképeket tartalmazó adattárak megtekintéséhez.

    ![Tárház kiválasztása](media/monitor-container-security/acr-finding-select-repository.png)

    Megnyílik a tárház részleteit tartalmazó lap. Felsorolja a sebezhető képeket, valamint a megállapítások súlyosságának értékelését.

1. A biztonsági rések megtekintéséhez jelöljön ki egy adott képet.

    ![Képek kijelölése](media/monitor-container-security/acr-finding-select-image.png)

    Megnyílik a kijelölt képre vonatkozó megállapítások listája.

    ![A megállapítások listája](media/monitor-container-security/acr-findings.png)

1. Ha többet szeretne megtudni a megállapításokról, válassza a keresés lehetőséget. 

    Megnyílik az eredmények részletei ablaktábla.

    [![Eredmények részletei ablaktábla](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Ez az ablaktábla részletesen ismerteti a problémát, és a fenyegetések csökkentése érdekében külső erőforrásokra mutató hivatkozásokat tartalmaz.

1. Kövesse az ablaktábla szervizelési szakaszának lépéseit.

1. Miután megtette a biztonsági probléma elhárításához szükséges lépéseket, cserélje le a lemezképet a beállításjegyzékben:

    1. Nyomja le a frissített képet. Ez elindítja a leés. 
    
    1. Tekintse meg a javaslatok oldalon a javaslat "Biztonsági rések az Azure Container Registry rendszerképek kell kikell újult". 
    
        Ha a javaslat továbbra is megjelenik, és a kezelt kép továbbra is megjelenik a sebezhető képek listájában, ellenőrizze újra a javítási lépéseket.

    1. Ha biztos benne, hogy a frissített lemezképet lenyomták, beolvasták, és már nem jelenik meg a javaslatban, törölje a "régi" sebezhető lemezképet a rendszerleíró adatbázisból.


## <a name="hardening-your-containers-docker-hosts"></a>A tárolók Docker-gazdagépeinek keményedése

A Security Center folyamatosan figyeli a Docker-állomások konfigurációját, és az iparági szabványoknak megfelelő biztonsági javaslatokat hoz létre.

Az Azure Security Center docker-gazdagépeire vonatkozó biztonsági javaslatok megtekintése:

1. A Biztonsági központ navigációs sávján nyissa meg a **Számítási & alkalmazásokat,** és válassza a **Tárolók** lapot.

1. Szükség esetén szűrje a tároló-erőforrások listáját a tárológazdagépekre.

    ![Tároló erőforrások szűrője](media/monitor-container-security/container-resources-filter.png)

1. A tárológazdagépek listájából válasszon egyet a további vizsgálathoz.

    ![Tárológazda javaslatok](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    Megnyílik **a Tárológazdagép információs lapja** az állomás részleteivel és a javaslatok listájával.

1. A javaslatok listájából válasszon ki egy javaslatot a további vizsgálathoz.

    ![Tárológazda javaslatlistája](media/monitor-container-security/container-host-rec.png)

1. Szükség esetén olvassa el a leírás, az információk, a fenyegetések és a javítási lépéseket. 

1. Válassza a **Művelet művelet et** a lap alján.

    [![Művelet ellépése gomb](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    A Log Analytics egy futtatására kész egyéni művelettel nyílik meg. Az alapértelmezett egyéni lekérdezés tartalmazza az összes értékelt sikertelen szabály listáját, valamint a problémák megoldásához szükséges irányelveket.

    [![Log Analytics művelet](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. Csípés a lekérdezési paramétereket, és válassza **a Futtatás,** ha biztos benne, hogy készen áll a gazdagép. 



## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a Security Center tárolóbiztonsági szolgáltatásait. 

Egyéb kapcsolódó anyagokról lásd a következő oldalakat: 

- [A Biztonsági központ javaslatai a tárolókhoz](recommendations-reference.md#recs-containers)
- [Riasztások az AKS-fürtszintjére](alerts-reference.md#alerts-akscluster)
- [Riasztások a tárológazdagép szintjéhez](alerts-reference.md#alerts-containerhost)
