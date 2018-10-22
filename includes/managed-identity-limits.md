---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: daveba
ms.service: active-directory
ms.component: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 55814f5515649e0fc6d646d3580f281fd9ec37a5
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49634656"
---
| Kategória | Korlát |
| --- | --- |
| Felhasználóhoz rendelt felügyelt identitások | <ul><li>Amikor a felhasználó létrehozása hozzárendelt felügyelt identitások, csak alfanumerikus karaktereket (0-9, a – z, A-Z), és a kötőjel (-) támogatottak. Ezenfelül a név nem lehet 24 karakternél hosszabb, hogy a VM-/VMSS-hozzárendelés megfelelően működhessen.</li><li>Felügyelt identitás virtuálisgép-bővítmény használata esetén a támogatott korlátot hozzárendelve a felügyelt identitásokból 32 felhasználói.  Nem felügyelt identitás virtuálisgép-bővítmény a támogatott határértéke 512 a felhasználóhoz hozzárendelt identitás.</li>|

