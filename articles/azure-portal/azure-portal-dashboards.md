---
title: Irányítópultok létrehozása és megosztása az Azure Portalon
description: Ez a cikk ismerteti, hogyan hozhat létre, testre szabhatja, közzéteheti és megoszthatja irányítópultok az Azure Portalon.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: e6eda3b522a5b46cd82ef29ae493891a624d3272
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459287"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Irányítópultok létrehozása és megosztása az Azure Portalon

Az irányítópultok a felhőbeli erőforrások koncentrált és rendezett nézetei az Azure Portalon. Az irányítópultokat munkaterületként használhatja, ahol gyorsan elindíthatja a napi műveletekhez szükséges feladatokat, és figyelheti az erőforrásokat. Például projektek, feladatok vagy felhasználói szerepkörök alapján hozhat létre egyéni irányítópultokat.

Az Azure Portal egy alapértelmezett irányítópultot biztosít kiindulási pontként. Az alapértelmezett irányítópult ot szerkesztheti. További irányítópultok létrehozása és testreszabása, valamint irányítópultok közzététele és megosztása, hogy azok más felhasználók számára is elérhetők legyenek. Ez a cikk bemutatja, hogyan hozhat létre új irányítópultot, szabhatja testre a felületet, valamint hogyan teheti közzé és oszthatja meg az irányítópultokat.

## <a name="create-a-new-dashboard"></a>Új irányítópult létrehozása

