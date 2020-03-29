---
title: Méretezhetőségi célok a prémium szintű lapblobstorage-fiókokhoz
titleSuffix: Azure Storage
description: A prémium szintű teljesítmény lap blob storage-fiók írási/írási műveletekre van optimalizálva. Ez a típusú tárfiók egy Azure virtuális gép nem felügyelt lemez.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756249"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Méretezhetőségi és teljesítménycélok a prémium szintű lapblobstorage-fiókokhoz

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>A prémium szintű lapblob-fiókok céljainak méretezése

A prémium szintű teljesítményű lapblob-tárfiók írási/írási műveletekre van optimalizálva. Ez a típusú tárfiók egy Azure virtuális gép nem felügyelt lemez.

> [!NOTE]
> A Microsoft azt javasolja, hogy a felügyelt lemezek azure-beli virtuális gépek (VM-ek) használata, ha lehetséges. A felügyelt lemezekről az [Azure Disk Storage – áttekintés a Windows virtuális gépekről](../../virtual-machines/windows/managed-disks-overview.md)című témakörben olvashat bővebben.

A prémium szintű lapblob-tárfiókok a következő méretezhetőségi célokkal rendelkeznek:

| Teljes számlakapacitás                            | Teljes sávszélesség egy helyileg redundáns tárfiókhoz                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Lemezkapacitás: 4 TB (egyedi lemez)/ 35 TB (az összes lemez összesítése) <br>Pillanatkép kapacitása: 10 TB | Akár 50 gigabit másodpercenként a bejövő<sup>1</sup> + kimenő<sup>2</sup> |

<sup>1</sup> A tárfiókba küldött összes adat (kérés)

<sup>2</sup> A tárfiókból érkező összes adat (válasz)

A prémium szintű lapblob-fiók egy általános célú fiók, amely prémium szintű teljesítményre van konfigurálva. Általános célú v2 tárfiókok ajánlott.

Ha prémium szintű lapblob storage-fiókokat használ a nem felügyelt lemezekhez, és az alkalmazás meghaladja az egyetlen tárfiók méretezhetőségi céljait, majd a Microsoft javasolja a felügyelt lemezekre való áttelepítést. A felügyelt lemezekről az [Azure Disk Storage áttekintése a Windows virtuális gépekhez](../../virtual-machines/windows/managed-disks-overview.md) vagy [az Azure Disk Storage áttekintése linuxos virtuális gépekhez című témakörben olvashat bővebben.](../../virtual-machines/linux/managed-disks-overview.md)

Ha nem tud áttelepíteni a felügyelt lemezekre, majd az alkalmazást több tárfiók használatára, és particionálja az adatokat a tárfiókok között. Ha például 51 TB-os lemezeket szeretne csatolni több virtuális géphez, két tárfiók között kell elosztania őket. 35 TB egy prémium szintű tárfiók korlátot. Győződjön meg arról, hogy egyetlen prémium szintű teljesítménytár-fiók soha nem rendelkezik több, mint 35 TB-os kiépített lemezek.

## <a name="see-also"></a>Lásd még

- [Méretezhetőségi és teljesítménycélok a szabványos tárfiókokhoz](../common/scalability-targets-standard-account.md)
- [Méretezhetőségi célok a prémium szintű blokkblob-tárfiókokhoz](../blobs/scalability-targets-premium-block-blobs.md)
- [Az Azure előfizetési korlátai és kvótái](../../azure-resource-manager/management/azure-subscription-service-limits.md)
