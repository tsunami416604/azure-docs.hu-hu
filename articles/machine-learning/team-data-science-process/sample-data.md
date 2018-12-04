---
title: Az Azure blob-tárolók, SQL Server, az adatokat, és Hive-táblákban |} A Microsoft Docs
description: Hogyan lehet a különböző Azure enviromnents tárolt adatok megismerése.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: (previous author=deguhath, ms.author=deguhath)
ms.openlocfilehash: 679d812c239e0f626d2872d29bc9de7848399bc6
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844600"
---
# <a name="heading"></a>Adatmintavétel az Azure blobtárolókban, az SQL Server, és Hive-táblákban

Az alábbi cikkek ismertetik, hogyan lehet az adatokat három különböző Azure-helyen tárolt:

* [**Az Azure blob-tároló adatok** ](sample-data-blob.md) töltse le a programozott módon, és ezután mintavételezi, a Python-mintakód mintavételezés.
* [**SQL Server-adatok** ](sample-data-sql-server.md) mintavételezés SQL és a Python programozási nyelv használatával. 
* [**Hive-táblák adatainak** ](sample-data-hive.md) mintavételezés Hive-lekérdezések segítségével.

Ez a mintavételi feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Miért érdemes az adatokat?**

Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. Ez lehetővé teszi az adatok megértését, feltárási és funkciófejlesztési. Szerepét a Cortana Analytics-folyamatban, az adatfeldolgozás funkciók és a gépi tanulási modellek gyors prototípus-készítés engedélyezése.

