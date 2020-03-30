---
title: Renderelési alkalmazások – Azure Batch
description: Az Azure Batch-el bármilyen renderelési alkalmazást használhat. Azonban az Azure Marketplace virtuális gép lemezképek érhetők el a közös alkalmazások előre telepített.
services: batch
ms.service: batch
author: LauraBrenner
ms.author: labrenne
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: 77672534b2aad993a44e9b637fbed58df8610e97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022987"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Előre telepített alkalmazások a virtuálisgép-lemezképek renderelésén

Az Azure Batch-el bármilyen renderelési alkalmazást használhat. Azonban az Azure Marketplace virtuális gép lemezképek érhetők el a közös alkalmazások előre telepített.

Adott esetben az előre telepített renderelési alkalmazások esetében használatalapú licencelés áll rendelkezésre. Kötegkészlet létrehozásakor a szükséges alkalmazások megadhatók, és mind a virtuális gép, mind az alkalmazások költsége percenként kerül számlázásra. Az alkalmazásárak az [Azure Batch díjszabási oldalán](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)jelennek meg.

Egyes alkalmazások csak a Windows rendszert támogatják, de a legtöbb a Windows és a Linux operációs rendszert is támogatja.

## <a name="applications-on-centos-7-rendering-images"></a>Alkalmazások a CentOS 7 renderelési képek

Az alábbi lista a CentOS 7.6 1.1.6-os verziójú renderelési képére vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
* Autodesk Maya I/O 2018 2- es frissítés (201711281015 vágás)
* Az Autodesk Maya I/O 2019 1.
* Autodesk Arnold a Maya 2017-hez (Arnold 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold a Maya 2018-hoz (Arnold 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold a Maya 2019-hez (Arnold 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió)
* Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió)
* Blender (2.68)
* Turmixgép (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Alkalmazások a windows Server 2016 legújabb renderelési lemezképein

Az alábbi lista a Windows Server 2016 1.3.8-as verziójának renderelési lemezképeire vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 6.
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk 3ds Max I/O 2019 1.
* Autodesk 3ds Max I/O 2020 2.
* Autodesk Arnold a Maya 2017-hez (Arnold 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold a Maya 2018-hoz (Arnold 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold a Maya 2019-hez (Arnold 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold a 3ds Max 2018-hoz (Arnold 5.3.0.2-es verzió) (1.2.926 verzió)
* Autodesk Arnold a 3ds Max 2019-hez (Arnold 5.3.0.2-es verzió) (1.2.926 verzió)
* Autodesk Arnold a 3ds Max 2020-hoz (Arnold 5.3.0.2-es verzió) (1.2.926 verzió)
* Chaos Group V-Ray a 2017-es Maya számára (4.12.01-es verzió)
* Chaos Group V-Ray a 2018-as Maya számára (4.12.01 verzió)
* Chaos Group V-Ray a 2019-es Maya számára (4.04.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018-hoz (4.20.01 verzió)
* Chaos Group V-Ray a 3ds Max 2019-hez (4.20.01 verzió)
* Chaos Group V-Ray a 3ds Max 2020-hoz (4.20.01 verzió)
* Blender (2.79)
* Turmixgép (2,80)
* AZ 10

> [!IMPORTANT]
> V-ray futtatásához maya kívül az [Azure Batch-bővítmény sablonok,](https://github.com/Azure/batch-extension-templates)indítsa el `vrayses.exe` a render futtatása előtt. A vrayses.exe program sablonokon kívüli elindításához használja `%MAYA_2017%\vray\bin\vrayses.exe"`a következő parancsot.
>
> Például tekintse meg a Maja és a [V-Ray sablon](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) kezdő feladatát a GitHubon.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Alkalmazások a korábbi Windows Server 2016 renderelési képeken

Az alábbi lista a Windows Server 2016 1.3.7-es verziójának renderelési lemezképeire vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 4.
* Autodesk 3ds Max I/O 2019 1.
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold a Maya 2017-hez (Arnold 5.2.0.1- MtoA-3.1.0.1-2017
* Autodesk Arnold a Maya 2018-hoz (Arnold 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold a 3ds Max 2018-hoz (Arnold 5.0.2.4-es verzió) (1.2.926 verzió)
* Autodesk Arnold a 3ds Max 2019-hez (Arnold 5.0.2.4-es verzió) (1.2.926 verzió)
* Chaos Group V-Ray a 2018-as Maya számára (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018-hoz (3.60.02 verzió)
* Chaos Group V-Ray a Maya 2019-hez (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2019-hez (4.10.01 verzió)
* Blender (2.79)

> [!NOTE]
> Chaos Group V-Ray for 3ds Max 2019 (version 4.10.01) bemutatja a V-ray törési módosításait. Az előző verzió (3.60.02-es verzió) használatához használja a Windows Server 2016 1.3.2-es verziójú renderelő csomópontjait.

## <a name="next-steps"></a>További lépések

A renderelési virtuálisgép-lemezképek használatához meg kell adni őket a készlet konfigurációjában a készlet létrehozásakor; lásd a [Kötegkészlet renderelési lehetőségeit.](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools)
