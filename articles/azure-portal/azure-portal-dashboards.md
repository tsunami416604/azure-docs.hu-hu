---
title: Azure Portal irányítópultok létrehozása és megosztása | Microsoft Docs
description: Ez a cikk a Azure Portal irányítópultok létrehozását, testreszabását, közzétételét és megosztását ismerteti.
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
ms.author: mblythe
ms.openlocfilehash: a3b4d7cb33bf0da0c4431d76a54644208ea6468f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640456"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Irányítópultok létrehozása és megosztása az Azure Portalon

Az irányítópultok lehetővé teszik, hogy a Felhőbeli erőforrások Azure Portalban célzott és szervezett nézetet hozzon létre. Az irányítópultokat munkaterületként használhatja, ahol gyorsan elindíthatja a napi műveletek feladatait, és figyelheti az erőforrásokat.  Egyéni irányítópultokat hozhat létre a projektek, feladatok vagy felhasználói szerepkörök alapján, például:.  A Azure Portal kiindulási pontként egy alapértelmezett irányítópultot biztosít. Szerkesztheti az alapértelmezett irányítópultot, létrehozhat és testreszabhat további irányítópultokat, és közzéteheti és megoszthatja az irányítópultokat, hogy azok elérhetők legyenek más felhasználók számára. Ez a cikk bemutatja, hogyan hozhat létre egy új irányítópultot, hogyan szabhatja testre a felületet, és hogyan tehet közzé és oszthat meg irányítópultokat.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

Ebben a példában egy új, privát irányítópultot hozunk létre, és hozzárendelünk egy nevet. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Válassza ki az **irányítópultot** a bal oldali oldalsáv felső részén. Lehetséges, hogy az alapértelmezett nézet már be van állítva az irányítópultra.
1. Válassza az **+ új irányítópult**lehetőséget.

    ![Képernyőfelvétel az alapértelmezett irányítópultról](./media/azure-portal-dashboards/dashboard-new.png)

4. Ez a művelet megnyitja a **csempe-tárat**, amelyből kiválaszthatja a csempéket, és egy üres rácsot, ahol a csempéket rendezni fogja.

    ![Képernyőfelvétel a csempe és az üres rácsról](./media/azure-portal-dashboards/dashboard-name.png)

5. Jelölje ki a **saját irányítópult** szövegét az irányítópult címkéjén, és adjon meg egy nevet, amely megkönnyíti az egyéni irányítópultok azonosítását.
1. A szerkesztési mód kilépéséhez válassza az oldal fejlécének **kész Testreszabás** elemét.

Az irányítópult nézet most már az üres irányítópultot jeleníti meg. Válassza az irányítópult neve melletti legördülő listát, hogy az elérhető irányítópultok megjelenjenek – a lista tartalmazhat olyan irányítópultokat is, amelyeket más felhasználók hoztak létre és oszthatnak meg.

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése

Most szerkesszük az irányítópultot az Azure-erőforrásokat jelölő csempék hozzáadásához, átméretezéséhez és rendezéséhez.

### <a name="add-tiles"></a>Csempék hozzáadása

