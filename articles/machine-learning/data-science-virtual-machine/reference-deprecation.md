---
title: 'Hivatkozás: DSVM-rendszerkép elavulása'
description: Az Azure Data Science Virtual Machinet (DSVM) érintő elavulás részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 252f9c66034dbadaf7a2e9e6f78665c26d414deb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456281"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Hivatkozás: a DSVM-lemezképek elavultak

Az alábbiakban megbeszéljük az Azure-Data Science Virtual Machine közelgő elavulás kezelésével kapcsolatos javaslatokat.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: adatlemezek áttelepítése

A Windows 2012 DSVM-rendszerkép támogatását a 2019. december 31-én leállítjuk. Ha a meglévő Windows 2012 DSVM adatlemezét Windows 2016 DSVM szeretné áttelepíteni, hajtsa végre a következő lépéseket:

1. Hozzon létre egy új Windows 2016 DSVM az [itt](./provision-vm.md#create-your-dsvm)látható utasításokat követve.
1. Leválaszthatja a meglévő adatlemezeket a Windows 2012-rendszerképből [ezen utasítások](../../virtual-machines/windows/detach-disk.md)alapján.
1. Csatlakoztassa a lemezt az előző lépésben a Windows 2016-lemezképhez az [alábbi utasítások](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)használatával.
