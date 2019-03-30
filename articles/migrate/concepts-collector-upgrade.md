---
title: Frissítse a az Azure Migrate Collector berendezést |} A Microsoft Docs
description: Az Azure Migrate Collector berendezést verziófrissítések kapcsolatos információkat tartalmazza.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: 7cd44318716200d665ece9ffecc45225bdfb85eb
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648673"
---
# <a name="collector-appliance-updates"></a>Gyűjtő berendezés frissítések

Ez a cikk összefoglalja a gyűjtő berendezés a frissítési információk [Azure Migrate](migrate-overview.md).

Az Azure Migrate Collector egy egyszerűsített berendezés, amely az Azure-ba való migrálás előtt értékelés céljából a helyszíni vCenter environment felderítésére szolgál. [További információk](concepts-collector.md).

## <a name="how-to-upgrade-the-appliance"></a>A berendezés frissítése

Az OVA újra letöltése nélkül frissítheti a gyűjtő a legújabb verzióra.

1. Zárja be az összes böngészőablakot, és bármilyen nyissa meg a fájlok és mappák az a készülék.
2. Töltse le a legújabb frissítési csomagot az alábbi cikkben említett frissítések listája.
3. Győződjön meg arról, hogy a letöltött csomag biztonságos, nyissa meg a rendszergazdai parancsablakot, és futtassa a következő parancsot a ZIP-fájl kivonatának. A létrehozott kivonatnak egyeznie kell az említett verzió elleni kivonat:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Példa: **C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
4. Másolja a zip-fájlt a gyűjtőberendezés virtuális Gépet.
5. Kattintson a jobb gombbal a zip-fájl > **összes kibontása**.
6. Kattintson a jobb gombbal a **Setup.ps1** > **Futtatás a PowerShell szolgáltatással**, és kövesse a telepítési utasításokat.

## <a name="collector-update-release-history"></a>Gyűjtő frissítés verziókiadások előzményei

### <a name="continuous-discovery-upgrade-versions"></a>Folyamatos felderítési: Frissítési verziók

#### <a name="version-101014-released-on-03292019"></a>Verzió: (03/29 vagy verzióját 2019 kiadott) 1.0.10.14

Néhány felhasználói felületi fejlesztéseket tartalmaz.

Ujjlenyomat-frissítéshez értékek [1.0.10.14 csomag](https://aka.ms/migrate/col/upgrade_10_14)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 846b1eb29ef2806bcf388d10519d78e6
SHA1 | 6243239fa49c6b3f5305f77e9fd4426a392d33a0
SHA256 | fb058205c945a83cc4a31842b9377428ff79b08247f3fb8bb4ff30c125aa47ad

#### <a name="version-101012-released-on-03132019"></a>Verzió: (03/13/verzióját 2019 kiadott) 1.0.10.12

Hibák javításait tartalmazza a készülék a felhő kiválasztása Azure.

Ujjlenyomat-frissítéshez értékek [1.0.10.12 csomag](https://aka.ms/migrate/col/upgrade_10_12)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 27704154082344c058238000dff9ae44
SHA1 | 41e9e2fb71a8dac14d64f91f0fd780e0d606785e
SHA256 | c6e7504fcda46908b636bfe25b8c73f067e3465b748f77e50027e66f2727c2a9

### <a name="one-time-discovery-deprecated-now-previous-upgrade-versions"></a>Egyszeri felderítés (most már elavult): Korábbi frissítési verziók

> [!NOTE]
> A felderítés egyszeri felderítés berendezés elavulttá vált, ez a módszer támaszkodtak a vCenter Server statisztikai beállításait teljesítmény adatok pont rendelkezésre állását és virtuális gépek áttelepítése az Azure-ba való korrigáljuk méretezésének eredményezett átlagos teljesítményszámlálók gyűjtése.

#### <a name="version-10916-released-on-10292018"></a>Verzió 1.0.9.16 (kiadott 10/29 vagy 2018)

A berendezés beállítása során hibákkal találkoztak PowerCLI hibák javításait tartalmazza.

Ujjlenyomat-frissítéshez értékek [1.0.9.16 csomag](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

#### <a name="version-10914"></a>Verzió 1.0.9.14

Ujjlenyomat-frissítéshez értékek [1.0.9.14 csomag](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

#### <a name="version-10913"></a>Verzió 1.0.9.13

Ujjlenyomat-frissítéshez értékek [1.0.9.13 csomag](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e


## <a name="next-steps"></a>További lépések

- [További](concepts-collector.md) a gyűjtőberendezés kapcsolatban.
- [Értékelés futtatása](tutorial-assessment-vmware.md) VMware virtuális gépekhez.