Ebben a példában létrehozunk egy új, privát irányítópultot, és hozzárendelünk egy nevet. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüben válassza az **Irányítópult**lehetőséget. Lehet, hogy az alapértelmezett nézet már az irányítópultra van beállítva.

    ![Az irányítópult megnyitása](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Válassza az **Új irányítópult lehetőséget.**

    ![Képernyőkép az új irányítópultról](./media/azure-portal-dashboards/create-new-dashboard.png)

    Ez a művelet megnyitja a **Mozaikgalériát,** ahonnan a csempéket kijelölheti, és egy üres rácsot, amelyből a csempéket elrendezheti.

    ![Képernyőkép a csempegyűjteményről és az üres rácsról](./media/azure-portal-dashboards/dashboard-name.png)

1. Jelölje ki a **Saját irányítópult** szövegét az irányítópult címkéjén, és adjon meg egy nevet, amely segít az egyéni irányítópult egyszerű azonosításában.

1. A **szerkesztési** módból való kilépéshez válassza a Kész testreszabás lehetőséget a lapfejlécen.

Az irányítópult nézet mostantól az új irányítópultot jeleníti meg. Az irányítópult neve melletti nyílra választva megtekintheti az Ön számára elérhető irányítópultokat. A lista tartalmazhat más felhasználók által létrehozott és megosztott irányítópultokat.

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése

Most szerkeszthetjük az irányítópultot az Azure-erőforrásokat képviselő csempék hozzáadásához, átméretezéséhez és elrendezéséhez.

### <a name="add-tiles-from-the-dashboard"></a>Csempék hozzáadása az irányítópultról

Csempék irányítópulthoz való hozzáadásához kövesse az alábbi lépéseket:

1. Válassza ![a](./media/azure-portal-dashboards/dashboard-edit-icon.png) Szerkesztés ikon **szerkesztése** lehetőséget az oldalfejlécből.

    ![Képernyőkép az irányítópult kiemeléséről szerkesztés](./media/azure-portal-dashboards/dashboard-edit.png)

1. Böngésszen a **mozaikgalériában,** vagy a keresőmező segítségével keresse meg a kívánt csempét.

1. A **Hozzáadás gombra** választva hozzáadhatja a csempét az irányítópulthoz az alapértelmezett mérettel és hellyel. Vagy húzza a csempét a rácsra, és helyezze oda, ahová szeretné.

> [!TIP]
> Ha egynél több szervezettel dolgozik, adja hozzá a **Szervezet identitáscsempét** az irányítópulthoz, hogy egyértelműen megjelenítse, mely szervezethez tartoznak az erőforrások.

### <a name="add-tiles-from-a-resource-page"></a>Csempék hozzáadása erőforráslapról

Van egy másik módja annak, hogy csempéket adjon az irányítópulthoz. Számos erőforráslap tartalmaz egy nyomógombos ikont a parancssávon. Ha az ikont választja, a forráslapot jelző csempe az aktuálisan aktív irányítópultra kerül. 

![Képernyőkép a lapvezérlősávról a pin ikonnal](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Csempék átméretezése vagy átrendezése

A csempék méretének módosításához vagy az irányítópult csempéinek átrendezéséhez hajtsa végre az alábbi lépéseket:

1. Válassza ![a](./media/azure-portal-dashboards/dashboard-edit-icon.png) Szerkesztés ikon **szerkesztése** lehetőséget az oldalfejlécből.

1. Válassza ki a helyi menüt a csempe jobb felső sarkában. Ezután válasszon egy csempeméretet. A bármilyen méretű csempék a jobb alsó sarokban egy "fogantyút" is tartalmaznak, amely lehetővé teszi a csempe kívánt méretére való húzását.

    ![Képernyőkép az irányítópultról, amelyen meg van nyitva a csempeméret menü](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Jelöljön ki egy csempét, és húzza a rács egy új helyére az irányítópult rendezéséhez.

### <a name="additional-tile-configuration"></a>További csempekonfiguráció

Egyes csempék több konfigurációt igényelhetnek a kívánt információk megjelenítéséhez. Például a **Metrikák diagram** csempét be kell állítani az **Azure Monitor**metrika megjelenítéséhez. A csempeadatok testreszabásával felülbírálhatja az irányítópult alapértelmezett időbeállításait.

Minden beállítandó csempe megjeleníti a **Mozaik szalagcímének konfigurálása** beállítást, amíg nem szabja testre a csempét. A csempe testreszabása:

1. A **szerkesztési** módból való kilépéshez válassza a Kész testreszabás lehetőséget a lapfejlécen.

1. Válassza ki a szalagcímet, majd végezze el a szükséges beállítást.

    ![Képernyőkép a konfigurációt igénylő csempéről](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> A markdown csempe lehetővé teszi, hogy egyéni, statikus tartalmat jelenítsen meg az irányítópulton. Ez lehet alapvető utasítás, kép, hivatkozások vagy akár kapcsolattartási adatok. A markdown csempe használatáról az [Egyéni tartalom megjelenítéséhez az Azure-irányítópultokon egy markdown csempe használata](azure-portal-markdown-tile.md)című témakörben talál további információt.

### <a name="customize-tile-data"></a>Csempeadatok testreszabása

Az irányítópulton lévő adatok automatikusan megjelenítik az elmúlt 24 óra tevékenységét. Ha csak ehhez a csempéhez más időtartamot szeretne megjeleníteni, kövesse az alábbi lépéseket:

1. Válassza a helyi menü **Csempeadatok testreszabása parancsát,** vagy a ![csempe bal felső sarkában lévő szűrőszűrőt.](./media/azure-portal-dashboards/dashboard-filter.png)

    ![Képernyőkép a csempe helyi menüjéről](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Jelölje be a jelölőnégyzetet, ha felül szeretné **írni az irányítópult időbeállításait a csempe szintjén**.

    ![Képernyőkép a csempe időbeállításainak beállításához](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Válassza ki a csempe megjelenítéséhez a kívánt időtartamot. Választhat az elmúlt 30 perc az elmúlt 30 nap, vagy egyéni tartományt.

1. Válassza ki a megjeleníteni kívánt időrészletességet. Az egyperces lépésektől az egy hónaposig bárhol megjelenhet.

1. Kattintson az **Alkalmaz** gombra.

## <a name="delete-a-tile"></a>Csempe törlése

Ha el szeretne távolítani egy csempét az irányítópultról, kövesse az alábbi lépéseket:

* Válassza a helyi menüt a csempe jobb felső sarkában, majd válassza az Eltávolítás az **irányítópultról parancsot.** Vagy

* A ![testreszabási](./media/azure-portal-dashboards/dashboard-edit-icon.png) módba való belépéshez válassza a **Szerkesztés** ikon szerkesztése lehetőséget. Mutasson a csempe jobb felső sarkában, majd a ![törlés ikonra](./media/azure-portal-dashboards/dashboard-delete-icon.png) lehetőséget választva távolítsa el a csempét az irányítópultról.

   ![Képernyőkép a csempe irányítópultról való eltávolításáról](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Irányítópult klónozása

Ha egy meglévő irányítópultot szeretne sablonként használni egy új irányítópulthoz, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy az irányítópult nézetben látható a másolni kívánt irányítópult.

1. Az oldalfejlécben ![válassza](./media/azure-portal-dashboards/dashboard-clone.png) a Klónikon Klón ikon **t.**

1. Az irányítópult egy példánya, melynek neve Az *irányítópult nevének* **klónozása** szerkesztési módban nyílik meg. A cikk előző lépéseivel átnevezheti és testreszabhatja az irányítópultot.

## <a name="publish-and-share-a-dashboard"></a>Irányítópult közzététele és megosztása

Amikor létrehoz egy irányítópultot, az alapértelmezés szerint privát, ami azt jelenti, hogy ön az egyetlen, aki láthatja. Ha az irányítópultokat mások számára is elérhetővé szeretné tenni, közzéteheti és megoszthatja őket. További információt az [Azure-irányítópultok megosztása szerepköralapú hozzáférés-vezérléssel című](azure-portal-dashboard-share-access.md)témakörben talál.

### <a name="open-a-shared-dashboard"></a>Megosztott irányítópult megnyitása

Megosztott irányítópult megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. Jelölje ki az irányítópult neve melletti nyilat.

1. Válasszon az irányítópultok megjelenített listájából. Ha a megnyitni kívánt irányítópult nem szerepel a listában:

    1. Válassza **az Összes irányítópult tallózása**lehetőséget.

        ![Képernyőkép az irányítópult kijelölési menüjéről](./media/azure-portal-dashboards/dashboard-browse.png)

    1. A **Típus** mezőben válassza a **Megosztott irányítópultok**lehetőséget.

        ![Képernyőkép az összes irányítópult kijelölési menüjéről](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Válasszon ki egy vagy több előfizetést. Az irányítópultok név szerinti szűréséhez szöveget is beírhat.

    1. Válasszon egy irányítópultot a megosztott irányítópultok listájából.

## <a name="delete-a-dashboard"></a>Irányítópult törlése

Privát vagy megosztott irányítópult végleges törléséhez kövesse az alábbi lépéseket:

1. Jelölje ki a törölni kívánt irányítópultot az irányítópult neve melletti listából.

1. Válassza ![a](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Törlés** ikon törlése lehetőséget az oldalfejlécből.

1. Magánjellegű irányítópult esetén kattintson az **OK** gombra a megerősítési párbeszédpanelen az irányítópult eltávolításához. Megosztott irányítópult esetén a megerősítést kérő párbeszédpanelen jelölje be a jelölőnégyzetet annak megerősítéséhez, hogy a közzétett irányítópult ot a továbbiakban nem tekinthetik meg mások. Ezután válassza **az OK gombot.**

    ![Képernyőkép a törlés megerősítéséről](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>További lépések

* [Azure-irányítópultok megosztása a szerepköralapú hozzáférés-vezérlés használatával](azure-portal-dashboard-share-access.md)
* [Programozott módon hozzon létre Azure-irányítópultokat](azure-portal-dashboards-create-programmatically.md)
