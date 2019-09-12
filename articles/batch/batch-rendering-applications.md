---
title: Alkalmazások renderelése – Azure Batch
description: Előre telepített batch renderelési alkalmazások
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 133742bf45bddf866d2dfcae97a331ee2a0f84fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639378"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Előre telepített alkalmazások a virtuálisgép-lemezképek megjelenítéséhez

Azure Batch használatával bármilyen renderelési alkalmazás használható. Az Azure Marketplace virtuálisgép-lemezképei azonban előre telepített általános alkalmazásokkal is elérhetők.

Ha alkalmazható, a használaton kívüli licencelés elérhető az előre telepített renderelési alkalmazásokhoz. Batch-készlet létrehozásakor a szükséges alkalmazások megadhatók, és a virtuális gép és az alkalmazások díja percenként történik. Az alkalmazások árai a [Azure batch díjszabási oldalon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)találhatók.

Néhány alkalmazás csak a Windowst támogatja, de a legtöbb Windows és Linux rendszeren is támogatott.

## <a name="applications-on-centos-7-rendering-images"></a>Alkalmazások CentOS 7 renderelési lemezképekben

A következő lista a CentOS 7,6-es verziójú 1.1.5-renderelési lemezképekre vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
* Autodesk Maya I/O 2018 2. frissítés (cut 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold verzió: 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió)
* Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>A legújabb Windows Server 2016-es renderelési rendszerképeket használó alkalmazások

A következő lista a Windows Server 2016-es verziójának 1.3.4-megjelenítési lemezképeit érinti.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 4. frissítés (18.4.0.7622 verzió)
* Autodesk 3ds Max I/O 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold a 3ds Max-hoz (Arnold Version 5.0.2.4) (verzió: 1.2.926)
* Chaos Group V-Ray a Maya 2018 (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018 (3.60.02 verzió)
* Chaos Group V-Ray a Maya 2019 (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2019 (4.10.01 verzió)
* Blender (2.79)

> [!NOTE]
> A Chaos-csoport V-Ray for 3ds Max 2019 (4.10.01 verzió) bevezeti a V-Ray változásait. Az előző verzió (3.60.02-verzió) használatához használja a Windows Server 2016-es verzióját, a 1.3.2 renderelési csomópontokat.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Alkalmazások a korábbi Windows Server 2016 képmegjelenítési lemezképeken

A következő lista a Windows Server 2016-es verziójának 1.3.2-es verziójú renderelési rendszerképeire vonatkozik.

* Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió)
* Autodesk Maya I/O 2018 4. frissítés (18.4.0.7622 verzió)  
* Autodesk 3ds Max I/O 2019 1. frissítés (21.2.0.2219 verzió)
* Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió)
* Autodesk Arnold Maya 2017 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold Maya 2018 (Arnold Version 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold a 3ds Max-hoz (Arnold Version 5.0.2.4) (verzió: 1.2.926)
* Chaos Group V-Ray a Maya 2019 (3.52.03 verzió)
* Chaos Group V-Ray a 3ds Max 2018 (3.60.02 verzió)
* Blender (2.79)

## <a name="next-steps"></a>További lépések

A renderelési virtuálisgép-lemezképek használatához a készlet létrehozásakor meg kell adni őket a készlet konfigurációjában. Tekintse [meg a Batch-készlet képességeit a rendereléshez](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
