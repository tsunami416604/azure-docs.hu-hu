---
title: Az Azure API Management fejlesztői portál áttekintése – Azure API Management | Microsoft Docs
description: Ismerje meg API Management fejlesztői portálját.
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
ms.openlocfilehash: 377ed8f17cfcf67f10cbdec77199c69c606afa3e
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74180693"
---
# <a name="azure-api-management-developer-portal-overview"></a>Az Azure API Management fejlesztői portál áttekintése

A fejlesztői portál egy automatikusan létrehozott, teljesen testreszabható webhely, amely az API-k dokumentációját tartalmazza. Ahol az API-felhasználók felfedezhetik az API-kat, megtudhatják, hogyan használhatják őket, hozzáférést igényelhetnek, és kipróbálhatja őket.

Ez a cikk a API Management fejlesztői portál saját üzemeltetésű és felügyelt verziói közötti különbségeket ismerteti. Emellett ismerteti az architektúráját, és választ ad a gyakori kérdésekre.

> [!WARNING]
> Az új fejlesztői portál jelenleg API Management szolgáltatásokra van bevezetve.
> Ha a szolgáltatás újonnan lett létrehozva vagy fejlesztői szintű szolgáltatás, akkor már rendelkezik a legújabb verzióval. Ellenkező esetben problémák merülhetnek fel (például a közzétételi funkcióval). A szolgáltatás bevezetése várhatóan a 2019. november 22-én fejeződik be.
>
> [Megtudhatja, hogyan telepítheti át az előzetes verzióról a fejlesztői portál általánosan elérhető verziójára](#preview-to-ga) .

![API Management fejlesztői portál](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="managed-vs-self-hosted"></a>Felügyelt és saját üzemeltetésű verziók

A fejlesztői portált kétféleképpen is létrehozhatja:

- **Felügyelt verzió** – az API Management-példányba épített portál szerkesztésével és testreszabásával, és az URL-`<your-api-management-instance-name>.developer.azure-api.net`keresztül érhető el. A [jelen dokumentációs cikkből](api-management-howto-developer-portal-customize.md) megtudhatja, hogyan érheti el és szabhatja testre a felügyelt portált.
- **Saját** üzemeltetésű verzió – a portál API Management-példányon kívüli üzembe helyezésével és önálló üzemeltetésével. Ezzel a módszerrel szerkesztheti a portált, és kiterjesztheti a megadott alapvető funkciókat. A portált a legújabb verzióra is frissítenie kell. A részletekért és az utasításokért tekintse meg a [GitHub-tárházat a portál forráskódjának][1]használatával. A [felügyelt verzió oktatóanyaga](api-management-howto-developer-portal-customize.md) végigvezeti a portál felügyeleti paneljén, amely a saját üzemeltetésű verzióban is szerepel.

## <a name="portal-architectural-concepts"></a>Portál építészeti fogalmak

A portál összetevői logikailag két kategóriába sorolhatók: *kód* és *tartalom*.

A *kód* [a GitHub-tárházban][1] van fenntartva, és a következőket tartalmazza:

- Widgetek – amelyek vizuális elemeket és HTML-, JavaScript-, formázási képességeket, beállításokat és tartalom-hozzárendelést képviselnek. Ilyenek például a képek, a szöveges bekezdések, az űrlapok, az API-k listája stb.
- Formázási definíciók – a widgetek stílusának meghatározása
- Motor – amely statikus weblapokat hoz létre a portál tartalmából, és JavaScript nyelven íródott
- Vizuális szerkesztő – lehetővé teszi a böngészőn belüli testreszabást és a szerzői műveletek elvégzését

A *tartalom* két alkategóriára oszlik: a *portál tartalma* és a *API Management tartalma*.

A *portál tartalma* a portálra vonatkozik, és a következőket tartalmazza:

- Lapok – például Kezdőlap, API-oktatóanyagok, blogbejegyzések
- Média – képek, animációk és egyéb fájl alapú tartalmak
- Elrendezések – sablonok, amelyek megfelelnek egy URL-címnek, és megadják a lapok megjelenítésének módját
- Stílusok – a formázási definíciók értékei, például betűtípusok, színek, szegélyek
- Beállítások – konfiguráció, például favicon, webhely metaadatainak

A *portál tartalma*, a média kivételével, JSON-dokumentumként van megadva.

*API Management tartalom* olyan entitásokat tartalmaz, mint az API-k, a műveletek, a termékek és az előfizetések.

A portál a [Paperbits-keretrendszer](https://paperbits.io/)adaptált villáján alapul. Az eredeti Paperbits funkció kibővült a API Management-specifikus widgetek (például az API-k listája, a termékek listája) és egy összekötő API Management szolgáltatás számára a tartalmak mentéséhez és lekéréséhez.

## <a name="faq"></a>Gyakori kérdések

Ebben a szakaszban az új fejlesztői portál általános kérdéseivel kapcsolatos gyakori kérdéseket választjuk. A saját üzemeltetésű verzióra vonatkozó kérdésekért tekintse meg a [GitHub-adattár wiki szakaszát](https://github.com/Azure/api-management-developer-portal/wiki).

### <a name="a-idpreview-to-ga-how-can-i-migrate-from-the-preview-version-of-the-portal"></a><a id="preview-to-ga"/> hogyan telepíthetek át a portál előzetes verziójáról?

A fejlesztői portál előzetes verziójának használatával kiépített egy előnézeti tartalmat a API Management szolgáltatásban. Az alapértelmezett tartalom jelentősen módosult az általánosan elérhető verzióban a jobb felhasználói élmény érdekében. Emellett új widgeteket is tartalmaz.

Ha felügyelt verziót használ, állítsa alaphelyzetbe a portál tartalmát az **Operations** ( **tartalom visszaállítása** ) lehetőségre kattintva. A művelet megerősítése eltávolítja a portál összes tartalmát, és kiépíti az új alapértelmezett tartalmat. A portál motorja automatikusan frissült a API Management szolgáltatásban.

![Portál tartalmának visszaállítása](media/api-management-howto-developer-portal/reset-content.png)

Ha a saját üzemeltetésű verziót használja, a GitHub-tárház `scripts/cleanup.bat` és `scripts/generate.bat` használatával távolítsa el a meglévő tartalmakat, és hozzon létre új tartalmakat. Győződjön meg róla, hogy a legújabb kiadásra frissíti a portál kódját a GitHub-adattárból.

Ha nem szeretné alaphelyzetbe állítani a portál tartalmát, érdemes lehet az újonnan elérhető widgeteket használni az összes oldalon. A meglévő widgetek automatikusan frissültek a legújabb verzióra.

Ha a portál az általános elérhetőségi bejelentés után lett kiépítve, akkor már az új alapértelmezett tartalmat is be kell építenie. Az oldaláról nincs szükség beavatkozásra.

### <a name="how-can-i-migrate-from-the-old-developer-portal-to-the-new-developer-portal"></a>Hogyan telepíthetek át a régi fejlesztői portálról az új fejlesztői portálra?

A portálok nem kompatibilisek, és manuálisan kell áttelepíteni a tartalmat.

### <a name="does-the-new-portal-have-all-the-features-of-the-old-portal"></a>Az új portál rendelkezik a régi portál összes szolgáltatásával?

Az új fejlesztői portál nem támogatja az *alkalmazásokat* és a *problémákat*. Ha a régi portálon *problémák* léptek fel, és szükség van rájuk az újat, tegye fel a megjegyzést [egy dedikált GitHub-problémába](https://github.com/Azure/api-management-developer-portal/issues/122).

### <a name="has-the-old-portal-been-deprecated"></a>Elavult a régi portál?

A régi fejlesztői és közzétevői portálok már *örökölt* funkciók – csak a biztonsági frissítéseket kapják meg. Az új funkciókat kizárólag az új fejlesztői portálon tesszük elérhetővé.

Az örökölt portálok elavulása külön lesz bejelentve. Ha kérdése, problémája vagy megjegyzése van, emelje fel őket [egy dedikált GitHub-problémával](https://github.com/Azure/api-management-developer-portal/issues/121).

### <a name="how-can-i-automate-portal-deployments"></a>Hogyan automatizálható a portál üzembe helyezése?

A REST API keresztül programozott módon érheti el és kezelheti a fejlesztői portál tartalmát, függetlenül attól, hogy felügyelt vagy saját üzemeltetésű verziót használ-e.

Az API-t [a GitHub-adattár wiki szakasza ismerteti][2]. A portál tartalmának a környezetek közötti áttelepítésének automatizálására is használható – például egy tesztkörnyezetben az éles környezetbe. Erről a folyamatról a GitHubról szóló [cikkben](https://aka.ms/apimdocs/migrateportal) olvashat bővebben.

### <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Támogatja a portál a Azure Resource Manager sablonokat, és/vagy kompatibilis-e a API Management DevOps Resource Kit-vel?

Nem.

### <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Engedélyezni kell további VNET-kapcsolatot a felügyelt portál függőségeihez?

Nem.

### <a name="im-getting-a-cors-error-when-using-the-interactive-console-what-should-i-do"></a>CORS hibaüzenetet kapok az interaktív konzol használatakor. Mit tegyek?

Az interaktív konzol ügyféloldali API-kérést tesz elérhetővé a böngészőből. A CORS probléma megoldásához vegyen fel [egy CORS-szabályzatot](https://docs.microsoft.com/azure/api-management/api-management-cross-domain-policies#CORS) az API (ok) ra. Az összes paramétert manuálisan is megadhatja, vagy helyettesítő karakteres `*` értékeket használhat. Például:

```XML
<cors>
    <allowed-origins>
        <origin>*</origin>
    </allowed-origins>
    <allowed-methods>
        <method>GET</method>
        <method>POST</method>
        <method>PUT</method>
        <method>DELETE</method>
        <method>HEAD</method>
        <method>OPTIONS</method>
        <method>PATCH</method>
        <method>TRACE</method>
    </allowed-methods>
    <allowed-headers>
        <header>*</header>
    </allowed-headers>
    <expose-headers>
        <header>*</header>
    </expose-headers>
</cors>
```

> [!NOTE]
> 
> Ha a CORS szabályzatot a termék hatókörében alkalmazza, az API-k hatóköre helyett, és az API az előfizetés-kulcs hitelesítését használja egy fejlécen keresztül, a konzol nem fog működni.
>
> A böngésző automatikusan kiadja a beállítások HTTP-kérését, amely nem tartalmaz az előfizetési kulccsal rendelkező fejlécet. A hiányzó előfizetési kulcs miatt API Management nem tudja hozzárendelni a beállításokat a termékhez, így nem tudja alkalmazni a CORS házirendet.
>
> Megkerülő megoldásként átadhatja az előfizetési kulcsot egy lekérdezési paraméterben.

## <a name="next-steps"></a>Következő lépések

További információ az új fejlesztői portálról:

- [A felügyelt fejlesztői portál elérése és testreszabása](api-management-howto-developer-portal-customize.md)
- [A portál saját üzemeltetésű verziójának beállítása][2]

Egyéb erőforrások tallózása:

- [GitHub-adattár a forráskódtal][1]
- [A projekt nyilvános ütemterve][3]

[1]: https://aka.ms/apimdevportal
[2]: https://github.com/Azure/api-management-developer-portal/wiki
[3]: https://github.com/Azure/api-management-developer-portal/projects