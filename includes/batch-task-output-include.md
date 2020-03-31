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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179472"
---
Az Azure Batch-ben futó feladat kimeneti adatokat hozhat létre, amikor fut. A feladat kimeneti adatait gyakran tárolni kell a feladat más feladatai, a feladatot elvezető ügyfélalkalmazás vagy mindkettő lekéréséhez. A feladatok kimeneti adatokat írnak egy Batch számítási csomópont fájlrendszerébe, de a csomóponton lévő összes adat elvész, amikor újralemezeli, vagy amikor a csomópont elhagyja a készletet. A feladatok nak fájlmegőrzési időszaka is lehet, amely után a feladat által létrehozott fájlok törlődnek. Ezen okok miatt fontos, hogy megőrizje a feladatkimenetet, amelyre később egy adattárba, például az [Azure Storage-ba](https://docs.microsoft.com/azure/storage/)lesz szüksége.

A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](../articles/batch/batch-api-basics.md#azure-storage-account) talál.
