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
ms.openlocfilehash: 1de7221f100077e07a2211bdb94e0198b35cb77c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179080"
---
## <a name="deployment-considerations"></a>Telepítési szempontok

* Az N sorozatú virtuális gépek rendelkezésre állásának, lásd: [elérhető termékek régiók szerint](https://azure.microsoft.com/regions/services/).

* Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben is telepíthető.

* Az N sorozatú virtuális gépek támogatják a lemezeket az Azure Storage típusú térnek el egymástól. NC- és NV-beli virtuális gépek csak támogatja a Virtuálisgép-lemezek által által a Standard Disk Storage (HDD). NCv2, az NCv3, ND, NDv2 és NVv2 virtuális gépek csak támogatja a prémium szintű Disk Storage (SSD) szerint biztonsági Virtuálisgép-lemezek.

* Ha azt szeretné, több N-sorozatú virtuális gépek telepítéséhez, fontolja meg a használatalapú fizetéses előfizetésre, vagy egyéb fizetési lehetőségeket. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

* Szüksége lehet a magkvóta növeléséhez (régiónként) az Azure-előfizetésben, és a hálózati vezérlő, NCv2, az NCv3, ND, NDv2, NV vagy NVv2 magok külön kvótájának növeléséhez. Kérje egy kvótájának növelését, [nyisson meg egy támogatási kérést online](../articles/azure-supportability/how-to-create-azure-support-request.md) díjmentesen. Alapértelmezett korlátok az előfizetés kategória függvénye.




