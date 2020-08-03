---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 138e7efd33ee36a77fd56deda155a29017432302
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420285"
---
- Csak egy virtuális hálózat lehet egy lemezes hozzáférési objektumhoz csatolva.
- A virtuális hálózatnak ugyanahhoz az előfizetéshez kell tartoznia, mint a lemez-hozzáférési objektumnak ahhoz, hogy összekapcsolja őket.
- Akár 10 lemez vagy pillanatkép is importálható vagy exportálható ugyanazzal a lemezes hozzáférési objektummal.
- Nem kérhet manuális jóváhagyást egy virtuális hálózat lemezes hozzáférési objektumhoz való csatolásához.
- A különbözeti képesség nem támogatott a lemezes hozzáférési objektummal társított növekményes Pillanatképek esetében.
- A AzCopy nem használható olyan lemez vagy pillanatkép VHD-fájljának letöltésére, amelyet a rendszer a Storage-fiókhoz privát hivatkozásokon keresztül biztosít. A virtuális gépeket azonban a AzCopy használatával is letöltheti.
