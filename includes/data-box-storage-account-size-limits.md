---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469873"
---
Az alábbiakban a tárfiókba másolt adatok méretére vonatkozó korlátokat. Győződjön meg arról, hogy a feltöltött adatok megfelelnek ezeknek a korlátoknak. Ezekről a korlátokról a [méretezhetőségi és teljesítménycélok a Blob storage](../articles/storage/blobs/scalability-targets.md) és az [Azure Files méretezhetőségi és teljesítménycélok](../articles/storage/files/storage-files-scale-targets.md)című témakörben talál.

| Az Azure storage-fiókba másolt adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| Blob és lapblob blokkolása                                            | 2 PB az Egyesült Államok és Európa számára.<br>500 TB az összes többi régió, amely magában foglalja az Egyesült Királyságban.  <br> Ez magában foglalja az összes forrásból származó adatokat, beleértve a Data Box-ot is.|
| Azure Files                                                          | 5 TB részvényenként.<br> *A StorageAccount_AzureFiles* alatt található összes mappának követnie kell ezt a korlátot.       |
