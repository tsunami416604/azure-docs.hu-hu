---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736977"
---
Itt láthatók a Storage-fiókba másolt adatmennyiség korlátai. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. A korlátozásokkal kapcsolatos legfrissebb információk: a [blob Storage skálázhatósági és teljesítményi céljai](../articles/storage/blobs/scalability-targets.md) , valamint a [méretezhetőség és a teljesítmény-Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Az Azure Storage-fiókba másolt adatmennyiség                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| BLOB és oldal blobjának letiltása                                            | 2 PB az USA és Európa számára.<br>500 TB az összes többi régióban, beleértve az Egyesült Királyságot is.  <br> Ide tartoznak az összes forrásból származó adatok, beleértve a Data Box is.|
| Azure Files                                                          | A standard fájlmegosztás maximális mérete 100TiB *, 5 TB, a prémium fájlmegosztás 100TiB száma.<br> A *StorageAccount_AzureFiles* alatti összes mappának ezt a korlátot kell követnie.       |
