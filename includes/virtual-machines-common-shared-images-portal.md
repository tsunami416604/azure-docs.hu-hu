---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/29/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 291ec651061b7a8a3ea3c0645a6bd6581d529ef6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66245010"
---
## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

> [!NOTE]
> Ha megosztott kép katalógusok használata az előzetes verzióban regisztrált, akkor előfordulhat, hogy, újra kell regisztrálnia az `Microsoft.Compute` szolgáltató. Nyissa meg [Cloud Shell](https://shell.azure.com/bash) , és írja be: `az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Lemezkép-katalógus létrehozása

Lemezkép-katalógus, hogy az elsődleges erőforrás használt lemezkép megosztása. Katalógus neveként engedélyezett karakterek:, kis-és nagybetűket, számokat, pontokat és időszakok. A katalógus neve nem tartalmazhat kötőjeleket.  Katalógus egyedieknek kell lenniük az előfizetésben. 

A következő példában létrehozunk egy katalógusban nevű *myGallery* a a *myGalleryRG* erőforráscsoportot.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Használja a típusának **megosztott lemezkép-katalógusában** a keresési mezőbe, majd válassza ki a **megosztott lemezkép-katalógusában** az eredmények között.
1. Az a **megosztott lemezkép-katalógusában** kattintson **létrehozás**.
1. Válassza ki a megfelelő előfizetés.
1. A **erőforráscsoport**válassza **új létrehozása** , és írja be *myGalleryRG* neve.
1. A **neve**, típus *myGallery* a gyűjtemény neve.
1. Hagyja bejelölve az alapértelmezett **régió**.
1. Beírhatja például a katalógusban, rövid leírását *tesztelési saját rendszerkép-katalógusában.* majd **felülvizsgálat + létrehozása**.
1. Ellenőrzés elvégzése után válassza **létrehozás**.
1. Ha a telepítés befejeződött, válassza ki a **erőforrás megnyitása**.
   
## <a name="create-an-image-definition"></a>Kép definíció létrehozása 

Lemezkép-definíciók létrehozása lemezképek logikai jellegű csoportosítását. A lemezkép-verziók azokon belül létrehozott adatainak kezeléséhez használhatók. Definíció rendszerképnevek a kis-és nagybetűket, számokat, pontokat, kötőjeleket és időszakok lehet tenni. Az értékeket is megadhat egy rendszerkép definíciójában kapcsolatos további információkért lásd: [definíciók kép](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

A katalógus rendszerkép definíciójában alapjait az a gyűjtemény létrehozásához. Ebben a példában a katalógus-lemezkép neve *myImageDefinition*.

1. Az új lemezkép-katalógusában lapon válassza ki a **adjon hozzá egy új rendszerkép definíciójában** az oldal tetején. 
1. A **definíció lemezképnév**, típus *myImageDefinition*.
1. A **operációs rendszer**, válassza ki a megfelelő beállítás a forrás lemezkép alapján.
1. A **közzétevő**, típus *myPublisher*. 
1. A **ajánlat**, típus *myOffer*.
1. A **Termékváltozat**, típus *mySKU*.
1. Amikor végzett, válassza ki a **felülvizsgálat + létrehozása**.
1. Miután a rendszerkép definíciójában ellenőrzése sikeres, válassza ki **létrehozás**.
1. Ha a telepítés befejeződött, válassza ki a **erőforrás megnyitása**.


## <a name="create-an-image-version"></a>Hozzon létre egy lemezkép verziója

Egy lemezkép-verzió létrehozása felügyelt rendszerképből. Ebben a példában a rendszerkép verziószáma *1.0.0-s* , és mindkét replikálás *USA nyugati középső Régiója* és *USA déli középső Régiójában* adatközpontokban. Replikáció célrégiók kiválasztásakor ne feledje, hogy is kell tartalmaznia a *forrás* régió a replikációs cél.

Lemezkép-verzió engedélyezett karakterek:, számokat és pontokat. Számok belül a 32 bites egész számnak kell lennie. Formátum: *Főverzió*. *MinorVersion*. *Javítás*.

1. Válassza ki a lapon a rendszerkép definíciójában **Hozzáadás verzió** az oldal tetején.
1. A **régió**, válassza ki a régiót, ahol a felügyelt rendszerkép található. Lemezkép verziója kell hozhatók létre ugyanabban a régióban a felügyelt rendszerkép alapján hoztak létre.
1. A **neve**, típus *1.0.0-s*. A lemezkép-verzió neve kell követniük *fő*. *kisebb*. *javítás* formázza az egész számok használata. 
1. A **forráslemezkép**, válassza ki a felügyelt forráslemezkép a legördülő listából.
1. A **kizárása a legújabb**, hagyja bejelölve az alapértelmezett érték *nem*.
1. A **életre dátum végén**, jelölje ki a dátumát a naptárból, amely néhány hónappal később.
1. A **replikációs**, hagyja a **alapértelmezett replika száma** legyen 1. Replikálja a forrásrégióban. ezért ne alapértelmezés szerint az első replika, majd válasszon egy második replika régiót kell *USA keleti Régiójában*.
1. Amikor elkészült, válassza ki a **felülvizsgálat + létrehozása**. Az Azure ellenőrzi a konfigurációt.
1. Amikor a lemezkép-verzió ellenőrzése sikeres, válassza ki a **létrehozás**.
1. Ha a telepítés befejeződött, válassza ki a **erőforrás megnyitása**.

A kép replikálása a célrégiók mindegyikét egy ideig is eltarthat.

## <a name="share-the-gallery"></a>A katalógus megosztása

Azt javasoljuk, hogy megosztott hozzáférés a lemezkép katalógus szintjén. A következő lépésről az imént létrehozott gyűjtemény megosztása.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. A bal oldali menüben válassza **erőforráscsoportok**. 
1. Az erőforráscsoportok listájából válassza ki **myGalleryRG**. Ekkor megnyílik a panel az erőforráscsoport.
1. A, a bal oldali menüben a **myGalleryRG** lapon jelölje be **hozzáférés-vezérlés (IAM)** . 
1. A **egy szerepkör-hozzárendelés hozzáadása**válassza **Hozzáadás**. A **egy szerepkör-hozzárendelés hozzáadása** panel nyílik meg. 
1. A **szerepkör**válassza **olvasó**.
1. A **rendelhet hozzáféréseket**, hagyja bejelölve az alapértelmezett **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**.
1. A **kiválasztása**, írja be azt, amelyet szeretne meghívni, aki az e-mail-cím.
1. Ha a felhasználó a szervezeten kívül, üzenet jelenik-e **a felhasználó kapni fog egy e-mailt, amely lehetővé teszi, hogy együttműködjön a Microsofttal.** Válassza ki az e-mail-címmel rendelkező felhasználót, és kattintson a **mentése**.

Ha a felhasználó a szervezeten kívül, kap egy e-mailes meghívót a szervezethez való csatlakozásról. A felhasználónak a meghívás elfogadásához, majd a gyűjtemény és az összes lemezképet meghatározásai és verziók az erőforrások listájában látni fogják.

