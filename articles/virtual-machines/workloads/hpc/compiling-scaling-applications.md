---
title: HPC-alkalmazások méretezése - Azure virtuális gépek | Microsoft dokumentumok
description: Ismerje meg, hogyan skálázhat HPC-alkalmazásokat az Azure-beli virtuális gépeken.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707842"
---
# <a name="scaling-hpc-applications"></a>HPC-alkalmazások méretezése

A HPC-alkalmazások azure-beli optimális skálázási és kibővített teljesítményéhez teljesítményhangolásra és optimalizálási kísérletekre van szükség az adott számítási feladathoz. Ez a szakasz és a virtuális gép sorozat-specifikus oldalak általános útmutatást nyújtanak az alkalmazások méretezéséhez.

## <a name="compiling-applications"></a>Alkalmazások összeállítása

Bár nem szükséges, az alkalmazások megfelelő optimalizálási jelzőkkel való összeállítása biztosítja a legjobb méretezési teljesítményt a HB és hc sorozatú virtuális gépeken.

### <a name="amd-optimizing-cc-compiler"></a>AMD c/c++ fordító optimalizálása

Az AMD Optimalizáló C/C++ fordító (AOCC) fordítórendszer magas szintű fejlett optimalizálást, többszálas és processzortámogatást kínál, amely magában foglalja a globális optimalizálást, a vektorizálást, az eljárásközi elemzéseket, a ciklusátalakításokat és a kódgenerálást. Az AOCC fordító binárisai alkalmasak olyan Linux rendszerekhez, amelyek GNU C Library (glibc) 2.17-es vagy újabb verzióval rendelkeznek. A fordító csomag egy C/C++ fordítóból (clang), egy Fortran fordítóból (FLANG) és egy Fortran frontendből áll a Clang (Dragon Egg) számára.

### <a name="clang"></a>Clang (clang)

A Clang egy C, C++ és Objective-C fordító, amely előfeldolgozást, elemzést, optimalizálást, kódgenerálást, összeállítást és csatolást kezel. A Clang `-march=znver1` támogatja a jelzőt, hogy a legjobb kódgenerálást és hangolást tegye lehetővé az AMD Zen alapú x86 architektúrájához.

### <a name="flang"></a>FLANG között

A FLANG fordító az AOCC csomag friss kiegészítése (2018 áprilisában hozzáadva), és jelenleg előzetes kiadásban van a fejlesztők számára a letöltéshez és teszteléshez. A Fortran 2008 alapján az AMD kiterjeszti a FLANG GitHub verzióját (https://github.com/flangcompiler/flang). A FLANG fordító támogatja az összes Clang fordító opciót és egy további számú FLANG-specifikus fordító opciót.

### <a name="dragonegg"></a>Sárkánytojás

DragonEgg egy gcc plugin, amely felváltja a GCC optimalizálók és kód generátorok azokkal a LLVM projekt. DragonEgg, hogy jön az AOCC működik gcc-4.8.x, tesztelték x86-32/x86-64 célokat, és sikeresen használják a különböző Linux platformokon.

A GFortran a Fortran programok tényleges előtér-programja, amely a GCC GIMPLE köztes ábrázolását (IR) generáló előfeldolgozásért, elemzésért és szemantikai elemzésért felelős. DragonEgg egy GNU plugin, dugulás a GFortran összeállítása áramlását. Megvalósítja a GNU plugin API-t. A plugin architektúra, DragonEgg lesz a fordító vezető, vezetői a különböző fázisai összeállítás.  A letöltési és telepítési utasítások követése után a Dragon Egg a következők kel hívható meg: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>OFJ fordító
PGI Közösségi Kiadás ver. 17 megerősítést nyert, hogy működjön együtt AMD EPYC. A STREAM OFJ által lefordított verziója biztosítja a platform teljes memóriasávszélességét. Az újabb Community Edition 18.10 (2018. november) szintén jól működik. Az alábbiakban minta CLI fordító optimálisan az Intel Fordító:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel fordító
Intel Fordító ver. 18 megerősítést nyert, hogy működjön együtt AMD EPYC. Az alábbiakban minta CLI fordító optimálisan az Intel Fordító.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>GCC fordító 
HPC esetén az AMD a GCC 7.3-as vagy újabb fordítóját ajánlja. A régebbi verziók, például az RHEL/CentOS 7.4 részét képező 4.8.5 verziók nem ajánlottak. A GCC 7.3 és az újabb kontüi jelentősen nagyobb teljesítményt nyújtanak a HPL, HPCG és DGEMM teszteken.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Alkalmazások méretezése 

Az alábbi javaslatok az alkalmazások optimális méretezési hatékonyságát, teljesítményét és konzisztenciáját alkalmazzák:

* A folyamatokat a 0-59 magokra rögzítse szekvenciális rögzítési módszerrel (szemben az automatikus egyensúly megközelítésével). 
* A Numa/Core/HwThread általi kötés jobb, mint az alapértelmezett kötés.
* Hibrid párhuzamos alkalmazások (OpenMP+MPI) esetén ccx-enként 4 szálat és 1 MPI-stagot használjon.
* A tiszta MPI alkalmazásokhoz kísérletezzen ccx-enként 1-4 MPI-s tanévenként az optimális teljesítmény érdekében.
* Egyes, a memóriasávszélességre rendkívül érzékeny alkalmazások számára előnyös lehet, ha ccx-enként kevesebb magot használnak. Ezeknél az alkalmazásoknál 3 vagy 2 mag ccx-enként használatával csökkentheti a memória sávszélességének versengését, és nagyobb valós teljesítményt vagy egyenletesebb méretezhetőséget eredményezhet. Az MPI Allreduce különösen előnyös lehet ebből.
* A lényegesen nagyobb léptékű futtatások esetén ud vagy hibrid RC+UD átvitel használata ajánlott. Számos MPI-kódtár/futásidejű kódtár ezt belsőleg (például UCX vagy MVAPICH2) teszi meg. Ellenőrizze az átviteli konfigurációk at nagyszabású futtatások.

## <a name="next-steps"></a>További lépések

További információ az Azure-beli [HPC-ről.](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)
