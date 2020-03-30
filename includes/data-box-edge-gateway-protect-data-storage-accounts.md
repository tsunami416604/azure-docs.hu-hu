---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466826"
---
Az eszköz egy olyan tárfiókkal van társítva, amely az Azure-beli adatok célhelyeként használatos. A tárfiókhoz való hozzáférést az előfizetés és az adott tárfiókhoz társított két 512 bites tárolóhozzáférési kulcs szabályozza.

Az egyik kulcs hitelesítésre szolgál, amikor a Data Box Edge eszköz hozzáfér a tárfiókhoz. A másik kulcs tartalékban van, így rendszeresen elforgathatja a kulcsokat.

Biztonsági okokból számos adatközpont kulcselforgatást igényel. Javasoljuk, hogy kövesse az alábbi gyakorlati tanácsokat a kulcselforgatáshoz:

- A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Gondosan védje fiókkulcsát. Ne ossza szét a jelszót más felhasználóknak, ne kódolja meg, és ne mentse bárhová olyan egyszerű szövegben, amely mások számára elérhető.
- Ha úgy gondolja, hogy az azure-portálon keresztül újragenerálhatja a fiókkulcsot, ha úgy gondolja, hogy az veszélybe kerülhet. További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../articles/storage/common/storage-account-keys-manage.md)
- Az Azure-rendszergazda rendszeresen módosítania kell, vagy újragenerálni az elsődleges vagy másodlagos kulcs az Azure Portal Storage szakaszhasználatával a tárfiók közvetlen eléréséhez.