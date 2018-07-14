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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750097"
---
Az Azure Batch szolgáltatásban futó feladat lehet, hogy kimeneti adatok előállításához, futtatásakor. A kimeneti adatokat gyakran kell más tevékenységek a feladat által lekérés kell tárolni, az ügyfélalkalmazás, amely a feladat vagy mindkettő végrehajtott feladat. Feladatok kimeneti adatokat írni a fájlrendszer, a Batch számítási csomópont, de a csomópont minden adat elveszik, amikor rendszerképének, vagy amikor a csomópont elhagyja-e a készlet. Feladatok is előfordulhat, hogy egy fájl megőrzési időtartamot, amely után a rendszer törli a feladat által létrehozott fájlokat. Ebből kifolyólag, fontos, hogy később szüksége lesz egy adattárba, mint például a feladat kimenetének megőrzése [Azure Storage](https://docs.microsoft.com/azure/storage/).

A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](../articles/batch/batch-api-basics.md#azure-storage-account) talál.
