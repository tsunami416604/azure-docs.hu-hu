---
title: Mintaadatok a különböző Azure tárhely - csoportos adatelemzési folyamat
description: Adatmintavétel az Azure blobtárolókban, az SQL Server, és Hive-táblákban egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc91aec80f74488125649cfe807757ba5ae49c9b
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466336"
---
# <a name="heading"></a>Adatmintavétel az Azure blobtárolókban, az SQL Server, és Hive-táblákban

Az alábbi cikkek ismertetik, hogyan lehet az adatokat három különböző Azure-helyen tárolt:

* [**Az Azure blob-tároló adatok** ](sample-data-blob.md) töltse le a programozott módon, és ezután mintavételezi, a Python-mintakód mintavételezés.
* [**SQL Server-adatok** ](sample-data-sql-server.md) mintavételezés SQL és a Python programozási nyelv használatával. 
* [**Hive-táblák adatainak** ](sample-data-hive.md) mintavételezés Hive-lekérdezések segítségével.

Ez a mintavételi feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

**Miért érdemes az adatokat?**

Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. Ez lehetővé teszi az adatok megértését, feltárási és funkciófejlesztési. Szerepét a Cortana Analytics-folyamatban, az adatfeldolgozás funkciók és a gépi tanulási modellek gyors prototípus-készítés engedélyezése.

