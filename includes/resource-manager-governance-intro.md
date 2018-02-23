---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 001bdf20f1d8756e63f15c68141aa415c000070e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
Erőforrások az Azure-ba való telepítésekor, hogy rengeteg rugalmasságot Amikor eldönti, milyen típusú erőforrásokkal telepíteni, hol és állítsa be őket. Azonban, hogy rugalmasan lehet, hogy nyissa meg a további beállítások, mint a szervezet lehetővé szeretné. Az Azure-bA erőforrásokat üzembe helyezi meghatározásához vegye figyelembe, talán kíváncsi:

* Hogyan felelnek meg a törvény írja elő az adatok közös joghatóság alá, az egyes országokban?
* Hogyan kapcsolatos költségek szabályozását?
* Hogyan ellenőrizze, hogy valaki nem módosíthatja a kritikus rendszer?
* Hogyan erőforrás költségek nyomon követése és számlázási pontosan azt?

Ez a cikk foglalkozik a fenti kérdések. Pontosabban hogy:

* Felhasználók hozzárendelése szerepkörökhöz, és a szerepkörök hozzárendelése a hatókör, így a felhasználók rendelkeznek engedéllyel várható műveleteket, de nincs további műveletek elvégzéséhez.
* Címkével olyan erőforrásokat, nyomon követéséhez őket értékeket, amelyeket célszerű a szervezet számára.
* Házirendeket alkalmazhat, határozza meg a vonatkozó erőforrást az előfizetésében.
* A rendszer kritikus erőforrások zárolása.

Ez a cikk foglalkozik a cégirányítási végrehajtásához végrehajtása tevékenységek. Szélesebb körű leírását a fogalmakat, lásd: [az Azure-ban irányítás](../articles/security/governance-in-azure.md). 
