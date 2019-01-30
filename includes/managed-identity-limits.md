---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 7cdd2ce44cfa24b2b6bad2bb45260299bc8eda5f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252370"
---
| Kategória | Korlát |
| --- | --- |
| Felhasználóhoz rendelt felügyelt identitások | <ul><li>Amikor a felhasználó létrehozása hozzárendelt felügyelt identitások, csak alfanumerikus karaktereket (0-9, a – z, A-Z), és a kötőjel (-) támogatottak. Ezenfelül a név nem lehet 24 karakternél hosszabb, hogy a VM-/VMSS-hozzárendelés megfelelően működhessen.</li><li>Felügyelt identitás virtuálisgép-bővítmény használata esetén a támogatott korlátot hozzárendelve a felügyelt identitásokból 32 felhasználói.  Nem felügyelt identitás virtuálisgép-bővítmény a támogatott határértéke 512 a felhasználóhoz hozzárendelt identitás.</li>|

