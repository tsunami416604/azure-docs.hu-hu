---
title: Az Azure HPC cache előzetes adatgyűjtése – párhuzamos másolási parancsfájl
description: Egy párhuzamos másolási parancsfájl használata az Azure HPC cache-ben lévő blob Storage-tárolóba való áthelyezéshez
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 852b4e692a4316c7701c8c179039104bee561949
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180951"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Azure HPC cache (előzetes verzió) – adatfeldolgozás – párhuzamos másolási parancsfájl metódusa

Ez a cikk útmutatást nyújt a ``parallelcp`` parancsfájl létrehozásához és a használatával az Azure HPC cache-vel való használatra szolgáló blob Storage-tárolóba való áthelyezéséhez.

Ha többet szeretne megtudni arról, hogy az Azure HPC gyorsítótára hogyan helyezi át az adatátvitelt a blob Storage-ba, olvassa el az [adatáthelyezés Azure-beli blob Storage](hpc-cache-ingest.md)-ba Azure

## <a name="create-the-parallelcp-script"></a>A parallelcp parancsfájl létrehozása

Az alábbi szkript hozzáadja a végrehajtható fájlt `parallelcp`. (Ezt a szkriptet Ubuntu-re tervezték, ha más disztribúciót használ ``parallel`` , külön kell telepítenie.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Példa párhuzamos másolásra

Ez a példa a párhuzamos másolási parancsfájl ``glibc`` használatával fordítja le a forrásfájlokat az Azure HPC cache-ben.

A forrásfájlok az Azure HPC cache csatlakoztatási pontján vannak gyorsítótárazva, és az objektum fájljai a helyi merevlemezen vannak tárolva.

Ebben a példában a párhuzamos másolási parancsfájlt használja ``-j`` a ``make`` kapcsolóval, és párhuzamos nyerhet.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
