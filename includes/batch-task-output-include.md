---
title: fájlbefoglalás
description: fájlbefoglalás
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83797181"
---
A Azure Batch futó feladatok a futásakor kimeneti adatokat hozhatnak létre. A feladat kimeneti adatait gyakran kell tárolni a feladat egyéb feladatai, a feladatot futtató ügyfélalkalmazás vagy mindkettő között. A tevékenységek a kimeneti adatokat egy batch számítási csomópont fájlrendszerére írják, de a csomóponton lévő összes adat elvész, amikor a rendszer újrarendszerképét végzi, vagy amikor a csomópont elhagyja a készletet. A feladatok is rendelkezhetnek fájl-megőrzési időtartammal, amely után a feladat által létrehozott fájlok törlődnek. Ezen okok miatt fontos a feladat kimenetének megőrzése, amelyre később szüksége lesz egy adattárba, például az [Azure Storage](https://docs.microsoft.com/azure/storage/)-ba.

A Storage-fiók beállításai a Batchben: [Batch-fiókok és Azure Storage-fiókok](../articles/batch/accounts.md#azure-storage-accounts).
