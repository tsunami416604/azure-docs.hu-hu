---
title: Munkaterület hibaelhárítása
titleSuffix: ML Studio (classic) - Azure
description: Ez az útmutató a Azure Machine Learning Studio (klasszikus) munkaterületek beállításakor gyakran felmerülő kihívásokhoz nyújt megoldásokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: ba792c225c9875bbad50e3fb460c84ed012ce615
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169099"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Hibaelhárítási útmutató: Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és kapcsolódás
Ez az útmutató a Azure Machine Learning Studio (klasszikus) munkaterületek beállításakor gyakran felmerülő kihívásokhoz nyújt megoldásokat.



## <a name="workspace-owner"></a>Munkaterület tulajdonosa
Ha Machine Learning Studio (klasszikus) munkaterületet szeretne megnyitni, be kell jelentkeznie a munkaterület létrehozásához használt Microsoft-fiókba, vagy meg kell kapnia egy meghívót a tulajdonostól, hogy csatlakozzon a munkaterülethez. A Azure Portal kezelheti a munkaterületet, amely a hozzáférés konfigurálását is lehetővé teszi.

A munkaterületek kezelésével kapcsolatos további információkért lásd: [Azure Machine Learning Studio (klasszikus) munkaterület kezelése].

[Azure Machine Learning Studio (klasszikus) munkaterület kezelése]: manage-workspace.md

## <a name="allowed-regions"></a>Engedélyezett régiók
Machine Learning jelenleg korlátozott számú régióban érhető el. Ha az előfizetése nem tartalmaz ilyen régiókat, a következő hibaüzenet jelenhet meg: "nincsenek előfizetések az engedélyezett régiókban."

Ha egy régiót szeretne felvenni az előfizetésbe, hozzon létre egy új Microsoft-támogatási kérelmet a Azure Portal, válassza a **számlázás** lehetőséget a probléma típusaként, és kövesse az utasításokat a kérelem elküldéséhez.

## <a name="storage-account"></a>Tárfiók
A Machine Learning szolgáltatásnak szüksége van egy Storage-fiókra az adattároláshoz. Használhat meglévő Storage-fiókot, vagy létrehozhat egy új Storage-fiókot az új Machine Learning Studio (klasszikus) munkaterület létrehozásakor (ha van kvótája új Storage-fiók létrehozásához).

Miután létrehozta az új Machine Learning Studio (klasszikus) munkaterületet, bejelentkezhet a Machine Learning Studio (klasszikus) webhelyre a munkaterület létrehozásához használt Microsoft-fiók használatával. Ha a "munkaterület nem található" hibaüzenet jelenik meg (az alábbi képernyőképhez hasonlóan), akkor a következő lépésekkel törölheti a böngésző cookie-jait.

![A munkaterület nem található](media/troubleshooting-creating-ml-workspace/screen3.png)

**Böngésző cookie-k törlése**

1. Ha az Internet Explorert használja, kattintson a jobb felső sarokban található **eszközök** gombra, és válassza az **Internetbeállítások**lehetőséget.  

   ![Internetbeállítások](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Az **általános** lapon kattintson a **Törlés...** elemre.

   ![Általános lap](media/troubleshooting-creating-ml-workspace/screen5.png)

3. A **böngészési előzmények törlése** párbeszédpanelen ellenőrizze, hogy ki van-e jelölve a **cookie-k és a webhely-adatértékek** , majd kattintson a **Törlés**gombra.

   ![Cookie-k törlése](media/troubleshooting-creating-ml-workspace/screen6.png)

A cookie-k törlése után indítsa újra a böngészőt, majd lépjen a [Microsoft Azure Machine learning Studio (klasszikus)](https://studio.azureml.net) lapra. Ha a rendszer a Felhasználónév és a jelszó megadását kéri, adja meg ugyanazt a Microsoft-fiók, amelyet a munkaterület létrehozásához használt.

## <a name="comments"></a>Megjegyzések

Célunk, hogy a lehető legzökkenőmentesebb legyen a Machine Learning élmény. Kérjük, tegye közzé megjegyzéseit és problémáit a [Azure Machine learning fórumban](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) , hogy segítsen jobban kiszolgálni.
