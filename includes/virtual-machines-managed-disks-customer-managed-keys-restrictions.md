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
ms.openlocfilehash: b660d3a0d49de80ed85cfbdcdf8e28b9828cbf26
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544985"
---
- Csak a 2 048 bites, a 3 072 bites és a 4 096 bites [szoftverek és HSM RSA-kulcsok](../articles/key-vault/keys/about-keys.md) támogatottak, más kulcsok és méretek nem.
    - A [HSM](../articles/key-vault/keys/hsm-protected-keys.md) -kulcsokhoz **prémium** szintű Azure Key Vault szükséges.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított egyéni rendszerképekből létrehozott lemezeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani, és ugyanahhoz az előfizetéshez kell tartoznia.
- A kiszolgálóoldali titkosítással és az ügyfél által felügyelt kulcsokkal titkosított lemezekről létrehozott pillanatképeket ugyanazzal az ügyfél által felügyelt kulcsokkal kell titkosítani.
- Az ügyfél által felügyelt kulcsokhoz (Azure Key Vaultok, lemez titkosítási készletek, virtuális gépek, lemezek és Pillanatképek) kapcsolódó összes erőforrásnak ugyanabban az előfizetésben és régióban kell lennie.
- Az ügyfél által felügyelt kulcsokkal titkosított lemezek, Pillanatképek és lemezképek nem helyezhetők át másik erőforráscsoporthoz és előfizetésbe.
- Az Azure Disk Encryption használatával jelenleg vagy korábban titkosított felügyelt lemezek nem titkosíthatók az ügyfél által felügyelt kulcsokkal.
- A legfeljebb 50 lemezes titkosítási csoportot tud létrehozni régiónként/előfizetéssel.
- Az ügyfél által felügyelt kulcsok megosztott képtárakkal történő használatáról további információt az [előzetes verzió: az ügyfél által felügyelt kulcsok használata a lemezképek titkosításához](../articles/virtual-machines/image-version-encryption.md)című témakörben talál.
