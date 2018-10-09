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
ms.openlocfilehash: 8580e19f1955becb2cfaee13742054af1a9edb7f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410464"
---
### <a name="run-the-service"></a>A szolgáltatás futtatása

1. A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy írja be a terminálablakba az `azds up` parancsot). A szolgáltatás automatikusan az újonnan kiválasztott `default/scott` térben fog futni. 
1. Az `azds list-up` ismételt futtatásával ellenőrizheti, hogy a szolgáltatás a saját területén fut-e. Először is, láthatja, hogy a `mywebapi` egy példánya most már a `default/scott` térben fut (a `default` térben futó verzió még fut, de nem szerepel a listán). Az `azds list-uris` futtatásakor megfigyelhető, hogy a `webfrontend` hozzáférési pontjának URL-címe a „scott.s.” előtaggal kezdődik. Ezzel az URL-címmel csakis a `default/scott` tér rendelkezik. A speciális URL-cím azt jelzi, hogy a „scott URL”-címre küldött kérelmeket a rendszer először megpróbálja a `default/scott` térbe irányítani, de ha az nem sikerül, a rendszer visszavált a `default` tér szolgáltatásaira.

```
Name                      DevSpace  Type     Updated  Status
------------------------  --------  -------  -------  -------
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               default   Service  26m ago  Running
```

```
Uri                                                            Status
-------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                    Tunneled
http://webfrontend.6364744826e042319629.canadaeast.aksapp.io/  Available
```

![](../articles/dev-spaces/media/common/space-routing.png)

Az Azure Dev Spaces ezen beépített szolgáltatása lehetővé teszi, hogy tesztelhesse a kódot egy megosztott térben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját tere teljes szolgáltatási vermét. Ez az útválasztás megköveteli az alkalmazáskódtól a propagálási fejlécek továbbítását, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-in-a-space"></a>Kód tesztelése egy térben
A `mywebapi` új verzióját a `webfrontend`-del úgy tudja tesztelni, hogy megnyitja a böngészőjében a `webfrontend` nyilvános URL-címét, és felkeresi az About (Információ) oldalt. Meg kell jelennie az új üzenetnek.

Most távolítsa el az URL-cím „scott.s.” előtagját, és frissítse a böngészőt. Ekkor újra a régi működési mód jelenik meg (amikor a `mywebapi` verzió a `default` térben fut).
