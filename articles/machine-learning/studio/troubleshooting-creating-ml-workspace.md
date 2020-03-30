---
title: Munkaterület – hibaelhárítás
titleSuffix: ML Studio (classic) - Azure
description: Ez az útmutató megoldásokat kínál néhány gyakran előforduló kihívásokhoz az Azure Machine Learning Studio (klasszikus) munkaterületek beállításakor.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 58ccd63e16382aca8e16eb67efba951a055eb254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217840"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-classic-workspace"></a>Hibaelhárítási útmutató: Azure Machine Learning Studio (klasszikus) munkaterület létrehozása és csatlakoztatása

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ez az útmutató megoldásokat kínál néhány gyakran előforduló kihívásokhoz az Azure Machine Learning Studio (klasszikus) munkaterületek beállításakor.

## <a name="workspace-owner"></a>Munkaterület-tulajdonos
A machine learning-stúdióban (klasszikus) munkaterület megnyitásához be kell jelentkeznie a munkaterület létrehozásához használt Microsoft-fiókba, vagy meghívót kell kapnia a tulajdonostól, hogy csatlakozzon a munkaterülethez. Az Azure Portalon kezelheti a munkaterületet, amely magában foglalja a hozzáférés konfigurálását.

A munkaterület kezeléséről az [Azure Machine Learning Studio (klasszikus) munkaterület kezelése]című témakörben talál további információt.

[Azure Machine Learning Studio (klasszikus) munkaterület kezelése]: manage-workspace.md

## <a name="allowed-regions"></a>Engedélyezett régiók
A Machine Learning jelenleg korlátozott számú régióban érhető el. Ha az előfizetés nem tartalmazza a következő régiók egyikét, akkor a következő hibaüzenet jelenhet meg: "Nincs előfizetése az engedélyezett régiókban."

Ha azt szeretné kérni, hogy egy régiót adjanak hozzá az előfizetéshez, hozzon létre egy új Microsoft-támogatási kérelmet az Azure Portalon, válassza a **Számlázás** lehetőséget a probléma típusaként, és kövesse a kérés elküldéséhez szükséges utasításokat.

## <a name="storage-account"></a>Tárfiók
A Machine Learning szolgáltatásnak szüksége van egy tárfiókra az adatok tárolásához. Használhatja a meglévő tárfiókot, vagy létrehozhat egy új tárfiókot, amikor létrehozza az új Machine Learning Studio (klasszikus) munkaterület (ha kvótával egy új tárfiók létrehozásához).

Az új Machine Learning Studio (klasszikus) munkaterület létrehozása után a munkaterület létrehozásához használt Microsoft-fiókkal bejelentkezhet a Machine Learning Studio (klasszikus) gépi tanulási stúdióba. Ha a "Munkaterület nem található" hibaüzenettel találkozik (hasonlóan az alábbi képernyőképhez), kérjük, kövesse a következő lépéseket a böngésző cookie-jainak törléséhez.

![A munkaterület nem található](media/troubleshooting-creating-ml-workspace/screen3.png)

**Böngészőcookie-k törlése**

1. Az Internet Explorer használata esetén kattintson a jobb felső sarokban található **Eszközök** gombra, és válassza az **Internetbeállítások lehetőséget**.  

   ![Internetbeállítások](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Az **Általános** lap csoportban kattintson a **Törlés gombra.**

   ![Általános lap](media/troubleshooting-creating-ml-workspace/screen5.png)

3. A **Böngészési előzmények törlése** párbeszédpanelen győződjön meg arról, hogy **a cookie-k és a webhelyadatai** ki vannak jelölve, majd kattintson a **Törlés gombra.**

   ![Cookie-k törlése](media/troubleshooting-creating-ml-workspace/screen6.png)

A cookie-k törlése után indítsa újra a böngészőt, és nyissa meg a [Microsoft Azure Machine Learning Studio (klasszikus)](https://studio.azureml.net) lapot. Amikor a rendszer felhasználónevet és jelszót kér, adja meg ugyanazt a Microsoft-fiókot, amelyet a munkaterület létrehozásához használt.

## <a name="comments"></a>Megjegyzések

Célunk, hogy a Machine Learning élménye a lehető leggördülékenyebb legyen. Kérjük, tegye közzé észrevételeit és kérdéseit az [Azure Machine Learning fórumán,](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) hogy jobban szolgálhassuk Önt.
