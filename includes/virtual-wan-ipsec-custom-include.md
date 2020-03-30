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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168393"
---
Ha egyéni IPsec-házirendekkel dolgozik, tartsa szem előtt az alábbi követelményeket:

* **IKE** - Az IKE-hez bármelyik paramétert kiválaszthatja az IKE titkosításból, valamint az IKE sértetlenség bármely paraméterét, valamint a DH-csoport bármely paraméterét.
* **IPsec** - Az IPsec, kiválaszthatja bármely paraméter iPsec titkosítás, valamint bármely paraméter iPsec integrity, plusz PFS. Ha az IPsec-titkosítás vagy az IPsec-integritás bármely paramétere GCM, akkor mindkét beállítás paramétereinek GCM-nek kell lenniük.

>[!NOTE]
> Az egyéni IPsec-házirendek esetén nincs fogalma a válaszadó és a kezdeményező (ellentétben az alapértelmezett IPsec-házirendek). Mindkét oldalon (a helyszíni és az Azure VPN-átjáró) ugyanazokat a beállításokat fogja használni az IKE 1. Mind az IKEv1, mind az IKEv2 protokoll támogatott. Az Azure-t csak válaszadóként nem támogatja.
>

**Elérhető beállítások és paraméterek**

| Beállítás | Paraméterek |
|--- |--- |
| IKE titkosítás | AES256, AES192, AES128 |
| Az INTERNETES kulcscsere integritása | SHA384, SHA256, SHA1 |
| DH-csoport | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-titkosítás | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec-integritás | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-csoport | PFS24, ECP384, ECP256, PFS2048, PFS2 |
