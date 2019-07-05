---
title: Az Azure portal irányítópultok létrehozása és megosztása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan létrehozását, testreszabását, közzététele és oszthat meg irányítópultokat az Azure Portalon.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/01/2019
ms.author: kfollis
ms.openlocfilehash: 8dd1349ca9ab62484eb6693291e3b869ff079dc1
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537390"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Az Azure Portalon irányítópultok létrehozása és megosztása

Az irányítópultok, hogy célzott és rendezettebb nézet létrehozása az Azure Portalon, a felhőalapú erőforrások révén. Az irányítópultok használjuk egy munkaterületet, ahol gyorsan indítsa el a napi operatív feladatok és erőforrások monitorozása.  Projektek, feladatok és felhasználói szerepkörök, például alapuló egyéni irányítópultokat hozhat létre.  Az Azure portal tartalmaz egy alapértelmezett irányítópultot kiindulási pontként. Szerkesztheti az alapértelmezett irányítópult, hozzon létre és további irányítópultok testreszabása és közzététele és oszthat meg irányítópultokat, hogy elérhetők legyenek más felhasználók számára. Ez a cikk azt ismerteti, hogyan hozzon létre egy új irányítópultot, a felület testreszabása és közzététele és oszthat meg irányítópultokat.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

Ebben a példában hozunk létre egy új, privát irányítópultot, és rendeljen egy nevet. Kövesse az alábbi lépéseket a kezdéshez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki **irányítópult** a felső részén a bal oldalsávon. Az alapértelmezett nézet már be lehet állítani az irányítópulton.
1. Válassza ki **+ új irányítópult**.

    ![Az alapértelmezett irányítópult képernyőképe](./media/azure-portal-dashboards/dashboard-new.png)

4. Ez a művelet megnyitja a **Csempetárral**, amelyen kiválaszthatja a csempéket, és a egy üres táblát, hol fogja elrendezése a csempék.

    ![Képernyőkép a csempetárral és üres rács](./media/azure-portal-dashboards/dashboard-name.png)

5. Válassza ki a **saját irányítópult** szöveget az irányítópult címkét, és adja meg, amelyek segítségével könnyen azonosíthatja azokat az egyéni irányítópult nevét.
1. Válassza ki **testreszabás kész** mód szerkesztése az oldal fejlécében való kilépéshez.

Az irányítópult-nézet az üres irányítópult mostantól látható. Válassza ki az Ön számára elérhető irányítópultok megtekintéséhez az irányítópult neve melletti legördülő – a lista tartalmazhatnak, amelyek más felhasználók létrehozott és megosztott irányítópultokat.

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése

Most tekintsük szerkesztése az irányítópultra való hozzáadása, átméretezése és elrendezése a csempék, amelyek az Azure-erőforrások.

### <a name="add-tiles"></a>Csempék hozzáadása

