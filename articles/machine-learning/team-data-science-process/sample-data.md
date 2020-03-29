---
title: Mintaadatok az Azure Storage különböző helyein – Csapatadat-elemzési folyamat
description: Mintaadatok az Azure blob tárolók, AZ SQL Server és a Hive-táblák kisebb, de reprezentatívabb és kezelhetőbb méretre csökkentése érdekében.
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
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718603"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Adatmintavétel az Azure blobtárolókban, az SQL Serveren, és a Hive-táblázatokban

Az alábbi cikkek ismertetik, hogyan minta adatokat, amelyek három különböző Azure-helyek:

* [**Az Azure blob tároló adatai**](sample-data-blob.md) mintavételezése programozott módon, majd mintavételezési mintát a Minta Python-kódot.
* [**Az SQL Server-adatok**](sample-data-sql-server.md) mintavételezése az SQL és a Python programozási nyelv használatával is történik. 
* [**Hive tábla adatok**](sample-data-hive.md) mintavételezése Hive-lekérdezések használatával.

Ez a mintavételezési feladat egy lépés a [csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

**Miért érdemes mintaadatokat venni?**

Ha az elemezni kívánt adatkészlet nagy, általában érdemes levenni az adatokat, hogy csökkentse azokat egy kisebb, de reprezentatívabb és kezelhetőbb méretre. A leépítés megkönnyítheti az adatok megértését, feltárását és a szolgáltatástervezést. Ez a mintavételi szerepkör a Cortana Analytics folyamat, hogy lehetővé tegye a gyors prototípus az adatfeldolgozási funkciók és a gépi tanulási modellek.

