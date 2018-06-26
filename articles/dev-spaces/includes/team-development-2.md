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
ms.openlocfilehash: 85f8632aae8a70b1282155881dbca6b25734a6c5
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936397"
---
### <a name="run-the-service"></a>A szolgáltatás futtatásához

1. Kattintson az F5 (vagy típus `azds up` a Terminálszolgáltatások ablakban) futtatni a szolgáltatást. A szolgáltatás automatikusan elindul az újonnan kijelölt területen `default/scott`. 
1. Ellenőrizheti, hogy a szolgáltatás fut a saját munkaterületen futtatásával `azds list` újra. Először, észrevehette példányának `mywebapi` innentől kezdve fut a `default/scott` terület (a verzió fut a `default` még mindig fut, de nem szerepel). Másodszor, a hozzáférési pont URL-címe `webfrontend` van előtagként a szöveg "scott.s.". Az URL-cím csak a `default/scott` terület. A speciális URL-cím jelzi, hogy a "scott URL-címe" küldött kérelmeket próbál meg szolgáltatásaihoz első útvonal a `default/scott` terület, de ha nem sikerül, akkor visszaáll a szolgáltatások a `default` terület.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

A beépített szolgáltatás Azure fejlesztői szóközök tesztelheti kód egy megosztott helyre anélkül, hogy mindegyik fejlesztő újra létre kell hoznia a teljes verem szolgáltatások a saját munkaterületen. Az alkalmazás kódját előre propagálás fejlécek, a útválasztásához szükség van, a jelen útmutató az előző lépésben ismertetett módon.

### <a name="test-code-in-a-space"></a>Olyan kódot tesztelése
Új verziójának tesztelése `mywebapi` rendelkező `webfrontend`, nyissa meg a böngészőt, hogy a nyilvános hozzáférés pont URL-címe `webfrontend` és a jogi tudnivalók megjelenítése Névjegy lapra. Meg kell jelennie az új üzenet jelenik meg.

Most távolítsa el a "scott.s." az URL-címet, és frissítse a böngészőt részét. A régi viselkedését kell megjelennie (az a `mywebapi` verziójával futó `default`)
