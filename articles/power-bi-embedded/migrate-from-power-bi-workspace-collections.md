---
title: Hogyan lehet a Power BI-Munkaterületcsoport tartalmainak migrálása a Power BI Embedded |} A Microsoft Docs
description: Megtudhatja, hogyan tudnak az alkalmazásokba való beágyazás a Power BI Embedded a Power BI munkaterületi gyűjtemények áttelepítése és.
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
ms.openlocfilehash: de20d532112ca73f34f7cb603d043579c28179d6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071232"
---
# <a name="how-to-migrate-power-bi-workspace-collection-content-to-power-bi-embedded"></a>Hogyan lehet a Power BI-Munkaterületcsoport tartalmainak migrálása a Power BI Embedded

Ismerje meg, a Power BI Embedded a Power BI munkaterületi gyűjtemények áttelepítése. Ez a cikk nyújt útmutatást a Power BI Embedded az Azure Power BI-Munkaterületcsoportok-ről. Emellett áttekintjük, hogy milyen hatással vannak az alkalmazások módosítására.

A Power BI-Munkaterületcsoportok erőforrás továbbra is elérhető kiadás a Power BI Premium általános elérhetőségét követően korlátozott ideig. Nagyvállalati szerződést használó ügyfelek hozzáférése a meglévő munkaterület-csoportok a meglévő szerződés lejártáig. Ügyfeleink számára, akik közvetlenül vagy CSP csatornákon keresztül beszerzett a Power BI-Munkaterületcsoportok általános rendelkezésre állás, a Power BI Premium egy évig használhatják.

> [!IMPORTANT]
> Bár a migrálás függőséget vesz fel a Power BI szolgáltatásban, nem áll függőség a Power bi-ban a felhasználók az alkalmazás használatakor egy **beágyazási token**. Ezek nem regisztrálnia kell a Power BI szolgáltatásba a beágyazott tartalmak megtekintéséhez az alkalmazásban. Ezzel a Power BI beágyazási megközelítéssel beágyazni az ügyfeleinek.

![Power BI Embedded folyamat](media/migrate-from-power-bi-workspace-collections/powerbi-embed-flow.png)

## <a name="prepare-for-the-migration"></a>Az áttelepítés előkészítése

Néhány dolgot kell tennie a Power BI munkaterületi gyűjtemények szolgáltatás áttelepítés során a Power BI Embedded előkészítése. Szüksége lesz egy elérhető bérlőre, valamint Power BI Pro-licenccel rendelkező felhasználóként.

