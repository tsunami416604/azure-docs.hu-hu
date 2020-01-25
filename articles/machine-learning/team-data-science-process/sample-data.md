---
title: Mintaadatok különböző Azure Storage-helyszíneken – csoportos adatelemzési folyamat
description: Mintaadatok az Azure Blob-tárolókban, a SQL Server és a kaptár-táblákban, így kisebb, de reprezentatív és felügyelhető méretre csökkenthetők.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718603"
---
# <a name="heading"></a>Mintaadatok az Azure Blob-tárolókban, a SQL Server és a kaptár táblákban

A következő cikkek a három különböző Azure-hely egyikében tárolt adatmintavételezést ismertetik:

* Az [**Azure Blob Container**](sample-data-blob.md) -adatmintavételt úgy kell megtervezni, hogy programozott módon letölti, majd a minta Python-kóddal végzi a mintavételezést.
* [**SQL Server adatokat**](sample-data-sql-server.md) az SQL és a Python programozási nyelv használatával kell mintát venni. 
* A [**kaptár-táblák adatai**](sample-data-hive.md) a kaptár-lekérdezések használatával lettek mintavétel alatt.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

**Miért érdemes a mintaadatok?**

Ha az elemezni kívánt adatkészlet nagy méretű, általában egy jó ötlet, hogy lerövidítse az adatokat, hogy csökkentse azt kisebb, de reprezentatív és felügyelhető méretre. A leépítés lehetővé teszi az adatfelismerést, a feltárást és a szolgáltatások fejlesztését. Ez a mintavételi szerepkör a Cortana Analytics-folyamatban az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

