---
title: Csapat adatelemzési folyamat projekt megtervezése – Azure |} A Microsoft Docs
description: A Microsoft Project és Excel-sablonok, amelyek segítségével tervezze és adatelemzési projektek kezeléséhez.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/27/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 702ea6152ce5bf90d1d1f61b566e91d1859e672b
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443100"
---
# <a name="team-data-science-process-project-planning"></a>Csoportos adatelemzési folyamat projekt megtervezése

A csoportos adatelemzési folyamat (TDSP) biztosít egy életciklussal, építse fel az adatelemzési projektek fejlesztését. Ez a cikk a Microsoft Project és Excel-sablonok, amelyek segítségével mutató hivatkozások tervezését és kezelését és projekt szakaszokat.

Az életciklus a fő szakaszai, projektek általában végrehajtható, iteratív gyakran ismerteti:

- Az üzleti igények felmérése
- Adatgyűjtés és adatértelmezés
- Modellezés
- Környezet
- Felhasználói elfogadás

Az egyes szakaszokat leírásáért lásd: [a csoportos adatelemzési folyamat életciklusa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle).

 
## <a name="microsoft-project-template"></a>A Microsoft Project sablon

Innen érhető el a Microsoft Project sablonját a csoportos adatelemzési folyamat: [a Microsoft Project sablon](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Team-Data-Science-Process/Project-Planning-and-Governance/Advanced%20Analytics%20Microsoft%20Project%20Plan.mpp) 

Amikor megnyitja a csomagot, kattintson a hivatkozásra kattintva a bal szélen a tdsp. Módosítsa a nevet és leírást, és adja hozzá a csapat erőforrásokat kell. Becsülje meg a dátumok, a felhasználói élmény a szükséges.

![1](./media/team-data-science-process-project-templates/ms-project-templates.png)

Minden tevékenység rendelkezik egy megjegyzés. Nyissa meg ezeket a feladatokat, megtekintheti, milyen erőforrásokat létre lett hozva az Ön számára.

![2](./media/team-data-science-process-project-templates/ms-project-template-task.png)


## <a name="excel-template"></a>Excel-sablon

Ha nem rendelkezik hozzáféréssel a Microsoft Project, az összes ugyanazokat az adatokat egy Excel-munkalapról is letölthető itt: [Excel-sablon](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Team-Data-Science-Process/Project-Planning-and-Governance/Advanced%20Analytics%20Microsoft%20Project%20Plan.xlsx) ezeket be lehet kötni, bármilyen eszközre szeretné használni.

A saját kockázatára használhatja ezeket a sablonokat. A [szokásos nyilatkozatok](https://www.gnu.org/licenses/gpl-3.0.en.html) vonatkoznak.

## <a name="repository-template"></a>Tárház-sablon

Ezzel [projekt sablontár](https://github.com/Azure/Azure-TDSP-ProjectTemplate) hatékony projekt végrehajtása és együttműködés támogatása. Ez a tárház egy szabványosított directory struktúra és a dokumentum sablonok is használhatja a saját TDSP projekt biztosítja.

## <a name="next-steps"></a>További lépések

[Hatékony adatelemzési projektek fejlesztését](agile-development.md) Ez a dokumentum ismerteti a csoportos adatelemzési folyamat használatával egy a egy szisztematikus adatelemzési projektjéhez, a szabályozott verzió és a egy projektcsapattal remek végrehajtásához.

Forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 

