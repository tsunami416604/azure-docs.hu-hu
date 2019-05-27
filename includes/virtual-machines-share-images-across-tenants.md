---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145885"
---
Megosztott kép katalógusok lehetővé teszik a megosztás képek RBAC használatával. Az RBAC használatával megoszthatja a bérlőn kívüli lemezképeket a bérlőn belül, és akár egyéni felhasználók számára. De ha meg szeretné osztani a lemezképek kívül az Azure-bérlőhöz, ipari méretekben, hozzunk létre egy alkalmazás regisztrációját a megosztás megkönnyítése érdekében.  Az alkalmazásregisztráció használatával engedélyezheti a összetettebb megosztási helyzetek, például: 

* Képek megosztott kezelése, ha egy vállalat beszerzi egy másik, és az Azure-infrastruktúra külön bérlők között megoszlik. 
* Az Azure-partnerek ügyfeleik nevében kezelhet az Azure-infrastruktúrákat. Lemezképek testreszabása a partnerek bérlőn belül történik, de az infrastruktúra központi telepítések akkor történik meg az ügyfél-bérlőben. 


## <a name="create-the-app-registration"></a>Az alkalmazásregisztráció létrehozása

Hozzon létre egy alkalmazás regisztrálása a lemezkép gyűjtemény-erőforrások megosztása mind a bérlők által használandó.
1. Nyissa meg a [alkalmazásregisztrációk (előzetes verzió) az Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Válassza ki **új regisztrációs** az oldal felső részén látható menüben.
1. A **neve**, típus *myGalleryApp*.
1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
1. A **átirányítási URI-t**, típus *https://www.microsoft.com* majd **regisztrálása**. Miután létrejött az alkalmazás regisztrációját, az Áttekintés lap nyílik meg.
1. Az Áttekintés oldalon másolja az **Alkalmazásazonosítót (ügyfél)** , és mentse későbbi használatra.   
1. Válassza ki **tanúsítványok és titkos kulcsok**, majd válassza ki **új titkos ügyfélkulcsot**.
1. A **leírás**, típus *megosztott kép katalógus több-bérlős alkalmazás titkos kulcsát*.
1. A **lejárat**, hagyja bejelölve az alapértelmezett **az 1 év** majd **Hozzáadás**.
1. Másolja a titkos kulcs értékét, és mentse azt biztonságos helyre. Ez az oldal elhagyása után nem lehet lekérdezni.


Adjon engedélyt az alkalmazásnak regisztrációs megosztott lemezkép-katalógus használatával.
1. Az Azure Portalon válassza ki a megosztott lemezkép-katalógusban, hogy meg szeretné osztani egy másik bérlőben.
1. Válassza ki **válassza ki a hozzáférés-vezérlés (IAM)**, majd a **szerepkör-hozzárendelés hozzáadása** kiválasztása *Hozzáadás*. 
1. A **szerepkör**válassza **olvasó**.
1. A **hozzáférés hozzárendelése:**, hagyja meg az **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
1. A **válassza**, típus *myGalleryApp* , és jelölje ki, amikor az megjelenik a listában. Amikor elkészült, válassza ki a **mentése**.


## <a name="give-tenant-2-access"></a>Bérlő 2 hozzáférést

Bérlői 2 hozzáférést biztosít a kérelem kérve egy böngészővel bejelentkezési. Cserélje le *<Tenant2 ID>* bérlőazonosítójú szeretné osztani a lemezkép-katalógusában a bérlőhöz. Cserélje le *< alkalmazás (ügyfél) azonosítója >* a létrehozott alkalmazás regisztráció az alkalmazás azonosítójával. Ha elkészült, így a cserét, illessze be az URL-címet egy böngészőben, és a bejelentkezési utasításokat követve jelentkezzen be a bérlő 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Az a [az Azure portal](https://portal.azure.com) jelentkezzen be a bérlő 2 és az alkalmazás regisztrációs hozzáférést biztosíthat az erőforráscsoportot kívánja a virtuális gép létrehozásához.

1. Válassza ki az erőforráscsoportot, majd **hozzáférés-vezérlés (IAM)**. A **szerepkör-hozzárendelés hozzáadása** kiválasztása **Hozzáadás**. 
1. A **szerepkör**, típus **közreműködői**.
1. A **hozzáférés hozzárendelése:**, hagyja meg az **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
1. A **kiválasztása** típus *myGalleryApp* majd válassza ki, amikor az megjelenik a listában. Amikor elkészült, válassza ki a **mentése**.

> [!NOTE]
> Várjon, amíg a rendszerkép verziószámát teljesen befejeződik, beépített és a replikált felügyelt ugyanazt a lemezképet létrehozni egy másik lemezkép-verzió használata előtt kell.

