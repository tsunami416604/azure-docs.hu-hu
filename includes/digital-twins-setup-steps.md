---
author: baanders
description: fájl belefoglalása az Azure digitális Twins telepítőjének lépéseinek áttekintéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 10/14/2020
ms.author: baanders
ms.openlocfilehash: bf442da43fd7945bf69fbb889ef0c517b8832809
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478847"
---
>[!NOTE]
>Ezeknek a műveleteknek a célja, hogy egy felhasználó elvégezze az Azure-előfizetéshez tartozó erőforrások és felhasználói hozzáférés felügyeletét. Bár egyes lépések alacsonyabb engedélyekkel is végezhetők, az ezekkel az engedélyekkel rendelkező személyekkel való együttműködéshez teljes mértékben be kell állítania egy használható példányt. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.

Az új Azure Digital Twins-példány teljes beállítása két részből áll:
1. **A példány létrehozása**
2. **Felhasználói hozzáférési engedélyek beállítása**: az Azure-felhasználóknak rendelkezniük kell az Azure Digital Twins- *adattulajdonosi* szerepkörrel az Azure digitális Twins-példányon, hogy kezelni tudja az IT-t és annak adatait. Ebben a lépésben az Azure-előfizetéshez tartozó tulajdonos/rendszergazda rendeli hozzá ezt a szerepkört az Azure Digital Twins-példányt kezelő személyhez. Ez lehet önmaga vagy valaki más a szervezetben.

[!INCLUDE [digital-twins-role-rename-note.md](digital-twins-role-rename-note.md)]