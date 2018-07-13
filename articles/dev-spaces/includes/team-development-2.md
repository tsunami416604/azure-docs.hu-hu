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
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37933204"
---
### <a name="run-the-service"></a>A szolgáltatás futtatása

1. A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy írja be a terminálablakba az `azds up` parancsot). A szolgáltatás automatikusan az újonnan kiválasztott `default/scott` térben fog futni. 
1. Az `azds list` ismételt futtatásával ellenőrizheti, hogy a szolgáltatás a saját területén fut-e. Először is, láthatja, hogy a `mywebapi` egy példánya most már a `default/scott` térben fut (a `default` térben futó verzió még fut, de nem szerepel a listán). Másodszor, a `webfrontend` hozzáférési pontjának URL-címe a „scott.s.” előtaggal kezdődik. Ezzel az URL-címmel csakis a `default/scott` tér rendelkezik. A speciális URL-cím azt jelzi, hogy a „scott URL”-címre küldött kérelmeket a rendszer először megpróbálja a `default/scott` térbe irányítani, de ha az nem sikerül, a rendszer visszavált a `default` tér szolgáltatásaira.

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
