---
title: Az alkalmazás állapotának megértése az Azure Spring Cloud-ban
description: Az alkalmazások állapotának kategóriáinak megismerése az Azure Spring Cloud-ban
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: brendm
ms.openlocfilehash: 70a9e6392e21422d7513197fbf7a1a75e1f6ab8f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569003"
---
# <a name="understanding-app-status-in-azure-spring-cloud"></a>Az alkalmazás állapotának megértése az Azure Spring Cloud-ban

Az Azure Spring Cloud felhasználói felülete információkat nyújt a futó alkalmazások állapotáról.  Az előfizetésben található minden erőforráscsoport esetében van **egy alkalmazási lehetőség,** amely az alkalmazások típusának általános állapotát jeleníti meg.  Minden egyes alkalmazás-típus esetében megjelenik az **alkalmazás példányai**.

## <a name="apps-status"></a>Alkalmazások állapota
Egy alkalmazás típusának általános állapotának megtekintéséhez válassza az **alkalmazások** lehetőséget az erőforráscsoport bal oldali navigációs paneljén. Az eredmény az üzembe helyezett alkalmazás állapotát jeleníti meg:

* A kiépítési **állapot** az üzemelő példány kiépítési állapotát jeleníti meg
* A **futó példány** azt mutatja, hogy hány alkalmazás fut, illetve hány alkalmazás-példányra van szükség. Ha az alkalmazást le kell állítani, az oszlop *leállt*.
* A **regisztrált példány** azt jeleníti meg, hogy hány alkalmazás-példány van regisztrálva az Eureka-ban/hány alkalmazás-példányra van szükség. Ha az alkalmazást le kell állítani, az oszlop *leállt*.


 ![Alkalmazások állapota](media/spring-cloud-concept-app-status/apps-ui-status.png)

**A központi telepítés állapota a következő értékek egyike lehet:**

| Felsorolás | Meghatározás |
|:--:|:----------------:|
| Fut | A központi telepítésnek futnia kell. |
| Leállítva | A központi telepítést le kell állítani. |

**A kiépítési állapot csak a parancssori felületről érhető el.  A jelentés az alábbi értékek egyike:**

| Felsorolás | Meghatározás |
|:--:|:----------------:|
| Létrehozás | Az erőforrás létrehozása. |
| Frissítése | Az erőforrás frissítése folyamatban van. |
| Sikeres | A rendszer sikeresen biztosította az erőforrásokat, és üzembe helyezi a bináris fájlt. |
| Sikertelen | Nem sikerült elérni a *sikeres* célt. |
| Törlése | Az erőforrás törlése folyamatban van. Ez megakadályozza a műveletet, és az erőforrás nem érhető el ebben az állapotban. |

## <a name="app-instances-status"></a>Alkalmazás példányainak állapota

Egy telepített alkalmazás adott példányának állapotának megtekintéséhez kattintson az alkalmazás **nevére** az **alkalmazások** felhasználói felületén. Az eredmények a következőket fogják megjeleníteni:
* **Állapot**: azt jelzi, hogy a példány fut-e, vagy az állapota
* **DiscoveryStatus**: az alkalmazás példányának regisztrált állapota az Eureka-kiszolgálón

 ![Alkalmazás példányainak állapota](media/spring-cloud-concept-app-status/apps-ui-instance-status.png)

**A példány állapota a következő értékek egyike:**

| Felsorolás | Meghatározás |
|:--:|:----------------:|
| Indítás | A bináris fájl sikeresen telepítve lett az adott példányon. A jar-fájl indítása sikertelen lehet, mert a jar nem futtatható megfelelően. |
| Fut | A példány működik. |
| Sikertelen | Az alkalmazás példánya több újrapróbálkozás után nem tudta elindítani a felhasználó bináris fájlját. |
| Megszüntetéséről | Az alkalmazás példánya leáll. |

**A példány felderítési állapota a következő értékek egyike lesz:**

| Felsorolás | Meghatározás |
|:--:|:----------------:|
| FEL | Az alkalmazás-példány regisztrálva van az Eureka-ban, és készen áll a forgalom fogadására |
| OUT_OF_SERVICE | Az alkalmazás példánya regisztrálva van az Eureka-ban, és képes fogadni a forgalmat. a forgalom azonban szándékosan leáll. |
| LE | Az alkalmazás példánya nincs regisztrálva az Eureka-ben, vagy regisztrálva van, de nem tud forgalmat fogadni. |


## <a name="see-also"></a>Lásd még
* [Java Spring-alkalmazás előkészítése az Azure Spring Cloud üzembe helyezéséhez](spring-cloud-tutorial-prepare-app-deployment.md)