---
title: 'Hibaelhárítás: Hozzon létre, csatlakozás egy Machine Learning Studio-munkaterülethez'
titleSuffix: Azure Machine Learning Studio
description: Ez az útmutató egyes megoldások gyakran közben kihívásokat állítja be az Azure Machine Learning Studio-munkaterületek.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: c12c87524ac1b8d0be5b691d599510f8e4573317
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56267325"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Hibaelhárítási útmutató: Létrehozás és csatlakozás az Azure Machine Learning Studio-munkaterülethez
Ez az útmutató egyes megoldások gyakran közben kihívásokat állítja be az Azure Machine Learning Studio-munkaterületek.



## <a name="workspace-owner"></a>Munkaterület tulajdonosa
A Machine Learning studióban megnyit egy munkaterületet, akkor be kell jelentkeznie a munkaterület létrehozásához használt Microsoft-Account, vagy egy meghívót kap a munkaterület tulajdonosa kell. Az Azure Portalról kezelheti a munkaterületet, amely lehetővé teszi a hozzáférés konfigurálásához.

A munkaterület kezeléséről további információkért lásd: [Az Azure Machine Learning Studio-munkaterület kezelése].

[Az Azure Machine Learning Studio-munkaterület kezelése]: manage-workspace.md

## <a name="allowed-regions"></a>Engedélyezett régiók
Machine Learning szolgáltatás jelenleg korlátozott számú régióban. Ha az előfizetés nem tartalmazza az egyik ezekben a régiókban, előfordulhat, hogy a hibaüzenetet látja, "Akkor nincsenek előfizetések az engedélyezett régiók a."

Szeretne kérni, egy régióban fel előfizetése, hozzon létre egy új Microsoft-támogatási kérelmet az Azure Portalon, válassza a **számlázási** a probléma típusaként, és kövesse az utasításokat követve küldje el a kérést.

## <a name="storage-account"></a>Tárfiók
A Machine Learning szolgáltatás egy storage-fiók adatok tárolására van szüksége. Használhat meglévő storage-fiókot, vagy létrehozhat egy új storage-fiókot (ha van egy új tárfiók létrehozására érvényes kvótáját) az új Machine Learning Studio-munkaterület létrehozásakor.

Az új Machine Learning Studio-munkaterület létrehozása után bejelentkezhet a Machine Learning Studio a munkaterület létrehozásához használt Microsoft-fiók használatával. Ha a hibaüzenetet tapasztal, "Munkaterület nem található" (az alábbi képernyőfelvételhez hasonlóan), használja a böngésző cookie-k törlése a következő lépéseket.

![A munkaterület nem található][screen3]

**Böngésző cookie-k törlése**

1. Ha az Internet Explorer böngészőt használ, kattintson a **eszközök** gombra a jobb felső sarokban, majd **Internetbeállítások**.  

   ![Internetbeállítások][screen4]

2. Alatt a **általános** lapra, majd **törlése...**

   ![Általános lap][screen5]

3. A a **böngészési előzmények törlése** párbeszédpanelen győződjön meg, hogy **cookie-k és webhelyadatok** van kiválasztva, majd kattintson **törlése**.

   ![Törölje a cookie-k][screen6]

A cookie-kat a törlés után indítsa újra a böngészőt, és folytassa a a [Microsoft Azure Machine Learning](https://studio.azureml.net) lapot. Amikor a felhasználónevet és jelszót kéri, adja meg a Microsoft-fiók, amellyel a munkaterület létrehozása.

## <a name="comments"></a>Megjegyzések

Célunk, hogy a gépi tanulási élményt, zökkenőmentes, amennyire csak lehetséges. Tegye közzé a megjegyzések és a problémák a [Azure Machine Learning fórum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) kiszolgálhassuk, jobb.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
