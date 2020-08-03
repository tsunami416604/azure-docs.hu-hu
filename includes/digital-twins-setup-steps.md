---
author: baanders
description: fájl belefoglalása az Azure digitális Twins telepítőjének lépéseinek áttekintéséhez
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: b8751685cfd273171cb8a5b500e4bed41ce2f59e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407522"
---
>[!NOTE]
>Ezeket a műveleteket egy, az Azure-előfizetéshez tartozó *tulajdonosi* szerepkörrel rendelkező felhasználó tölti ki. Bár egyes darabok ezen emelt szintű engedély nélkül is elvégezhető, a tulajdonos együttműködésére lesz szükség a felhasználható példány teljes beállításához. Tekintse meg a további információkat az [*Előfeltételek: szükséges engedélyek*](#prerequisites-permission-requirements) szakaszban alább.

Az új Azure Digital Twins-példány teljes beállítása három részből áll:
1. **A példány létrehozása**
2. **Felhasználói hozzáférési engedélyek beállítása**: az Azure-felhasználóknak az Azure Digital Twins *tulajdonos (előzetes verzió)* szerepkörrel kell rendelkezniük az Azure digitális Twins-példányon, hogy kezelni tudják az informatikai és az adatkezelési jogosultságokat. Ebben a lépésben az Azure-előfizetés tulajdonosaként ezt a szerepkört fogja hozzárendelni az Azure Digital Twins-példányt kezelő személyhez. Ez lehet önmaga vagy valaki más a szervezetben.
3. **Ügyfélalkalmazások hozzáférési engedélyeinek beállítása**: gyakori, hogy ír egy ügyfélalkalmazás, amely a példányával való interakcióra lesz felhasználva. Ahhoz, hogy az ügyfélalkalmazás hozzáférhessen az Azure Digital Twins-hoz, be kell állítania egy alkalmazás- *regisztrációt* [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , hogy az ügyfélalkalmazás a példány hitelesítésére használja majd.

A folytatáshoz szüksége lesz egy Azure-előfizetésre. [Itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)megadhat egyet ingyenesen.
