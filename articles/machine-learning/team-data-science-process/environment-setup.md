---
title: Állítsa be az adatok adatelemzési környezetek az Azure-ban – a csoportos adatelemzési folyamat
description: Adatok beállítása adatelemzési környezetek Azure-beli használatra a csoportos adatelemzési folyamat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c66da6717018237aebaf5fdaf803a8ac98f82be
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459092"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Adatelemzési környezetek beállítása a csoportos adatelemzési folyamatban történő felhasználásra
A csoportos adatelemzési folyamat különböző adatelemzési környezetek a tárolási, feldolgozási és elemzési adatokat használ. Azure Blob Storage számos különböző típusú Azure-beli virtuális gépek, a HDInsight (Hadoop) fürtök és az Azure Machine Learning-munkaterületek tartalmazzák. A döntést, mely környezetben használandó attól függ, modellezni típusát és az adatok mennyisége és a cél cél, hogy az adatok a felhőben. 

* Kérdéseket érdemes figyelembe venni, amikor ezt a döntést, tekintse át [megtervezése az Azure Machine Learning adatelemzési környezetet](plan-your-environment.md). 
* Egy katalógus, néhány, a speciális előkészítésétől előforduló forgatókönyveket, lásd: [forgatókönyvek a csoportos adatelemzési folyamat](plan-sample-scenarios.md)

Az alábbi cikkek ismertetik a különböző adatokat a csoportos adatelemzési folyamat által használt adatelemzési környezetek beállítása.

* [Az Azure storage-fiók](../../storage/common/storage-quickstart-create-account.md)
* [HDInsight (Hadoop) fürtön](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio-munkaterület](../studio/create-workspace.md)

A **Microsoft Data Science virtuális gép (DSVM)** is az Azure virtuális gép (VM) képként érhető el. Ez a virtuális gép előre telepített és konfigurált számos népszerű eszközök, amelyek gyakran használják az adatelemzési és machine learning. A DSVM Windows és Linux rendszereken egyaránt érhető el. További információkért lásd: [bemutatása, a felhőalapú Data Science virtuális gép Linux és Windows](../data-science-virtual-machine/overview.md).

Ismerje meg, hogyan hozhat létre:

- [A DSVM Windows](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu dsvm-hez](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS dsvm-hez](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Mélytanulási virtuális gép](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
