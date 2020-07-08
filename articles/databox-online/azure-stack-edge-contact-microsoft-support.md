---
title: A Azure Stack Edge, Azure Data Box Gateway támogatási jegyének naplózása | Microsoft Docs
description: Megtudhatja, hogyan naplózhatja a támogatási kérelmet az Azure Stack Edge-vagy Data Box Gateway-rendelésekkel kapcsolatos problémákhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4d513471e288c1aadbf70b24ef367965a0b69a80
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84339909"
---
# <a name="open-a-support-ticket-for-azure-stack-edge-and-azure-data-box-gateway"></a>Támogatási jegy megnyitása Azure Stack Edge-hez és Azure Data Box Gateway

Ez a cikk Azure Stack Edge-re vonatkozik, és Azure Data Box Gateway mindkettőt az Azure Stack Edge/Azure Data Box Gateway szolgáltatás kezeli. Ha problémák merülnek fel a szolgáltatással kapcsolatban, létrehozhat egy szolgáltatási kérelmet a technikai támogatáshoz. Ez a cikk végigvezeti a következő lépéseken:

* Támogatási kérelem létrehozása.
* Támogatási kérelmek életciklusának kezelése a portálon belülről.

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

Támogatási kérelem létrehozásához hajtsa végre a következő lépéseket:

1. Nyissa meg az Azure Stack Edge-vagy Data Box Gateway-sorrendet. Navigáljon a **támogatás + hibaelhárítás** szakaszhoz, majd válassza az **új támogatási kérelem**lehetőséget.

2. Az **új támogatási kérelemben**az **alapok** lapon végezze el a következő lépéseket:

    1. A **probléma típusa** legördülő listából válassza a **technikai**lehetőséget.
    2. Válassza ki az **Előfizetést**.
    3. A **szolgáltatás**alatt keresse meg **a saját szolgáltatások**szolgáltatást. A legördülő listából válassza az **Azure stack Edge és a Data Box Gateway**lehetőséget.
    4. Válassza ki az **erőforrást**. Ez megfelel a megrendelés nevének.
    5. Adja meg a tapasztalt probléma rövid **összefoglalását** . 
    6. Válassza ki a **probléma típusát**.
    7. A kiválasztott probléma típusa alapján válasszon ki egy megfelelő **problémát altípust**.
    8. Válassza a **tovább lehetőséget: megoldások >>**.

        ![Alapvető beállítások](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)

3. A **részletek** lapon hajtsa végre a következő lépéseket:

    1. Adja meg a probléma kezdő dátumát és időpontját.
    2. Adja meg a probléma **leírását** .
    3. A **fájl feltöltése**lapon válassza a mappa ikont a feltölteni kívánt fájlok tallózásához.
    4. A **diagnosztikai adatok megosztásának**megadásához.
    5. Az előfizetés alapján a rendszer automatikusan kitölti a **támogatási csomagot** .
    6. A legördülő listából válassza ki a **súlyosságot**.
    7. Válasszon ki egy **előnyben részesített kapcsolattartási módszert**.
    8. Az előfizetési terv alapján a rendszer automatikusan kiválasztja a **válaszidőt** .
    9. Adja meg a kívánt nyelvet a támogatáshoz.
    10. A **kapcsolattartási adatok**területen adja meg a nevét, e-mail-címét, telefonszámát, nem kötelező elérhetőségét, országát/régióját. Microsoft ügyfélszolgálata ezeket az információkat a további információk, a diagnosztika és a megoldás eléréséhez használja fel. 
    11. Válassza a **Tovább: Áttekintés + >>létrehozása **elemet.

        ![Probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-2.png)

4. A **felülvizsgálat + létrehozás** lapon tekintse át a támogatási jegyhez kapcsolódó információkat. Válassza a **Létrehozás** lehetőséget. 

    ![Probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-3.png)

    Miután létrehozta a támogatási jegyet, a támogatási szakember a lehető leghamarabb kapcsolatba lép Önnel, hogy folytassa a kérést.

## <a name="get-hardware-support"></a>Hardveres támogatás beszerzése

Ez az információ csak Azure Stack eszközre vonatkozik. A hardveres problémák jelentésének folyamata a következő:

1. Hardveres probléma esetén nyisson meg egy támogatási jegyet a Azure Portal. A **probléma típusa**területen válassza a **Azure stack hardver**lehetőséget. Válassza ki a **probléma altípusát** **hardverhibaként**.

    ![Hardveres probléma](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-hardware-issue-1.png)

    Miután létrehozta a támogatási jegyet, a támogatási szakember a lehető leghamarabb kapcsolatba lép Önnel, hogy folytassa a kérést.

2. Ha Microsoft ügyfélszolgálata megállapítja, hogy ez egy hardveres probléma, akkor a következő műveletek egyike történik:

    * A rendszer elküldje egy mező típusú helyettesítő egységet a meghibásodott hardver részhez. Jelenleg a tápegységek és a merevlemezek az egyetlen támogatott FRUs.
    * A következő munkanapon belül csak a FRUs lesznek lecserélve, minden más esetben teljes rendszer-cserét (FSR) kell leszállítani.

3. Ha a támogatási jegyet a helyi idő szerint (hétfőtől péntekig) 4:30 óráig emelik ki, akkor a helyszíni technikus a következő munkanapon elküldi azt a helyet, amely egy cserélhető eszköz vagy teljes eszköz cseréjét hajt végre.

## <a name="manage-a-support-request"></a>Támogatási kérelem kezelése

A támogatási jegy létrehozása után a jegyet a teljes életciklusán keresztül kezelheti a portálon.

### <a name="to-manage-your-support-requests"></a>A támogatási kérések kezelése

1. A Súgó és támogatás lapon keresse meg a **tallózás > Súgó + támogatás**menüpontot.

    ![Támogatási kérelmek kezelése](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-manage-support-request-1.png)

2. A **Súgó és támogatás**szolgáltatásban megjelenik a **legújabb támogatási kérelmek** táblázatos listája.

    <!--[Manage support requests](./media/azure-stack-edge-contact-microsoft-support/data-box-edge-support-request-1.png)--> 

3. Válasszon ki egy támogatási kérést, és kattintson rá. Megtekintheti a kérelem állapotát és részleteit. Ha ezt a kérést szeretné követni, kattintson az **+ új üzenet** elemre.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan lehet [elhárítani az Azure stack Edge-vel kapcsolatos problémákat](azure-stack-edge-troubleshoot.md).
Ismerje meg, hogyan lehet [elhárítani a Data Box Gateway kapcsolatos problémákat](data-box-gateway-troubleshoot.md).
