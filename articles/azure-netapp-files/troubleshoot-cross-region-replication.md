---
title: Régiók közötti replikációval kapcsolatos hibák – Azure NetApp Files | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segíthetnek a Azure NetApp Files régiók közötti replikációs problémáinak elhárításában.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 3aaa5d2bc6fdbda0d1db212539c719aa65cae61b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90709017"
---
# <a name="troubleshoot-cross-region-replication"></a>Régiók közötti replikáció hibaelhárítása

Ez a cikk azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segíthetnek a Azure NetApp Files régiók közötti replikációs problémáinak elhárításában. 

## <a name="errors-creating-replication"></a>Hiba történt a replikáció létrehozásakor  

|     Hibaüzenet    |     Feloldás    |
|-|-|
|     `Volume {0} cannot   be used as source because it is already in replication`    |     Nem hozhat létre replikálást olyan forrás kötettel, amely már adatreplikációs kapcsolatban van.    |
|     `Peered region   '{0}' is not accepted`    |     Replikációt próbál létrehozni a nem egymással összekapcsolt régiók között.    |
|     `RemoteVolumeResource   '{0}' of wrong type '{1}'`    |     Ellenőrizze, hogy a távoli erőforrás-azonosító mennyiségi erőforrás-azonosító-e.    |

## <a name="errors-authorizing-volume"></a>A kötetet engedélyező hibák  

|     Hibaüzenet    |     Feloldás    |
|-|-|
|     `Missing value   for 'AuthorizeSourceReplication'`    |     A   `RemoteResourceID` hiányzik vagy érvénytelen a felhasználói felület vagy az API-kérelemben (hibajavítási hibaüzenet).    |
|     `Missing value   for 'RemoteVolumeResourceId'`    |     `RemoteResourceID`Hiányzik vagy érvénytelen a felhasználói felület vagy az API-kérelem.    |
|     `Data   Protection volume not found for RemoteVolumeResourceId: {remoteResourceId}`    |     Ellenőrizze   `RemoteResourceID` , hogy helyes vagy létezik-e a felhasználó számára.    |
|     `Remote volume   '{0}' is not configured for replication`    |     A célként megadott kötet nem egy adatvédelmi kötet.    |
|     `Remote volume   '{0}' does not have source volume '{1}' as RemoteVolumeResourceId`    |     Az adatvédelmi kötet nem rendelkezik a forrás kötettel a távoli erőforrás-AZONOSÍTÓjában (nem megfelelő forrás-azonosító lett megadva).    |
|     `The   destination volume replication creation failed (message: {0})`    |     Ez a hiba kiszolgálóhiba hibát jelez. Forduljon az ügyfélszolgálathoz.    |

## <a name="errors-deleting-replication"></a>A replikálást törlő hibák

|     Hibaüzenet    |     Feloldás    |
|-|-|
|     `Replication   cannot be deleted, mirror state needs to be in status: Broken before deleting`    |     Ellenőrizze, hogy a replikáció megszakadt-e, vagy nincs-e inicializálva és üresjáratban (sikertelen inicializálás).    |
|     `Cannot delete   source replication`    |     Nem engedélyezett a replikálás törlése a forrás oldalról. Győződjön meg arról, hogy a célhelyről törli a replikálást.    |

## <a name="errors-resyncing-volume"></a>Hiba történt a kötet újraszinkronizálásakor

|     Hibaüzenet    |     Feloldás    |
|-|-|
|     `Volume Replication is in invalid status: (Mirrored|Uninitialized) for operation: 'ResyncReplication'`     |     Ellenőrizze, hogy a kötet replikálása "Broken" állapotú-e.    |

## <a name="errors-deleting-snapshot"></a>Pillanatkép-törlési hibák 

|     Hibaüzenet    |     Feloldás    |
|-|-|
|     `Snapshot   cannot be deleted, parent volume is a Data Protection volume with a   replication object`    |     Ellenőrizze, hogy megszakította-e a kötet replikálását, ha törölni szeretné ezt a pillanatképet.    |
|     `Cannot delete   volume replication generated snapshot`    |     A replikációs alapterv Pillanatképek törlése nem engedélyezett.    |

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs társviszony létrehozása](cross-region-replication-create-peering.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Vészhelyreállítás kezelése](cross-region-replication-manage-disaster-recovery.md)
* [Régiók közötti replikáció hibaelhárítása](troubleshoot-cross-region-replication.md)
