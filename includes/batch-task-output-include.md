---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
Azure Batch futó feladat lehet, hogy eredményezett kimeneti adatokat, amikor fut az. A kimeneti adatok gyakran kell tárolni, a lekérdezés egyéb feladatok, a feladat, az ügyfélalkalmazás a feladat vagy mindkettő végrehajtott feladat. Feladatok kimeneti adatokat írni a fájlrendszer, a Batch számítási csomópont, de a csomóponton lévő összes adat elvész, amikor lemezképet, vagy amikor a csomópont elhagyja a készletet. Feladatok is fájl megőrzési időtartamot, amely után a feladat által létrehozott fájlok törlődnek. Ezen okok miatt, fontos, hogy továbbra is fennáll, amelyek később szüksége számára egy adattárból, mint a feladat kimenetének [Azure Storage](https://docs.microsoft.com/azure/storage/).

Kötegelt tárolási fiók beállításait, tekintse meg a [Batch funkcióinak áttekintése](../articles/batch/batch-api-basics.md#azure-storage-account).
