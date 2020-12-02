---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466854"
---
Az eszköze társítva van a tárfiókhoz, amelyet az Azure-ban tárolt adatok célhelyeként szolgál. A tárfiók elérését az előfizetés és két 512 bites tárelérési kulcs szabályozza, amelyek társítva vannak a tárfiókkal.

A kulcsok egyike a hitelesítéshez használatos, amikor a Data Box Edge eszköz hozzáfér a Storage-fiókhoz. A másik kulcs a tartalék, így a kulcsok rendszeresen lecserélhetők.

Biztonsági okokból számos adatközpont megköveteli a kulcsváltást. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Ügyeljen a fiókja kulcsának védelmére. Ne ossza meg a jelszót más felhasználókkal, rögzítse szoftveresen, vagy mentse el egy mások által is elérhető egyszerű szövegbe.
- Ha úgy gondolja, hogy sérült, akkor a [fiók kulcsát](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) a Azure Portal használatával újra létrehozhatja.
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.