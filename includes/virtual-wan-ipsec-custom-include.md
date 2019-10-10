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
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168393"
---
Ha egyéni IPsec-házirendekkel dolgozik, vegye figyelembe a következő követelményeket:

* **IKE** – az IKE esetében bármelyik paramétert kiválaszthatja az IKE-titkosításból, továbbá bármely paramétert az IKE-integritásból, valamint a DH csoport bármely paraméterét.
* **IPSec** – az IPSec esetében bármelyik paramétert kiválaszthatja az IPSec-titkosításból, valamint az IPSec integritásának paramétereit, valamint a PFS-t is. Ha az IPsec-titkosításhoz vagy az IPsec-integritáshoz tartozó paraméterek bármelyike GCM, akkor mindkét beállítás paramétereinek GCM kell lennie.

>[!NOTE]
> Az Egyéni IPsec-házirendekkel nincs a válaszadó és a kezdeményező fogalma (az alapértelmezett IPsec-házirendektől eltérően). Mindkét oldal (helyszíni és Azure VPN Gateway) ugyanazokat a beállításokat fogja használni az IKE 1. és 2. fázisában. A IKEv1 és a IKEv2 protokollok egyaránt támogatottak. Az Azure-t csak válaszadóként lehet támogatni.
>

**Elérhető beállítások és paraméterek**

| Beállítás | Paraméterek |
|--- |--- |
| IKE-titkosítás | AES256, AES192, AES128 |
| IKE-integritás | SHA384, SHA256, SHA1 |
| DH-csoport | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec-integritás | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-csoport | PFS24, ECP384, ECP256, PFS2048, PFS2 |
