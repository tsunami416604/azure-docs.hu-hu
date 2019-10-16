---
title: 'Hivatkozás: DSVM-rendszerkép elavulása'
description: Az Azure Data Science Virtual Machinet (DSVM) érintő elavulás részletei
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 10/14/2019
ms.topic: reference
ms.openlocfilehash: 4968ac08b75141ed84994ca287215a34728232c4
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333338"
---
# <a name="reference-deprecation-of-dsvm-images"></a>Hivatkozás: a DSVM-lemezképek elavultak

Az alábbiakban megbeszéljük az Azure-Data Science Virtual Machine közelgő elavulás kezelésével kapcsolatos javaslatokat.

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: adatlemezek áttelepítése

A Windows 2012 DSVM rendszerképet a 2019. november 5-én fogjuk támogatni. Ha a meglévő Windows 2012 DSVM adatlemezét Windows 2016 DSVM szeretné áttelepíteni, hajtsa végre a következő lépéseket:

1. Hozzon létre egy új Windows 2016 DSVM az [itt](./provision-vm.md#create-your-dsvm)látható utasításokat követve.
1. Leválaszthatja a meglévő adatlemezeket a Windows 2012-rendszerképből [ezen utasítások](../../virtual-machines/windows/detach-disk.md)alapján.
1. Csatlakoztassa a lemezt az előző lépésben a Windows 2016-lemezképhez az [alábbi utasítások](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)használatával.
