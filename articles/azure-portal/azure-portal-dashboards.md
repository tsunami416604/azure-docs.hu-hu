---
title: Az Azure portal irányítópultok létrehozása és megosztása |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozhat létre és szerkeszthet irányítópultokat az Azure Portalon.
services: azure-portal
documentationcenter: ''
author: sewatson
manager: doubeby
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/06/2016
ms.author: kfollis
ms.openlocfilehash: 693e973fb988a57c15b4ea2fae47f16b4ff39011
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56818578"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Az Azure Portalon irányítópultok létrehozása és megosztása
Több irányítópultokat hozhat létre, és megoszthatja másokkal, akik hozzáférhetnek az Azure-előfizetést a.  Ez a cikk létrehozása, szerkesztése, közzététel és irányítópultokhoz való hozzáférés kezelésére való használatának alapvető kerül.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása
Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.  

![irányítópult létrehozása](./media/azure-portal-dashboards/new-dashboard.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat.  Ebben a módban az összecsukható csempetárral átveszi a bal oldali navigációs menüben.  A csempe-katalógus lehetővé teszi, az Azure-erőforrások különböző módokon csempék keresése: szerint tallózhat [erőforráscsoport](../azure-resource-manager/resource-group-overview.md#resource-groups), erőforrás típusa, a [címke](../azure-resource-manager/resource-group-using-tags.md), vagy az erőforrás keresése név alapján.  

![testre szabhatja az irányítópultot](./media/azure-portal-dashboards/customize-dashboard.png)

Csempék hozzáadása húzással őket irányítópult felületre akarja.

Van egy új kategóriába **általános** csempék, amelyek nem tartoznak az adott erőforrás esetében.  Ebben a példában azt Markdown csempe rögzítése.  Ez a csempe használatával egyéni tartalom hozzáadása az irányítópulthoz.  A csempe támogatja az egyszerű szöveges, [Markdown-szintaxisa](https://daringfireball.net/projects/markdown/syntax), és a egy korlátozott számú HTML.  (A biztonsági, például az beszúrása nem hajtható végre `<script>` címkék, vagy bizonyos stílusának elemmel, CSS, amely a portálon.) 

![Adja hozzá a markdown](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése
Miután létrehozta az irányítópultot, a csempe katalógus vagy panelek csempe reprezentációja csempéit rögzítheti. Most rögzítése leképezése az létrehoztuk az erőforráscsoportot. Az elem böngészésekor, vagy az erőforráscsoport panelen vagy PIN-kódot is. Az erőforráscsoport csempe reprezentációja rögzítés mindkét módszerénél eredményez.

![Rögzítés az irányítópulton](./media/azure-portal-dashboards/pin-to-dashboard.png)

Az elemet rögzíthet, után megjelenik az irányítópulton.

![Irányítópult megtekintése](./media/azure-portal-dashboards/view-dashboard.png)

Most, hogy egy Markdown-csempe van, és a egy erőforráscsoportot az irányítópulton rögzített, lehet átméretezheti és átrendezheti a csempéket be egy megfelelő elrendezés.

Viszi, és válassza a "...", vagy kattintson a jobb gombbal a csempére láthatja a környezetfüggő parancsokat az adott csempéhez. Alapértelmezés szerint a rendszer két elemet:

1. **Rögzítés feloldása az irányítópulton** – eltávolítja a csempét az irányítópulton
2. **Testre szabhatja** – kerül testreszabási módban

![csempe testreszabása](./media/azure-portal-dashboards/customize-tile.png)

Kiválasztásával testreszabása, átméretezheti és átrendezheti a csempéket. Egy csempe átméretezéséhez jelölje be az új méret a helyi menüben, az alábbi képen látható módon.

![csempe átméretezése](./media/azure-portal-dashboards/resize-tile.png)

Vagy, ha a csempe támogatja a tetszőleges méretű, áthúzhatja a kívánt méretet a jobb alsó sarokban.

![csempe átméretezése](./media/azure-portal-dashboards/resize-corner.png)

Csempék átméretezése, miután az irányítópult megtekintésére.

![nézet csempe](./media/azure-portal-dashboards/view-tile.png)

Amikor elkészült, egy irányítópult testre szabása, egyszerűen válassza ki a **testreszabás kész** való kilépéshez testreszabási módban, vagy kattintson a jobb gombbal, és válassza ki **testreszabás kész** a helyi menüből.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Egy irányítópult közzététele és hozzáférés-vezérlés kezelése
Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja.  Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Irányítópult megosztása](./media/azure-portal-dashboards/share-dashboard.png)

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. Az irányítópultok zökkenőmentesen integrálható az ökoszisztéma, az általunk megvalósított megosztott irányítópultok mint Azure-erőforrások (tehát nem oszthat meg egy e-mail-cím beírásával).  A portálon a csempék többsége által megjelenített információk biztonságos elérését vonatkoznak rájuk [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md). Access control szempontból a megosztott irányítópultok nem egy virtuális gép vagy a storage-fiók nem azonosak.  

Tegyük fel, az Azure-előfizetéssel rendelkezik, és a csapat tagjai vannak hozzárendelve a szerepköröket **tulajdonosa**, **közreműködői**, vagy **olvasó** az előfizetés.  Tulajdonos vagy közreműködő felhasználók listában, megtekintése, létrehozása, módosítása vagy törlése az adott előfizetésen belül irányítópultok képesek.  Felhasználók, akik olvasó lehet listázása és megtekintése az irányítópultok, de nem módosíthatja vagy törölheti őket.  Olvasó hozzáféréssel rendelkező felhasználók helyi módosításokat hajthat végre egy megosztott irányítópulthoz, de nem jelennek meg ezek a módosítások közzététele a kiszolgálóra.  Azonban elvégezhessék a saját használatra az irányítópult egy privát másolatot.  Mint mindig az irányítópulton lévő egyes csempék kényszerítése a saját hozzáférés-vezérlési szabályok megfelelnek erőforrások alapján.  

A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  

![Irányítópult közzététele](./media/azure-portal-dashboards/publish-dashboard.png)

Azt is beállíthatja egy irányítópult közzététele egy adott erőforráscsoporthoz.  A hozzáférés-vezérlést az irányítópult a hozzáférés-vezérlést, az erőforráscsoport megegyezik.  Az erőforráscsoport az erőforrások is kezelhetők a felhasználók is irányítópultokhoz való hozzáférést.

![erőforrás-csoportba irányítópult közzététele](./media/azure-portal-dashboards/publish-to-resource-group.png)

Az irányítópult közzététele után a **megosztás + hozzáférés** vezérlőpanelen frissítse, és azt a közzétett irányítópultot, és a telepítőre mutató az irányítópulton való felhasználói hozzáférés kezelését adatait.  Ez a hivatkozás elindítja a standard segítségével bármely Azure-erőforrás-hozzáférés kezelése szerepköralapú hozzáférés-vezérlés panel.  Mindig visszatérhet erre a nézetre kiválasztásával **megosztás**.

![hozzáférés-vezérlés kezelése](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>További lépések
* Erőforrások kezelése, tekintse meg [Azure-erőforrások kezelése az Azure portal használatával](../azure-resource-manager/manage-resources-portal.md).
* Erőforrások üzembe helyezéséhez lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

