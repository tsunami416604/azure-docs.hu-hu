---
title: Az Azure Stackhez az infrastruktúra biztonsági másolat szolgáltatással biztonsági mentés és adat helyreállítási |} A Microsoft Docs
description: Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és szolgáltatási adatokat az infrastruktúra biztonsági másolat szolgáltatással.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
mss.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 4cb8ffe218ef1cd64b93201eddbbd09bb16026db
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087389"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Az Azure Stackhez az infrastruktúra biztonsági mentési szolgáltatással a biztonsági mentés és adat-helyreállítás

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és szolgáltatási adatokat az infrastruktúra biztonsági másolat szolgáltatással. Minden egyes Azure Stack-telepítés tartalmazza a szolgáltatás egy példányát. Az újbóli üzembe helyezés az Azure Stack-felhőben, a szolgáltatás által létrehozott biztonsági mentéseket használatával állítsa vissza az identitás, a biztonság és az Azure Resource Manager-adatokat.

Amikor készen áll a felhőben üzembe éles engedélyezheti a biztonsági mentés. Ne engedélyezze a biztonsági mentés, ha azt tervezi, hogy a teszt végrehajtása és a egy hosszú ideig ellenőrzése.

Mielőtt engedélyezné a biztonsági mentési szolgáltatás, ellenőrizze, hogy [követelmények teljesülnek](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Az infrastruktúra biztonsági mentési szolgáltatás nem tartalmaz felhasználói adatokat és alkalmazásokat. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>A biztonsági mentési infrastruktúra-szolgáltatás

A szolgáltatások a következő szolgáltatásokat tartalmazza.

| Szolgáltatás                                            | Leírás                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Biztonsági mentési infrastruktúra-szolgáltatások                     | A biztonsági mentés koordináta az Azure Stack infrastruktúra-szolgáltatások egy részhalmaza között. Katasztrófa esetén az adatok újbóli üzembe helyezés részeként is visszaállítható. |
| A tömörítés és titkosítás exportált biztonsági mentési adatok | Biztonsági mentési adatok tömörített, és a rendszer titkosítja, mielőtt exportálja azt a külső tárhelyen találhatók, a rendszergazda által biztosított.                |
| Biztonsági mentési feladat figyelése                              | Rendszer értesíti, ha a biztonsági mentési feladatok sikertelenek, a javítási lépéseket.                                                                                                |
| Biztonságimásolat-kezelési élményt                       | Biztonsági mentési helyreállítási pont Védettként engedélyezése biztonsági mentést támogatja.                                                                                                                         |
| Felhőbeli helyreállítási                                     | Végzetes adatvesztés esetén a biztonsági mentések visszaállítása az Azure Stack legfontosabb tudnivalók a központi telepítésének részeként használható.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Az infrastruktúra biztonsági mentési szolgáltatás követelményeinek ellenőrzése

- **Tárolási hely**  
  Fájlmegosztás elérhető az Azure Stacken, amely tartalmazhat hét biztonsági mentéseket kell. Minden biztonsági mentés: 10 GB. A megosztás tárolja a biztonsági mentések 140 GB képesnek kell lennie. Az Azure Stack infrastruktúra Backup szolgáltatás egy tárolási hely kiválasztásával kapcsolatos további információkért lásd: [biztonsági mentést vezérlő követelmények](azure-stack-backup-reference.md#backup-controller-requirements).
- **Hitelesítő adatok**  
  Szüksége van egy tartományi felhasználói fiók és a hitelesítő adatok, például használhatja az Azure Stack rendszergazdai hitelesítő adatait.
- **Titkosítási kulcs**  
  Biztonsági másolatok ezt a kulcsot az vannak titkosítva. Ellenőrizze, hogy ezt a kulcsot biztonságos helyen tárolja. Után állítsa be ezt a kulcsot az első alkalommal vagy a kulcs rotálása a jövőben, akkor ezt a kulcsot a felületén nem jelennek meg. Előre megosztott kulcs létrehozásához további utasításokért hajtsa végre a scripts [biztonsági mentés engedélyezése az Azure Stack a PowerShell-lel](azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [biztonsági mentés engedélyezése az Azure Stack a felügyeleti portálról](azure-stack-backup-enable-backup-console.md).
- Ismerje meg, hogyan [biztonsági mentés engedélyezése a PowerShell-lel az Azure Stack](azure-stack-backup-enable-backup-powershell.md).
- Ismerje meg, hogyan [biztonsági mentése az Azure Stackben](azure-stack-backup-back-up-azure-stack.md )
- Ismerje meg, hogyan [végzetes adatvesztés utáni helyreállítás](azure-stack-backup-recover-data.md)
