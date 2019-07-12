---
title: HPC-alkalmazások – Azure virtuális gépek méretezése |} A Microsoft Docs
description: Ismerje meg, hogyan skálázhatja a HPC-alkalmazások Azure-beli virtuális gépeken.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707842"
---
# <a name="scaling-hpc-applications"></a>HPC-alkalmazások méretezése

Optimális vertikális és horizontális felskálázás az Azure-ban a HPC-alkalmazások teljesítményének igényel, teljesítményhangolás, és optimalizálási kísérleteket adott számítási feladatra. Ebben a szakaszban, és a virtuális gép adatsorozat-hez kapcsolódó oldallal kínálnak az alkalmazások méretezését általános útmutatást.

## <a name="compiling-applications"></a>Alkalmazások összeállítására

Bár nem szükséges, megfelelő optimalizálási jelzővel alkalmazások összeállítására nyújt a legjobb teljesítmény vertikális felskálázás érdekében HB és a hibrid kapcsolat-sorozat virtuális gépei.

### <a name="amd-optimizing-cc-compiler"></a>C optimalizálása AMD /C++ fordító

AMD optimalizálása C /C++ fordító (AOCC) fordítási rendszer biztosít magas szintű speciális optimalizálások, többszálas, és a processzor-támogatás, amely tartalmazza a globális optimalizálás, vektorizációt, közötti eljárási elemzések, hurok átalakítások, és generování kódu. AOCC fordító bináris fájljait a megfelelő Linux rendszerek, amelyek GNU C-kódtár (glibc) 2.17 verzió vagy újabb. A fordítási csomag áll a C /C++ fordító (clang), a Fortran fordító (FLANG) és a egy Fortran előtér Clang (Dragon tojás).

### <a name="clang"></a>Clang

Clang a C C++, és az Objective-C nyelvű fordítón belülre előfeldolgozása, elemzés, optimalizálás, generování kódu, szerelvény kezelése és csatolása. Clang támogatja a `-march=znver1` legjobb engedélyező jelölőre code és a hangolási AMD a Zen x86-alapú architektúra.

### <a name="flang"></a>FLANG

A FLANG fordító a közelmúltban lett hozzáadva az AOCC Suite (2018 április hozzáadva), és jelenleg kiadás előtti letöltheti és tesztelheti a fejlesztők számára. Fortran 2008 alapján, AMD kibővíti a GitHub-verziójában FLANG (https://github.com/flangcompiler/flang). A FLANG fordító összes Clang fordítóprogram kapcsolói és a egy további száma FLANG-specifikus fordítóprogram kapcsolói támogatja.

### <a name="dragonegg"></a>DragonEgg

DragonEgg egy gcc beépülő modult, amely a GCC optimalizálókat és kód generátorok lecseréli azokat a LLVM projektből. DragonEgg gcc-4.8.x, AOCC együttműködik az x86-32/x86-64 cél tesztelve lett, és sikeresen használták a különféle Linux-platformokon.

GFortran Fortran programok előfeldolgozása, elemzés és a GCC GIMPLE köztes reprezentáció (IR) létrehozása Szemantikai felelős a tényleges előtérbeli. DragonEgg egy GNU beépülő modul csatlakoztatása GFortran összeállítása flow-bA. A GNU API beépülő modul valósítja meg. A beépülő modul architektúrával az DragonEgg válik a fordító illesztőprogram vezetési fordítási különböző szakaszaiban.  Utasítások végrehajtása után a letöltés és telepítés, a Dragon tojás használatával lehet meghívni őket: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>OFJ fordító
OFJ Community Edition ver. 17 megerősítik AMD EPYC dolgozhat. STREAM OFJ lefordított verzióját a platform teljes memória sávszélesség biztosításához. Az újabb Community Edition 18.10 (2018. november) hasonlóképpen megfelelően kell működjön. Alább a következő fordítóprogram optimális a az Intel fordító CLI minta:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Intel Compiler ver. 18 megerősítik AMD EPYC dolgozhat. Alább optimális a az Intel fordító fordító CLI minta.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC Compiler 
HPC AMD javasol a GCC-fordító 7.3-as vagy újabb. Régebbi verziók, például az RHEL/CentOS 7.4, mellékelt 4.8.5 használata nem ajánlott. GCC 7.3-as és újabb, HPL HPCG és DGEMM tesztek alapján jelentősen nagyobb teljesítményt nyújt.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Alkalmazások méretezése 

A következő javaslatok alkalmazása az alkalmazások optimális méretezése a hatékonyságot, teljesítményt és konzisztencia:

* PIN-kód magok 0-59 használata egy szekvenciális rögzítés megközelítést (azaz nem automatikus kiegyenlítése megközelítés) dolgozza fel. 
* Kötés által Numa/Core/HwThread jobb, mint alapértelmezett kötést.
* Hibrid párhuzamos alkalmazások (OpenMP + MPI) használjon 4 a szálak és a CCX száma 1 MPI rang.
* Tiszta MPI-alkalmazások kísérletezhet az optimális teljesítmény érdekében kiszolgálónként CCX ranks MPI 1 – 4.
* Néhány alkalmazás az érzékenységi memória sávszélesség részesülhetnek magok száma CCX csökkentett számos használatával. Ezekhez az alkalmazásokhoz 3 vagy 2 magok száma CCX előfordulhat, hogy memória sávszélesség versengés és eddig is számtalan előnyét valós nagyobb teljesítményt vagy egységes méretezhetőség. Különösen MPI Allreduce előnyösebb lehet ez.
* Jelentősen nagyobb méretezés futtatások UD vagy hibrid RC + UD átvitelek használata ajánlott. Számos MPI-kódtárak és futásidő-kódtár ehhez belsőleg (például UCX vagy MVAPICH2). Ellenőrizze a nagy méretű futtatások átviteli konfigurációk.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