1. Ellenőrizze, hogy hozzáfér az Azure Active Directory (Azure AD) bérlő.

    Melyik-bérlőt használja?

    * A meglévő vállalati Power BI-bérlőt használja?
    * Külön bérlőt használ az alkalmazáshoz?
    * Külön bérlőt használ minden egyes ügyfél?

    Ha úgy dönt, hogy hozzon létre egy új bérlőt az alkalmazáshoz, vagy minden egyes ügyfél számára, tekintse meg az alábbi lehetőségek közül:

    * [Az Azure Active Directory-bérlő létrehozása](https://powerbi.microsoft.com/documentation/powerbi-developer-create-an-azure-active-directory-tenant/)
    * [Az Azure Active Directory-bérlő beszerzése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant).

2. Hozzon létre egy felhasználót az új bérlőn, amely a alkalmazás "fő" fiókjaként funkcionál. Hogy a fiók a Power bi regisztrálnia kell, és rendelkeznie kell egy Power BI Pro licenccel.

## <a name="accounts-within-azure-ad"></a>Fiókok Azure AD-ben

A következő fiókoknak kell létezniük a bérlőben.

> [!NOTE]
> Ezeket a fiókokat kell rendelkeznie a Power BI Pro-licenceket az alkalmazás-munkaterületek használatához.

1. Egy Bérlői rendszergazda felhasználó.

    Javasoljuk, hogy rendelkezik-e a bérlő rendszergazdája, amelynek felsorolt a beágyazási alkalmazás-munkaterületen.

2. Tartalomkészítő elemzők fiókok.

    Ezeknek a felhasználóknak hozzá kell rendelni az alkalmazás-munkaterületek igény szerint.

3. Egy alkalmazás *fő* felhasználói vagy szolgáltatási fiókkal.

    Az alkalmazások háttérrendszere tárolja ezen fiók hitelesítő adatait. Használja a *fő* a a Power BI REST API-k segítségével az Azure AD-token beszerzése a tárfiókot. Ez a fiók a beágyazási tokent az alkalmazás létrehozására szolgál. A *fő* fióknak a beágyazáshoz létrehozott alkalmazás-munkaterület rendszergazdájának lennie kell.

    **Ez egy csak egy normál felhasználói fiók a szervezet, amely beágyazási célokra szolgál.**

## <a name="app-registration-and-permissions"></a>Alkalmazásregisztráció és engedélyek

Ahhoz, hogy a REST API-hívások, alkalmazás regisztrálása az Azure ad-ben. Emellett konfiguráció alkalmazása a Microsoft Azure Portalon kívül a Power BI alkalmazásregisztrációs oldalán. További információkért lásd: [regisztrálása az Azure AD-alkalmazás, a Power BI-tartalmak beágyazásához](https://powerbi.microsoft.com/documentation/powerbi-developer-register-app/).

Az javasoljuk, hogy regisztrálnia kell az alkalmazást, az alkalmazás használatával **fő** fiókot.

## <a name="create-app-workspaces-required"></a>(Kötelező) az alkalmazás-munkaterületek létrehozása

Ha az alkalmazás több ügyfelet szolgál ki, kihasználhatja az alkalmazás-munkaterületek jobb elkülönítéshez. Irányítópultok és jelentések ügyfelenként elkülöníthetőek lenne. Ezután használhatja a Power BI-fiók alkalmazás-munkaterületenként még jobban elszigetelheti az alkalmazásfelületeket az ügyfelek közt, de csak egy fiók használatával legyen egyszerű.

> [!IMPORTANT]
> Nem használhat személyes munkaterületek (a "Saját munkaterület"), az ügyfelek való beágyazás kínálta előnyöket.

Szüksége van ahhoz, hogy a Power BI alkalmazás-munkaterületek létrehozása a Pro-licenccel rendelkező felhasználóként. Az alkalmazás-munkaterületet létrehozó Power BI-felhasználó alapértelmezés szerint a munkaterület rendszergazdája. **Az alkalmazás *fő* fióknak rendelkeznie kell a munkaterület rendszergazdája.**

## <a name="content-migration"></a>Webhelytartalom-migrálás

A tartalmak migrálása a munkaterület-csoportokból a Power BI Embedded az aktuális megoldással párhuzamosan végezhető, és nem okoz állásidőt igényel.

A **áttelepítési eszköz** használatra való másolásában tartalom Power BI munkaterület-csoportokból a Power BI Embedded érhető el. Különösen akkor, ha sok jelentés. További információkért lásd: [Power BI Embedded migrálási eszköz](migrate-tool.md).

Tartalommigrálás leginkább két API-k támaszkodik.

1. Download PBIX: Ez az API letöltheti a Power bi-bA 2016 októbere után feltöltött PBIX-fájlokat.
2. Import PBIX: Ez az API bármilyen pbix a Power bi-bA.

Néhány kapcsolódó kódrészletért lásd: [való migrálásához használható kódrészletek tartalmak a Power BI Embedded](migrate-code-snippets.md).

### <a name="report-types"></a>Jelentéstípusok

Nincsenek számos különböző típusú jelentéseket, egy eltérő migrálási folyamat megkövetelése.

#### <a name="cached-dataset-and-report"></a>Gyorsítótárazott adatkészlet és jelentés

Gyorsítótárazott adatkészletek tekintse meg a PBIX-fájlokat, amelyek korábban importálta az adatokat egy élő kapcsolatot vagy a DirectQuery-kapcsolatok helyett.

**A folyamat**

1. Töltse le a PBIX API-t hívja meg a Power BI-Munkaterületcsoport-munkaterületről.
2. Mentse a pbix-fájlt.
3. Import pbix API meghívása a Power BI Embedded-munkaterület.

#### <a name="directquery-dataset-and-report"></a>DirectQuery-adatkészlet és jelentés

**A folyamat**

1. GET hívás `https://api.powerbi.com/v1.0/collections/{collection_id}/workspaces/{wid}/datasets/{dataset_id}/Default.GetBoundGatewayDataSources` , és mentse a visszakapott kapcsolati karakterláncot.
2. Töltse le a PBIX API-t hívja meg a Power BI-Munkaterületcsoport-munkaterületről.
3. Mentse a pbix-fájlt.
4. Import pbix API meghívása a Power BI Embedded-munkaterület.
5. Kapcsolati karakterlánc frissítése ezzel a hívással: POST  `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.SetAllConnections`
6. GW-azonosító és adatforrás-azonosító beszerzése meghívásával – LEKÉRÉSE `https://api.powerbi.com/v1.0/myorg/datasets/{dataset_id}/Default.GetBoundGatewayDataSources`
7. Meghívásával felhasználó hitelesítő adatainak frissítése: PATCH `https://api.powerbi.com/v1.0/myorg/gateways/{gateway_id}/datasources/{datasource_id}`

#### <a name="old-dataset-and-reports"></a>Régi adatkészlet és jelentések

Feltöltés előtt 2016. október nem támogatják a pbix-fájlt töltse le a szolgáltatás jelentéseket. 

**A folyamat**

1. Töltse le a PBIX a fejlesztői környezetből (a belső Forráskezelő).
2. Import pbix API meghívása a Power BI Embedded-munkaterület.

#### <a name="push-dataset-and-report"></a>Leküldéses adatkészlet és jelentés

Töltse le nem támogatja a pbix-fájlt *Push API* adatkészletek. Push API adatkészleteinek adatai nem portolhatóak Power BI munkaterületi gyűjtemények, a Power BI Embedded.

**A folyamat**

1. Az adatkészlet Json-adatkészlet a Power BI Embedded-munkaterület létrehozásához "Create dataset" API meghívásához.
2. Építse újra a jelentést a létrehozott adatkészlethez *.

Lehetséges, hogy néhány lehetséges megoldások használatával migrálni a push api jelentést a Power BI-Munkaterületcsoportok Power BI Embedded szerint próbálkozzon a következőkkel:

1. Feltöltés néhány helyőrző pbix-fájlt a Power BI-Munkaterületcsoport-munkaterületre.
2. Klónozza a push api kösse az 1. a helyőrző pbix-fájlt, és jelentse be.
3. Töltse le a push API jelentést a helyőrző pbix-fájlt.
4. Töltse fel a helyőrző pbix-fájlt a Power BI Embedded-munkaterületre.
5. Leküldéses adatkészlet létrehozása a Power BI Embedded munkaterületén.
6. Újrakötése a jelentést a push api-adatkészletet.

## <a name="create-and-upload-new-reports"></a>Hozzon létre és töltse fel az új jelentések

Mellett a Power BI-Munkaterületcsoportok migrált tartalmak létrehozhatja a jelentéseket és adatkészleteket a Power BI Desktop használatával, és majd közzéteheti ezeket a jelentéseket egy alkalmazás-munkaterületen. A jelentéseket közzétevő végfelhasználónak Power BI Pro-licencre van egy alkalmazás-munkaterületen történő közzétételhez kell.

## <a name="rebuild-your-application"></a>Az alkalmazás újrabuildelése

1. Módosítsa az alkalmazás a Power BI REST API-k és a powerbi.com-on belüli jelentéshely megadásához használhatja.

2. Építse újra az AuthN/AuthZ hitelesítést használ a *fő* fiók az alkalmazás számára. Igénybe veheti a egy [beágyazási token](https://msdn.microsoft.com/library/mt784614.aspx) , hogy a felhasználó más felhasználók nevében.

3. Beágyazni a jelentéseket a Power BI Embedded szolgáltatást az alkalmazásba. További információkért lásd: [a Power BI irányítópultok, jelentések és csempék beágyazása](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

## <a name="map-your-users-to-a-power-bi-user"></a>A felhasználók leképezése Power BI-felhasználó

Az alkalmazásban, az alkalmazás számára abban kezelt felhasználókat leképezi egy *fő* Power BI hitelesítő adatait az alkalmazás az alkalmazásában. A hitelesítő adatokat a Power bi *fő* fiók tárolódnak az alkalmazáson belül, és hozhat létre használható beágyazási tokenek.

## <a name="what-to-do-when-you-are-ready-for-production"></a>Mi a teendő, ha készen áll az éles környezetben

Ha készen áll a termelési környezetre, tegye a következőket kell:

- Ha külön bérlőt használ a fejlesztéshez, majd meg kell, hogy az alkalmazás-munkaterületek, irányítópultok és jelentések, valamint az éles környezetben érhető el. Győződjön meg arról, hogy az alkalmazás Azure AD-ben létrehozott az éles környezetbeli bérlőhöz, és a megfelelő engedélyeket az 1. lépésben jelzett módon.

- Vásároljon az igényeinek megfelelő kapacitást. Használhatja a [Embedded elemzési kapacitásának tervezésével kapcsolatos tanulmány](https://aka.ms/pbiewhitepaper) annak megértésében, hogy milyen szükség lehet. Ha készen áll a beszerzési, egy Power BI Embedded-erőforrást az Azure Portalon vásárolhat.

- Az alkalmazás-munkaterület szerkesztése, és rendelje hozzá a speciális felületen kapacitásokhoz.

    ![Alkalmazás-munkaterület hozzárendelése egy kapacitáshoz powerbi.com](media/migrate-from-power-bi-workspace-collections/embedded-capacity.png)

- A frissített alkalmazást az éles üzembe helyezése, és kezdje beágyazni a jelentéseket a Power BI Embedded.

## <a name="after-migration"></a>Az áttelepítés után

Néhány karbantartása belül a Power BI-Munkaterületcsoportok van szükség.

- Távolítsa el az Azure-szolgáltatás a Power BI munkaterületi gyűjtemények a telepített megoldás összes munkaterületét.
- Törölje minden olyan munkaterület-csoportok, amelyek az Azure-ból.

## <a name="next-steps"></a>További lépések

Gratulálunk! Az alkalmazás most már a Power BI Embedded áttelepítése. A Power BI-irányítópultok, jelentések és adatkészletek beágyazásával kapcsolatos információkért lásd: [a Power BI irányítópultok, jelentések és csempék beágyazása](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Forduljon a Power BI közösségéhez](http://community.powerbi.com/)