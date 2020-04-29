---
title: 'Hivatkozás: Data Science Virtual Machine rendszerkép-érvénytelenítés'
titleSuffix: Azure Data Science Virtual Machine
description: Az Azure Data Science Virtual Machinet érintő elavulás részletei
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80754766"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Hivatkozás: a DSVM-lemezképek elavultak

Az alábbiakban megbeszéljük az Azure-Data Science Virtual Machine közelgő elavulás kezelésével kapcsolatos javaslatokat.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: adatlemezek áttelepítése

A Windows 2012 DSVM-rendszerkép támogatását a 2019. december 31-én leállítjuk. Ha a meglévő Windows 2012 DSVM adatlemezét Windows 2016 DSVM szeretné áttelepíteni, hajtsa végre a következő lépéseket:

1. Hozzon létre egy új Windows 2016 DSVM az [itt](./provision-vm.md#create-your-dsvm)látható utasításokat követve.
1. Leválaszthatja a meglévő adatlemezeket a Windows 2012-rendszerképből [ezen utasítások](../../virtual-machines/windows/detach-disk.md)alapján.
1. Csatlakoztassa a lemezt az előző lépésben a Windows 2016-lemezképhez az [alábbi utasítások](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)használatával.

## <a name="centos"></a>CentOS

Az új felhasználóknak a legújabb Ubuntu-vagy Windows-rendszerképeket kell használniuk. A CentOS továbbra is elérhető lesz a meglévő megoldási sablonokkal való használatra.