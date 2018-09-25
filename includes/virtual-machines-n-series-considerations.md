---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1a9c4dc5a4d21f8837bde171283cd8a070297674
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47043868"
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* Az N sorozatú virtuális gépek rendelkezésre állásának, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).

* Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben is telepíthető.

* Az N sorozatú virtuális gépek támogatják a lemezeket az Azure Storage típusú térnek el egymástól. NC- és NV-beli virtuális gépek csak támogatja a Virtuálisgép-lemezek által által a Standard Disk Storage (HDD). NCv2, az NCv3, ND és NVv2 virtuális gépek csak támogatja a Virtuálisgép-lemezek által szerint prémium szintű Disk Storage (SSD).

* Ha azt szeretné, több N-sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szüksége lehet a magkvóta növeléséhez (régiónként) az Azure-előfizetésben, és a hálózati vezérlő, NCv2, az NCv3, ND, NV vagy NVv2 magok külön kvótájának növeléséhez. Kérje egy kvótájának növelését, [nyisson meg egy támogatási kérést online](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátok az előfizetés kategória függvénye.




