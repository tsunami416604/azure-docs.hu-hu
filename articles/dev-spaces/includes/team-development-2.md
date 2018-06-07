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
ms.openlocfilehash: f6245a97f5d94c90e022ac509b61da477f4d9494
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823832"
---
### <a name="run-the-service"></a>A szolgáltatás futtatásához

1. Kattintson az F5 (vagy típus `azds up` a Terminálszolgáltatások ablakban) futtatni a szolgáltatást. A szolgáltatás automatikusan elindul az újonnan kijelölt területen `scott`. 
1. Ellenőrizheti, hogy a szolgáltatás fut a saját munkaterületen futtatásával `azds resource list` újra. Először, észrevehette példányának `mywebapi` innentől kezdve fut a `scott` terület (a verzió fut a `default` hely továbbra is fut, de nem szerepel). Második, a hozzáférési pont URL-címe `webfrontend` most már a előtag `scott.s.`. Az URL-cím csak a `scott` áll rendelkezésre, és azt jelenti, hogy a "scott URL-címe" küldött kérelmek megpróbálja útvonal-szolgáltatásaihoz a `scott` terület, és ha nem adott `scott` példányt a szolgáltatás kérelmek visszaállni a szolgáltatások a `default` terület.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

A beépített kapacitásprofilokban Azure fejlesztői szóközöket lehetővé teszi, hogy tesztelje a code végpont a megosztott fejlesztői adhatja anélkül, hogy mindegyik fejlesztő újra létre kell hoznia a teljes verem foglalt terület szolgáltatások. Az alkalmazás kódját előre propagálás fejlécek, a útválasztásához szükség van, a jelen útmutató az előző lépésben ismertetett módon.

### <a name="test-code-in-a-space"></a>Olyan kódot tesztelése
Új verziójának tesztelése `mywebapi` rendelkező `webfrontend`, nyissa meg a böngészőben a webfrontend nyilvános hozzáférés pont URL-CÍMÉT, és a jogi tudnivalók megjelenítése Névjegy lapra. Meg kell jelennie az új üzenet jelenik meg.

Most, távolítsa el a `scott.s.` része az URL-címet, és frissítse a böngészőt. A régi viselkedését kell megjelennie (által végrehajtott a `mywebapi` verziójával futó `default`)