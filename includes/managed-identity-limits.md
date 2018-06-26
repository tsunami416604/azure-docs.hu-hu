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
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753965"
---
| Kategória | Korlát |
| --- | --- |
| Kezelt identitások hozzárendelt felhasználó | <ul><li>Amikor a felhasználó létrehozása hozzárendelt kezelt identitások, csak alfanumerikus karaktereket (0-9, a – z, A-Z) és a kötőjel (-) támogatottak. Emellett a név korlátozni kell 24 karakter hosszú, a hozzárendelés VM/VMSS megfelelően működjön.</li><li>Ha használja a felügyelt identitás virtuálisgép-bővítmény, a támogatott határon 32 felhasználó lehet hozzárendelve kezelt identitások.  Nélkül felügyelt identitás virtuálisgép-bővítmény a támogatott határon 512 felhasználói identitások rendelve.</li>|

