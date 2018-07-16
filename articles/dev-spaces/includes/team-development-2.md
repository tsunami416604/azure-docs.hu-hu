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
ms.openlocfilehash: 2f2177b289ba9d13ba635bddcd61748e1e8ab0d1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39063050"
---
### <a name="run-the-service"></a>A szolgáltatás futtatása

1. A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy írja be a terminálablakba az `azds up` parancsot). A szolgáltatás automatikusan az újonnan kiválasztott `default/scott` térben fog futni. 
1. Az `azds list-up` ismételt futtatásával ellenőrizheti, hogy a szolgáltatás a saját területén fut-e. Először is, láthatja, hogy a `mywebapi` egy példánya most már a `default/scott` térben fut (a `default` térben futó verzió még fut, de nem szerepel a listán). Másodszor, a `webfrontend` hozzáférési pontjának URL-címe a „scott.s.” előtaggal kezdődik. Ezzel az URL-címmel csakis a `default/scott` tér rendelkezik. A speciális URL-cím azt jelzi, hogy a „scott URL”-címre küldött kérelmeket a rendszer először megpróbálja a `default/scott` térbe irányítani, de ha az nem sikerül, a rendszer visszavált a `default` tér szolgáltatásaira.

```
Name         Space          Chart              Ports   Updated     Access Points
-----------  --------       -----------------  ------  ----------  -------------
mywebapi     default/scott  mywebapi-0.1.0     80/TCP  15s ago     http://localhost:61466
webfrontend  default        webfrontend-0.1.0  80/TCP  5h ago      http://scott.s.webfrontend-contosodev.1234abcdef.eastus.aksapp.io
```

![](../media/common/space-routing.png)

Az Azure Dev Spaces ezen beépített szolgáltatása lehetővé teszi, hogy tesztelhesse a kódot egy megosztott térben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját tere teljes szolgáltatási vermét. Ez az útválasztás megköveteli az alkalmazáskódtól a propagálási fejlécek továbbítását, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-in-a-space"></a>Kód tesztelése egy térben
A `mywebapi` új verzióját a `webfrontend`-del úgy tudja tesztelni, hogy megnyitja a böngészőjében a `webfrontend` nyilvános URL-címét, és felkeresi az About (Információ) oldalt. Meg kell jelennie az új üzenetnek.

Most távolítsa el az URL-cím „scott.s.” előtagját, és frissítse a böngészőt. Ekkor újra a régi működési mód jelenik meg (amikor a `mywebapi` verzió a `default` térben fut)
