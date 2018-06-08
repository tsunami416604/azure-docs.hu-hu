---
title: 'Hibaelhárítás: Hozzon létre, és kapcsolódjon a Machine Learning-munkaterület |} Microsoft Docs'
description: Megoldások gyakori problémákat létrehozása és az Azure Machine Learning-munkaterülethez csatlakozik
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 262c9af4e0f3ee34dc89986affacb6c0d8a0d801
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835722"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>Hibaelhárítási útmutató: Machine Learning-munkaterület létrehozása és kapcsolódás a Machine Learning-munkaterülethez
Ez az útmutató egyes megoldások gyakran ütközött kihívást állítja be az Azure Machine Learning munkaterületek.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>Munkaterület tulajdonosa
A munkaterület megnyitásához a Machine Learning Studióban be kell jelentkeznie Microsoft Account a munkaterület létrehozásához használt, vagy meghívót kapott a tulajdonos a munkaterület csatlakozni szeretne. Az Azure-portálon kezelheti a munkaterületen található, amely lehetővé teszi a hozzáférés konfigurálása.

A munkaterület kezeléséről további információkért lásd: [Egy Azure Machine Learning munkaterülettel kezelése].

[Egy Azure Machine Learning munkaterülettel kezelése]: manage-workspace.md

## <a name="allowed-regions"></a>Engedélyezett régiók
Gépi tanulás már régiók korlátozott számú érhető el. Az előfizetés nem tartalmazza a régiók egyikéhez sem, ha a hiba üzenet jelenhet meg, "Nincs előfizetése van a megengedett régiókban."

Kérelem, az előfizetés fel egy régiót, hozzon létre egy új Microsoft-támogatási kérelmet az Azure-portálon, válassza a **számlázási** probléma típusa és kövesse a megjelenő utasításokat küldje el a kérést.

## <a name="storage-account"></a>Tárfiók
A Machine Learning szolgáltatás szükséges a tárfiók adatok tárolására. Meglévő tárfiókot is használhatja, vagy létrehozhat egy új tárfiókot, az új Machine Learning munkaterületének létrehozásakor (ha van kvótát egy új tárfiók létrehozása).

Az új Machine Learning-munkaterület létrehozása után is bejelentkezik a Machine Learning Studio a munkaterület létrehozásához használt Microsoft-fiók használatával. Ha a hibaüzenet a következő, "Munkaterület nem található" (az alábbi képernyőfelvételhez hasonló) észlel, akkor használja a következő lépéseket a böngésző cookie-k törléséhez.

![Nem található munkaterület][screen3]

**Törli a böngésző cookie-k**

1. Ha az Internet Explorer, kattintson a **eszközök** gombra a jobb felső sarokban, majd az **Internetbeállítások**.  

![Internetbeállítások][screen4]

2. Az a **általános** lapra, majd **törlése...**

![Általános lap][screen5]

3. A a **böngészési előzmények törlése** párbeszédpanelen győződjön meg arról, hogy **cookie-k és a webhely adatok** van kiválasztva, és kattintson a **törlése**.

![Törölje a cookie-k][screen6]

Miután a cookie-k törlődnek, indítsa újra a böngészőt, és folytassa a a [Microsoft Azure Machine Learning](https://studio.azureml.net) lap. Amikor a felhasználónevet és jelszót kéri, adja meg a Microsoft-fiók a munkaterület létrehozásához használt.

## <a name="comments"></a>Megjegyzések

Célunk, zökkenőmentes lehető ellenőrizze a Machine Learning élmény. Tegye a megjegyzések és részén a problémák a [Azure Machine Learning fórum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) elküldésével kiszolgálására jobb.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
