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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67179472"
---
A Azure Batch futó feladatok a futásakor kimeneti adatokat hozhatnak létre. A feladat kimeneti adatait gyakran kell tárolni a feladat egyéb feladatai, a feladatot futtató ügyfélalkalmazás vagy mindkettő között. A tevékenységek a kimeneti adatokat egy batch számítási csomópont fájlrendszerére írják, de a csomóponton lévő összes adat elvész, amikor a rendszer újrarendszerképét végzi, vagy amikor a csomópont elhagyja a készletet. A feladatok is rendelkezhetnek fájl-megőrzési időtartammal, amely után a feladat által létrehozott fájlok törlődnek. Ezen okok miatt fontos a feladat kimenetének megőrzése, amelyre később szüksége lesz egy adattárba, például az [Azure Storage](https://docs.microsoft.com/azure/storage/)-ba.

A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](../articles/batch/batch-api-basics.md#azure-storage-account) talál.
