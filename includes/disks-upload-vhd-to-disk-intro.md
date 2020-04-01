---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420951"
---
Ez a cikk bemutatja, hogyan tölthet fel egy virtuális merevlemezt a helyi gépről egy Azure által kezelt lemezre, vagy egy felügyelt lemezt másolhat egy másik régióba az AzCopy használatával. Ez a folyamat, közvetlen feltöltés, azt is lehetővé teszi, hogy feltölt egy 32 TiB méretű virtuális merevlemezt közvetlenül egy felügyelt lemezre. Jelenleg a közvetlen feltöltés támogatott szabványos HDD, standard SSD és prémium szintű SSD felügyelt lemezek. Ez nem támogatott ultra lemezek, még.

Ha az Azure-beli IaaS virtuális gépek biztonsági mentési megoldását biztosítja, javasoljuk, hogy közvetlen feltöltéssel állítsa vissza az ügyfelek biztonsági mentését a felügyelt lemezekre. VHD feltöltésekesetén az Azure-on kívüli forrásból, a sebesség a helyi sávszélességtől függ. Azure-beli virtuális gép feltöltésekor vagy másolásakor, akkor a sávszélesség ugyanaz lesz, mint a szabványos HDD-k.