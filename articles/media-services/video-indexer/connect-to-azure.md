---
title: Az Azure-hoz csatlakoztatott Video Indexer-fiók létrehozása
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan hozhat létre az Azure-hoz csatlakoztatott Video Indexer fiókot.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/08/2020
ms.author: juliako
ms.openlocfilehash: 2093132abaa7aed06113b1856c85ac54fd7ae60d
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995715"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Az Azure-hoz csatlakoztatott Video Indexer-fiók létrehozása

Video Indexer-fiók létrehozásakor kiválaszthat egy ingyenes próbaverziós fiókot (amely bizonyos számú ingyenes indexelési percet igénybe vehet) vagy egy fizetős lehetőséget (ha nem korlátozza a kvótát). Az ingyenes próbaverzióval a Video Indexer akár 600 perces ingyenes indexelést biztosít a webhely felhasználói számára, illetve akár 2400 perc ingyenes indexelést az API-felhasználók számára. A fizetős lehetőséggel létrehoz egy Video Indexer fiókot, amely az Azure-előfizetéshez és egy Azure Media Services-fiókhoz csatlakozik. Az indexelt percek és a Media Accounting szolgáltatás díját is fizetni kell.

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-előfizetéshez és egy Azure Media Services-fiókhoz kapcsolódó Video Indexer fiókot. A témakör lépéseit követve csatlakozhat az Azure-hoz az automatikus (alapértelmezett) folyamat használatával. Azt is bemutatja, hogyan csatlakozhat az Azure-hoz manuálisan (speciális).

