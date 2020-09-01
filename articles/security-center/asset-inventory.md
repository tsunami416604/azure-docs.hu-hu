---
title: Azure Security Center leltározása
description: Ismerkedjen meg Azure Security Center Asset Management-felülettel, amely teljes körű láthatóságot biztosít az összes Security Center figyelt erőforráson.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: f3a542cd62c3d593dbc0cce7982d47222e9a7c88
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181103"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel

Azure Security Center eszközök leltározási lapja egyetlen oldalt biztosít a Security Centerhoz csatlakoztatott erőforrások biztonsági állapotának megtekintéséhez. 

Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát az esetleges biztonsági rések azonosítása érdekében. Ezután javaslatokat tesz a biztonsági rések megoldására.

Ha bármilyen erőforráshoz függőben lévő javaslatok vannak, akkor azok megjelennek a leltárban.

Ezt a nézetet és a hozzá tartozó szűrőket használhatja a következő kérdések megválaszolásához:

- A standard szintű előfizetések közül melyiknek van kiemelkedő javaslatai?
- A "Production" címkével rendelkező gépek közül melyiknek hiányzik a Log Analytics ügynöke?
- Az adott címkével rendelkező gépek közül hányon vannak Kiemelt javaslatok?
- Egy adott erőforráscsoport hány erőforrása rendelkezik biztonsági megállapításokkal a sebezhetőségi felmérési szolgáltatástól?

