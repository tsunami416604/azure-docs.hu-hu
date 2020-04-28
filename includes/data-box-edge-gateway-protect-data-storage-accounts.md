---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75466826"
---
Az eszköz egy olyan Storage-fiókhoz van társítva, amelyet az Azure-ban tárolt adataihoz célként használt. A Storage-fiókhoz való hozzáférést az előfizetés és az ehhez a Storage-fiókhoz tartozó 2 512 bites tárterület-hozzáférési kulcsok vezérlik.

A kulcsok egyike a hitelesítéshez használatos, amikor a Data Box Edge eszköz hozzáfér a Storage-fiókhoz. A másik kulcs a tartalékban van tárolva, így a kulcsok rendszeres elforgatása is megtörténik.

Biztonsági okokból számos adatközponthoz kulcsfontosságú rotáció szükséges. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. A fiók kulcsa körültekintően védhető. Ne ossza meg a jelszót más felhasználók számára, vagy jegyezze fel a kódot, vagy mentse a többi felhasználó számára elérhető egyszerű szövegbe.
- Ha úgy gondolja, hogy sérült, akkor a fiók kulcsát a Azure Portal használatával újra létrehozhatja. További információ: a [Storage-fiók elérési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.