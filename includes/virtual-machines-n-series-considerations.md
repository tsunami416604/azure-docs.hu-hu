---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 14f5998ee1c562b649257f7dce9ffc2f52a66226
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174961"
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* Az N sorozatú virtuális gépek elérhetőségét lásd: [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/).

* Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben helyezhetők üzembe.

* Az N sorozatú virtuális gépek különböznek a lemezük által támogatott Azure Storage-típustól. Az NC-és NV-alapú virtuális gépek csak a standard Disk Storage (HDD) által támogatott VM-lemezeket támogatják. A NCv2, a NCv3, az ND, a NDv2 és a NVv2 virtuális gépek csak a Premium Disk Storage (SSD) által támogatott virtuálisgép-lemezeket támogatják.

* Ha több N sorozatú virtuális gépet szeretne üzembe helyezni, vegye fontolóra az utólagos elszámolású előfizetést vagy más vásárlási lehetőséget. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Előfordulhat, hogy az Azure-előfizetésében meg kell emelni a magok kvótáját (régiónként), és meg kell emelni az NC, a NCv2, a NCv3, az ND, a NDv2, az NV vagy a NVv2 magok külön kvótáját. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.




