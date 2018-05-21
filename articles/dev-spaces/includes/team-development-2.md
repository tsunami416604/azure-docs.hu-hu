---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: 01b830f8a5e6e5f957b36bc2d6ef035754c06157
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
### <a name="run-the-service"></a>A szolgáltatás futtatásához

1. Kattintson az F5 (vagy típus `azds up` a Terminálszolgáltatások ablakban) futtatni a szolgáltatást. A szolgáltatás automatikusan elindul az újonnan kijelölt területen `scott`. 
1. Ellenőrizheti, hogy a szolgáltatás fut a saját munkaterületen futtatásával `azds resource list` újra. Először, észrevehette példányának `mywebapi` innentől kezdve fut a `scott` terület (a verzió fut a `default` még mindig fut, de nem szerepel). Másodszor, a hozzáférési pont URL-címe `webfrontend` van előtagként a szöveg *scott.s.*. Az URL-cím csak a `scott` szóköz, és jelzi, hogy a "scott URL-címe" küldött kérelmek szolgáltatásaihoz első útvonal megkísérli a `scott` szóköz, és a szolgáltatások visszaáll a `default` terület.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

A beépített kapacitásprofilokban Azure fejlesztői szóközöket lehetővé teszi, hogy tesztelje a code végpontok megosztott környezetben anélkül, hogy mindegyik fejlesztő újra létre kell hoznia a teljes verem foglalt terület szolgáltatások. Az alkalmazás kódját előre propagálás fejlécek, a útválasztásához szükség van, a jelen útmutató az előző lépésben ismertetett módon.

## <a name="test-code-in-a-space"></a>Olyan kódot tesztelése
Új verziójának tesztelése `mywebapi` együtt `webfrontend`, nyissa meg a böngészőben a webfrontend nyilvános hozzáférés pont URL-CÍMÉT, és a jogi tudnivalók megjelenítése Névjegy lapra. Meg kell jelennie az új üzenet jelenik meg.

Most távolítsa el a "scott.s." az URL-címet, és frissítse a böngészőt részét. A régi viselkedését kell megjelennie (által végrehajtott a `mywebapi` verziójával futó `default`)