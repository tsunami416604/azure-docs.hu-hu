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
ms.openlocfilehash: 364d4a11772e6bb72e2e258503f3cce49dc61453
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66112259"
---
## <a name="create-one-or-more-access-reviews"></a>Hozzon létre egy vagy több hozzáférési felülvizsgálatok

1. Kattintson a **új** egy új hozzáférési felülvizsgálat létrehozása.

1. A hozzáférési felülvizsgálat neve. A felülvizsgálat igény szerint adjon meg leírást. A nevet és leírást a felülvizsgálóknak jelennek meg.

    ![Hozzáférési felülvizsgálat létrehozása – felülvizsgálat neve és leírása](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. Állítsa be a **kezdő dátum**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer végbemegy, létrejön egy időben kezdődik, és egy hónap alatt végződik. Módosíthatja a kezdő és záró dátumát férnek hozzá egy tekintse át kezdő legutóbbi és a jövőben azonban hány nappal kívánja.

    ![Hozzáférési felülvizsgálat - létrehozása kezdési és befejezési dátuma](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődési, módosítsa a **gyakorisága** beállítást **egyszer** való **heti**, **havi**,  **Negyedéves**, **évente**, vagy **félig annually**. Használja a **időtartama** meghatározásához, hány napig lesz az ismétlődő sorozatának kritika nyissa meg a felülvizsgálóknak a bemenetet a csúszka vagy a beviteli mezőben. Például a maximális időtartam, amely lehet a havi felülvizsgálat 27 nap, kerülje az átfedésben lévő értékelések.

1. Használja a **záró** a beállítás határozza meg az ismétlődő hozzáférés befejezése tekintse át a sorozat. A sorozat fejezheti be háromféleképpen: folyamatosan felülvizsgálatok határozatlan ideig, amíg egy adott dátumot, vagy egy meghatározott előfordulások száma befejezése után el futtatja. Azt, egy másik felhasználó rendszergazda vagy egy másik globális rendszergazda állíthatja le a sorozat létrehozása után a dátumot a módosításával **beállítások**, így az adott dátumon végződik.

1. Az a **felhasználók** területen válassza ki, hogy meg szeretné tekinteni a tagságát egy vagy több szerepkört.

    ![Hozzáférési felülvizsgálat létrehozása - felhasználók](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > Több szerepkör kiválasztása több hozzáférési felülvizsgálatok hoz létre. Ha például öt szerepkör kiválasztása hoz létre öt külön a hozzáférési felülvizsgálatok.

    Az Azure AD-szerepkörök hozzáférési felülvizsgálat létrehozásakor, az alábbiakban látható egy példa a felülvizsgálati tagsági lista.

    ![Hozzáférési felülvizsgálat létrehozása - szerepkörtagság felülvizsgálata](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    Hozzáférési felülvizsgálat Azure-erőforrásszerepkörök létrehozásakor, az alábbiakban látható egy példa a felülvizsgálati tagsági lista.

    ![Hozzáférési felülvizsgálat létrehozása - szerepkörtagság felülvizsgálata](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. Az a **felülvizsgálók** területen válassza ki egy vagy több személyt, tekintse át az összes felhasználó. Vagy tekintse át a saját hozzáférését tagokból ki.

    ![Hozzáférési felülvizsgálat - felülvizsgálók létrehozása](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **A kijelölt felhasználók** -használja ezt a beállítást, ha nem ismeri ki hozzá kell férnie. Ezzel a beállítással a felülvizsgálat hozzárendelheti egy erőforrás tulajdonosa vagy a csoport kezelőjének végrehajtásához.
    - **Tagok (saját)** -használja ezt a beállítást szeretné, hogy a felhasználók, ellenőrizze a saját szerepkör-hozzárendeléseket.

### <a name="upon-completion-settings"></a>A befejezést követő művelet beállításai

1. Adja meg, mi történik, a felülvizsgálat befejezése után, bontsa ki a **befejezést követő művelet beállításai** szakaszban.

    ![A befejezést követő művelet beállításai](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. Ha szeretne automatikusan remove hozzáférést a felhasználók számára, hogy el lett utasítva, **eredmények automatikus alkalmazása az erőforrás** való **engedélyezése**. Ha szeretné manuálisan alkalmazza az eredményeket, ha a felülvizsgálatot követően, állítsa a kapcsolót **letiltása**.

1. Használja a **a felülvizsgáló nem válaszol** listát használva adhatja meg, mi történik, a felhasználók számára, nem ellenőrzött felülvizsgálója ellenőrzi a felülvizsgálati időszak alatt. Ez a beállítás nem befolyásolja a felhasználók, akik korábban a többi felhasználó által manuálisan. Ha a végső felülvizsgáló döntési megtagadás, akkor a felhasználó hozzáférése törlődni fog.

    - **Nincs változás** -hagyja változatlanul a felhasználó hozzáférése
    - **Hozzáférés eltávolítása** – felhasználói hozzáférés letiltása
    - **Hozzáférés jóváhagyása** – felhasználói hozzáférés jóváhagyása
    - **Ajánlatok elfogadása** – a rendszer elutasítja az ajánlást igénybe, vagy hagyja jóvá a felhasználó a folyamatos hozzáférést

### <a name="advanced-settings"></a>Speciális beállítások

1. Szeretne megadni további beállításokat, bontsa ki a **speciális beállítások** szakaszban.

    ![Speciális beállítások](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. Állítsa be **javaslatok megjelenítése** való **engedélyezése** lehet megjeleníteni a felülvizsgálók a rendszer javaslatok alapján a felhasználó hozzáférési adatokat.

1. Állítsa be **a jóváhagyás oka szükséges** való **engedélyezése** , adja meg a jóváhagyás okát a felülvizsgálónak kötelező.

1. Állítsa be **az E-mail értesítések** való **engedélyezése** szeretné, hogy az Azure AD küldjön értesítő felülvizsgálóknak a hozzáférési felülvizsgálatok megkezdésekor, valamint a rendszergazdák felülvizsgálatok befejezésekor.

1. Állítsa be **emlékeztetők** való **engedélyezése** , az Azure AD emlékeztetőt küldjön a hozzáférési felülvizsgálatok folyamatban felülvizsgálóknak, akik még nem fejezték be a felülvizsgálatot.
