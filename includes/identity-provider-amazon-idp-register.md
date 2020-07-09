---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82900367"
---
1. Jelentkezzen be az [Amazon fejlesztői konzolba](https://developer.amazon.com/dashboard) az Amazon-fiókja hitelesítő adataival.
1. Ha még nem tette meg, kattintson a **regisztráció**gombra, kövesse a fejlesztői regisztráció lépéseit, és fogadja el a szabályzatot.
1. Az irányítópulton válassza a **Bejelentkezés az Amazon**-ban lehetőséget.
1. Válassza **az új biztonsági profil létrehozása**lehetőséget.
1. Adja meg a **biztonsági profil nevét**, a **biztonsági profil leírását**és a belefoglalt **adatvédelmi nyilatkozat URL-címét**, például `https://www.contoso.com/privacy` az adatvédelmi nyilatkozat URL-címe a felügyelni kívánt oldal, amely adatvédelmi információkat biztosít a felhasználóknak. Ezután kattintson a **Mentés**gombra.
1. A **Bejelentkezés az Amazon-konfigurációk** szakaszban válassza ki a létrehozott **biztonsági profil nevét** , kattintson a **kezelés** ikonra, és válassza a **webes beállítások**lehetőséget.
1. A **webes beállítások** szakaszban másolja ki az **ügyfél-azonosító**értékét. Válassza a **titok megjelenítése** lehetőséget az ügyfél titkos kódjának beolvasásához, majd másolja azt. Mindkettőnek szüksége van egy Amazon-fiók konfigurálására a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A **webes beállítások** szakaszban válassza a **Szerkesztés**lehetőséget. Az **engedélyezett eredetek** és az **engedélyezett visszatérési URL-címek**mezőben adja meg a megfelelő URL-címeket (lásd fent). 
1. Kattintson a **Save** (Mentés) gombra.
