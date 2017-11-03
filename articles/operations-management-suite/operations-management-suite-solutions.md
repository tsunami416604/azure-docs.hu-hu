---
title: "Az Operations Management Suite (OMS) megoldások |} Microsoft Docs"
description: "Megoldások bővítése Operations Management Suite (OMS), adja meg a csomagolt felügyeleti lehetőségeket, amelyek az ügyfelek az OMS-munkaterület adhat hozzá.  Ez a cikk ügyfelek és partnerek által létrehozott hogyan egyéni megoldások részleteit."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2443dd73fdf441721bd6f6f340da515d9f5a22a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-management-solutions-in-operations-management-suite-oms-preview"></a>Felügyeleti megoldások Operations Management Suite (OMS) (előzetes verzió) használatával
> [!NOTE]
> Ez a felügyeleti megoldásokra az OMS Szolgáltatáshoz, amely jelenleg előzetes verzióban érhetők előzetes dokumentáció.    
> 
> 

Megoldások bővítése Operations Management Suite (OMS), adja meg a csomagolt felügyeleti lehetőségeket, amelyek az ügyfelek a környezetükben adhat hozzá.  Kívül [Microsoft által biztosított megoldások](../log-analytics/log-analytics-add-solutions.md), partnereinknek és ügyfeleinknek hozhat létre a saját környezetben használt vagy elérhetővé tenni az ügyfelek a közösségi keresztül megoldások.

## <a name="finding-and-installing-management-solutions"></a>Keresése és telepítése a megoldások
Több módon keresése és telepítése a felügyeleti megoldás, a következő szakaszokban ismertetett módon.

### <a name="azure-marketplace"></a>Azure Piactér
A Microsoft által biztosított megoldások és megbízható partnerei az Azure-portálon az Azure piactérről is települ.

