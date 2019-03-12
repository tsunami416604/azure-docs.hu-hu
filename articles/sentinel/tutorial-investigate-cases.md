---
title: Vizsgálja meg az Azure-on Előzetesben Sentinel-esetek |} A Microsoft Docs
description: Ez az oktatóanyag segítségével megtudhatja, hogyan esetekben, ahol az Azure-Sentinel vizsgálatára.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a493cd67-dc70-4163-81b8-04a9bc0232ac
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 7c4994863f3c145c7095bcc12dd69ff02fab8455
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540234"
---
# <a name="tutorial-investigate-cases-with-azure-sentinel-preview"></a>Oktatóanyag: Vizsgálja meg az Azure-on Előzetesben Sentinel-esetek

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az oktatóanyag segít az Azure Sentinel-fenyegetések észlelése.

Miután [csatlakoztatva az adatforrások](quickstart-onboard.md) Azure Sentinel, hogy valami gyanús történik, ha értesítést szeretne. Ahhoz, hogy ehhez, az Azure Sentinel-részeként létrehozhat speciális riasztási szabályok, esetek, amelyeket hozzárendelhet generáló és mélyen a rendellenességeket és a környezetét fenyegető veszélyek vizsgálatához használja. 

> [!div class="checklist"]
> * Eseteket hoznak létre.
> * Esetek kivizsgálása
> * Reagálás a fenyegetésekre

## <a name="investigate-cases"></a>Esetek kivizsgálása

Eset több riasztás is tartalmazhatnak. Fontos a megfelelő bizonyíték egy konkrét vizsgálat összesítést. Eset meghatározott riasztások alapján jön létre a **Analytics** lapot. A riasztások fontossága és állapota például kapcsolódó tulajdonságok az eset szintjén vannak beállítva. Engedélyezze, hogy az Azure Sentinel-tudja, milyen fenyegetéseket keres, és hogyan találhatja meg azokat, miután esetek kivizsgálása által észlelt fenyegetések követheti nyomon. 

1. Válassza ki **esetek**. A **esetek** lap lehetővé teszi, hogy hány esetben rendelkezik, nyissa meg, hány beállított hány **folyamatban**, és hány bezáródik. Minden esetben láthatja az idő, történt, és az az eset állapotát. Tekintse meg a súlyosságot. döntse el, mi első kezelésére. Az a **esetek** lap, kattintson a **riasztások** lapján megtekintheti a riasztásokat, amelyek egy esetet kapcsolódnak. Entitások, amelyeket korábban az eset része lehet megtekinteni leképezett a **entitások** fülre.  Ha szükséges, például állapot és súlyosság szerint szűrheti az esetek. Ha a **esetek** lapon látni fogja, amelyek tartalmazzák a megadott észlelési szabályok által aktivált riasztások nyitott esetek **Analytics**. Tetején látni fogja az aktív eseteket, új eseteket, és azokban az esetekben, folyamatban van. Súlyosság szerint minden esetben áttekintését is megtekinthető.

  ![Riasztási irányítópult](./media/tutorial-investigate-cases/cases.png)

2. A vizsgálat megkezdéséhez kattintson az egy adott esetet. A jobb oldalon láthatja a részletes információkat az esetet, beleértve a súlyosság, az érintett entitások száma összefoglalása (a hozzárendelés alapján). Minden esetben van egy egyedi azonosítót. Az eset súlyosságának tartalmazza abban az esetben a legsúlyosabb riasztás alapján határozza meg.  

1. Abban az esetben a riasztások és az entitások kapcsolatos további információk megtekintéséhez kattintson a **részletes információk megtekintéséhez** abban az esetben lapon, és tekintse át a vonatkozó lapok, amely megkülönbözteti a kis információk összegzése.  Teljes megkülönbözteti a kis nézetben egyesíti a összes bizonyíték a riasztást, a hozzá tartozó riasztási és entitásokat.

1. Az a **riasztások** lapján, a magán - riasztás elindításakor volt, és úgy, hogy mennyit túllépte a beállított küszöbértékeket. Láthatja, hogy a riasztás – a lekérdezést, amely kiváltotta a riasztást, valamint a lekérdezés és a riasztások forgatókönyvet futtatni képes visszaadott eredmények száma releváns információt. Részletes lefelé még tovább az a helyzet, kattintson a találatok száma. Ekkor megnyílik a lekérdezést, amely az eredményeket, és az eredményeket a Log Analytics a riasztást kiváltó jön létre.

3. Az a **entitások** lapon láthatja, hogy a leképezett összes entitást a riasztási szabály definíciójának részeként. 

4. Ha egy eset aktívan lekérdezéskapcsolatokról, célszerű esetállapot beállítása **folyamatban** bezárásáig. Is bezárhatja a helyzet, ahol **lezárt megoldott** állapota az esetek, amelyek jelzik, hogy az incidens kezelése történt meg, amíg **lezárt elbocsátott** az esetek kezelése nem igénylő állapota. Elmagyarázza a indoklása eset bezárásának magyarázatok szükségesek.

5. Egy adott felhasználó esetek is hozzárendelhető. Minden esetben rendelhet tulajdonosa, az eset beállításával **tulajdonosa** mező. Minden esetben kezdő, ki nem osztott. Esetekben lép, és megtekintheti a saját minden esetben a név szerint szűrheti. 

5. Kattintson a **vizsgálat** vizsgálati térkép és hatókörét a korrekciós lépésekkel és illetéktelen behatolás megtekintéséhez. 



## <a name="respond-to-threats"></a>Reagálás a fenyegetésekre

Az Azure Sentinel fenyegetésekkel szembeni forgatókönyvek használatával két elsődleges lehetőséget kínál. Egy forgatókönyv riasztást vált ki, vagy manuálisan futtatható a forgatókönyv a választ figyelmeztetést automatikus futtatását állíthatja be.

- Egy forgatókönyv riasztást vált ki, hogy a forgatókönyv konfigurálásakor automatikusan futtatását állíthatja be. 

- A riasztás belül az egy forgatókönyv kattintva manuálisan futtathatja **forgatókönyvek megtekintése** , és válassza a forgatókönyv végrehajtásához.




## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan kezdheti el az Azure Sentinel az esetek kivizsgálása. A következő oktatóanyagban [hogyan reagáljon a fenyegetéseket a automatizált forgatókönyvek](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Fenyegetések](tutorial-respond-threats-playbook.md) fenyegetések a válaszok automatizálására.

