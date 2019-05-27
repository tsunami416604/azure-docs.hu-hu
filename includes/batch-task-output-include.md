---
title: fájl belefoglalása
description: fájl belefoglalása
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127526"
---
Az Azure Batch szolgáltatásban futó feladat lehet, hogy kimeneti adatok előállításához, futtatásakor. A kimeneti adatokat gyakran kell más tevékenységek a feladat által lekérés kell tárolni, az ügyfélalkalmazás, amely a feladat vagy mindkettő végrehajtott feladat. Feladatok kimeneti adatokat írni a fájlrendszer, a Batch számítási csomópont, de a csomópont minden adat elveszik, amikor rendszerképének, vagy amikor a csomópont elhagyja-e a készlet. Feladatok is előfordulhat, hogy egy fájl megőrzési időtartamot, amely után a rendszer törli a feladat által létrehozott fájlokat. Ebből kifolyólag, fontos, hogy később szüksége lesz egy adattárba, mint például a feladat kimenetének megőrzése [Azure Storage](https://docs.microsoft.com/azure/storage/).

A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](../articles/batch/batch-api-basics.md#azure-storage-account) talál.
