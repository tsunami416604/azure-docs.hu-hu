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
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903709"
---
De ha szeretné megosztani a képeket az Azure-bérlőn kívül, nagy méretekben, hozzon létre egy alkalmazásregisztrációmegosztás megkönnyítése érdekében.  Az alkalmazásregisztráció használata összetettebb megosztási forgatókönyveket tesz lehetővé, például: 

* Megosztott lemezképek kezelése, amikor az egyik vállalat beszerez egy másikat, és az Azure-infrastruktúra külön bérlők között van elosztva. 
* Az Azure-partnerek az azure-infrastruktúrát az ügyfeleik nevében kezelik. A lemezképek testreszabása a partnerek bérlőn belül történik, de az infrastruktúra-telepítések az ügyfél bérlőjében fognak megtörténni. 


## <a name="create-the-app-registration"></a>Az alkalmazásregisztráció létrehozása

Hozzon létre egy alkalmazásregisztrációt, amelyet mindkét bérlő a képgaléria erőforrásainak megosztására fog használni.
1. Nyissa meg az [alkalmazásregisztrációkat (előzetes verzió) az Azure Portalon.](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/)    
1. Válassza az **Új regisztráció** lehetőséget a lap tetején található menüből.
1. A **Név**mezőbe írja be a *myGalleryApp (neve) mezőbe a myGalleryApp*című részt.
1. A **Támogatott fióktípusok**csoportban válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
1. Az **Átirányítás**URI *https://www.microsoft.com* -be írja be a parancsot, és válassza a **Regisztráció**lehetőséget. Az alkalmazás regisztrációjának létrehozása után megnyílik az áttekintő lap.
1. Az áttekintő lapon másolja az **alkalmazás (ügyfél) azonosítóját,** és mentse későbbi használatra.   
1. Válassza **a Tanúsítványok & titkos kulcsok**at, majd az Új **ügyféltitok**lehetőséget.
1. A **Leírás mezőbe**írja be a Megosztott *képgaléria bérlői alkalmazástitkos kulcsot.*
1. A **Expires (Lejár)** **mezőben** hagyja meg az 1 év ben az alapértelmezett értéket, majd válassza a **Hozzáadás**lehetőséget.
1. Másolja a titok értékét, és mentse el egy biztonságos helyre. A lap elhagyása után nem lehet visszakérni.


Adjon engedélyt az alkalmazás regisztrációs engedélyének a megosztott képtár használatára.
1. Az Azure Portalon válassza ki a megosztott képtár, amely szeretné megosztani egy másik bérlővel.
1. Válassza **a Hozzáférés-vezérlés (IAM)** lehetőséget, majd a **Szerepkör-hozzárendelés hozzáadása** csoportban válassza a *Hozzáadás*lehetőséget. 
1. A **Role (Szerepkör) csoportban**válassza az **Olvasó**lehetőséget.
1. A **Hozzáférés hozzárendelése a:** csoportban hagyja ezt **Azure AD-felhasználóként, csoportként vagy egyszerű szolgáltatásként.**
1. A **Kijelölés csoportban**írja be a *myGalleryApp* parancsot, és jelölje ki, amikor megjelenik a listában. Amikor elkészült, válassza a **Mentés** gombot.


## <a name="give-tenant-2-access"></a>2. bérlő hozzáférése

Adjon bérlő 2 hozzáférést az alkalmazáshoz egy böngészőhasználatával történő bejelentkezés kérésével. * \<Tenant2-azonosító>* a bérlőazonosítót a bérlői azonosítót a bérlő, amely szeretné megosztani a képgalériát. Cserélje le * \<az alkalmazásazonosítót>* a létrehozott alkalmazásregisztráció alkalmazásazonosítójára. Ha végzett a cserék, illessze be az URL-t a böngészőbe, és kövesse a bejelentkezési utasításokat, hogy jelentkezzen be a bérlő 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Az [Azure Portalon](https://portal.azure.com) jelentkezzen be bérlőként 2 és adja meg az alkalmazás regisztrációs hozzáférést az erőforráscsoport, ahol létre szeretné hozni a virtuális gép.

1. Jelölje ki az erőforráscsoportot, majd válassza **a Hozzáférés-vezérlés (IAM) lehetőséget.** A **Szerepkör-hozzárendelés hozzáadása** csoportban válassza **a Hozzáadás**lehetőséget. 
1. A **Szerepkör csoportban**írja be a Közreműködő be **írásba való beírását.**
1. A **Hozzáférés hozzárendelése a:** csoportban hagyja ezt **Azure AD-felhasználóként, csoportként vagy egyszerű szolgáltatásként.**
1. A Select myGalleryApp **(Típus:** *myGalleryApp)* csoportban jelölje ki, amikor megjelenik a listában. Amikor elkészült, válassza a **Mentés** gombot.

> [!NOTE]
> Meg kell várnia, amíg a lemezkép-verzió teljesen befejeződik a létrehozás és a replikálás, mielőtt ugyanazt a felügyelt lemezképet használhatna egy másik lemezkép-verzió létrehozásához.

