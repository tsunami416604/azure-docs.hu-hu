---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562124"
---
Az eszköz egy olyan Storage-fiókhoz van társítva, amelyet az Azure-ban tárolt adataihoz célként használt. A Storage-fiókhoz való hozzáférést az előfizetés és az ehhez a Storage-fiókhoz tartozó 2 512 bites tárterület-hozzáférési kulcsok vezérlik.

Az egyik kulcs a hitelesítéshez használatos, ha az Azure Stack Edge-eszköz hozzáfér a Storage-fiókhoz. A másik kulcs a tartalékban van tárolva, így a kulcsok rendszeres elforgatása is megtörténik.

Biztonsági okokból számos adatközponthoz kulcsfontosságú rotáció szükséges. Javasoljuk, hogy kövesse az alábbi ajánlott eljárásokat a Key rotációhoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. A fiók kulcsa körültekintően védhető. Ne ossza meg a jelszót más felhasználók számára, vagy jegyezze fel a kódot, vagy mentse a többi felhasználó számára elérhető egyszerű szövegbe.
- Ha úgy gondolja, hogy sérült, akkor a fiók kulcsát a Azure Portal használatával újra létrehozhatja. További információ: a [Storage-fiók elérési kulcsainak kezelése](../articles/storage/common/storage-account-keys-manage.md).
- Az Azure-rendszergazdának rendszeresen módosítania vagy újra kell generálnia az elsődleges vagy másodlagos kulcsot a Azure Portal Storage szakaszának használatával, hogy közvetlenül hozzáférhessen a Storage-fiókhoz.