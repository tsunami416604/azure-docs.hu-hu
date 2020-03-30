---
title: A felügyelt fejlesztői portál elérése és testreszabása – Azure API Management | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a fejlesztői portál felügyelt verzióját az API Management ben.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244094"
---
# <a name="access-and-customize-developer-portal"></a>Fejlesztői portál elérése és testreszabása

A fejlesztői portál egy automatikusan generált, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ez az a hely, ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, és kérhetnek hozzáférést.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A fejlesztői portál felügyelt verziójának elérése
> * Navigálás a felügyeleti felületen
> * A tartalom testreszabása
> * A módosítások közzététele
> * A közzétett portál megtekintése

További részleteket a fejlesztői portálon talál az [Azure API Management fejlesztői portál áttekintésében.](api-management-howto-developer-portal.md)

![API Management fejlesztői portál - felügyeleti mód](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Előfeltételek

- A következő rövid útmutató befejezése: [Hozzon létre egy Azure API Management-példányt](get-started-create-service-instance.md)
- Azure API Management-példány importálása és közzététele. További információt az Importálás és közzététel című témakörben [talál.](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>A portál elérése rendszergazdaként

Kövesse az alábbi lépéseket a portál felügyelt verziójának eléréséhez.

1. Nyissa meg az API Management szolgáltatáspéldányát az Azure Portalon.
1. Kattintson a **fejlesztői portál** gombra a felső navigációs sávon. Megnyílik egy új böngészőlap a portál felügyeleti verziójával.

## <a name="understand-the-portals-administrative-interface"></a>A portál felügyeleti felületének ismertetése

### <a name="default-content"></a>Alapértelmezett tartalom 

Ha első alkalommal éri el a portált, az alapértelmezett tartalom automatikusan kilesz építve a háttérben. Az alapértelmezett tartalom úgy lett kialakítva, hogy bemutassa a portál képességeit, és minimalizálja a portál testreszabásához szükséges testreszabások mennyiségét. Az [Azure API-kezelés fejlesztői portáljának áttekintése című témakörben](api-management-howto-developer-portal.md)további információ.

### <a name="visual-editor"></a>Vizuális szerkesztő

A portál tartalmát a vizuális szerkesztővel testreszabhatja. A bal oldali menürészek lehetővé teszik az oldalak, adathordozók, elrendezések, menük, stílusok vagy webhelybeállítások létrehozását vagy módosítását. Az alsó menüpontok lehetővé teszik a nézetablakok közötti váltást (például mobil vagy asztali számítógép), a portál hitelesített vagy névtelen felhasználók számára látható elemeinek megtekintését, illetve a műveletek mentését vagy visszavonását.

Sorokat adhat az oldalhoz, ha egy pluszjellel ellátott kék ikonra kattint. A widgetek (például szöveg, képek vagy API-k listája) hozzáadhatók egy szürke ikon pluszjellel való megnyomásával. A lap elemeit átrendezheti a húzási művelettel. 

### <a name="layouts-and-pages"></a>Elrendezések és oldalak

![Oldalak és elrendezések](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Az elrendezések határozzák meg az oldalak megjelenítését. Az alapértelmezett tartalomban például két elrendezés található : az egyik a kezdőlapra, a másik pedig az összes többi oldalra vonatkozik.

Az elrendezés úgy lesz alkalmazva az oldalra, hogy az URL-sablont az oldal URL-címéhez igazítja. Például az URL-sablont `/wiki/*` tartalmazó elrendezés minden olyan `/wiki/` oldalra vonatkozik, `/wiki/getting-started` `/wiki/styles`ahol az URL szegmense van: , , stb.

A fenti képen az elrendezéshez tartozó tartalom kék, míg az oldal pirosszínnel van megjelölve. A menüszakaszok at jelölik.

### <a name="styling-guide"></a>Stílus útmutató

![Stílus útmutató](media/api-management-howto-developer-portal-customize/styling-guide.png)

Styling útmutató egy panel létre a tervezők szem előtt tartva. Ez lehetővé teszi a portál összes vizuális elemének felügyeletét és formázását. A stílus hierarchikus - sok elem örökli tulajdonságait más elemekből. A gombelemek például színeket használnak a szöveghez és a háttérhez. Egy gomb színének módosításához meg kell változtatnia az eredeti színváltozatot.

Egy változat szerkesztéséhez kattintson rá, és jelölje ki a rajta megjelenő ceruza ikont. Miután végrehajtotta a módosításokat az előugró ablakban, zárja be.

### <a name="save-button"></a>Mentés gomb

![Mentés gomb](media/api-management-howto-developer-portal-customize/save-button.png)

Amikor módosítást hajtott végre a portálon, manuálisan kell mentenie, ha megnyomja a **Mentés** gombot az alsó menüben. A módosítások mentésekor a módosított tartalom automatikusan feltöltődik az API Management szolgáltatásba.

## <a name="customize-the-portals-content"></a>A portál tartalmának testreszabása

Mielőtt a portált elérhetővé tenné a látogatók számára, személyre kell szabnia az automatikusan generált tartalmat. Az ajánlott módosítások közé tartoznak az elrendezések, a stílusok és a kezdőlap tartalma.

> [!NOTE]
> Integrációs szempontok miatt a következő oldalak nem távolíthatók el `/404`és `/500` `/captcha`nem `/change-password` `/config.json`helyezhetők át egy másik URL-cím alatt: , , , `/confirm/invitation` `/confirm-v2/identities/basic/signup`, , `/confirm-v2/password`, , `/internal-status-0123456789abcdef`, , `/publish`, , `/signin`, , , , , , , `/signin-sso`, , , , . `/signup`

### <a name="home-page"></a>Kezdőlap

Az alapértelmezett **kezdőlap** üres tartalommal van megtöltve. Eltávolíthatja az egész szakaszokat a tartalommal, vagy megtarthatja a szerkezetet, és egyenként módosíthatja az elemeket. Cserélje le a létrehozott szöveget és képeket a sajátjára, és győződjön meg arról, hogy a hivatkozások a kívánt helyekre mutatnak.

### <a name="layouts"></a>Elrendezések

Cserélje le az automatikusan generált emblémát a navigációs sávon a saját képére.

### <a name="styling"></a>Stílus

Bár nem kell módosítania a stílusokat, érdemes lehet bizonyos elemeket módosítani. Módosíthatja például az elsődleges színt a márka színének megfelelően.

### <a name="customization-example"></a>Példa testreszabásra

Az alábbi videóban bemutatjuk, hogyan lehet módosítani a portál tartalmát, testreszabni a webhely megjelenését, és közzétenni a módosításokat.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>A portál közzététele

Ahhoz, hogy a portál és annak legutóbbi módosításai elérhetők legyenek a látogatók számára, közzé kell tennie azt.

1. A **Mentés** ikonra kattintva győződjön meg arról, hogy mentette a módosításokat.
1. Kattintson a **Webhely közzététele** gombra a menü **Műveletek** szakaszában. A művelet eltarthat néhány percig.  
    ![Portál közzététele](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> A portált újra közzé kell tenni az API Management szolgáltatás konfigurációjának módosítása után, például egyéni tartomány hozzárendelése, az identitásszolgáltatók frissítése, delegálás beállítása, a bejelentkezés és a termékkifejezések megadása stb.

## <a name="visit-the-published-portal"></a>Látogasson el a közzétett portálra

A portál közzététele után a portált a felügyeleti panellel azonos `https://contoso-api.developer.azure-api.net`URL-címen érheti el, például . Tekintse meg egy külön böngésző ülésén (inkognitó / privát böngészési mód), mint egy külső látogató.

## <a name="apply-the-cors-policy-on-apis"></a>A CORS-házirend alkalmazása az API-kra

Engedélyeznie kell a CORS (cross-origin erőforrás-megosztás) az API-k, hogy a portál látogatói teszteljék az API-k at a beépített interaktív konzolon keresztül. További részleteket ebben a [dokumentációs cikkben](api-management-howto-developer-portal.md#cors) talál.

## <a name="next-steps"></a>További lépések

További információ a fejlesztői portálról:

- [Az Azure API Management fejlesztői portál – áttekintés](api-management-howto-developer-portal.md)
