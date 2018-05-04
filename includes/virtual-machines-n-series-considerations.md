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
ms.openlocfilehash: 3267f649e360c512a5523ce1d5948719a1969934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* N sorozatú virtuális gépek rendelkezésre állását, lásd: [régiónként rendelkezésre álló termékek](https://azure.microsoft.com/regions/services/).

* N sorozatú virtuális gépek csak a Resource Manager üzembe helyezési modellel is telepíthető.

* N sorozatú virtuális gépek nem egyezik a támogatják a lemezek Azure tárolási típusát. NC és portok HV virtuális gépek csak támogatja a virtuális gépek lemezei, amelyekről által a szabványos lemez tárolási (HDD). NCv2 ND és NCv3 virtuális gépek csak támogatja a virtuális gépek lemezei, amelyekről a prémium szintű lemez tárolási (SSD) által.

* Ha azt szeretné, csak néhány N sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú előfizetés vagy egyéb beszerzési lehetőségek. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szükség lehet a magok kvótája (régiónként) az Azure-előfizetése, és a hálózati vezérlő által, NCv2, NCv3, ND vagy portok HV magok külön kvóta növeléséhez. A kvóta növelését [nyissa meg az online támogatás ügyfélkérés](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátokat eltérhetnek attól függően, hogy az előfizetés kategóriát.




