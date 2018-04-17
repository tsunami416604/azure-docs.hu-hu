---
title: Hogyan kell áttelepíteni a Power BI-Munkaterületcsoport tartalom Power BI Embedded |} Microsoft Docs
description: Megtudhatja, hogyan telepíthetők át a Power BI munkaterület gyűjtemények Power BI Embedded, és használja ki az alkalmazásokban monitorfelbontás kiadásokban.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: d138b0c26ffc0a44947f79811fd586dda7df4509
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>A Power BI-Munkaterületcsoport tartalom Power BI Embedded áttelepítése

Megtudhatja, hogyan telepíthetők át a Power BI munkaterület gyűjtemények Power BI Embedded. Ez a cikk történő áttelepítéséhez nyújt támogatást Azure Power BI munkaterület gyűjtemények a Power BI Embedded. Úgy is tekintünk, mi történik, az alkalmazás módosításait.

A Power BI munkaterület gyűjtemények erőforrás továbbra is fennáll, adott időtartamra általános rendelkezésre állását a Power BI prémium kiadás után elérhető legyen. Az ügyfelek egy nagyvállalati szerződés értelmében a meglévő munkaterület gyűjtemények keresztül a meglévő megállapodások lejárati hozzáféréssel rendelkeznek. Ügyfelek, amelyek a Power BI munkaterület gyűjtemények közvetlen vagy CSP csatornákon keresztül beszerzett egy évig prémiumról általános rendelkezésre állási a Power BI hozzáféréssel rendelkezik.

> [!IMPORTANT]
> Az áttelepítés függőség a Power BI szolgáltatásban vesz igénybe, amíg nincs függőség a Power bi-ban a felhasználók az alkalmazás használata esetén egy **beágyazási jogkivonatot**. Nincs szükségük Feliratkozás a Power BI és a beágyazott tartalom az alkalmazás megtekintéséhez. Ezzel a Power BI megközelítés beágyazás beágyazása az ügyfelek számára.

