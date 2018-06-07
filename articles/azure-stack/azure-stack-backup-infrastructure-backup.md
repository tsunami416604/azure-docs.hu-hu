---
title: Biztonsági mentés és adatok helyreállítása Azure verem az infrastruktúra biztonsági másolat szolgáltatással |} Microsoft Docs
description: Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és a szolgáltatás adatokat az infrastruktúra biztonsági másolat szolgáltatással.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 12138ac5a173f66d8b6b0041de9f31f4ac326485
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34822955"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Azure verem és az infrastruktúra biztonsági mentési szolgáltatás biztonsági mentési és az adatok helyreállítása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és a szolgáltatás adatokat az infrastruktúra biztonsági másolat szolgáltatással. Minden Azure verem telepítés tartalmaz-e a szolgáltatás egy példánya. A központi telepítésre, az Azure-verem felhőalapú szolgáltatás által létrehozott biztonsági másolatok segítségével azonosító, biztonsági és Azure Resource Manager-adatok visszaállítása.

Engedélyezheti a biztonsági mentés, ha készen áll a felhőalapú üzembe helyezésre. Ne engedélyezze a biztonsági mentés, ha azt tervezi, hogy tesztek végrehajtása és -ellenőrzés hosszú ideig.

A biztonsági mentési szolgáltatás engedélyezése előtt győződjön meg arról, hogy [követelmények teljesülnek](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Az infrastruktúra biztonsági mentési szolgáltatás nem tartalmaz felhasználói adatokat és alkalmazásokat. A következő cikkekben további tájékoztatást lásd a biztonsági mentése és visszaállítása [alkalmazásszolgáltatások](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), és [MySQL](https://aka.ms/azure-stack-mysql) erőforrás-szolgáltatók és a kapcsolódó felhasználói adatokat.

## <a name="the-infrastructure-backup-service"></a>Az infrastruktúra a biztonsági mentési szolgáltatás

A szolgáltatások a következő szolgáltatásokat tartalmazza.

| Szolgáltatás                                            | Leírás                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentési infrastruktúra-szolgáltatásokat                     | Biztonsági mentés koordinálja a verem Azure infrastruktúra-szolgáltatások egy részhalmaza között. Ha egy olyan vészhelyzet esetén, az adatok visszaállítása végezhető el az újratelepítés részeként. |
| A tömörítés és az exportált biztonsági mentési adatok titkosítása | Biztonsági mentési adatok tömörített, és a rendszer a külső tárhelyen, a rendszergazda által biztosított exportálása előtt titkosítja.                |
| Biztonsági mentési feladat figyelése                              | Rendszer értesítést küld, ha a biztonsági mentési feladat sikertelen lesz, a javítási lépéseket.                                                                                                |
| Biztonságimásolat-felügyeleti élmény                       | Biztonsági mentési RP lehetővé teszi biztonsági mentését is támogatja.                                                                                                                         |
| A helyreállítási felhő                                     | Végzetes adatvesztés esetén a biztonsági mentések visszaállításáról core Azure verem központi telepítésének részeként használható.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Ellenőrizze az infrastruktúra biztonsági mentési szolgáltatás követelményei

- **Tárolási helye**  
  A fájlmegosztás elérhető Azure veremből hét biztonsági másolatokat tartalmazó kell. Minden biztonsági mentés körülbelül 10 GB-TAL. A megosztás képes tárolni a biztonsági mentések 140 GB lehet. További információ a tárolási helyének kiválasztása az Azure verem infrastruktúra biztonsági másolat szolgáltatás: [biztonsági mentést vezérlő követelmények](azure-stack-backup-reference.md#backup-controller-requirements).
- **Hitelesítő adatok**  
  Szükség van egy tartományi felhasználói fiók és a hitelesítő adatok, például használhatja az Azure-verem rendszergazdájának hitelesítő adatait.
- **Titkosítási kulcs**  
  A biztonságimásolat-fájlok titkosítása a kulcs használatával. Győződjön meg arról, hogy ezt a kulcsot biztonságos helyen tárolja. Állítsa be ezt a kulcsot az első alkalommal vagy a jövőben forgassa el a kulcsot, nem lehet megtekinteni, ezt a kulcsot, az adott csatolón. Egy előre megosztott kulcs létrehozásához további útmutatást kövesse a scripts [Azure verem PowerShell és a biztonsági mentés engedélyezése](azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [biztonsági mentés engedélyezése a felügyeleti portál Azure verem](azure-stack-backup-enable-backup-console.md).
- Megtudhatja, hogyan [biztonsági mentésének engedélyezése a PowerShell-lel Azure verem](azure-stack-backup-enable-backup-powershell.md).
- Megtudhatja, hogyan [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md )
- Megtudhatja, hogyan [végzetes adatvesztés helyreállítása](azure-stack-backup-recover-data.md)
