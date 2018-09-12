---
title: Az Azure Stackhez az infrastruktúra biztonsági másolat szolgáltatással biztonsági mentés és adat helyreállítási |} A Microsoft Docs
description: Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és szolgáltatási adatokat az infrastruktúra biztonsági másolat szolgáltatással.
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
ms.date: 9/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 9f2668ff84ade4ba99b7aa7dcd67feafadc1c6c4
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44377836"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Az Azure Stackhez az infrastruktúra biztonsági mentési szolgáltatással a biztonsági mentés és adat-helyreállítás

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Készítsen biztonsági másolatot, és állítsa vissza a konfigurációs és szolgáltatási adatokat az infrastruktúra biztonsági másolat szolgáltatással. Minden egyes Azure Stack-telepítés tartalmazza a szolgáltatás egy példányát. Az újbóli üzembe helyezés az Azure Stack-felhőben, a szolgáltatás által létrehozott biztonsági mentéseket használatával állítsa vissza az identitás, a biztonság és az Azure Resource Manager-adatokat.

Amikor készen áll a felhőben üzembe éles engedélyezheti a biztonsági mentés. Ne engedélyezze a biztonsági mentés, ha azt tervezi, hogy a teszt végrehajtása és a egy hosszú ideig ellenőrzése.

Mielőtt engedélyezné a biztonsági mentési szolgáltatás, ellenőrizze, hogy [követelmények teljesülnek](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Az infrastruktúra biztonsági mentési szolgáltatás nem tartalmaz felhasználói adatokat és alkalmazásokat. Lásd az alábbi cikkeket, útmutatást a biztonsági mentése és visszaállítása [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), és [MySQL](https://aka.ms/azure-stack-mysql) erőforrás-szolgáltatók és a kapcsolódó felhasználói adatok...

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
