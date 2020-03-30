---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168373"
---
>[!NOTE]
>Az alapértelmezett szabályzatokkal való munka során az Azure kezdeményezőként és válaszadóként is működhet az IPsec-alagút beállítása során. Az Azure-t csak válaszadóként nem támogatja.
>

### <a name="initiator"></a>Kezdeményező

A következő szakaszok a támogatott szabályzatkombinációkat sorolják fel, ha az Azure az alagút kezdeményezője.

**1. fázis**

* AES_256, SHA1, DH_GROUP_2
* SHA_256 DH_GROUP_2 AES_256
* AES_128, SHA1, DH_GROUP_2
* SHA_256 DH_GROUP_2 AES_128.

**2. fázis**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE

### <a name="responder"></a>Válaszadó

A következő szakaszok a támogatott szabályzatkombinációkat sorolják fel, amikor az Azure a bújtatás válaszadója.

**1. fázis**

* AES_256, SHA1, DH_GROUP_2
* SHA_256 DH_GROUP_2 AES_256
* AES_128, SHA1, DH_GROUP_2
* SHA_256 DH_GROUP_2 AES_128.

**2. fázis**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* SHA_1 PFS_14 AES_256.
* AES_128, SHA_1, PFS_1
* SHA_1 PFS_2 AES_128
* AES_128, SHA_1, PFS_14
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* SHA_1 AES_256 PFS_24
* SHA_256 AES_256 PFS_24
* SHA_256 PFS_NONE AES_128
* AES_128, SHA_256, PFS_1
* PFS_2 SHA_256 AES_128
* AES_128, SHA_256, PFS_14