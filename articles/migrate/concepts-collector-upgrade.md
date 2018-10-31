---
title: Frissítse a az Azure Migrate Collector berendezést |} A Microsoft Docs
description: Az Azure Migrate Collector berendezést verziófrissítések kapcsolatos információkat tartalmazza.
author: musa-57
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: hamusa
services: azure-migrate
ms.openlocfilehash: cd48b824845a0195fc78814a88dd449507c99394
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50241463"
---
# <a name="collector-update-release-history"></a>Gyűjtő frissítés verziókiadások előzményei

Ez a cikk összefoglalja a gyűjtő berendezés a frissítési információk [Azure Migrate](migrate-overview.md).

Az Azure Migrate Collector egy egyszerűsített berendezés, amely az Azure-ba való migrálás előtt értékelés céljából a helyszíni vCenter environment felderítésére szolgál. [További információk](concepts-collector.md).


## <a name="one-time-discovery-upgrade-versions"></a>Felderítés egyszeri felderítés:-verziók frissítésére

### <a name="version-10916-released-on-10292018"></a>Verzió 1.0.9.16 (kiadott 10/29 vagy 2018)

A berendezés beállítása során hibákkal találkoztak PowerCLI hibák javításait tartalmazza. 

Ujjlenyomat-frissítéshez értékek [1.0.9.16 csomag](https://aka.ms/migrate/col/upgrade_9_16)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | d2c53f683b0ec7aaf5ba3d532a7382e1
SHA1 | e5f922a725d81026fa113b0c27da185911942a01
SHA256 | a159063ff508e86b4b3b7b9a42d724262ec0f2315bdba8418bce95d973f80cfc

### <a name="version-10914"></a>Verzió 1.0.9.14

Ujjlenyomat-frissítéshez értékek [1.0.9.14 csomag](https://aka.ms/migrate/col/upgrade_9_14)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | c5bf029e9fac682c6b85078a61c5c79c
SHA1 | af66656951105e42680dfcc3ec3abd3f4da8fdec
SHA256 | 58b685b2707f273aa76f2e1d45f97b0543a8c4d017cd27f0bdb220e6984cc90e

### <a name="version-10913"></a>Verzió 1.0.9.13

Ujjlenyomat-frissítéshez értékek [1.0.9.13 csomag](https://aka.ms/migrate/col/upgrade_9_13)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 739f588fe7fb95ce2a9b6b4d0bf9917e
SHA1 | 9b3365acad038eb1c62ca2b2de1467cb8eed37f6
SHA256 | 7a49fb8286595f39a29085534f29a623ec2edb12a3d76f90c9654b2f69eef87e

### <a name="version-10911"></a>Verzió 1.0.9.11

Ujjlenyomat-frissítéshez értékek [1.0.9.11 csomag](https://aka.ms/migrate/col/upgrade_9_11)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 0e36129ac5383b204720df7a56b95a60
SHA1 | aa422ef6aa6b6f8bc88f27727e80272241de1bdf
SHA256 | 5f76dbbe40c5ccab3502cc1c5f074e4b4bcbf356d3721fd52fb7ff583ff2b68f

### <a name="version-1097"></a>1.0.9.7-es verziója esetében

Ujjlenyomat-frissítéshez értékek [1.0.9.7-es csomag](https://aka.ms/migrate/col/upgrade_9_7)

**Algoritmus** | **Kivonat értéke**
--- | ---
MD5 | 01ccd6bc0281f63f2a672952a2a25363
SHA1 | 3e6c57523a30d5610acdaa14b833c070bffddbff
SHA256 | e3ee031fb2d47b7881cc5b13750fc7df541028e0a1cc038c796789139aa8e1e6

## <a name="continuous-discovery-upgrade-versions"></a>Folyamatos felderítési:-verziók frissítésére

Nincs frissítés a folyamatos felderítési berendezés még nem érhető el.

## <a name="run-an-upgrade"></a>Frissítés

Az OVA újra letöltése nélkül frissítheti a gyűjtő a legújabb verzióra.

1. Az alábbi listában a legújabb frissítési csomag letöltése.
2. Győződjön meg arról, hogy a letöltött gyorsjavítás biztonságos, nyissa meg a rendszergazdai parancsablakot, és futtassa a következő parancsot a ZIP-fájl kivonatának. A létrehozott kivonatnak egyeznie kell az említett verzió elleni kivonat:

    ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```

    Példa: **C:\>CertUtil - HashFile C:\AzureMigrate\CollectorUpdate_release_1.0.9.14.zip SHA256)**
3. Másolja a zip-fájlt a gyűjtőberendezés virtuális Gépet.
4. Kattintson a jobb gombbal a zip-fájl > **összes kibontása**.
5. Kattintson a jobb gombbal a **Setup.ps1** > **Futtatás a PowerShell szolgáltatással**, és kövesse a telepítési utasításokat.


## <a name="next-steps"></a>További lépések

- [További](concepts-collector.md) a gyűjtőberendezés kapcsolatban.
- [Értékelés futtatása](tutorial-assessment-vmware.md) VMware virtuális gépekhez.
