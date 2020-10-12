---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7700b1118c4f04643607f44a474338ffdd5c09e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86231029"
---
- A nem támogatja az ultra-lemezeket.
- Nem engedélyezhető, ha a virtuális gépeken/virtuálisgép-méretezési csoportokon a Azure Disk Encryption (vendég-VM-titkosítás BitLocker/VM-visszafejtés használatával) engedélyezve van.
- Azure Disk Encryption nem engedélyezhető olyan lemezeken, amelyeken engedélyezve van az állomás titkosítása.
- A titkosítás engedélyezhető a meglévő virtuálisgép-méretezési csoportokban. Azonban csak a titkosítás engedélyezése után létrehozott új virtuális gépek automatikusan titkosítva lesznek.
- A meglévő virtuális gépeket fel kell osztani és újra le kell osztani a titkosítás érdekében.