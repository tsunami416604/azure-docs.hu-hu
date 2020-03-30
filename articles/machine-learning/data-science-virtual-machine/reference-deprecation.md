---
title: 'Hivatkozás: Adatelemzési virtuálisgép-rendszerkép-gyengítés'
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science virtuális gépet érintő eprekations részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 71ce8519dca6fa71340b1c34fe3a6891ccf5753f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525787"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Hivatkozás: A DSVM-képek eprecációja

Az alábbiakban az Azure Data Science virtuális gépen közelgő eprekations kezelésére vonatkozó javaslatokat tárgyaljuk.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Adatlemezek áttelepítése

2019. december 31-én leállítjuk a Windows 2012 DSVM-lemezkép támogatását. Ahhoz, hogy adatlemezt telepítsa át a meglévő Windows 2012 DSVM rendszerről egy Windows 2016 DSVM rendszerbe, tegye a következő lépéseket:

1. Hozzon létre egy új Windows 2016 DSVM-et az [itt](./provision-vm.md#create-your-dsvm)bemutatott utasításokat követve.
1. A meglévő adatlemezeket az alábbi utasítások nak [megfelelően](../../virtual-machines/windows/detach-disk.md)válassza le a Windows 2012-lemezképről.
1. Az előző lépéslemez csatlakoztatása a Windows 2016-lemezképhez [az alábbi utasítások szerint](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
