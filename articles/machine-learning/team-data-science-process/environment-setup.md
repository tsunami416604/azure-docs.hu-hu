---
title: Adatelemzési környezetek beállítása az Azure-ban – csoportos adatelemzési folyamat
description: Adatelemzési környezeteket állíthat be az Azure-ban a csoportos adatelemzési folyamatban való használatra.
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
ms.openlocfilehash: 6b5571f24cc7acfd35cf2979318110ba2eecbb0e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320540"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Adatelemzési környezetek beállítása a csoportos adatelemzési folyamatban történő felhasználásra
A csoportos adatelemzési folyamat különböző adatelemzési környezeteket használ az adattároláshoz, feldolgozáshoz és elemzéshez. Ezek közé tartoznak az Azure Blob Storage, az Azure Virtual Machines, a HDInsight (Hadoop)-fürtök és a Azure Machine Learning-munkaterületek különböző típusai. A használni kívánt környezettől való döntés a modellezett adatok típusától és mennyiségétől, valamint a felhőben tárolt adatok céljától függ. 

* A döntés meghozatala során megfontolandó kérdésekkel kapcsolatos útmutatásért lásd [a Azure Machine learning adatelemzési környezet megtervezése](plan-your-environment.md)című témakört. 
* A speciális elemzések során esetlegesen felmerülő forgatókönyvek katalógusának megtekintéséhez tekintse meg [a csoportos adatelemzési folyamat forgatókönyveit](plan-sample-scenarios.md) .

A következő cikkek azt ismertetik, hogyan állíthatja be a csoportos adatelemzési folyamat által használt különböző adatelemzési környezeteket.

* [Azure Storage – fiók](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) fürt](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klasszikus) munkaterület](../classic/create-workspace.md)

A **Microsoft Data Science Virtual Machine (DSVM)** Azure-beli virtuális gép (VM) képként is elérhető. Ez a virtuális gép előre telepítve és konfigurálva van számos olyan népszerű eszközzel, amelyet általában az adatelemzéshez és a gépi tanuláshoz használnak. A DSVM Windows és Linux rendszereken egyaránt elérhető. További információkért lásd: [a Linux és Windows rendszerhez készült felhőalapú Data Science Virtual Machine bemutatása](../data-science-virtual-machine/overview.md).

Útmutató a létrehozásához:

- [Windowsos DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
