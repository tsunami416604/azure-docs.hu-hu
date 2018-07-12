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
ms.openlocfilehash: 4bec8c8ea29c10b8c0d0351a41ebc9183bb45d4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944217"
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* Az N sorozatú virtuális gépek rendelkezésre állásának, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).

* Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben is telepíthető.

* Az N sorozatú virtuális gépek támogatják a lemezeket az Azure Storage típusú térnek el egymástól. NC- és NV-beli virtuális gépek csak támogatja a Virtuálisgép-lemezek által által a Standard Disk Storage (HDD). NCv2, ND és NCv3 virtuális gépek csak támogatja a Virtuálisgép-lemezek által szerint prémium szintű Disk Storage (SSD).

* Ha azt szeretné, több N-sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szüksége lehet a magkvóta növeléséhez (régiónként) az Azure-előfizetésben, és a hálózati vezérlő, NCv2, az NCv3, ND vagy NV magok külön kvótájának növeléséhez. Kérje egy kvótájának növelését, [nyisson meg egy támogatási kérést online](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátok az előfizetés kategória függvénye.




