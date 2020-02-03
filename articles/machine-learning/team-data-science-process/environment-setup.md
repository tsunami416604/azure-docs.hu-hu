---
title: Állítsa be az adatok adatelemzési környezetek az Azure-ban – a csoportos adatelemzési folyamat
description: Adatok beállítása adatelemzési környezetek Azure-beli használatra a csoportos adatelemzési folyamat.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d11e1614307d5b6cb267ae72bae6f32adb4b244a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722220"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Adatelemzési környezetek beállítása a csoportos adatelemzési folyamatban történő felhasználásra
A csoportos adatelemzési folyamat különböző adatelemzési környezetek a tárolási, feldolgozási és elemzési adatokat használ. Azure Blob Storage számos különböző típusú Azure-beli virtuális gépek, a HDInsight (Hadoop) fürtök és az Azure Machine Learning-munkaterületek tartalmazzák. A döntést, mely környezetben használandó attól függ, modellezni típusát és az adatok mennyisége és a cél cél, hogy az adatok a felhőben. 

* A döntés meghozatala során megfontolandó kérdésekkel kapcsolatos útmutatásért lásd [a Azure Machine learning adatelemzési környezet megtervezése](plan-your-environment.md)című témakört. 
* A speciális elemzések során esetlegesen felmerülő forgatókönyvek katalógusának megtekintéséhez tekintse meg [a csoportos adatelemzési folyamat forgatókönyveit](plan-sample-scenarios.md) .

Az alábbi cikkek ismertetik a különböző adatokat a csoportos adatelemzési folyamat által használt adatelemzési környezetek beállítása.

* [Azure Storage – fiók](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) fürt](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klasszikus) munkaterület](../studio/create-workspace.md)

A **Microsoft Data Science Virtual Machine (DSVM)** Azure-beli virtuális gép (VM) képként is elérhető. Ez a virtuális gép előre telepített és konfigurált számos népszerű eszközök, amelyek gyakran használják az adatelemzési és machine learning. A DSVM Windows és Linux rendszereken egyaránt érhető el. További információkért lásd: [a Linux és Windows rendszerhez készült felhőalapú Data Science Virtual Machine bemutatása](../data-science-virtual-machine/overview.md).

Ismerje meg, hogyan hozhat létre:

- [Windowsos DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu-DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
