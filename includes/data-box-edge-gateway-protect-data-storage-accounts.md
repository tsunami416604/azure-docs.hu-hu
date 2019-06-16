---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120528"
---
Az eszköz kapcsolódik egy storage-fiókot, amely az adatok az Azure-ban egy célhelyként szolgál. A tárfiókhoz való hozzáférést vezérlik az előfizetést és a két 512 bites tárelérési eléréséhez a storage-fiókhoz társított kulcsokat.

A kulcsok egyikét a hitelesítéshez során használatos a Data Box peremhálózati eszköz hozzáfér a tárfiókhoz. A másik hívóbetűt tárolt fenntartott, ezért a kulcsok rendszeres időközönként elfordításával.

Biztonsági okokból a sok adatközpontok kulcsrotálás szükséges. Javasoljuk, hogy kövesse az ajánlott eljárások kulcsrotálás:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Alaposan a fiókja kulcsának védelmére. Nem terjesztheti más felhasználók jelszavát, keményen code, vagy mentse azt bárhol, hogy mások számára elérhető egyszerű szöveges.
- [A fiók kulcsának újragenerálásával](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) keresztül az Azure Portalon, ha úgy véli, sérülhet.
- Az Azure-rendszergazdai rendszeres időközönként kell módosítani vagy az elsődleges vagy másodlagos kulcsának újragenerálása: közvetlenül hozzáférni a tárfiókhoz a Storage szakaszban az Azure Portal használatával.