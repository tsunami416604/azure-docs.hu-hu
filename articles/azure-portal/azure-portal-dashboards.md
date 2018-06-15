---
title: Hozzon létre és osszon meg az Azure portál irányítópultok |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan hozhat létre és szerkeszthet irányítópultok az Azure portálon.
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
ms.author: sewatson
ms.openlocfilehash: 3bdeb3255c8dc97d2867eca0b9cdec8223d31b21
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165349"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Hozzon létre és osszon irányítópultok az Azure-portálon
Több irányítópultot létrehozni, és megoszthatja őket másokkal, akik hozzáférhetnek a Azure-előfizetését.  Ez a cikk végighalad létrehozására, szerkesztésére, közzétételének és irányítópultokhoz való hozzáférés kezelése a alapjait.

## <a name="create-a-dashboard"></a>Irányítópult létrehozása
Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.  

![irányítópult létrehozása](./media/azure-portal-dashboards/new-dashboard.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat.  Ha ebben a módban a összecsukható csempe gyűjtemény átveszi a bal oldali navigációs menü.  A csempe gyűjtemény lehetővé teszi, hogy az Azure-erőforrások különböző módokon csempék találhatók: által tallózással [erőforráscsoport](../azure-resource-manager/resource-group-overview.md#resource-groups), az erőforrás írja be, ha [címke](../azure-resource-manager/resource-group-using-tags.md), vagy az erőforrás neve alapján keres.  

![testre szabhatja az irányítópultot](./media/azure-portal-dashboards/customize-dashboard.png)

Vegyen fel csempéket húzással őket irányítópult felületre bárhová be.

Van egy új kategóriába **általános** csempék, amelyek nincsenek társítva van egy adott erőforráshoz.  Ebben a példában a Markdown csempe azt rögzíti.  Ez a csempe használatával ad hozzá az egyéni tartalom az irányítópulton való rögzítéséhez.  A csempe támogatja az egyszerű szöveges [Markdown-szintaxis](https://daringfireball.net/projects/markdown/syntax), és a HTML egy korlátozott körét.  (A biztonsági, mint szúrjon nem hajtható végre `<script>` címkéket, vagy bizonyos stílusbeállításokat elemmel, amely a portál CSS.) 

![markdown hozzáadása](./media/azure-portal-dashboards/add-markdown.png)

## <a name="edit-a-dashboard"></a>Irányítópult szerkesztése
Miután létrehozta az irányítópulton, PIN-kód csempét a csempe vagy a paneleken csempe ábrázolását. Most PIN-kód az erőforráscsoport ábrázolását. A cikk böngészésekor, vagy az erőforráscsoport panel vagy PIN-kódot is. Az erőforráscsoport csempe ábrázolását rögzítési mindkét megközelítés eredményez.

![Rögzítés az irányítópulton](./media/azure-portal-dashboards/pin-to-dashboard.png)

Miután a cikk, megjelenik az irányítópulton.

![Irányítópult nézet](./media/azure-portal-dashboards/view-dashboard.png)

Most, hogy egy Markdown csempe van, és egy erőforráscsoport rögzítve az irányítópulton, azt átméretezése, és átrendezheti a csempéket megfelelő elrendezésekben.

Rámutató és válassza a "...", vagy kattintson a jobb gombbal a mozaikokra tekintheti meg a környezetfüggő parancsok az adott csempe. Alapértelmezés szerint nincsenek két elemek:

1. **Az irányítópult rögzítését** – a csempe eltávolítása az irányítópulton
2. **Testre szabhatja** – kerül üzemmód testreszabása

![Mozaik elrendezés testreszabása](./media/azure-portal-dashboards/customize-tile.png)

Kiválasztásával testreszabása, méretezze át, és újra sorrendbe állítja a csempéket. Egy csempe átméretezéséhez adja meg új a helyi menüben a következő ábrán látható módon.

![Automatikus oszlopszélesség csempe](./media/azure-portal-dashboards/resize-tile.png)

Vagy, ha a csempe támogatja bármilyen méretű, áthúzhatja a kívánt méretet a jobb alsó sarokban.

![Automatikus oszlopszélesség csempe](./media/azure-portal-dashboards/resize-corner.png)

Csempék átméretezése, miután az irányítópult megtekintéséhez.

![csempéje](./media/azure-portal-dashboards/view-tile.png)

Miután befejezte a testreszabása egy irányítópultot, egyszerűen jelölje ki a **végzett Testreszabás** való kilépéshez testreszabási módban, vagy kattintson a jobb gombbal, és válassza ki **végzett Testreszabás** a helyi menüből.

## <a name="publish-a-dashboard-and-manage-access-control"></a>Irányítópult közzétételét és kezelését a hozzáférés-vezérlés
Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja.  Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Irányítópult megosztása](./media/azure-portal-dashboards/share-dashboard.png)

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. Irányítópultok zökkenőmentesen integrálja az ökoszisztéma, azt korábban megosztott irányítópultok, megvalósítva Azure-erőforrások (tehát nem lehet megosztani, írja be az e-mail címet).  A csempék a portál által megjelenített információk elérését az irányadó [Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control/role-assignments-portal.md). Access control szempontból megosztott irányítópultok ugyanazok a virtuális gép vagy egy tárfiókot.  

Tegyük fel, Azure-előfizetéssel rendelkezik, és a csoport tagjai hozzárendelt szerepkörök adott biztonsági **tulajdonos**, **közreműködő**, vagy **olvasó** az előfizetés.  Felhasználók, akik a tulajdonos vagy közreműködő szerepkörrel rendelkező személyek képesek listában, megtekintése, létrehozása, módosítása vagy törlése irányítópultok adott előfizetésen belül.  Olvasók felhasználók lehet lista, és tekintse meg az irányítópultok, de nem módosítsa vagy törölje őket.  Olvasási joggal rendelkező felhasználók lehet a helyi módosíthatja is egy megosztott irányítópult, de a rendszer nem tudja közzétenni az ezeket a módosításokat a kiszolgálóra.  Azonban akkor az irányítópult saját használatra titkos másolatát.  Egyes csempéit az irányítópultra, mindig a saját alapján megfelelnek az erőforrások hozzáférés-vezérlési szabályok kényszerítése.  

A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  

![Irányítópult közzététele](./media/azure-portal-dashboards/publish-dashboard.png)

Választhatja azt is, egy irányítópultot közzétételére egy adott erőforráscsoportban található.  A hozzáférés-vezérlés az irányítópult a hozzáférés-vezérlés az erőforráscsoport megegyezik.  Kezelheti az erőforrásokat, amelyeket a felhasználók az irányítópultok hozzáféréssel is rendelkeznek.

![Irányítópult erőforráscsoportba történő közzététele](./media/azure-portal-dashboards/publish-to-resource-group.png)

Az irányítópult közzététele után a **megosztás + hozzáférés** Vezérlőpulton frissítse, és azt a közzétett irányítópulton, többek között a felügyelheti a felhasználók hozzáférését az irányítópultra mutató hivatkozás információkat jelenít meg.  Ez a hivatkozás a szabványos szerepköralapú hozzáférés-vezérlés panel az Azure-erőforrásokkal hozzáférés kezelésére szolgáló indít.  Mindig kaphat vissza erre a nézetre kiválasztásával **megosztás**.

![hozzáférés-vezérlés kezelése](./media/azure-portal-dashboards/manage-access.png)

## <a name="next-steps"></a>További lépések
* Erőforrások kezeléséhez, tekintse meg a [kezelése Azure-erőforrások portálon keresztül](../azure-resource-manager/resource-group-portal.md).
* Erőforrások telepítéséhez lásd: [erőforrások a Resource Manager-sablonok és az Azure-portál telepítése](../azure-resource-manager/resource-group-template-deploy-portal.md).

