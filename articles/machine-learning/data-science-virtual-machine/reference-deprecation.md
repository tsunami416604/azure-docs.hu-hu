---
title: 'Hivatkozás: Adatelemzési virtuálisgép-rendszerkép-gyengítés'
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science virtuális gépet érintő eprekations részletei
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754766"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Hivatkozás: A DSVM-képek eprecációja

Az alábbiakban az Azure Data Science virtuális gépen közelgő eprekations kezelésére vonatkozó javaslatokat tárgyaljuk.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: Adatlemezek áttelepítése

2019. december 31-én leállítjuk a Windows 2012 DSVM-lemezkép támogatását. Ahhoz, hogy adatlemezt telepítsa át a meglévő Windows 2012 DSVM rendszerről egy Windows 2016 DSVM rendszerbe, tegye a következő lépéseket:

1. Hozzon létre egy új Windows 2016 DSVM-et az [itt](./provision-vm.md#create-your-dsvm)bemutatott utasításokat követve.
1. A meglévő adatlemezeket az alábbi utasítások nak [megfelelően](../../virtual-machines/windows/detach-disk.md)válassza le a Windows 2012-lemezképről.
1. Az előző lépéslemez csatlakoztatása a Windows 2016-lemezképhez [az alábbi utasítások szerint](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).

## <a name="centos"></a>CentOS

Az új felhasználóknak a legújabb Ubuntu vagy Windows képeket kell használniuk. A CentOS továbbra is elérhető marad a meglévő megoldássablonokkal.