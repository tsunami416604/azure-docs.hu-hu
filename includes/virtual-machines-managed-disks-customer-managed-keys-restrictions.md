---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f45ecf9985f17008ce7f751b21a04deefc285461
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88809825"
---
- Csak a 2 048 bites, a 3 072 bites és a 4 096 bites [szoftverek és HSM RSA-kulcsok](../articles/key-vault/keys/about-keys.md) támogatottak, más kulcsok és méretek nem.
    - A [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -kulcsokhoz **prémium** szintű Azure Key Vault szükséges.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni rendszerképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani, és ugyanahhoz az előfizetéshez kell tartoznia.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani.
- Az ügyfél által felügyelt kulcsokhoz (Azure Key Vaultok, lemez titkosítási készletek, virtuális gépek, lemezek és Pillanatképek) kapcsolódó összes erőforrásnak ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulcsokkal titkosított lemezek, Pillanatképek és lemezképek nem helyezhetők át másik előfizetésbe.
- Az Azure Disk Encryption használatával jelenleg vagy korábban titkosított felügyelt lemezek nem titkosíthatók az ügyfél által felügyelt kulcsokkal.
- A legfeljebb 50 lemezes titkosítási csoportot tud létrehozni régiónként/előfizetéssel.
- Az ügyfél által felügyelt kulcsok megosztott képtárakkal történő használatáról további információt az [előzetes verzió: az ügyfél által felügyelt kulcsok használata a lemezképek titkosításához](../articles/virtual-machines/image-version-encryption.md)című témakörben talál.
