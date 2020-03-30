---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 04/29/2019
ms.author: rolyon
ms.custom: include file
ms.openlocfilehash: d791c4ba46587ac5709d72cb31bc76f087118b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67476247"
---
## <a name="create-one-or-more-access-reviews"></a>Egy vagy több hozzáférési ellenőrzés létrehozása

1. Új hozzáférési felülvizsgálat létrehozásához kattintson az **Új** gombra.

1. Nevezze el a hozzáférési felülvizsgálatot. Opcionálisan adjon leírást a felülvizsgálatnak. A név és a leírás megjelenik a véleményezők számára.

    ![Hozzáférési ellenőrzés létrehozása - Név és leírás áttekintése](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Állítsa be a **kezdési dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer történik, a létrehozáskor kezdődik, és egy hónap múlva ér véget. Módosíthatja a kezdési és befejezési dátumokat, hogy a hozzáférési felülvizsgálat a jövőben kezdődjön, és a kívánt hány napig tart.

    ![Kezdési dátum, gyakoriság, időtartam, befejezés, alkalmak száma és záró dátuma](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. A hozzáférési felülvizsgálat ismétlődővé tenni, módosítsa a **Gyakoriság** beállítást **egy időpontról** **hetire**, **havira,** **negyedévesre**, **évesre**vagy **félévente.** Az **Időtartam** csúszka vagy a szövegmező segítségével határozhatja meg, hogy az ismétlődő adatsorok egyes felülvizsgálatai hány napig legyenek megnyitva az ellenőrzők tőlük érkező adatok számára. A havi ellenőrzéshez beállíthatj maximális időtartam például 27 nap, hogy elkerülje az egymást átfedő értékeléseket.

1. A **Befejezés** beállítással megadhatja, hogyan lehet befejezni az ismétlődő hozzáférés-ellenőrzési sorozatot. A sorozat háromféleképpen végződhet: folyamatosan fut, hogy határozatlan ideig, egy adott dátumig vagy meghatározott számú előfordulás befejezéséig kezdje el az értékeléseket. Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda leállíthatja a sorozatot a létrehozás után, ha módosítja a dátumot a **Beállítások**ban, hogy az az adott napon végződjön.

1. A **Felhasználók csoportban** jelöljön ki egy vagy több szerepkört, amelynek tagságát felül szeretné vizsgálni.

    ![A felhasználók hatóköre a szerepkör-tagság áttekintésére](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Ha egynél több szerepkört választ ki, több hozzáférési felülvizsgálat jön létre. Ha például öt szerepkört választ ki, öt külön hozzáférési felülvizsgálatot hoz létre.

    Ha az Azure AD-szerepkörök hozzáférési felülvizsgálatát hozza létre, az alábbiakban egy példa látható a tagsági lista áttekintése.

    ![Tekintse át a tagsági ablaktáblát, amely felsorolja a kiválasztható Azure AD-szerepköröket](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Ha az Azure-erőforrás-szerepkörök hozzáférési felülvizsgálatát hozza létre, az alábbiakban egy példa látható a Tagsági lista áttekintése.

    ![Tekintse át a tagsági ablaktáblát, amely felsorolja a kiválasztható Azure-erőforrásszerepköröket](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Az **Ellenőrzők** csoportban jelöljön ki egy vagy több felhasználót az összes felhasználó áttekintéséhez. Vagy választhatja ki, hogy a tagok áttekintsék a saját hozzáférésüket.

    ![A kijelölt felhasználók vagy tagok véleményezőinek listája (saját)](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **Kijelölt felhasználók** – Akkor használja ezt a beállítást, ha nem tudja, kinek van szüksége hozzáférésre. Ezzel a beállítással hozzárendelheti az értékelést egy erőforrás-tulajdonoshoz vagy csoportkezelőhöz.
    - **Tagok (saját)** – Ezzel a beállítással a felhasználók saját szerepkör-hozzárendeléseikáttekintését is megszeretnék.

### <a name="upon-completion-settings"></a>A befejezési beállítások után

1. Ha meg szeretné adni, hogy mi történjen az ellenőrzés befejezése után, bontsa ki a **Befejezéskor beállítás szakaszt.**

    ![Befejezését követően beállítások automatikus alkalmazása, és felül kell vizsgálnia, hogy nem válaszol](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Ha automatikusan el szeretné távolítani a hozzáférést a megtagadott felhasználók számára, állítsa be az **Automatikus alkalmazás az eredményeket az erőforrásra** **az Engedélyezés beállításhoz.** Ha az ellenőrzés befejezésekor manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót **Letiltás gombra.**

1. A **Véleményező nem válaszol** listában adhatja meg, hogy mi történjen azon felhasználók esetében, akiket a véleményező nem ellenőriz a felülvizsgálati időszakon belül. Ez a beállítás nincs hatással azokat a felhasználókat, akiket az ellenőrzők manuálisan ellenőriznek. Ha a végső véleményező döntése Megtagadás, akkor a felhasználó hozzáférése törlődik.

    - **Nincs változás** - Hagyja változatlanul a felhasználó hozzáférését
    - **Hozzáférés eltávolítása** – A felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – A felhasználó hozzáférésének jóváhagyása
    - **Javaslatok ajánlásai** – A rendszer ajánlása a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások szakaszt.**

    ![Speciális beállítások a javaslatok megjelenítéséhez, jóváhagyáshoz, e-mail értesítésekhez és emlékeztetőkhöz](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Állítsa be a **Javaslatok megjelenítése** **az Engedélyezés beállításhoz,** hogy a rendszerjavaslatok a felhasználó hozzáférési adatai alapján jelenítse meg a véleményezőknek.

1. Állítsa **be a Jóváhagyás okának megkövetelése** **beállítást: Annak engedélyezése,** hogy a véleményezőnek meg kell adnia a jóváhagyás okát.

1. Állítsa be **a Mail-értesítéseket** **annak engedélyezése** beállításra, hogy az Azure AD e-mail-értesítéseket küldjön a véleményezőknek, amikor egy hozzáférési felülvizsgálat elindul, és a rendszergazdáknak, amikor az ellenőrzés befejeződik.

1. **Emlékeztetők** **beállítása az engedélyezéshez,** hogy az Azure AD emlékeztetők küldése a folyamatban lévő hozzáférési felülvizsgálatokról olyan ellenőrzőknek, akik nem fejezték be az értékelést.