Az eszköz erőforrás-kezelési lehetőségei jelentősek, és folyamatosan növekednek. 


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|Minden felhasználó|
|Felhők|![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Nem](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Mik az eszközök leltárának főbb jellemzői?

A leltár oldal a következő eszközöket biztosítja:

- **Összegzések** – a szűrők definiálása előtt a leltár nézet felső részén látható értékek egy kiemelt sáv jelenik meg:

    - **Összes erőforrás**: Security Centerhoz csatlakoztatott erőforrások teljes száma.
    - **Sérült erőforrások**: aktív biztonsági javaslatokkal rendelkező erőforrások. [További információ a biztonsági javaslatokról](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - Nem **figyelt erőforrások**: az ügynök figyelésével kapcsolatos problémákkal rendelkező erőforrások – log Analytics ügynök telepítve van, de az ügynök nem küld adatokat, vagy egyéb állapottal kapcsolatos problémákba ütközik.

- **Szűrők** – a lap tetején található több szűrő lehetővé teszi, hogy gyorsan pontosítsa az erőforrások listáját a válaszolni kívánt kérdésnek megfelelően. Ha például a *"Production" címkével rendelkező gépekkel* kapcsolatos kérdést szeretné megválaszolni, akkor a log Analytics ügynök hiányzik? az **ügynök figyelési** szűrőjét a **címkék** szűrővel kombinálhatja a következő klipben látható módon:

    ![A nem figyelt éles erőforrásokra való szűrés](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Amint alkalmazta a szűrőket, a rendszer frissíti az összegző értékeket, hogy azok a lekérdezés eredményeire vonatkozzanak. 

- **Exportálási beállítások** – a leltár lehetővé teszi, hogy a kiválasztott szűrési beállítások eredményeit egy CSV-fájlba exportálja. Emellett a lekérdezést az Azure Resource Graph Explorerben is exportálhatja, így tovább pontosíthatja, mentheti vagy módosíthatja az KQL-lekérdezést.

    ![Leltár exportálási beállításai](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > A KQL dokumentációja olyan adatbázist biztosít, amely bizonyos mintaadatok mellett néhány egyszerű lekérdezéssel biztosítja a "Feel" kifejezést a nyelvhez. [További információ ebben a KQL-oktatóanyagban](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Eszközkezelés beállításai** – a leltár lehetővé teszi összetett felderítési lekérdezések végrehajtását. Ha megtalálta a lekérdezéseknek megfelelő erőforrásokat, a leltár a következő műveletekhez nyújt parancsikonokat:

    - Címkék kiosztása a szűrt erőforrásokhoz – jelölje be a jelölőnégyzeteket a címkével ellátni kívánt erőforrások mellett.
    - Új kiszolgálók előkészítése a Security Center – a **nem Azure-kiszolgálók hozzáadása** eszköztár gombjának használata


## <a name="how-does-asset-inventory-work"></a>Hogyan működik a tárgyieszköz-leltár?

Az Asset Inventory az [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)szolgáltatást használja, amely lehetővé teszi, hogy a Security Center biztonsági állapotát több előfizetésen keresztül lekérdezze.

Az ARG úgy lett kialakítva, hogy hatékony erőforrás-feltárást biztosítson a nagy léptékű lekérdezési lehetőségekkel.

A [Kusto lekérdezési nyelv (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)használatával az eszközök leltározása gyors elemzéseket KÉSZÍTHET az ASC-adatforrások más erőforrás-tulajdonságokkal való hivatkozásával.


## <a name="how-to-use-asset-inventory"></a>Az eszközök leltározásának használata

1. A Security Center oldalsávján válassza a **leltár**lehetőséget.

1. Egy adott erőforrás megjelenítéséhez adja meg a nevet a **szűrés név szerint** mezőben.

1. A szűrőkben válassza ki a megfelelő beállításokat a végrehajtani kívánt lekérdezés létrehozásához.

    ![Leltár szűrőinek](./media/asset-inventory/inventory-filters.png)

    Alapértelmezés szerint az erőforrásokat az aktív biztonsági javaslatok száma alapján rendezi a rendszer.

    > [!IMPORTANT]
    > Az egyes szűrők beállításai az aktuálisan kijelölt előfizetésekben lévő erőforrásokra **és** a többi szűrőben megadott beállításokra vonatkoznak.
    >
    > Ha például csak egy előfizetést jelölt ki, és az előfizetés nem rendelkezik olyan erőforrásokkal, amelyek nem rendelkeznek magas biztonsági javaslatokkal a szervizeléshez (0 sérült erőforrás), a **javaslatok** szűrője nem lesz elérhető. 

1. A **biztonsági eredmények** szűrővel való használatához adja meg az érintett erőforrásokhoz szűrni kívánt biztonsági rés azonosítójának, biztonsági vizsgálatának vagy CVE-nevének a szabad szövegét:

    !["Biztonsági eredmények" szűrő](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > A **biztonsági megállapítások** és a **címkék** szűrők csak egyetlen értéket fogadnak el. Ha egynél többre szeretne szűrni, használja a **szűrők hozzáadása**lehetőséget.

1. Az **árképzési szint** szűrő használatához válasszon ki egy vagy több beállítást (ingyenes, részleges vagy standard):

    - **Ingyenes** – az ingyenes díjszabási szinten elérhető erőforrások
    - **Standard** – a standard díjszabási szinten található erőforrások
    - **Részleges** – a standard díjszabási szinten lévő előfizetésekre vonatkozik, de a választható biztonsági csomagok némelyike le van tiltva. A következő előfizetés például a standard szinten van, de a standard szint öt eleme le van tiltva. 

        ![Előfizetés standard (részleges) díjszabási szintjére](./media/asset-inventory/pricing-tier-partial.png)

1. A lekérdezés eredményeinek további vizsgálatához válassza ki az Önt érdeklő erőforrásokat.

1. Ha szeretné, válassza a nézet lehetőséget az **erőforrás Graph Explorerben** a lekérdezés a Resource Graph Explorerben való megnyitásához.

    ![Leltár lekérdezése az ARG-ben](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Ha meghatározta a szűrőket, és megnyitotta a lapot, Security Center nem frissíti automatikusan az eredményeket. Az erőforrások módosításai nem befolyásolják a megjelenített eredményeket, hacsak nem tölti be manuálisan a lapot, vagy a **frissítés**lehetőséget választja.


## <a name="faq---inventory"></a>Gyakori kérdések – leltár

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Miért nem jelenik meg az összes előfizetésem, gép, Storage-fiók stb.?

A leltár nézet a Felhőbeli biztonsági állapot-felügyeleti (CSPM) perspektívából sorolja fel az erőforrásokat. A szűrők nem adják vissza a környezet minden erőforrását; csak a kiemelkedő (vagy "aktív") javaslatok közül. 

Ha például kilenc előfizetéssel rendelkezik, de csak nyolc jelenleg van javaslata, akkor az **Erőforrás típusa = előfizetés** alapján csak a nyolc előfizetést fogja látni aktív javaslatokkal:

![Nem minden olyan feliratot adott vissza, amikor nincsenek aktív javaslatok](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Miért mutatnak be néhány erőforrás üres értéket a díjszabás vagy az ügynök figyelési oszlopaiban?

Nem minden Security Center figyelt erőforrás rendelkezik ügynökkel. Ilyenek például az Azure Storage-fiókok vagy a Pásti-erőforrások, például lemezek, Logic Apps, Data Lake elemzések és az Event hub.

Ha a díjszabás vagy az ügynök figyelése nem vonatkozik egy erőforrásra, a leltárban semmi sem jelenik meg.

![Egyes erőforrások üres információkat jelenítenek meg az ügynök figyelési vagy árképzési oszlopaiban](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>További lépések

Ez a cikk a Azure Security Center eszköz leltározási lapját ismerteti.

A kapcsolódó eszközökről a következő lapokon talál további információt:

- [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto lekérdezési nyelv (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)