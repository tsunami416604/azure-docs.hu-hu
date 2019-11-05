---
title: A felügyelt fejlesztői portál elérése és testreszabása – Azure API Management | Microsoft Docs
description: Megtudhatja, hogyan használhatja a API Management a fejlesztői portál felügyelt verzióját.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 62fb5552d86a802c3ba0213d99be2f91f21025e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472127"
---
# <a name="access-and-customize-developer-portal"></a>A fejlesztői portál elérése és testreszabása

A fejlesztői portál egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, és hogyan kérhetnek hozzáférést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fejlesztői portál felügyelt verziójának elérése
> * Navigáljon a felügyeleti felületén
> * A tartalom testreszabása
> * A módosítások közzététele
> * A közzétett portál megtekintése

A fejlesztői portálról az [Azure API Management fejlesztői portál áttekintésében](api-management-howto-developer-portal.md)talál további információt.

![API Management fejlesztői portál – felügyeleti mód](media/api-management-howto-developer-portal-customize/cover.png)

> [!WARNING]
> A fejlesztői portál jelenleg API Management szolgáltatások számára van bevezetve.
> Ha a szolgáltatás újonnan lett létrehozva vagy fejlesztői szintű szolgáltatás, akkor már rendelkezik a legújabb verzióval. Ellenkező esetben problémák merülhetnek fel (például a közzétételi funkcióval). A szolgáltatás bevezetésének befejezése a 2019. november 11-én történik. 

## <a name="prerequisites"></a>Előfeltételek

