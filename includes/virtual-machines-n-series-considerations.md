---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: c1e57ea28f597293d8bb52207bbbb76892b1d5f5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* N sorozatú virtuális gépek rendelkezésre állását, lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/en-us/regions/services/).

* N sorozatú virtuális gépek csak a Resource Manager üzembe helyezési modellel is telepíthető.

* N sorozatú virtuális gépek nem egyezik a támogatják a lemezek Azure tárolási típusát. NC és portok HV virtuális gépek csak támogatja a virtuális gépek lemezei, amelyekről által a szabványos lemez tárolási (HDD). NCv2 ND és NCv3 virtuális gépek csak támogatja a virtuális gépek lemezei, amelyekről a prémium szintű lemez tárolási (SSD) által.

* Ha azt szeretné, csak néhány N sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú előfizetés vagy egyéb beszerzési lehetőségek. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szükség lehet a magok kvótája (régiónként) az Azure-előfizetése, és a hálózati vezérlő által, NCv2, NCv3, ND vagy portok HV magok külön kvóta növeléséhez. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátokat eltérhetnek attól függően, hogy az előfizetés kategóriát.




