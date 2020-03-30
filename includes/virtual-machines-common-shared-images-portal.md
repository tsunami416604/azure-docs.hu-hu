---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 729e757c69887bbdce324e2d8383c970995dc94a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73903673"
---
## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

> [!NOTE]
> Ha regisztrált a megosztott képgalériák használatára az előzetes verzió `Microsoft.Compute` során, előfordulhat, hogy újra regisztrálnia kell a szolgáltatót. Nyissa meg [a Cloud Shell-t,](https://shell.azure.com/bash) és írja be a következőt:`az provider register -n Microsoft.Compute`

## <a name="create-an-image-gallery"></a>Képgaléria létrehozása

A képgaléria a képmegosztás engedélyezéséhez használt elsődleges erőforrás. A Galéria nevének megengedett karakterei a nagy- vagy kisbetűk, számjegyek, pontszámok és pont. A gyűjtemény neve nem tartalmazhat kötőjeleket.  A galérianeveknek egyedinek kell lenniük az előfizetésen belül. 

A következő példa létrehoz egy *myGallery* nevű galériát a *myGalleryRG* erőforráscsoportban.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
1. Használja a **Megosztott képgyűjtemény** t a keresőmezőben, és válassza a **Megosztott képgyűjtemény** lehetőséget a találatok között.
1. A **Megosztott képgyűjtemény lapon** kattintson a **Létrehozás gombra.**
1. Válassza ki a megfelelő előfizetést.
1. Az **Erőforrás csoportban**válassza **az Új létrehozása lehetőséget,** és írja be a *myGalleryRG* nevet a névhez.
1. A **Név mezőbe**írja be a *myGallery* nevet a gyűjtemény nevéhez.
1. Hagyja meg az alapértelmezett értéket a **Régió**mezőben.
1. Beírhatja a gyűjtemény rövid leírását, például a *Saját képgaléria tesztelésre.* majd kattintson **a Véleményezés + create gombra.**
1. Az érvényesítési fázisok után válassza a **Létrehozás gombot.**
1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.
   
## <a name="create-an-image-definition"></a>Képdefiníció létrehozása 

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. A bennük létrehozott lemezkép-verziók adatainak kezelésére szolgálnak. A képdefiníciós nevek nagy- vagy kisbetűkből, számjegyekből, pontokból, kötőjelekből és pontokból állhatnak. A képdefinícióhoz megadható értékekről a [Képdefiníciók](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions)című témakörben talál további információt.

Hozza létre a galéria képdefinícióját a galérián belül. Ebben a példában a galéria kép neve *myImageDefinition*.

1. Az új képgyűjtemény lapján válassza az **Új képdefiníció hozzáadása** elemet a lap tetején. 
1. A **Képdefiníció nevéhez**írja be a *myImageDefinition (myImageDefinition ) mezőbe.*
1. **Az operációs rendszer**esetén válassza ki a megfelelő beállítást a forrás virtuális gép alapján.
1. Virtuális **gép létrehozása esetén**válassza ki a lehetőséget a forrás virtuális gép alapján. A legtöbb esetben ez gen *1*. További információ: [Támogatás a 2.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2)
1. Az **operációs rendszer állapota**esetén válassza ki a lehetőséget a forrás virtuális gép alapján. További információ: [Generalized and specialized](../articles/virtual-machines/linux/shared-image-galleries.md#generalized-and-specialized-images).
1. A **Publisher**mezőbe írja be a *myPublisher (Publisher ) mezőbe.* 
1. Az **Ajánlat**mezőbe írja be a *myOffer (myOffer) mezőbe.*
1. A **Termékváltozathoz**írja be a *mySKU (misku)* mezőbe.
1. Ha végzett, válassza **a Véleményezés + create**lehetőséget.
1. Miután a képdefiníció átment az ellenőrzésen, válassza a **Létrehozás gombot.**
1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.


## <a name="create-an-image-version"></a>Képverzió létrehozása

Lemezkép-verzió létrehozása felügyelt lemezképből. Ebben a példában a lemezkép verziója *1.0.0,* és replikálja mind *az USA nyugati középső régiója* és az USA déli középső *régiói* adatközpontok. A replikáció célterületeinek kiválasztásakor ne feledje, hogy a *forrásrégiót* is meg kell adnia a replikáció célként.

A képverzió megengedett karakterei számok és időszakok. A számoknak a 32 bites egész szám tartományán belül kell lenniük. Formátum: *MajorVersion*. *MinorVersion*. *Patch*.

A rendszerkép-verzió létrehozásának lépései némileg eltérőek, attól függően, hogy a forrás egy általános rendszerkép vagy egy speciális virtuális gép pillanatképe. 

### <a name="option-generalized"></a>Opció: Általános

1. A képdefiníciólapján válassza a Lap tetején a **Verzió hozzáadása lehetőséget.**
1. A **Régió ban**válassza ki azt a régiót, ahol a felügyelt lemezképet tárolja. A lemezképverziókat ugyanabban a régióban kell létrehozni, mint a felügyelt lemezképet, amelyből létrehozták őket.
1. A **Név**mezőbe írja be az *1.0.0*. A lemezkép verziónevének a fő verziónevet kell *követnie.* *kisebb*. *egész* számokat használva. 
1. A **Forrás képen**válassza ki a forrás felügyelt képet a legördülő menüből.
1. A **Kizárás a legutóbbiból mezőben**hagyja meg a *Szám*alapértelmezett értékét.
1. Az **Élettartam vége dátum a**naptárban olyan dátumot válasszon, amely néhány hónappal a jövőben lesz.
1. A **replikációban**hagyja az **alapértelmezett replikaszámot** 1-ként. Replikálnia kell a forrásrégióba, ezért hagyja az első replika az alapértelmezett, majd válasszon egy második replika *régiót,* hogy USA keleti régiója legyen.
1. Ha elkészült, válassza **a Véleményezés + create**lehetőséget. Az Azure ellenőrzi a konfigurációt.
1. Amikor a képverzió megfelel az ellenőrzésen, válassza a **Létrehozás gombot.**
1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

Eltarthat egy ideig, amíg replikálja a lemezképet az összes célrégióban.

### <a name="option-specialized"></a>Opció: Specializált

1. A képdefiníciólapján válassza a Lap tetején a **Verzió hozzáadása lehetőséget.**
1. A **Régióban**válassza ki azt a régiót, ahol a pillanatkép tárolják. A lemezkép-verziókat ugyanabban a régióban kell létrehozni, mint a forrást, amelyből létrehozták őket.
1. A **Név**mezőbe írja be az *1.0.0*. A lemezkép verziónevének a fő verziónevet kell *követnie.* *kisebb*. *egész* számokat használva. 
1. Az **operációs rendszer lemezének pillanatképében**válassza ki a pillanatképet a forrás virtuális gép a legördülő menüből. Ha a forrásvirtuális gépnek volt egy adatlemeze, amelyet fel szeretne tüntetni, válassza ki a megfelelő **logikai számát** a legördülő menüből, majd válassza ki az **adatlemez pillanatképét az adatlemez pillanatképéhez.** 
1. A **Kizárás a legutóbbiból mezőben**hagyja meg a *Szám*alapértelmezett értékét.
1. Az **Élettartam vége dátum a**naptárban olyan dátumot válasszon, amely néhány hónappal a jövőben lesz.
1. A **replikációban**hagyja az **alapértelmezett replikaszámot** 1-ként. Replikálnia kell a forrásrégióba, ezért hagyja az első replika az alapértelmezett, majd válasszon egy második replika *régiót,* hogy USA keleti régiója legyen.
1. Ha elkészült, válassza **a Véleményezés + create**lehetőséget. Az Azure ellenőrzi a konfigurációt.
1. Amikor a képverzió megfelel az ellenőrzésen, válassza a **Létrehozás gombot.**
1. Amikor a központi telepítés befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.

## <a name="share-the-gallery"></a>A galéria megosztása

Azt javasoljuk, hogy ossza meg a hozzáférést a képgaléria szintjén. Az alábbi végigvezeti az imént létrehozott galéria megosztásán.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
1. A bal oldali menüben válassza az **Erőforráscsoportok**lehetőséget. 
1. Az erőforráscsoportok listájában válassza a **myGalleryRG**lehetőséget. Megnyílik az erőforráscsoport panelje.
1. A **myGalleryRG** lap bal oldalán található menüben válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget. 
1. A **Szerepkör-hozzárendelés hozzáadása**csoportban válassza a **Hozzáadás**lehetőséget. Megnyílik **a Szerepkör-hozzárendelés hozzáadása** ablaktábla. 
1. A **Role (Szerepkör) csoportban**válassza az **Olvasó**lehetőséget.
1. A **hozzáférés hozzárendelése csoportban**hagyja meg az **Azure AD-felhasználó, -csoport vagy egyszerű szolgáltatás alapértelmezett beállítását.**
1. A **Kijelölés csoportban**írja be a meghívni kívánt személy e-mail címét.
1. Ha a felhasználó a szervezeten kívül van, akkor az Ez a **felhasználó e-mailt kap, amely lehetővé teszi számukra, hogy együttműködjenek a Microsofttal.** Jelölje ki az e-mail címmel rendelkező felhasználót, majd kattintson a **Mentés gombra.**

Ha a felhasználó a szervezeten kívül van, e-mailben meghívást kap a szervezethez való csatlakozásra. A felhasználónak el kell fogadnia a meghívást, majd láthatja a galériát és az összes képdefiníciót és verziót az erőforrások listájában.