Ha *próbaverzióról* *fizetett* video Indexer fiókra vált, az összes videó és modell testreszabását átmásolhatja az új fiókra, amint azt a [tartalom importálása a próbaverziós fiókból](#import-your-content-from-the-trial-account)című cikkben tárgyaljuk.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

    Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Azure ingyenes próbaverzióra](https://azure.microsoft.com/free/).

* Egy Azure Active Directory (Azure AD) tartomány.

    Ha nem rendelkezik Azure AD-tartománnyal, hozza létre ezt a tartományt az Azure-előfizetésével. További információ: [Egyéni tartománynevek kezelése az Azure ad-ben](../../active-directory/users-groups-roles/domains-manage.md)

* Egy felhasználó az Azure AD-tartományban egy **alkalmazás-rendszergazdai** szerepkörrel. Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    A felhasználónak munkahelyi vagy iskolai fiókkal rendelkező Azure AD-felhasználónak kell lennie. Ne használjon személyes fiókot (például outlook.com, live.com vagy hotmail.com).

    ![minden HRE-felhasználó](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Az automatikus folyamat további előfeltételei

* Egy felhasználó és egy tag az Azure AD-tartományban.

    Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    Ennek a felhasználónak az Azure-előfizetésében egy **tulajdonosi** szerepkörrel, vagy a **közreműködő** és a **felhasználói hozzáférés rendszergazdai** szerepköreivel rendelkező tagnak kell lennie. A felhasználók kétszer is hozzáadhatók két szerepkörrel. Egyszer a közreműködővel és egyszer a felhasználói hozzáférés rendszergazdájával.

    ![hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>További előfeltételek a manuális adatforgalomhoz

* Regisztrálja a EventGrid erőforrás-szolgáltatót a Azure Portal használatával.

    A [Azure Portal](https://portal.azure.com/)lépjen az **előfizetések**-> [előfizetés] – >**ResourceProviders**elemre.

    Keressen rá a **Microsoft. Media** és a **Microsoft. EventGrid**kifejezésre. Ha nem a "regisztrált" állapotban van, kattintson a **regisztráció**elemre. A regisztráció néhány percet vesz igénybe.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

> [!NOTE]
> Ha az Azure-előfizetése tanúsítványalapú többtényezős hitelesítést használ, rendkívül fontos, hogy a következő lépéseket olyan eszközön végezze el, amelyen telepítve vannak a szükséges tanúsítványok.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.

2. Válassza az **új fiók létrehozása** gombot:

    ![Új Video Indexer fiók létrehozása](./media/create-account/connect-to-azure.png)

3. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést.

    ![Video Indexer kapcsolódása az Azure-hoz](./media/create-account/connect-vi-to-azure-subscription.png)

4. Válasszon ki egy Azure-régiót a támogatott helyekről: USA 2. nyugati régiója, Észak-Európa vagy Kelet-Ázsia.
5. A **Azure Media Services fiók**területen válasszon egyet a következő lehetőségek közül:

    * Új Media Services-fiók létrehozásához válassza az **Új erőforráscsoport létrehozása**lehetőséget. Adja meg az erőforráscsoport nevét.

        Az Azure létrehoz egy új fiókot az előfizetésében, beleértve egy új Azure Storage-fiókot is. Az új Media Services fiók alapértelmezett kezdeti konfigurációja egy streaming Endpoint és 10 S3 fenntartott egység.
    * Meglévő Media Services-fiók használatához válassza a **meglévő erőforrás használata**lehetőséget. A fiókok listából válassza ki a fiókját.

        A Media Services-fióknak a Video Indexer-fiókkal megegyező régióval kell rendelkeznie.

        > [!NOTE]
        > Az indexelési időtartam és az alacsony átviteli sebesség minimálisra csökkentése érdekében erősen ajánlott a Media Services-fiókban lévő [fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) típusának és számának módosítása **10 S3 fenntartott egységre**. [A fenntartott egységek módosításához lásd: a portál használata](../previous/media-services-portal-scale-media-processing.md).

    * A kapcsolat manuális konfigurálásához válassza a **váltás manuális konfigurációra** hivatkozásra.

        Részletes információk: [Kapcsolódás az Azure-hoz manuálisan](#connect-to-azure-manually-advanced-option) (speciális beállítás) szakasz, amely az alábbiak szerint jelenik meg.
6. Ha elkészült, válassza a **Csatlakozás**lehetőséget. Ez a művelet akár néhány percet is igénybe vehet.

    Miután csatlakozott az Azure-hoz, az új Video Indexer fiók megjelenik a fiók listában:

    ![új fiók](./media/create-account/new-account.png)

7. Tallózással keresse meg az új fiókot.

## <a name="connect-to-azure-manually-advanced-option"></a>Kapcsolódás az Azure-hoz manuálisan (speciális beállítás)

Ha nem sikerült csatlakozni az Azure-hoz, a probléma megoldásához manuálisan is csatlakozhat.

> [!NOTE]
> Javasoljuk, hogy a következő három fiókkal rendelkezzen ugyanabban a régióban: a Media Services-fiókkal összekapcsolt Video Indexer fiókkal, valamint az ugyanahhoz a Media Services fiókhoz csatlakoztatott Azure Storage-fiókkal.

### <a name="create-and-configure-a-media-services-account"></a>Media Services fiók létrehozása és konfigurálása

1. Az [Azure](https://portal.azure.com/) Portal használatával hozzon létre egy Azure Media Services fiókot a [fiók létrehozása](../previous/media-services-portal-create-account.md)című témakörben leírtak szerint.

    A Media Services-fiókhoz tartozó Storage-fiók létrehozásakor válassza a **StorageV2** és a **geo-redundáns (GRS)** beállítást a replikálási mezőkhöz.

    ![Új AMS-fiók](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Ügyeljen rá, hogy jegyezze fel a Media Services erőforrás és fiók nevét. Ehhez a következő szakaszban ismertetett lépéseket kell elvégeznie.

2. Állítsa be a [fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) típusát és számát **10 S3 fenntartott egységre** a létrehozott Media Services fiókban. [A fenntartott egységek módosításához lásd: a portál használata](../previous/media-services-portal-scale-media-processing.md).
3. A videók a Video Indexer webalkalmazásban való lejátszása előtt el kell indítania az új Media Services-fiók alapértelmezett **adatfolyam-végpontját** .

    Az új Media Services fiókban válassza a **folyamatos átviteli végpontok**lehetőséget. Ezután válassza ki a folyamatos átviteli végpontot, és kattintson a Start gombra.

    ![Új AMS-fiók](./media/create-account/create-ams-account2.png)

4. A Media Services API-val való hitelesítéshez Video Indexer létre kell hozni egy AD-alkalmazást. Az alábbi lépések végigvezetik az Azure ad- [hitelesítés használatának első lépései a Azure Portal használatával](../previous/media-services-portal-get-started-with-aad.md)című témakörben ismertetett Azure ad-hitelesítési folyamaton:

    1. Az új Media Services fiókban válassza az **API-hozzáférés**lehetőséget.
    2. Válassza ki az [egyszerű szolgáltatás hitelesítési módszerét](../previous/media-services-portal-get-started-with-aad.md).
    3. Az ügyfél-azonosító és az ügyfél titkos kulcsának beolvasása

        Miután kiválasztotta a **Beállítások**->**kulcsokat**, adja hozzá a **leírást**, nyomja meg a **Mentés**gombot, és a kulcs értéke feltöltve lesz.

        Ha a kulcs lejár, a fiók tulajdonosának kapcsolatba kell lépnie Video Indexer támogatással a kulcs megújításához.

        > [!NOTE]
        > Ügyeljen rá, hogy jegyezze fel a kulcs értékét és az alkalmazás AZONOSÍTÓját. Ehhez a következő szakaszban ismertetett lépések szükségesek.

### <a name="connect-manually"></a>Manuális kapcsolat

A **video Indexer csatlakoztatása egy Azure-előfizetéshez** párbeszédpanel [video Indexer](https://www.videoindexer.ai/) lapján válassza a **váltás kézi konfigurációra** hivatkozásra.

A párbeszédpanelen adja meg a következő adatokat:

|Beállítás|Leírás|
|---|---|
|Video Indexer fiók régiója|Az Video Indexer-fiók régiójának neve. A jobb teljesítmény és az alacsonyabb költségek érdekében javasoljuk, hogy adja meg annak a régiónak a nevét, ahol a Azure Media Services erőforrás és az Azure Storage-fiók található. |
|Azure AD-bérlő|Az Azure AD-bérlő neve, például "contoso.onmicrosoft.com". A bérlő adatai a Azure Portalból kérhetők le. Vigye a kurzort a bejelentkezett felhasználó nevére a jobb felső sarokban. Keresse meg a **tartománytól**jobbra található nevet.|
|Előfizetés azonosítója|Az Azure-előfizetés, amely alatt ezt a kapcsolatokat létre kell hozni. Az előfizetés-azonosító a Azure Portalból kérhető le. Válassza a **minden szolgáltatás** lehetőséget a bal oldali panelen, és keressen rá az "előfizetések" kifejezésre. Válassza az **előfizetések** lehetőséget, majd válassza ki a kívánt azonosítót az előfizetések listájából.|
|Azure Media Services erőforráscsoport neve|Azon erőforráscsoport neve, amelyben létrehozta a Media Services fiókot.|
|Media Service-erőforrás neve|Az előző szakaszban létrehozott Azure Media Services fiók neve.|
|Alkalmazásazonosító|Az Azure AD-alkalmazás azonosítója (a megadott Media Services fiókra vonatkozó engedélyekkel), amelyeket az előző szakaszban hozott létre.|
|Alkalmazás kulcsa|Az előző szakaszban létrehozott Azure AD-alkalmazás kulcsa. |

## <a name="import-your-content-from-the-trial-account"></a>Tartalom importálása a *próbaverziós* fiókból

[Új fiók létrehozásakor](#connect-to-azure)lehetősége van importálni a tartalmat a *próbaverziós* fiókból az új fiókba. Ha az **új fiók létrehozása az Azure-előfizetéshez** párbeszédpanelen az *Importálás* lehetőséget választja, a rendszer a *próbaverziós* fiókból az új fiókba másolja a média és a tartalom modell testreszabását.

A tartalom importálásának lehetősége a fent ismertetett automatizált és manuális megközelítések esetében is érvényes.

> [!NOTE]
> A tartalom csak egyszer importálható minden fiókból.

## <a name="considerations"></a>Megfontolandó szempontok

A következő Azure Media Services kapcsolódó megfontolások érvényesek:

* Ha automatikusan kapcsolódik, megjelenik egy új erőforráscsoport, Media Services fiók és egy Storage-fiók az Azure-előfizetésében.
* Ha automatikusan csatlakozik, Video Indexer beállítja a Media szolgáltatás **számára fenntartott egységeket** 10 S3 egységre:

    ![Media Services fenntartott egységek](./media/create-account/ams-reserved-units.png)

* Ha meglévő Media Services-fiókhoz csatlakozik, Video Indexer nem módosítja a meglévő Media szolgáltatás **számára fenntartott egységek** konfigurációját.

   Előfordulhat, hogy a tervezett terhelésnek megfelelően módosítania kell a Media szolgáltatás számára fenntartott egységek típusát és számát. Ne feledje, hogy ha a terhelés magas, és nincs elég egysége vagy sebessége, a videók feldolgozása időtúllépési hibát okozhat.

* Ha új Media Services-fiókhoz csatlakozik, Video Indexer automatikusan elindítja az alapértelmezett **folyamatos átviteli végpontot** :

    ![Media Services streaming végpont](./media/create-account/ams-streaming-endpoint.png)

    A streaming-végpontok jelentős indítási idővel rendelkeznek. Ezért több percet is igénybe vehet, amikor a fiókját az Azure-ba csatlakoztatta, amíg a videók nem továbbíthatók és nem nézték a Video Indexer webalkalmazásban.

* Ha meglévő Media Services-fiókhoz csatlakozik, Video Indexer nem módosítja az alapértelmezett adatfolyam-végpont konfigurációját. Ha nincs futó adatfolyam- **végpont**, akkor nem nézhet meg videókat ebből a Media Services fiókból vagy video Indexerból.

## <a name="next-steps"></a>További lépések

Programozott módon használhatja a próbaverziós fiókját és/vagy az Azure-hoz kapcsolódó Video Indexer-fiókokat a következő témakörben található utasítások végrehajtásával: API-k [használata](video-indexer-use-apis.md).

Ugyanazt az Azure AD-felhasználót kell használnia, amelyet az Azure-hoz való csatlakozáskor használt.