- Végezze el a következő rövid útmutatót: [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
- Azure API Management-példány importálása és közzététele. További információ: [Importálás és közzététel](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>A portál elérése rendszergazdaként

Kövesse az alábbi lépéseket a portál felügyelt verziójának eléréséhez.

1. Nyissa meg a Azure Portal API Management Service-példányát.
1. Kattintson a **fejlesztői portál** gombra a felső navigációs sávon. Megnyílik a portál felügyeleti verzióját tartalmazó új böngésző lap.

## <a name="understand-the-portals-administrative-interface"></a>A portál felügyeleti felületének megismerése

### <a name="default-content"></a>Alapértelmezett tartalom 

Ha első alkalommal éri el a portált, az alapértelmezett tartalom automatikusan kiépítve lesz a háttérben. Az alapértelmezett tartalom úgy lett kialakítva, hogy bemutassa a portál képességeit, és minimálisra csökkentse a portál személyre szabásához szükséges testreszabások mennyiségét. Ha többet szeretne megtudni arról, hogy mit tartalmaz a portál tartalma az [Azure API Management fejlesztői portálon – áttekintés](api-management-howto-developer-portal.md).

### <a name="visual-editor"></a>Vizuális szerkesztő

Testreszabhatja a portál tartalmát a vizualizáció szerkesztővel. A bal oldali menüben a lapok, a média, az elrendezések, a menük, a stílusok és a webhely beállításai hozhatók létre vagy módosíthatók. Az alsó menüelemek lehetővé teszik a munkaterületek (például a mobil vagy az asztali) közötti váltást, megtekintheti a portál elemeit a hitelesített vagy névtelen felhasználók számára, vagy mentheti vagy visszavonhatja a műveleteket.

A laphoz egy pluszjelet tartalmazó kék ikonra kattintva adhat hozzá sorokat. A widgetek (például szöveg, képek vagy API-k) hozzáadhatók egy pluszjelet tartalmazó szürke ikon megnyomásával. Egy oldalon átrendezheti az elemeket a fogd és vidd interakció használatával. 

### <a name="layouts-and-pages"></a>Elrendezések és lapok

![Lapok és elrendezések](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Az elrendezések határozzák meg a lapok megjelenítésének módját. Az alapértelmezett tartalomban például két elrendezés létezik – az egyik a kezdőlapra, a másik pedig az összes többi oldalra vonatkozik.

A rendszer egy elrendezést alkalmaz egy lapra úgy, hogy az URL-sablonját a lap URL-címéhez társítja. A `/wiki/*` URL-címét tartalmazó elrendezést például a rendszer minden olyan oldalra alkalmazza, amely az URL-cím `/wiki/` szegmensét fogja alkalmazni: `/wiki/getting-started`, `/wiki/styles`stb.

A fenti képen az elrendezéshez tartozó tartalom kék színnel van megjelölve, míg a lap piros színnel van megjelölve. A menüpontok vannak megjelölve.

### <a name="styling-guide"></a>Útmutató a stílushoz

![Útmutató a stílushoz](media/api-management-howto-developer-portal-customize/styling-guide.png)

A Styling útmutató tervezőkkel létrehozott panel. Lehetővé teszi a portál összes vizualizációs elemének áttekintését és formázását. A stílus hierarchikus – számos elem örökli a tulajdonságokat a többi elemtől. A gomb elemei például a szöveg és a háttér színét használják. A gomb színének módosításához módosítania kell az eredeti szín változatát.

Egy változat szerkesztéséhez kattintson rá, és válassza ki a tetején megjelenő ceruza ikont. Miután elvégezte a módosításokat az előugró ablakban, zárjuk le.

### <a name="save-button"></a>Mentés gomb

![Mentés gomb](media/api-management-howto-developer-portal-customize/save-button.png)

Amikor változást hajt végre a portálon, manuálisan kell mentenie a **Save (Mentés** ) gombra a lenti menüben. A módosítások mentésekor a rendszer automatikusan feltölti a módosított tartalmat a API Management szolgáltatásba.

## <a name="customize-the-portals-content"></a>A portál tartalmának testreszabása

Ahhoz, hogy a portál elérhető legyen a látogatók számára, testre kell szabnia az automatikusan generált tartalmat. Az ajánlott módosítások közé tartozik a Kezdőlap elrendezései, stílusa és tartalma.

> [!NOTE]
> Az integrációs megfontolások miatt a következő lapok nem távolíthatók el vagy nem helyezhetők át másik URL-cím alatt: `/404`, `/500`, `/captcha`, `/change-password`, `/config.json`, `/confirm/invitation`, `/confirm-v2/identities/basic/signup`, `/confirm-v2/password`, `/internal-status-0123456789abcdef`, `/publish`, `/signin`, `/signin-sso`, `/signup`.

### <a name="home-page"></a>Kezdőlap

Az alapértelmezett **kezdőlapot** a rendszer a próbabábu tartalmával tölti ki. Eltávolíthatja a teljes szakaszt a tartalommal, vagy megtarthatja a struktúrát, és egyenként is módosíthatja az elemeket. Cserélje le a generált szöveget és képeket a saját adataira, és győződjön meg arról, hogy a hivatkozások a kívánt helyekre mutatnak.

### <a name="layouts"></a>Elrendezések

Cserélje le az automatikusan generált emblémát a navigációs sávon a saját képére.

### <a name="styling"></a>Stílus

Bár nem kell módosítania a stílusokat, érdemes lehet módosítani bizonyos elemeket. Például módosítsa az elsődleges színt úgy, hogy az megfeleljen a márka színének.

### <a name="customization-example"></a>Testreszabási példa

Az alábbi videóban bemutatjuk, hogyan szerkesztheti a portál tartalmát, testreszabhatja a webhely megjelenését, és közzéteheti a módosításokat.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a>A portál közzététele

Ahhoz, hogy a portál és a legújabb módosításai elérhetők legyenek a látogatók számára, közzé kell tennie.

1. Győződjön meg róla, hogy mentette a módosításokat a **Save (Mentés** ) ikonra kattintva.
1. A menü **műveletek** területén kattintson a **Közzététel webhely** elemre. A művelet eltarthat néhány percig.  
    ![közzétételi portál](media/api-management-howto-developer-portal-customize/publish-portal.png)

## <a name="visit-the-published-portal"></a>A közzétett portál meglátogatása

A portál közzététele után a felügyeleti panellel megegyező URL-címen érheti el, például `https://contoso-api.portal.azure-api.net`. Nyissa meg egy új böngésző lapon, és tekintse meg külső látogatóként.

## <a name="next-steps"></a>További lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál áttekintése](api-management-howto-developer-portal.md)