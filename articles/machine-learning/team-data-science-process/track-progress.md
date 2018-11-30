---
title: Adatelemzési projektek – az Azure Machine Learning végrehajtásának |} A Microsoft Docs
description: Hogyan értenie az adatokhoz is előrehaladását úgy követheti nyomon az adatelemzési projektjéhez.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 202ac89b8a281012dbcf5f4c4df11e97ba2c8c65
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441468"
---
# <a name="track-progress-of-data-science-projects"></a>Adatelemzési projektek előrehaladását úgy követheti nyomon

Data science csoportvezetők Csapatvezetők és projekt érdeklődők kell a projektek előrehaladását úgy követheti nyomon, milyen munkahelyi végeztük el őket, és ki használja őket, és a feladatlisták nem marad. 

## <a name="azure-devops-dashboards"></a>Az Azure DevOps-irányítópult
Ha használja az Azure DevOps, is nyomon követheti a tevékenységeket és a egy adott projektkezelési társított munkaelemek irányítópultokat hozhat létre. 

További információ a létrehozása és testreszabása az Azure DevOps widgeteket és az irányítópultokat a következő eljárások egyikét az utasításokat lásd:

- [Adja hozzá, és az irányítópultok kezelése](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [Widgetek hozzáadása egy irányítópulthoz](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard).

## <a name="example-dashboard"></a>Példa irányítópult

Íme egy egyszerű példa irányítópult, amely egy hatékony adatelemzési projektjéhez, valamint a véglegesítések száma sprint tevékenységének nyomon társított tárházakba. A **bal felső** panelen látható:

- az aktuális sprint, a visszaszámlálás 
- az elmúlt 7 napban minden tárház a véglegesítések száma
- a munkaelem egyedi felhasználók számára. 

A fennmaradó panelek a összegző flow diagram (CFD), a projektek burndown és a projekt burnup megjelenítése:

- **Bal alsó sarokban található**: egy adott állapotban munka mennyisége CFD megjelenítő jóváhagyott szürke, látható kék és zöld színnel történik.
- **Jobb oldalán**: burndown diagram a munka elvégzéséhez és a hátralévő idő maradt).
- **Alul a jobb oldalon**: burnup diagram munkát, és a munkahelyi teljes mennyisége befejeződött.

![irányítópult](./media/track-progress/dashboard.png)

Bemutatja, hogyan hozhat létre a diagramokat, olvassa el a rövid útmutatók és oktatóanyagok, [irányítópultok](https://docs.microsoft.com/azure/devops/report/dashboards/).
 
## <a name="next-steps"></a>További lépések

Forgatókönyvek, amelyek bemutatják, a folyamat összes lépését **meghatározott forgatókönyvek** is rendelkezésre állnak. Felsorolt, és a miniatűr leírásokat tartalmazó társított a [példa forgatókönyvek](walkthroughs.md) cikk. Ezek bemutatják, hogyan kombinálhatja a felhőbeli, helyszíni eszközöket és szolgáltatásokat, munkafolyamat vagy folyamat, intelligens alkalmazások létrehozására. 
