---
title: Adatelemzési környezetek beállítása az Azure-ban – Csapatadat-elemzési folyamat
description: Állítsa be az azure-beli adatelemzési környezeteket a csapatadat-elemzési folyamathoz.
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
ms.openlocfilehash: 6d1185e13397b3e466bd1e8d609a46f2f68bf390
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063939"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Adatelemzési környezetek beállítása a csoportos adatelemzési folyamatban történő felhasználásra
A csapat adatelemzési folyamat különböző adatelemzési környezeteket használ az adatok tárolásához, feldolgozásához és elemzéséhez. Ezek közé tartozik az Azure Blob Storage, többféle Azure virtuális gépek, HDInsight (Hadoop) fürtök, és az Azure Machine Learning-munkaterületek. A rendszer által a használandó környezet használatának eldöntése a modellezendő adatok típusától és mennyiségétől, valamint a felhőben lévő adatok célcéljától függ. 

* For guidance on questions to consider when making this decision, see [Plan Your Azure Machine Learning Data Science Environment](plan-your-environment.md). 
* A speciális elemzések során esetleg előforduló forgatókönyvek katalógusát [a Csapatadat-elemzési folyamat forgatókönyvei című](plan-sample-scenarios.md) témakörben

Az alábbi cikkek ismertetik, hogyan állíthatja be a különböző adatelemzési környezetek által használt team data science folyamat.

* [Azure-tárfiók](../../storage/common/storage-account-create.md)
* [HDInsight (Hadoop) fürt](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio (klasszikus) munkaterület](../studio/create-workspace.md)

A **Microsoft Data Science virtuális gép (DSVM)** is elérhető, mint egy Azure virtuális gép (VM) rendszerkép. Ez a virtuális gép előre telepített és konfigurált számos népszerű eszközök, amelyek gyakran használják az adatelemzés és a gépi tanulás. A DSVM windows os és Linux rendszeren is elérhető. További információ: [Bevezetés a felhőalapú adatelemzési virtuális gép linuxos és windowsos rendszerbe című témakörben.](../data-science-virtual-machine/overview.md)

További információ a létrehozásról:

- [Windowsos DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
