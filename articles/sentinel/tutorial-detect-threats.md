---
title: Az Azure-on Előzetesben Sentinel-riasztásokra |} A Microsoft Docs
description: Ez az oktatóanyag segítségével megismerheti, hogyan riasztásokra az Azure-Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 35268d8001f8869838745d14f0e8c0ba7253d4c9
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56993201"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>Oktatóanyag: Az Azure-on Előzetesben Sentinel-fenyegetések észlelése

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag segít az Azure Sentinel-fenyegetések észlelése.

Miután [csatlakoztatva az adatforrások](quickstart-onboard.md) Azure Sentinel, hogy valami gyanús történik, ha értesítést szeretne. Ahhoz, hogy ehhez, az Azure Sentinel-részeként létrehozhat speciális riasztási szabályok, esetek, amelyeket hozzárendelhet generáló és mélyen a rendellenességeket és a környezetét fenyegető veszélyek vizsgálatához használja. 


> [!div class="checklist"]
> * Észlelési szabályok létrehozása
> * Fenyegetések

## <a name="create-detection-rules"></a>Észlelési szabályok létrehozása

Esetek vizsgálatához, először be kell észlelési szabályok létrehozásához. Észlelési szabályok biztosító illesztett, vizsgálni, és javítja a fenyegetések és a rendellenességeket, amelyek oka az lehet az adott környezetben, azonnal tudni szeretné, gyanús alapulnak. 

1. Válassza ki az Azure portál Azure Sentinel-, **Analytics**.

  ![Elemzés](./media/tutorial-detect-threats/alert-rules.png)

2. A felső menüsávon kattintson **+ Hozzáadás**.  

  ![Riasztási szabály létrehozása](./media/tutorial-detect-threats/create-alert-rule.png)

3. A **riasztási szabály létrehozása**, adjon meg egy leíró nevet, és állítsa be a **súlyossági** szükség szerint. 

4. A Log Analytics a lekérdezés létrehozásához, és illessze be azt a **riasztási szabály beállítása** mező. Itt látható, amely szeretne riasztást küld, ha egy rendellenes erőforrások száma jön létre az Azure-tevékenység mintalekérdezés.

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

5. Az a **entitásleképezése** területén terület **entitástípus** a lekérdezésben az oszlopok leképezése ismeri fel az Azure-Sentinel Entitásmezők. Az egyes mezőkhöz tartozó képezze le a Log Analytics, a megfelelő entitás mezőjére létrehozott lekérdezés a megfelelő oszlopban. Válassza ki a megfelelő oszlop neve alatt a **tulajdonság**. Minden entitás tartalmaz több mező, például a biztonsági azonosító, GUID, stb. Leképezheti az entitás valamelyik mezőjére, nem csak a felső szintű entitás alapján.

6. Adja meg a riasztás aktiválásakor feltételeit **riasztás aktiválásakor**. Ez határozza meg a feltételeket, amelyeknek a riasztást. 

7. Állítsa be a **gyakorisága** gyakoriságát. a lekérdezés futtatása – gyakran át 5 percenként vagy ritkán naponta egyszer, a. 

8. Állítsa be a **időszak** szabályozhatja a lekérdezés fut – mennyi adatot időtartományából például Futtatás minden órában 60 perc adatai között.

9. Is beállíthat a **tiltási**. Tiltási akkor hasznos, ha meg szeretné szüntetni az ugyanezen incidens aktiválása a duplikált riasztásokat. Ezzel a módszerrel egy adott időszakban aktiválódó riasztásai is leállíthatja. Ez segíthet az ugyanezen incidens ismétlődő riasztások elkerülése, és lehetővé teszi, hogy az egymást követő riasztások mellőzése egy ideig. Például ha a **riasztás ütemezés** **gyakorisága** 60 percre van beállítva, és a **riasztás ütemezési időszak** két óra és a lekérdezés eredményeit a definiált túllépése küszöbérték, azt egy riasztást aktivál kétszer, amikor először észlelt az elmúlt 60 perc után, és újra van az adatok mintavételezése a 2 órán keresztül az első 60 perc. Azt javasoljuk, hogy egy riasztás akkor aktiválódik, ha a Mellőzés legyen ennyi ideig állítsa be a riasztás időszakban. Ebben a példában érdemes 60 perc, a Mellőzés beállítása úgy, hogy csak a figyelmeztetéseket az eseményeket, amelyek a legutóbbi órában történt.

8. Után, illessze be a lekérdezést a **riasztási szabály beállítása** mező, azonnal láthatja a riasztás alapján szimulálás **logikai riasztási szimuláció** így kaphatnak, lehet, hogy mennyi adatot ismertetése létrehozott egy adott idő alatt a létrehozott riasztás. Ez függ a beállítása **gyakorisága** és **küszöbérték**. Ha látja, hogy átlagosan, a riasztás akkor aktiválódik, túl gyakran, érdemes magasabb eredmények számának beállítása, hogy az átlagos alapterv felett.

9. Kattintson a **létrehozás** inicializálása a riasztási szabályt. A riasztás létrehozása után egy eset jön létre, amely tartalmazza a riasztás. Megjelenik a megadott észlelési szabályok sorként szerepel a **biztonsági elemzési** fülre. Megtekintheti az egyes szabály - egyezések aktivált riasztások számát is. Ebből a listából engedélyezheti tiltsa le, vagy minden egyes szabály törlése. Akkor is is jobb gombbal válassza az egyes riasztások szerkesztése, letiltása, klónozza, egyezéseket, vagy egy szabály törlése a sor végén található három pontra (...). A **Analytics** lap egy gyűjtemény összes aktív riasztás szabály, köztük a sablonok engedélyezi és riasztási szabályok létrehozása sablon alapján.

1. A riasztási szabályok eredményei láthatók a **esetek** oldal, ahol Ön is osztályozása [esetek vizsgálata](tutorial-investigate-cases.md), és az elhárításban.



## <a name="respond-to-threats"></a>Fenyegetések

Az Azure Sentinel fenyegetésekkel szembeni forgatókönyvek használatával két elsődleges lehetőséget kínál. Egy forgatókönyv riasztást vált ki, vagy manuálisan futtatható a forgatókönyv a választ figyelmeztetést automatikus futtatását állíthatja be.

- Állítsa be egy forgatókönyv riasztást vált ki, hogy a forgatókönyv konfigurálásakor automatikusan futtatható. 

- Futtathatja manuálisan a riasztás belül az egy forgatókönyv kattintva **forgatókönyvek megtekintése** , és válassza a forgatókönyv végrehajtásához.




## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el használatával Azure Sentinel-fenyegetések észlelése. A következő oktatóanyagban [hogyan reagáljon a fenyegetéseket a automatizált forgatókönyvek](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Fenyegetések](tutorial-respond-threats-playbook.md) fenyegetések a válaszok automatizálására.

