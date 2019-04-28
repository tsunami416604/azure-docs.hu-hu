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
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549944"
---
Az Azure Batch szolgáltatásban futó feladat lehet, hogy kimeneti adatok előállításához, futtatásakor. A kimeneti adatokat gyakran kell más tevékenységek a feladat által lekérés kell tárolni, az ügyfélalkalmazás, amely a feladat vagy mindkettő végrehajtott feladat. Feladatok kimeneti adatokat írni a fájlrendszer, a Batch számítási csomópont, de a csomópont minden adat elveszik, amikor rendszerképének, vagy amikor a csomópont elhagyja-e a készlet. Feladatok is előfordulhat, hogy egy fájl megőrzési időtartamot, amely után a rendszer törli a feladat által létrehozott fájlokat. Ebből kifolyólag, fontos, hogy később szüksége lesz egy adattárba, mint például a feladat kimenetének megőrzése [Azure Storage](https://docs.microsoft.com/azure/storage/).

A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](../articles/batch/batch-api-basics.md#azure-storage-account) talál.
