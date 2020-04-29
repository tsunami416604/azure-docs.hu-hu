---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75469873"
---
Itt láthatók a Storage-fiókba másolt adatmennyiség korlátai. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. A korlátozásokkal kapcsolatos legfrissebb információk: a [blob Storage skálázhatósági és teljesítményi céljai](../articles/storage/blobs/scalability-targets.md) , valamint a [méretezhetőség és a teljesítmény-Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Az Azure Storage-fiókba másolt adatmennyiség                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| BLOB és oldal blobjának letiltása                                            | 2 PB az USA és Európa számára.<br>500 TB az összes többi régióban, beleértve az Egyesült Királyságot is.  <br> Ide tartoznak az összes forrásból származó adatok, beleértve a Data Box is.|
| Azure Files                                                          | 5 TB/megosztás.<br> A *StorageAccount_AzureFiles* alatti összes mappának ezt a korlátot kell követnie.       |
