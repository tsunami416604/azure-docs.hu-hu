---
title: fájlbefoglalás
description: fájlbefoglalás
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377465"
---
- Csak egy virtuális hálózat lehet egy lemezes hozzáférési objektumhoz csatolva.
- A virtuális hálózatnak ugyanahhoz az előfizetéshez kell tartoznia, mint a lemez-hozzáférési objektumnak ahhoz, hogy összekapcsolja őket.
- Akár 10 lemez vagy pillanatkép is importálható vagy exportálható ugyanazzal a lemezes hozzáférési objektummal.
- Nem kérhet manuális jóváhagyást egy virtuális hálózat lemezes hozzáférési objektumhoz való csatolásához.
- A növekményes pillanatképeket nem lehet exportálni, ha lemezes hozzáférési objektummal vannak társítva.
- A AzCopy nem használható olyan lemez vagy pillanatkép VHD-fájljának letöltésére, amelyet a rendszer a Storage-fiókhoz privát hivatkozásokon keresztül biztosít. A virtuális gépeket azonban a AzCopy használatával is letöltheti.
