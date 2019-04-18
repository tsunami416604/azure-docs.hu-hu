---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684447"
---
Az eszköz kapcsolódik egy storage-fiókot, amely az adatok az Azure-ban egy célhelyként szolgál. A tárfiókhoz való hozzáférést vezérlik az előfizetést és a két 512 bites tárelérési eléréséhez a storage-fiókhoz társított kulcsokat.

A kulcsok egyikét a hitelesítéshez során használatos a Data Box peremhálózati eszköz hozzáfér a tárfiókhoz. A másik hívóbetűt tárolt tartalék, amely lehetővé teszi a kulcsok rotálására rendszeres időközönként.

Biztonsági okokból a sok adatközpontok kulcsrotálás szükséges. Javasoljuk, hogy kövesse az ajánlott eljárások kulcsrotálás:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Alaposan a fiókja kulcsának védelmére. Nem terjesztheti más felhasználók jelszavát, keményen code, vagy mentse bárhol, mások számára elérhető egyszerű szöveges fájlban.
- [A fiók kulcsának újragenerálásával](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) az Azure portal használatával, ha úgy véli, előfordulhat, hogy sérült a biztonsága.
- Rendszeres időközönként az Azure-rendszergazda kell módosítani, vagy az elsődleges vagy másodlagos kulcsának újragenerálása: közvetlenül hozzáférni a tárfiókhoz a Storage szakaszban az Azure Portal használatával.