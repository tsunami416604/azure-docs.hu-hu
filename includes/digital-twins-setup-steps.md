---
author: baanders
description: fájl belefoglalása az Azure digitális Twins telepítőjének lépéseinek áttekintéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: 5ab6812d144122c61018ad740892db869a7ba43d
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205487"
---
>[!NOTE]
>Ezeknek a műveleteknek a célja, hogy egy felhasználó elvégezze az Azure-előfizetéshez tartozó erőforrások és felhasználói hozzáférés felügyeletét. Bár egyes lépések alacsonyabb engedélyekkel is végezhetők, az ezekkel az engedélyekkel rendelkező személyekkel való együttműködéshez teljes mértékben be kell állítania egy használható példányt. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.

Az új Azure Digital Twins-példány teljes beállítása két részből áll:
1. **A példány létrehozása**
2. **Felhasználói hozzáférési engedélyek beállítása**: az Azure-felhasználóknak az Azure Digital Twins *tulajdonos (előzetes verzió)* szerepkörrel kell rendelkezniük az Azure digitális Twins-példányon, hogy kezelni tudják az informatikai és az adatkezelési jogosultságokat. Ebben a lépésben az Azure-előfizetéshez tartozó tulajdonos/rendszergazda rendeli hozzá ezt a szerepkört az Azure Digital Twins-példányt kezelő személyhez. Ez lehet önmaga vagy valaki más a szervezetben.