1. Jelentkezzen be az Azure-portálon.
2. A bal oldali panelen válassza ki a **további szolgáltatások**.
3. Görgessen le a vagy **megoldások** vagy típus *megoldások* be a **szűrő** párbeszédpanel.
4. Kattintson a **+ Hozzáadás** gombra.
5. Keresse meg a megoldások, amelyek továbbra is érdekli vagy keresse meg azt, kattintson a **szűrő** gombra, vagy írja be a a **keresési Everthing** mezőbe.
6. Kattintson a Piactéri elemet a részletes információk megtekintéséhez.
7. Kattintson a **létrehozása** megnyitásához a **megoldás hozzáadása** ablaktáblán.
8. Kérni fogja a szükséges információkat, mint a [OMS munkaterületet, és az Automation-fiók](#oms-workspace-and-automation-account) mellett a megoldásban a paraméterek értékeit.
9. Kattintson a **létrehozása** a megoldás telepítéséhez.

### <a name="oms-portal"></a>OMS-portálon
A Microsoft által biztosított megoldások is települ a megoldások galériából az OMS-portálon.

1. Jelentkezzen be az OMS-portálon.
2. Kattintson a **megoldások gyűjtemény** csempére.
3. Az OMS-megoldások gyűjtemény oldalon megismerése minden elérhető megoldás. Kattintson a nevére, a megoldás, amely hozzá szeretne adni az OMS Szolgáltatáshoz.
4. A választott megoldás az oldalon a megoldás részletes információkat jelenít meg. Kattintson az **Add** (Hozzáadás) parancsra.
5. Egy új csempe jelenik meg a lap OMS, és kezdheti azt követően az OMS-szolgáltatás feldolgozza az adatok áttekintésében hozzáadott a megoldáshoz.

### <a name="azure-quickstart-templates"></a>Azure-gyorssablonok
A Közösség tagjai elküldheti a megoldások Azure gyors üzembe helyezési sablonokat.  Töltse le a későbbi telepítési ezeket a sablonokat vagy vizsgálja meg, hogy ismerje meg, hogyan [hozzon létre egy saját megoldások](#creating-a-solution).

1. Kövesse az ismertetett folyamatot [OMS munkaterületet, és az Automation-fiók](#oms-workspace-and-automation-account) egy munkaterület és a fiók összekapcsolásához.
2. Ugrás a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/).  
3. Keressen olyan megoldás, amely kíváncsiak vagyunk.
4. Az eredmények a részletek megtekintéséhez válasszon a megoldás.
5. Kattintson a **az Azure telepítéséhez** gombra.
6. Adja meg az információkat, például az erőforráscsoportot és helyet értékek mellett azokat a paramétereket a megoldásban kéri.
7. Kattintson a **beszerzési** a megoldás telepítéséhez.

### <a name="deploy-azure-resource-manager-template"></a>Azure Resource Manager-sablon üzembe helyezése
Megoldások, hogy a közösségi vagy, hogy [saját](#creating-a-solution) valósíthatók meg a Resource Manager sablonként is használhatja a szabványos módszerek és [a sablonok telepítésével](../azure-resource-manager/resource-group-template-deploy-portal.md).  Vegye figyelembe, hogy a megoldás a telepítés előtt létre kell hoznia és csatolja a [OMS munkaterületet, és az Automation-fiók](#oms-workspace-and-automation-account).

## <a name="oms-workspace-and-automation-account"></a>OMS-munkaterület és Automation-fiók
A legtöbb felügyeleti megoldás szükséges egy [OMS-munkaterület](../log-analytics/log-analytics-manage-access.md) nézeteket tartalmaz, és egy [Automation-fiók](../automation/automation-security-overview.md#automation-account-overview) magában foglalja a runbookok és kapcsolódó erőforrások. A munkaterület és a fiók az alábbi követelményeknek kell megfelelniük.

* A megoldás csak akkor tudja használni, egy OMS-munkaterület és egy Automation-fiók.  
* Az OMS-munkaterület és a megoldás által használt Automation-fiók össze kell kapcsolni egy másik. Az OMS-munkaterület csak lehet, hogy egy Automation-fiók csatolva, és előfordulhat, hogy egy OMS-munkaterület csak csatolható Automation-fiók.
* Kell társítani, az OMS-munkaterület és Automation-fiók az erőforráscsoportot és a régióban kell lennie.  A kivétel: USA keleti régiójában az OMS-munkaterület és és az Automation-fiók az USA keleti régiója 2.

### <a name="creating-a-link-between-an-oms-workspace-and-automation-account"></a>Automation-fiók és az OMS-munkaterület közötti kapcsolat létrehozása
Hogyan az OMS-munkaterület ad meg, és a megoldás a telepítési módszer függ az Automation-fiókhoz.

* A Microsoft-megoldás az OMS-portálon keresztül történő telepítésekor van telepítve, a jelenlegi OMS-munkaterület, és nem Automation-fiók szükséges.
* Amikor telepít egy megoldást az Azure piactéren keresztül, az OMS-munkaterület és Automation-fiók kéri, és azokat közötti kapcsolat jön létre.  
* Az Azure piactéren kívül megoldásainak kell kapcsolni az OMS-munkaterület és Automation-fiók a megoldás telepítése előtt.  Ehhez jelölje ki a megoldás az Azure piactéren, és az OMS-munkaterület és Automation-fiók kiválasztása.  Nem kell ténylegesen telepíteni a megoldás, mert a kapcsolat létrejön, amint az OMS-munkaterület Automation-fiók ki van választva.  Ha a kapcsolat létrejött, majd használhatja, hogy OMS-munkaterület és Automation-fiók minden megoldás. 

### <a name="verifying-the-link-between-an-oms-workspace-and-automation-account"></a>Az OMS-munkaterület és az Automation-fiók közötti kapcsolat ellenőrzése
Ellenőrizheti az alábbi eljárást követve Automation-fiók és az OMS-munkaterület közötti kapcsolat.

1. Válassza ki az Automation-fiók az Azure portálon.
2. Alján görgessen a **beállítások** ablaktáblán.
3. Van-e nevű szakaszban **OMS-erőforrások** a a **beállítások** ablaktáblán, majd ezt a fiókot az OMS-munkaterület van csatolva.
4. Válassza ki **munkaterület** az OMS-munkaterület részletes adatainak megtekintéséhez az Automation-fiókhoz csatolva.

## <a name="listing-management-solutions"></a>Megoldások listázása
A következő eljárás segítségével a tekintse meg a felügyeleti megoldásokra a munkaterületek csatolva az Azure-előfizetéshez.

1. Jelentkezzen be az Azure-portálon.
2. A bal oldali panelen válassza ki a **további szolgáltatások**.
3. Görgessen le a vagy **megoldások** vagy típus *megoldások* be a **szűrő** párbeszédpanel.
4. A munkaterületek telepített megoldások jelenik meg.

Vegye figyelembe, hogy csak az aktuális munkaterület használata az OMS-portálon telepített Microsoft megoldások megtekintése.

## <a name="removing-a-management-solution"></a>A felügyeleti megoldás eltávolítása
Ha eltávolítja az olyan felügyeleti megoldást, a megoldás összes erőforrást is eltávolítja.  

1. Keresse meg a megoldást az Azure portálon, hajtsa végre az eljárást a [megoldások listázása](#listing-solutions).
2. Válassza ki az eltávolítani kívánt megoldás.
3. Kattintson a **törlése** gombra.

## <a name="creating-a-management-solution"></a>A felügyeleti megoldás létrehozása
Teljes útmutatás létrehozása kezelési megoldások érhetők el [megoldások létrehozása az Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md). 

## <a name="next-steps"></a>Következő lépések
* Keresési [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates) példákért különböző Resource Manager-sablonok.
* Hozza létre a sajátját [megoldások](operations-management-suite-solutions-creating.md).