A csempék irányítópulthoz való hozzáadásához kövesse az alábbi lépéseket:
1. Válassza ki ![szerkesztés ikont](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Szerkesztés** az oldal fejlécében.

    ![Képernyőkép az irányítópult kijelölésének szerkesztéséről](./media/azure-portal-dashboards/dashboard-edit.png)

2. Tallózással keresse meg a **csempét** , vagy a Keresés mező használatával keresse meg a kívánt csempét.
1. A **Hozzáadás** gombra kattintva automatikusan hozzáadhatja a csempét az irányítópulthoz alapértelmezett mérettel és hellyel. Vagy húzza a csempét a rácsra, és helyezze a kívánt helyre.

Számos erőforrás-lap (más néven "Blades") tartalmaz egy gombostű ikont a parancssáv. Ha az ikont választja, a forrás lapot jelképező csempe a jelenleg aktív irányítópultra van rögzítve. Ez a módszer a csempék irányítópulthoz való hozzáadásának másik módja.

![Képernyőkép az oldal parancssáv rögzítési ikonnal](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> Ha egynél több szervezettel dolgozik, vegye fel a **szervezeti identitás** csempét az irányítópultra, hogy egyértelműen megmutassa, melyik szervezethez tartoznak az erőforrások.
>
>
### <a name="resize-or-rearrange-tiles"></a>Csempék átméretezése vagy átrendezése
A csempe méretének módosításához vagy az irányítópult csempéi átrendezéséhez kövesse az alábbi lépéseket:

1. Válassza ki ![szerkesztés ikont](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Szerkesztés** az oldal fejlécében.
1. Válassza a helyi menüt a csempe jobb felső sarkában. Ezután válassza ki a csempe méretét. A méretet támogató csempék a jobb alsó sarokban lévő "Handle" kifejezést is tartalmazzák, amely lehetővé teszi, hogy a csempét a kívánt méretre húzza.

   ![Képernyőkép az irányítópultról a csempe mérete menü megnyitva](./media/azure-portal-dashboards/dashboard-tile-resize.png)

3. Válasszon ki egy csempét, és húzza a rács egy új helyére az irányítópult elrendezéséhez.

### <a name="additional-tile-configuration"></a>További csempe-konfiguráció

Előfordulhat, hogy egyes csempék több konfigurációt igényelnek a kívánt információk megjelenítéséhez. Például a **metrikák diagram** csempét úgy kell beállítani, hogy a metrika megjelenjen a **Azure monitor**. Az irányítópult alapértelmezett időbeállításainak felülbírálásához emellett testre is szabhatja a csempét.

Minden olyan csempe, amelyet be kell állítani, egy **csempe** -szalagcímet jelenít meg, amíg testre nem szabja a csempét. Válassza ki a szalagcímet, majd hajtsa végre a szükséges beállításokat.

![A konfigurációt igénylő csempe képernyőképe](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> A Markdown csempe lehetővé teszi az egyéni, statikus tartalmak megjelenítését az irányítópulton. Ez lehet az alapszintű utasítások, a képek, a hiperhivatkozások halmaza, vagy akár kapcsolattartási adatok is. A Markdown csempe használatával kapcsolatos további információkért lásd: [Egyéni Markdown csempe használata](azure-portal-markdown-tile.md).
>
>
### <a name="customize-tile-data"></a>Csempe-adattárolás testreszabása

Az irányítópulton lévő adatműveletek automatikusan megjelenítik az elmúlt 24 órában végzett tevékenységet. Ha egy másik időtartományt szeretne megjeleníteni ehhez a csempéhez, kövesse az alábbi lépéseket:

1. Jelölje be a ![szűrő ikont a csempe bal felső sarkában található szűrő ikon](./media/azure-portal-dashboards/dashboard-filter.png) a helyi menüben a **csempe adatainak testreszabása** elemre.

   ![Képernyőfelvétel a csempe helyi menüjéről](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

2. Jelölje be a jelölőnégyzetet az **irányítópult időbeállításainak felülbírálásához a csempe szintjén**.

   ![Képernyőfelvétel a párbeszédpanelről a csempe-idő beállításainak konfigurálásához](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

3. Válassza ki a csempéhez megjelenítendő időtartományt. Az elmúlt 30 percből az elmúlt 30 napra kiválaszthat, vagy egyéni tartományt is meghatározhat.
1. Válassza ki a megjelenítendő idő részletességét. Egy percen belül bárhol megjelenítheti az egy hónapos növekményeket.
1. Kattintson az **Alkalmaz** gombra.

## <a name="delete-a-tile"></a>Csempe törlése

Ha el szeretne távolítani egy csempét az irányítópultról, kövesse az alábbi lépéseket:

* Válassza a helyi menüt a csempe jobb felső sarkában, majd válassza az **Eltávolítás az irányítópultról**lehetőséget. Vagy
* A testreszabási mód megadásához válassza ![szerkesztés ikon](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Szerkesztés** elemét. Vigye a kurzort a csempe jobb felső sarkában, majd válassza az ![törlés ikont](./media/azure-portal-dashboards/dashboard-delete-icon.png) törlés ikont a csempe az irányítópultról való eltávolításához.

   ![Képernyőfelvétel: csempe eltávolítása az irányítópultról](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Irányítópult klónozása

Ha egy meglévő irányítópultot sablonként szeretne használni egy új irányítópulthoz, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az irányítópult nézet a másolni kívánt irányítópultot jeleníti meg.
1. Az oldal fejlécében válassza ![klónozás ikon](./media/azure-portal-dashboards/dashboard-clone.png) **klón**elemet.
1. Az irányítópult "az *irányítópult nevének*klónozása" nevű másolata szerkesztési módban nyílik meg. A cikk előző lépéseivel átnevezheti és testreszabhatja az irányítópultot.

## <a name="publish-and-share-a-dashboard"></a>Irányítópult közzététele és megosztása

Amikor létrehoz egy irányítópultot, az alapértelmezés szerint magánjellegű, ami azt jelenti, hogy Ön az egyetlen, aki láthatja. Ha az irányítópultokat mások számára is elérhetővé kívánja tenni, megoszthatja azokat más felhasználókkal. Először közzé kell tennie az irányítópultot Azure-erőforrásként. Egyéni irányítópult közzétételéhez és megosztásához kövesse az alábbi lépéseket:

1. Válassza ki ![megosztás ikont](./media/azure-portal-dashboards/dashboard-share-icon.png) **megosztás** az oldal fejlécében. Megjelenik a **megosztás + hozzáférés-vezérlés** űrlap.
1. Ellenőrizze, hogy megjelenik-e a helyes irányítópult neve.
1. Válassza ki az **előfizetés nevét**. Az előfizetéshez hozzáféréssel rendelkező felhasználók használhatják a megosztott irányítópultot. Az egyes csempék által képviselt erőforrásokhoz való hozzáférést az Azure szerepköralapú hozzáférés-vezérlés határozza meg.
1. Jelölje be a jelölőnégyzetet, ha közzé szeretné tenni ezt az irányítópultot az "irányítópultok" erőforráscsoporthoz a kiválasztott előfizetéshez. Vagy törölje a jelet a jelölőnégyzetből, és válasszon egy meglévő erőforráscsoporthoz való közzétételt.
1. Válassza ki az irányítópult erőforrásának helyét. Javasoljuk, hogy keresse meg az irányítópultot más erőforrásokkal. Megjegyzés: Ha a meglévő erőforráscsoportok közül választ, az irányítópult automatikusan az adott erőforráscsoporthoz kerül.
1. Kattintson a **Publish** (Közzététel) elemre.

   ![Képernyőkép az irányítópult-közzétételi párbeszédpanelről](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>Hozzáférés-vezérlés beállítása megosztott irányítópulton

Az irányítópult közzététele után az alábbi lépéseket követve kezelheti, hogy ki férhet hozzá az irányítópulthoz:

1. A **megosztás és hozzáférés-vezérlés** ablaktáblán válassza a **felhasználók kezelése**lehetőséget.

   ![Képernyőkép az irányítópult megosztása és a hozzáférés-vezérlés párbeszédpanelről](./media/azure-portal-dashboards/dashboard-share-access-control.png)

2. Megnyílik a **Access Control** lap. Ezen az oldalon áttekintheti valaki hozzáférési szintjét, vagy hozzáadhat egy új szerepkör-hozzárendelést. Ha itt ad hozzá szerepkör-hozzárendelést, engedélyeket ad az irányítópultnak.

> [!NOTE]
> A csempék a szervezet erőforrásainak reprezentatív nézetei. Az erőforrásokhoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérlési hozzárendelésen keresztül történik, és az engedélyek az előfizetéstől kezdve az erőforrásig lesznek örökölve. Az irányítópulthoz való hozzáférés nem rendel automatikusan engedélyeket az irányítópulton megjelenített erőforrásokhoz. További információ a megosztott irányítópultok és az erőforrások szerepköralapú hozzáférés-vezérlési engedélyeiről: [irányítópultok megosztása szerepköralapú hozzáférés-vezérléssel](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Megosztott irányítópult megnyitása

Megosztott irányítópult megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. Válassza ki az irányítópult neve melletti legördülő listát.
1. Válassza ki a megjelenített irányítópultok listáját, vagy **tallózással keresse meg az összes irányítópultot** , ha a megnyitni kívánt irányítópult nem szerepel a listában.

   ![Az irányítópult kiválasztási menüjének képernyőképe](./media/azure-portal-dashboards/dashboard-browse.png)

3. A **típus** mezőben válassza a **megosztott irányítópultok**lehetőséget.
1. Válasszon ki egy vagy több előfizetést. Szöveget is megadhat az irányítópultok név szerinti szűréséhez.
1. Válasszon egy irányítópultot a megosztott irányítópultok listájáról.

## <a name="delete-a-dashboard"></a>Irányítópult törlése

Egy privát vagy megosztott irányítópult végleges törléséhez kövesse az alábbi lépéseket:

1. Válassza ki a törölni kívánt irányítópultot az irányítópult neve melletti legördülő listából.
1. Válassza ![delete **(Törlés) ikont](./media/azure-portal-dashboards/dashboard-delete-icon.png) az** oldal fejlécében.
1. Privát irányítópult esetén a megerősítő párbeszédpanelen kattintson az **OK gombra** az irányítópult eltávolításához. Megosztott irányítópult esetén a megerősítő párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy a közzétett irányítópultot mások nem fogják megtekinteni. Ezután válassza az **OK** lehetőséget.

   ![Képernyőkép a törlés megerősítéséről](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Következő lépések

* [Irányítópultok megosztása szerepköralapú hozzáférés-vezérléssel](azure-portal-dashboard-share-access.md)
* [Azure-irányítópultok programozott létrehozása](azure-portal-dashboards-create-programmatically.md)
