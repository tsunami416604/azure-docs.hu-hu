---
title: Alkalmazások – Azure Batch Rendering
description: Előre telepített Batch renderelő alkalmazásokkal
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496020"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>A renderelési Virtuálisgép-rendszerképek előre telepített alkalmazásokkal

Az Azure Batch renderelési alkalmazások használata lehetőség. Azonban az Azure piactér Virtuálisgép-rendszerképek érhetők el az előre telepített gyakran használt alkalmazásokba.

Ha vannak ilyenek, használatalapú fizetés licencelési érhető el az előre telepített renderelő alkalmazásokkal. Batch-készlet létrehozásakor adható meg a szükséges alkalmazásokat, és a virtuális gép és az alkalmazások mindkét költségeit számlázása percenként. Alkalmazás árak szerepel a [Azure Batch díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Egyes alkalmazások csak a Windows támogatják, de a legtöbb Windows és Linux rendszereken egyaránt támogatottak.

## <a name="applications-on-centos-7-rendering-images"></a>Alkalmazások CentOS 7 renderelési képeken

* Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
* Autodesk Maya I/O 2018 Update 2 (Kivágás 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold verzió: 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió)
* Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>A legújabb Windows Server 2016 renderelési lemezképekre alkalmazások

Az alábbi lista a Windows Server 2016 verziót 1.3.4 renderelési lemezképek vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 verzió)
* Autodesk 3ds Max i/o 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 verzió) MtoA-3.1.0.1 – 2017
* Autodesk Arnold for Maya 2018-as (Arnold verzió 5.2.0.1) MtoA-3.1.0.1 – 2018
* Autodesk Arnold for 3ds Max (Arnold verzió 5.0.2.4)(version 1.2.926)
* A Chaos Group V-Ray for Maya (3.52.03-as verzió)-2018.
* A Chaos Group V-Ray for 3ds Max 2018-as (3.60.02-es verzió)
* A Chaos Group V-Ray for Maya 2019 (3.52.03-as verzió)
* A Chaos Group V-Ray for 3ds Max 2019 (4.10.01 verzió)
* Blender (2.79)

> [!NOTE]
> A Chaos Group V-Ray for 3ds Max 2019 (verzió 4.10.01) V-Ray kompatibilitástörő változásokat vezet be. Az előző verzió (3.60.02-es verzió) használatához a Windows Server 2016 verziót 1.3.2 renderelési csomópontok.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>A korábbi Windows Server 2016 renderelési lemezképekre alkalmazások

Az alábbi lista a Windows Server 2016 verziót 1.3.2 renderelési lemezképek vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 Update 4 (18.4.0.7622 verzió)  
* Autodesk 3ds Max i/o 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold for Maya 2017 (Arnold 5.2.0.1 verzió) MtoA-3.1.0.1 – 2017
* Autodesk Arnold for Maya 2018-as (Arnold verzió 5.2.0.1) MtoA-3.1.0.1 – 2018
* Autodesk Arnold for 3ds Max (Arnold verzió 5.0.2.4)(version 1.2.926)
* A Chaos Group V-Ray for Maya 2019 (3.52.03-as verzió)
* A Chaos Group V-Ray for 3ds Max 2018-as (3.60.02-es verzió)
* Blender (2.79)

## <a name="next-steps"></a>További lépések

A készletkonfiguráció megadását egy készlet létrehozásakor; szükségük a renderelési Virtuálisgép-rendszerképek használatához Tekintse meg a [Batch-készlet renderelési képességeket](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).