![A Power BI Embedded folyamata](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Az áttelepítés előkészítése

Néhány dolgot kell tennie, való áttelepítés során a Power BI munkaterület gyűjtemények szolgáltatásból keresztül Power BI Embedded előkészítéséhez. A bérlő és a Power BI Pro licenccel rendelkező felhasználónak kell.

1. Győződjön meg arról, hogy az Azure Active Directory (Azure AD) bérlő eléréséhez.

    Mely bérlői használni?

    * Használja a meglévő vállalati Power BI-bérlő?
    * Az alkalmazás egy külön bérlőt használni?
    * Minden ügyfél esetében egy külön bérlő használata

    Ha úgy dönt, hogy hozzon létre egy új bérlőt az alkalmazást, vagy minden ügyfél, tekintse meg a következők egyikét:

    * [Azure Active Directory-bérlő létrehozása](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Az Azure Active Directory-bérlő beszerzése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Hozzon létre egy felhasználó belül az új bérlőhöz, amely a "fő" alkalmazás fiókját. Hogy a fiók a Power BI regisztrálni kell, és a Power BI Pro licenccel, rendelkeznie kell.

## <a name="accounts-within-azure-ad"></a>Az Azure AD fiók

A következő fiókokat kell a bérlő belül.

> [!NOTE]
> Ezeket a fiókokat kell rendelkeznie a Power BI Pro-licenccel ahhoz, hogy az alkalmazás munkaterületek.

1. A bérlői rendszergazda felhasználó.

    Javasoljuk, hogy a beágyazási alk rendelkezik-e a bérlői rendszergazda felsorolt tagja.

2. Az elemzők által létrehozott tartalom fiókokat.

    Ezek a felhasználók hozzá kell rendelni app munkaterületek igény szerint.

3. Egy alkalmazás *fő* felhasználói fiókot, vagy szolgáltatási fiókkal.

    Az alkalmazások háttér tárolja a fiók hitelesítő adatait. Használja a *fő* fiókot használja a Power BI REST API-k való használathoz az Azure AD-token beszerzése. Ez a fiók segítségével hozza létre az alkalmazáshoz a beágyazási jogkivonatot. A *fő* fióknak kell lennie a beágyazás készült alkalmazás munkaterületek rendszergazdája.

    **Ez egy csak a szervezet beágyazás alkalmazásában használt felhasználói fiók.**

## <a name="app-registration-and-permissions"></a>Alkalmazás regisztrálása és engedélyek

Ahhoz, hogy a REST API-hívásokkal, egy alkalmazás regisztrálása az Azure ad-val. Továbbá konfiguráció alkalmazása a Microsoft Azure portálon kívül a Power BI alkalmazás regisztrációs lapjához. További információkért lásd: [regisztrálása az Azure AD alkalmazás beágyazandó Power BI-tartalmakat](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Az javasoljuk, hogy regisztrálja az alkalmazást az alkalmazás **fő** fiók.

## <a name="create-app-workspaces-required"></a>Alkalmazás munkaterületek létrehozásáért (kötelező)

Ha az alkalmazás több ügyfél szolgál ki, kihasználhatja az alkalmazás munkaterületek jobb elkülönítést biztosítani. Irányítópultok és jelentések elkülönített, az ügyfelek közötti lehet. Aztán használhatja a Power BI-fiók alkalmazás munkaterület / további az ügyfelek közötti az alkalmazás lép elkülönítését, de csak egy fiók használatával legyen egyszerű.

> [!IMPORTANT]
> Nem használhatja a saját munkaterület (a "Saját munkaterület") az ügyfelek számára beágyazás előnyeit.

Ahhoz, hogy hozzon létre egy alkalmazást munkaterületet belül a Power BI Pro licenccel rendelkező felhasználónak kell. A Power BI felhasználót, hogy az alkalmazások munkaterület létrehoz egy rendszergazdát, hogy a munkaterület alapértelmezés szerint. **Az alkalmazás *fő* fióknak kell lennie a munkaterület rendszergazdája.**

## <a name="content-migration"></a>Tartalom áttelepítése

A munkaterület-gyűjtemények a tartalmat telepít át a Power BI Embedded teheti az aktuális megoldás párhuzamos, és nincs szükség az állásidő.

A **áttelepítési eszköz** lesz szükség ahhoz, hogy elősegítse a Power BI munkaterület Ügyfélgyűjteményekből történő tartalommásoláskor Power BI Embedded érhető el. Különösen akkor, ha sok jelentés. További információkért lásd: [Power BI Embedded áttelepítési eszköz](migrate-tool.md).

Tartalom áttelepítésének főként két API-k támaszkodik.

1. Letöltési PBIX - Ez az API le pbix-fájlt, amely 2016. október után töltődtek Power bi-bA.
2. Importálás PBIX - Ez az API a pbix-fájlt feltölti a Power bi-bA.

Bizonyos kapcsolódó kódrészletek, lásd: [kódtöredékek áttelepítéséhez a Power BI Embedded tartalom Code](migrate-code-snippets.md).

### <a name="report-types"></a>Jelentés típusa

Többféle jelentések, melyekhez a különböző áttelepítési folyamata.

#### <a name="cached-dataset-and-report"></a>Gyorsítótárazott adatkészlet és a jelentés

Gyorsítótárazott adatkészletek tekintse meg a pbix-fájlt fájlt, amely kellett importálta az adatokat a élő kapcsolattal vagy DirectQuery kapcsolat szemben.

**Attribútumfolyam**

1. Töltse le a pbix-fájlt API-t hívja a Power BI-Munkaterületcsoport munkaterületről.
2. Mentse a pbix-fájlt.
3. A Power BI Embedded munkaterület hívható importálási pbix-fájlt.

#### <a name="directquery-dataset-and-report"></a>DirectQuery adatkészlet és a jelentés

**Attribútumfolyam**

1. GET hívás https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources , és mentse a kapcsolati karakterlánc érkezett.
2. Töltse le a pbix-fájlt API-t hívja a Power BI-Munkaterületcsoport munkaterületről.
3. Mentse a pbix-fájlt.
4. A Power BI Embedded munkaterület hívható importálási pbix-fájlt.
5. Hívás - kapcsolati karakterlánc POST frissítése  https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections
6. GW azonosító és az adatforrás azonosító beszerzése meghívásával – LEKÉRDEZÉSE. https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources
7. Felhasználó hitelesítő adatainak frissítése meghívásával – javítás https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}

#### <a name="old-dataset-and-reports"></a>Régi adatkészletet és jelentések

A jelentések feltöltés előtt október 2016 nem támogatja a PBIX letöltése szolgáltatást.

**Attribútumfolyam**

1. Pbix-fájlt a fejlesztési környezetet (a belső verziókezelő) beolvasása sikertelen.
2. A Power BI Embedded munkaterület hívható importálási pbix-fájlt.

#### <a name="push-dataset-and-report"></a>Leküldéses adatkészlet és a jelentés

Töltse le nem támogatja a pbix-fájlt *leküldéses API* adatkészletek. Leküldéses API dataset adatok nem használatát. a Power BI munkaterület Ügyfélgyűjteményekből Power BI Embedded.

**Attribútumfolyam**

1. A dataset adatkészlet Power BI Embedded munkaterület létrehozásához Json "Adatkészlet létrehozása" API hívása.
2. Építse újra a jelentést a létrehozott adatkészlet *.

Néhány lehetséges megoldások segítségével telepítse át a leküldéses lehetséges api jelentést a Power BI munkaterület Ügyfélgyűjteményekből Power BI Embedded úgy, a következőket:

1. A Power BI-Munkaterületcsoport munkaterület feltöltése néhány dummy pbix-fájlt.
2. A leküldéses klónozni api jelentést, majd kösse 1. lépésben a dummy pbix-fájlt.
3. Töltse le a leküldéses API jelentés üres pbix-fájlt.
4. Töltse fel a Power BI Embedded munkaterület dummy pbix-fájlt.
5. A Power BI Embedded munkaterületen leküldéses dataset létrehozni.
6. Api-adatkészlet leküldéses jelentés újbóli kötése.

## <a name="create-and-upload-new-reports"></a>Hozzon létre, és töltse fel az új jelentések

Mellett a tartalom a Power BI munkaterület gyűjtemények végezte az áttelepítést a jelentések és adatkészletek Power BI Desktop használatával létrehozhat és majd tegye közzé a jelentések az alkalmazás-munkaterülethez. A végfelhasználó a jelentések közzététele kell rendelkeznie a Power BI Pro licenccel az alkalmazás-munkaterülethez való közzétételhez.

## <a name="rebuild-your-application"></a>Az alkalmazás

1. Módosítsa az alkalmazás használhatja a Power BI REST API-k és a powerbi.com webhelyen belüli jelentés hely.

2. Építse újra a AuthN/engedélyezési használatával a *fő* fiókot használja az alkalmazást. Kihasználhatja a használatával egy [beágyazási jogkivonatot](https://msdn.microsoft.com/library/mt784614.aspx) engedélyezése a felhasználó számára más felhasználók nevében végzik a tevékenységüket.

3. A jelentéseket a Power BI Embedded az alkalmazásba beágyazni. További információkért lásd: [beágyazása a Power BI irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>A felhasználók hozzárendelése a Power BI-felhasználó

Az alkalmazáson belül, amelyeket Ön kezel, az alkalmazásban a felhasználók leképez egy *fő* Power BI hitelesítő adatait az alkalmazás célját. A hitelesítő adatait a Power BI *fő* fiók az alkalmazáson belül tárolják, és létrehozásához használható beágyazása a jogkivonatokat.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Mi a teendő, ha készen áll az éles

Ha készen áll az éles áthelyezni, tegye a következőket kell:

- Ha egy külön bérlői fejlesztési használ, akkor szüksége annak biztosítását, hogy az alkalmazás munkaterületek irányítópultokat és jelentéseket, valamint elérhető az éles környezetben. Győződjön meg arról, hogy az alkalmazás létrehozása az Azure ad-ben az éles bérlő és a megfelelő alkalmazás engedélyeket az 1. lépésben leírtak szerint.

- Vásárolja meg a kapacitás, amely megfelel az igényeinek. Használhatja a [beágyazott analytics kapacitástervezési tanulmány](https://aka.ms/pbiewhitepaper) megismeréséhez, hogy mi szükség lehet. Amikor készen áll a beszerzési, vásárolhat egy Power BI Embedded erőforrás az Azure portálon.

- Az alkalmazások munkaterület szerkesztése, és rendelje hozzá a speciális kapacitása.

    ![Rendelje hozzá a powerbi.com webhelyen belül kapacitás alk.](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- A frissített alkalmazást üzemi környezetben telepíti, és megkezdheti a jelentéseket a Power BI Embedded beágyazás.

## <a name="after-migration"></a>Az áttelepítés után

Néhány tisztítás Power BI munkaterület gyűjtemények belül van szükség.

- Távolítsa el az összes munkaterületek ki a központilag telepített megoldás a Power BI munkaterület gyűjtemények Azure szolgáltatáson belül.
- Törölje a meglévő Azure-ban munkaterület gyűjtemények.

## <a name="next-steps"></a>További lépések

Gratulálunk! Az alkalmazás most át, a Power BI Embedded. A Power BI-irányítópultok, jelentések és adatkészletek beágyazásával kapcsolatos információkért lásd: [beágyazása a Power BI irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)