---
title: A Batch renderelő alkalmazásokkal
description: Előre telepített Batch renderelő alkalmazásokkal
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 28acd1b7275694d38a52f14d2b2c32b79cc8183e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034782"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>A renderelési Virtuálisgép-rendszerképek előre telepített alkalmazásokkal

Az Azure Batch renderelési alkalmazások használata lehetőség. Azonban az Azure piactér Virtuálisgép-rendszerképek érhetők el az előre telepített gyakran használt alkalmazásokba.

Ha vannak ilyenek, használatalapú fizetés licencelési érhető el az előre telepített renderelő alkalmazásokkal. Batch-készlet létrehozásakor adható meg a szükséges alkalmazásokat, és a virtuális gép és az alkalmazások mindkét költségeit számlázása percenként. Alkalmazás árak szerepel a [Azure Batch díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Egyes alkalmazások csak a Windows támogatják, de a legtöbb Windows és Linux rendszereken egyaránt támogatottak.

## <a name="applications-on-centos-7-rendering-nodes"></a>CentOS 7 renderelési csomópontokon alkalmazások

* Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
* Autodesk Maya I/O 2018 Update 2 (Kivágás 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold verzió: 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió)
* Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió)
* Blender (2.68)

## <a name="applications-on-windows-server-2016-rendering-nodes"></a>Alkalmazások a Windows Server 2016 renderelési csomópontokon

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 Update 3 (18.3.0.7040 verzió)  
* Autodesk 3ds Max i/o 2019 1. frissítés (21.10.1314 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold for Maya (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya (Arnold verzió: 5.0.1.4) MtoA-2.0.2.3-2018
* Autodesk Arnold for 3ds Max (Arnold verzió 5.0.2.4)(version 1.2.926)
* Chaos Group V-Ray for Maya (3.52.03-as verzió)
* Chaos Group V-Ray for 3ds Max (3.60.02-es verzió)
* Blender (2.79)

## <a name="next-steps"></a>További lépések

A készletkonfiguráció megadását egy készlet létrehozásakor; szükségük a renderelési Virtuálisgép-rendszerképek használatához Tekintse meg a [Batch-készlet renderelési képességeket](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).