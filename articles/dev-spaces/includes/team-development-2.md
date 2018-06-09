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
ms.openlocfilehash: 78dca327a470394d19e6befc6578abf2d499850c
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247585"
---
### <a name="run-the-service"></a>A szolgáltatás futtatásához

1. Kattintson az F5 (vagy típus `azds up` a Terminálszolgáltatások ablakban) futtatni a szolgáltatást. A szolgáltatás automatikusan elindul az újonnan kijelölt területen `scott`. 
1. Ellenőrizheti, hogy a szolgáltatás fut a saját munkaterületen futtatásával `azds space list` újra. Először, észrevehette példányának `mywebapi` innentől kezdve fut a `scott` terület (a verzió fut a `default` még mindig fut, de nem szerepel). Másodszor, a hozzáférési pont URL-címe `webfrontend` van előtagként a szöveg "scott.s.". Az URL-cím csak a `scott` terület. A speciális URL-cím jelzi, hogy a "scott URL-címe" küldött kérelmeket próbál meg szolgáltatásaihoz első útvonal a `scott` terület, de ha nem sikerül, akkor visszaáll a szolgáltatások a `default` terület.

```
Name         Space     Chart              Ports   Updated     Access Points
-----------  --------  -----------------  ------  ----------  -------------
mywebapi     scott     mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default  webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

A beépített szolgáltatás Azure fejlesztői szóközök tesztelheti kód egy megosztott helyre anélkül, hogy mindegyik fejlesztő újra létre kell hoznia a teljes verem szolgáltatások a saját munkaterületen. Az alkalmazás kódját előre propagálás fejlécek, a útválasztásához szükség van, a jelen útmutató az előző lépésben ismertetett módon.

### <a name="test-code-in-a-space"></a>Olyan kódot tesztelése
Új verziójának tesztelése `mywebapi` rendelkező `webfrontend`, nyissa meg a böngészőt, hogy a nyilvános hozzáférés pont URL-címe `webfrontend` és a jogi tudnivalók megjelenítése Névjegy lapra. Meg kell jelennie az új üzenet jelenik meg.

Most távolítsa el a "scott.s." az URL-címet, és frissítse a böngészőt részét. A régi viselkedését kell megjelennie (az a `mywebapi` verziójával futó `default`)