Csempék hozzáadása egy irányítópulthoz, kövesse az alábbi lépéseket:
1. Válassza ki ![Szerkesztés ikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** az oldal fejlécében.

    ![Képernyőkép az irányítópult szerkesztése kiemelése](./media/azure-portal-dashboards/dashboard-edit.png)

2. Keresse meg a **Csempetárral** , vagy keresse meg a csempét, melyhez a keresőmező használatával.
1. Válassza ki **Hozzáadás** automatikusan a csempe hozzáadása az irányítópult egy alapértelmezett méret és a helyen. Vagy húzza át a csempét a rácshoz, és helyezze el azt.

Sok erőforrás lapok (más néven "panelek") tartalmazza a rajzszög ikonra a parancssávon. Az ikont választja, ha a forrás lap jelölő csempe van rögzítve az irányítópulton, amely a jelenleg aktív. Ez a módszer egy alternatív módot adhat csempét az irányítópulton.

![Képernyőkép a parancssávon lap PIN-kód ikon](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Ha egynél több szervezet dolgozik, vegye fel a **szervezeti azonosító** melyik szervezetben az erőforrások egyértelműen megjelenítése az irányítópulton csempe tartozik.
>
>
### <a name="resize-or-rearrange-tiles"></a>Átméretezheti vagy átrendezheti a csempéket
A csempe méretének módosítása vagy átrendezheti a csempéket az irányítópulton, kövesse az alábbi lépéseket:

1. Válassza ki ![Szerkesztés ikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** az oldal fejlécében.
1. A helyi menüben válassza a csempe jobb felső sarkában található. Ezután válassza a csempe méretéhez. Csempék, amelyek bármilyen méretű is vegye fel a "leíró" jobb alsó sarokban, amely lehetővé teszi a csempe húzza a kívánt méretre.

   ![Képernyőkép a csempe méretének menüben nyissa meg az irányítópulton](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Válasszon ki egy csempét, és húzza azt az új helyre a rácson az irányítópult elrendezése.

### <a name="additional-tile-configuration"></a>További csempe konfigurálása

Egyes csempék szükség lehet további konfigurációs információk megjelenítéséhez. Például a **metrikadiagram** csempe rendelkezik egy metrikát a megjelenítendő beállítását **Azure Monitor**. Az Irányítópult alapértelmezett időbeállításainak felülbírálása a csempe adatai is testreszabhatja.

Bármelyik csempére, amelyet úgy jeleníti meg egy **konfigurálása csempe** szalag, amíg a csempe testreszabása. Válassza ki, hogy a szalagcím, majd hajtsa végre a szükséges beállításokat.

![Képernyőkép a csempét, amely a konfigurálást igényel](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown csempe lehetővé teszi egyéni, a statikus tartalom megjelenítése az irányítópulton. Ez sikerült alapvető útmutatás látható, kép, hivatkozások csoportja, vagy akár kapcsolattartási adatait. Markdown csempe használatával kapcsolatos további információkért lásd: [használata egy egyéni markdown-csempe](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Csempe adatainak testreszabása

Adatok az irányítópulton a tevékenység automatikusan megjeleníti az elmúlt 24 órában. Csak a csempe a különböző azt az időtartományt, megjelenítéséhez kövesse az alábbi lépéseket:

1. Válassza ki a ![szűrő ikon](./media/azure-portal-dashboards/dashboard-filter.png) szűrő ikonra a bal felső sarkában a csempére, vagy válasszon **testreszabás csempék adatainak** a helyi menüből.

   ![Csempe helyi menü képernyőképe](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. A jelölőnégyzet bejelölésével **a csempe szintjén az irányítópult időbeállításainak felülbírálása**.

   ![Idő csempebeállítások konfigurálása párbeszédpanel képernyőképe](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Válassza ki a csempe megjelenítése teljes időtartamát. Az elmúlt 30 perctől az elmúlt 30 napban, válassza ki vagy adja meg egy egyéni tartományt is.
1. Válassza ki az idő részletessége megjelenítéséhez. Megjelenítheti bárhol az egy perces időszakokra bontva, egy hónapig.
1. Kattintson az **Alkalmaz** gombra.

## <a name="delete-a-tile"></a>Csempe törlése

Irányítópult csempe eltávolításához kövesse az alábbi lépéseket:

* A helyi menüben válassza a csempe jobb felső sarokban, majd válassza ki **Eltávolítás az irányítópultról**. Vagy
* Válassza ki ![Szerkesztés ikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **szerkesztése** , adja meg a testreszabási módban. Mutasson a csempe jobb felső sarokban, majd válassza ki a ![Törlés ikon](./media/azure-portal-dashboards/dashboard-delete-icon.png) Törlés ikon el szeretné távolítani a csempét az irányítópulton.

   ![Képernyőfelvétel: hogyan el szeretné távolítani a csempét az irányítópulton](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Egy irányítópult klónozása

Egy meglévő irányítópultot használ egy sablont egy új irányítópultot, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az irányítópult-nézet jelenik-e meg a másolni kívánt irányítópultot.
1. Válassza az oldal fejlécében ![Klónozás ikon](./media/azure-portal-dashboards/dashboard-clone.png) **Klónozás**.
1. Az irányítópultot, egy példányát "klónozza *az irányítópult neve*" megnyílik a szerkesztési módból. Ebben a cikkben az előző lépések használatával nevezze át, és testre szabhatja az irányítópultot.

## <a name="publish-and-share-a-dashboard"></a>Közzététel és az irányítópult megosztása

Létrehoz egy irányítópultot, esetén privát alapértelmezetten, ami azt jelenti, hogy Ön az egyetlen, akik láthatják őket. Ahhoz, hogy az irányítópultok másoknak is elérhető, más felhasználókkal megoszthatja őket. Először meg kell az Azure-erőforrásként irányítópult közzététele. Tehet közzé, és egyéni irányítópult megosztása, kövesse az alábbi lépéseket:

1. Válassza ki ![megosztás ikon](./media/azure-portal-dashboards/dashboard-share-icon.png) **megosztása** az oldal fejlécében. A **megosztás + hozzáférés szabályozása** képernyő jelenik meg.
1. Győződjön meg arról, hogy látható-e a megfelelő irányítópult nevét.
1. Válassza ki a **előfizetésnevet**. Az előfizetés-hozzáféréssel rendelkező felhasználók használhatják a megosztott irányítópultot. Az egyes csempékhez által képviselt-erőforrásokhoz való hozzáférés az Azure szerepköralapú hozzáférés-vezérlés határozza meg.
1. Jelölje be a "irányítópultok" erőforráscsoporthoz a kijelölt előfizetéshez tartozó az irányítópult közzététele. Vagy törölje a jelölőnégyzet jelölését, és ehelyett közzétenni egy meglévő erőforráscsoportot.
1. Adja meg az irányítópult erőforrás helyét. Azt javasoljuk, keresse meg az irányítópultot más erőforrásokkal. Megjegyzés: Ha úgy dönt, a meglévő erőforráscsoportok, az irányítópult helyezkedik el automatikusan az adott erőforráscsoporton.
1. Kattintson a **Publish** (Közzététel) elemre.

   ![Irányítópult közzététele párbeszédpanel képernyőképe](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>A megosztott irányítópult hozzáférés-vezérlés beállítása

Az irányítópult közzététele után kezelése, ki férhet hozzá az irányítópult az alábbi lépéseket:

1. Az a **megosztás + hozzáférés szabályozása** ablaktáblán válassza előbb **felhasználók kezelése**.

   ![Képernyőkép az irányítópult-megosztás + hozzáférés szabályozása párbeszédpanel](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. A **hozzáférés-vezérlés** lap megnyitásakor. Ezen a lapon tekintse át a hozzáférési szintet, ha valaki, vagy adjon hozzá egy új szerepkör-hozzárendelést. Itt egy szerepkör-hozzárendelés hozzáadásakor biztosítunk engedélyeket az irányítópulton.

> [!NOTE]
> Csempék nézetei reprezentatív a szervezet erőforrásaihoz. Erőforrásokhoz való hozzáférést szerepköralapú hozzáférés-vezérlési hozzárendeléssel kezeli, és az előfizetésből a erőforrás le az engedélyek öröklődnek. Hozzáférést ad az irányítópulton nem automatikusan engedélyeket az erőforrásokhoz az irányítópulton látható. Megosztott irányítópultok és az erőforrások szerepköralapú hozzáférés-vezérlési engedélyekkel kapcsolatos további információkért lásd: [irányítópultok megosztása szerepköralapú hozzáférés-vezérlés](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Nyisson meg egy megosztott irányítópultot

Keresse meg, és nyisson meg egy megosztott irányítópultot, kövesse az alábbi lépéseket:

1. Válassza ki az irányítópult neve melletti legördülő.
1. Az irányítópultok a megjelenő listából válassza vagy **összes irányítópult tallózása** Ha meg szeretné nyitni az irányítópultot nem szerepel a listán.

   ![Irányítópult kiválasztása menü képernyőképe](./media/azure-portal-dashboards/dashboard-browse.png)

3. Az a **típus** mezőben válassza **megosztott irányítópultokat**.
1. Válassza ki egy vagy több előfizetés. Irányítópultok szűrése név alapján a szöveget is megadhat.
1. Válasszon egy irányítópultot a megosztott irányítópultok listájából.

## <a name="delete-a-dashboard"></a>Irányítópult törlése

Véglegesen törli a saját vagy megosztott irányítópultot, kövesse az alábbi lépéseket:

1. Válassza ki az irányítópultot, törli az irányítópult neve melletti legördülő listából.
1. Válassza ki ![Törlés ikon](./media/azure-portal-dashboards/dashboard-delete-icon.png) **törlése** az oldal fejlécében.
1. A privát irányítópultot, válassza ki a **OK** az irányítópult eltávolítása a megerősítő párbeszédpanelen. A megosztott irányítópultot, a megerősítési párbeszédpanelen válassza ki a jelölőnégyzetet annak megerősítéséhez, hogy a közzétett irányítópult már nem a mások által megtekinthető. Ezután válassza az **OK** lehetőséget.

   ![Képernyőkép a törlésének megerősítése](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>További lépések

* [Irányítópultok megosztása szerepköralapú hozzáférés-vezérlés](azure-portal-dashboard-share-access.md)
* [Azure-irányítópultok szoftveres létrehozása](azure-portal-dashboards-create-programmatically.md)
