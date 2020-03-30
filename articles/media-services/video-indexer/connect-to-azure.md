---
title: Azure-hoz csatlakoztatott videoindexelő-fiók létrehozása
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan hozhat létre Az Azure-hoz csatlakoztatott Video Indexer-fiókot.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/05/2019
ms.author: juliako
ms.openlocfilehash: 7ec8004fa0a54265962e79320c02287b2fcf814a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499915"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure-hoz csatlakoztatott videoindexelő-fiók létrehozása

Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol bizonyos számú ingyenes indexelési percet kap) vagy egy fizetett opciót (ahol nem korlátozza a kvóta). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználóinak, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználók számára. A fizetős beállítással létrehozhat egy Video Indexer-fiókot, amely az Azure-előfizetéséhez kapcsolódik, és egy Azure Media Services-fiókot. Az indexelt percekért, valamint a médiafiókkal kapcsolatos díjakért kell fizetnie.

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-előfizetéshez és egy Azure Media Services-fiókhoz kapcsolódó Video Indexer-fiókot. A témakör az automatikus (alapértelmezett) folyamat használatával az Azure-hoz való csatlakozás lépéseit ismerteti. Azt is bemutatja, hogyan csatlakozhat manuálisan az Azure-hoz (speciális).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

    Ha még nem rendelkezik Azure-előfizetéssel, regisztráljon az [Ingyenes Azure-próbaverzióra.](https://azure.microsoft.com/free/)

* Egy Azure Active Directory (Azure AD) tartomány.

    Ha nem rendelkezik Azure AD-tartománnyal, hozza létre ezt a tartományt az Azure-előfizetéssel. További információ: [Egyéni tartománynevek kezelése az Azure AD-ben](../../active-directory/users-groups-roles/domains-manage.md)

* Az Azure AD-tartomány ban egy **alkalmazás rendszergazdai** szerepkörrel rendelkező felhasználó. Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    Ez a felhasználó egy Azure AD-felhasználó egy munkahelyi vagy iskolai fiókkal. Ne használjon személyes fiókot, például outlook.com, live.com vagy hotmail.com.

    ![az összes AAD-felhasználó](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Az automatikus áramlás további előfeltételei

* Az Azure AD-tartomány egyik felhasználója és tagja.

    Ezt a tagot fogja használni a Video Indexer-fiók Azure-hoz való csatlakoztatásakor.

    Ennek a felhasználónak az Azure-előfizetés tagja kell lennie **tulajdonosi** szerepkörrel, vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai** szerepkörökkel. A felhasználó kétszer is hozzáadható, két szerepkörrel. Egyszer a közreműködővel, egyszer pedig a felhasználói hozzáférés-rendszergazda.

    ![hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>A kézi áramlás további előfeltételei

* Regisztrálja az EventGrid-erőforrás-szolgáltatót az Azure Portalon.

    Az [Azure Portalon](https://portal.azure.com/)nyissa meg **az Előfizetések**->[előfizetés]->**ResourceProviders**.

    Keresse meg a **Microsoft.Media** és a **Microsoft.EventGrid**. Ha nem a "Regisztrált" állapotban, kattintson a **Regisztráció gombra.** A regisztráció néhány percet vesz igénybe.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

> [!NOTE]
> Ha az Azure-előfizetés tanúsítványalapú többtényezős hitelesítést használ, rendkívül fontos, hogy a következő lépéseket hajtsa végre egy olyan eszközön, amelyrendelkezik a szükséges tanúsítványokkal.

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.

2. Válassza az **Új fiók létrehozása** gombot:

    ![Új videoindexelő-fiók létrehozása](./media/create-account/connect-to-azure.png)

3. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést.

    ![Videoindexelő csatlakoztatása az Azure-hoz](./media/create-account/connect-vi-to-azure-subscription.png)

4. Válasszon ki egy Azure-régiót a támogatott helyekközül: USA nyugati régiója 2, Észak-Európa vagy Kelet-Ázsia.
5. Az **Azure Media Services-fiók csoportban**válasszon az alábbi lehetőségek közül:

    * Új Media Services-fiók létrehozásához válassza **az Új erőforráscsoport létrehozása**lehetőséget. Adja meg az erőforráscsoport nevét.

        Az Azure létrehozza az új fiókot az előfizetésében, beleértve egy új Azure Storage-fiókot is. Az új Media Services-fiók alapértelmezett kezdeti konfigurációval rendelkezik egy streamelési végponttal és 10 S3 fenntartott egységgel.
    * Meglévő Media Services-fiók használatához válassza **a Meglévő erőforrás használata**lehetőséget. A fióklistában válassza ki a fiókját.

        A Media Services-fióknak a Video Indexer-fiókkal azonos régióval kell rendelkeznie.

        > [!NOTE]
        > Az indexelés időtartamának és az alacsony átviteli feszültségnek a minimalizálása érdekében erősen ajánlott a Media Services-fiókban [lévő fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) típusát és számát **10 S3 fenntartott egységre**módosítani. Lásd: [A portál használata a fenntartott egységek módosításához](../previous/media-services-portal-scale-media-processing.md).

    * A kapcsolat manuális konfigurálásához válassza a **Váltás kézi konfigurációra** kapcsolatot.

        Részletes információt a Csatlakozás az [Azure-hoz manuálisan](#connect-to-azure-manually-advanced-option) (speciális beállítás) című, az alábbi szakaszban talál.
6. Ha elkészült, válassza a **Csatlakozás**lehetőséget. Ez a művelet akár néhány percet is igénybe vehet.

    Miután csatlakozott az Azure-hoz, az új Video Indexer-fiókja megjelenik a fióklistában:

    ![új fiók](./media/create-account/new-account.png)

7. Tallózással keresse meg az új fiókot.

## <a name="connect-to-azure-manually-advanced-option"></a>Csatlakozás manuálisan az Azure-hoz (speciális beállítás)

Ha az Azure-ral létesített kapcsolat nem sikerült, manuális csatlakozással megkísérelheti a probléma elhárítását.

> [!NOTE]
> Erősen ajánlott, hogy a következő három fiók ugyanabban a régióban: a Video Indexer fiók, amely csatlakozik a Media Services-fiók, valamint az Azure storage-fiók csatlakozik ugyanahhoz a Media Services-fiókhoz.

### <a name="create-and-configure-a-media-services-account"></a>Media Services-fiók létrehozása és konfigurálása

1. Az [Azure](https://portal.azure.com/) Portal használatával hozzon létre egy Azure Media Services-fiókot, a fiók létrehozása című részben leírtak [szerint.](../previous/media-services-portal-create-account.md)

    Amikor tárfiókot hoz létre a Media Services-fiókjához, válassza a **StorageV2** lehetőséget a fiók-típusú és **a georedundáns (GRS)** lehetőséget a replikációs mezőkhöz.

    ![Új AMS-fiók](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Győződjön meg arról, hogy írja le a Media Services erőforrás- és fiókneveket. A következő szakaszban található lépésekhez szüksége lesz rájuk.

2. Állítsa be a [lefoglalt egységek](../previous/media-services-scale-media-processing-overview.md ) típusát és számát **10 S3 lefoglalt egységre** a létrehozott Media Services-fiókban. Lásd: [A portál használata a fenntartott egységek módosításához](../previous/media-services-portal-scale-media-processing.md).
3. Mielőtt lejátszhatja a videókat a Video Indexer webalkalmazásban, el kell indítania az új Media Services-fiók alapértelmezett **streamelési végpontját.**

    Az új Media Services-fiókban válassza a **Streamelési végpontok**lehetőséget. Ezután válassza ki a streamelési végpontot, és nyomja le a start gombot.

    ![Új AMS-fiók](./media/create-account/create-ams-account2.png)

4. Ahhoz, hogy a Video Indexer hitelesítse magát a Media Services API-val, létre kell hozni egy AD-alkalmazást. A következő lépések végigvezetik az Azure AD hitelesítési folyamat a [Get started with Azure AD-hitelesítés az Azure Portal használatával:](../previous/media-services-portal-get-started-with-aad.md)

    1. Az új Media Services-fiókban válassza az **API-hozzáférés**lehetőséget.
    2. Válassza [az egyszerű szolgáltatás hitelesítési módját](../previous/media-services-portal-get-started-with-aad.md).
    3. Az ügyfélazonosító és az ügyféltitok beszerezése

        Miután kiválasztotta a **Beállítások**->**kulcsok lehetőséget,** adja hozzá a **Leírás elemet,** nyomja le a **Mentés**billentyűt, és a kulcs értéke kitöltődik.

        Ha a kulcs lejár, a fiók tulajdonosának kapcsolatba kell lépnie a Video Indexer ügyfélszolgálatával a kulcs megújításához.

        > [!NOTE]
        > Győződjön meg arról, hogy írja le a kulcs értékét és az alkalmazás azonosítóját. A következő szakaszban található lépésekhez szüksége lesz rá.

### <a name="connect-manually"></a>Csatlakozás manuálisan

A **Videoindexelő csatlakoztatása a** [Videoindexelő](https://www.videoindexer.ai/) hez a Video Indexelő lapjának egy Azure-előfizetési párbeszédpanelén válassza a **Váltás manuális konfigurációra** hivatkozást.

A párbeszédpanelen adja meg a következő információkat:

|Beállítás|Leírás|
|---|---|
|Videoindexer-fiók régiója|A Video Indexer-fiók régió neve. A jobb teljesítmény és az alacsonyabb költségek érdekében erősen ajánlott megadni annak a régiónak a nevét, ahol az Azure Media Services-erőforrás és az Azure Storage-fiók található. |
|Azure AD-bérlő|Az Azure AD-bérlő neve, például "contoso.onmicrosoft.com". A bérlői adatok az Azure Portalon lehívhatók. Helyezze a kurzort a bejelentkezett felhasználó neve fölé a jobb felső sarokban. Keresse meg a **domain**jobb oldalán található nevet.|
|Előfizetés azonosítója|Az Azure-előfizetés, amely alatt ezt a kapcsolatot létre kell hozni. Az előfizetés-azonosító lehívható az Azure Portalon. Válassza a Minden **szolgáltatás lehetőséget** a bal oldali panelen, és keressen az "előfizetések" kifejezésre. Válassza **az Előfizetések** lehetőséget, és válassza ki a kívánt azonosítót az előfizetések listájából.|
|Az Azure Media Services erőforráscsoportjának neve|Annak az erőforráscsoportnak a neve, amelyben létrehozta a Media Services-fiókot.|
|Médiaszolgáltatás-erőforrás neve|Az előző szakaszban létrehozott Azure Media Services-fiók neve.|
|Alkalmazásazonosító|Az Azure AD alkalmazásazonosítója (a megadott Media Services-fiók engedélyeivel), amelyet az előző szakaszban hozott létre.|
|Alkalmazáskulcs|Az előző szakaszban létrehozott Azure AD alkalmazáskulcs. |

## <a name="considerations"></a>Megfontolandó szempontok

Az Azure Media Services következő kapcsolódó szempontjai érvényesek:

* Ha automatikusan csatlakozik, megjelenik egy új erőforráscsoport, media services-fiók és egy Storage-fiók az Azure-előfizetésben.
* Ha automatikusan csatlakozik, a Video Indexer 10 S3 egységre állítja a **media Reserved Units értéket:**

    ![Media Services által fenntartott egységek](./media/create-account/ams-reserved-units.png)

* Ha meglévő Media Services-fiókhoz csatlakozik, a Video Indexelő nem módosítja a meglévő média **fenntartott egységek konfigurációját.**

   Előfordulhat, hogy a tervezett terhelésnek megfelelően módosítania kell a Media számára fenntartott egységek típusát és számát. Ne feledje, hogy ha a terhelés magas, és nincs elég egység vagy sebesség, a videók feldolgozása időtúllépési hibákat eredményezhet.

* Ha új Media Services-fiókhoz csatlakozik, a Video Indexer automatikusan elindítja benne az alapértelmezett **streamelési végpontot:**

    ![Media Services streamelési végpontja](./media/create-account/ams-streaming-endpoint.png)

    A streamelési végpontok jelentős indítási idővel rendelkeznek. Ezért a fiók Azure-hoz való csatlakoztatása óta több percig is eltarthat, amíg a videóit streameli és meg nem nézi a Video Indexer webalkalmazásban.

* Ha meglévő Media Services-fiókhoz csatlakozik, a Video Indexer nem módosítja az alapértelmezett streamelési végpont-konfigurációt. Ha nincs futó **streamelési végpont,** nem nézhet videókat ebből a Media Services-fiókból vagy a Video Indexer ben.

## <a name="next-steps"></a>További lépések

Programozott módon kezelheti próbafiókját és/vagy az Azure-hoz kapcsolódó Video Indexer-fiókjait a következő utasítások követésével: [API-k használata](video-indexer-use-apis.md).

Ugyanazt az Azure AD-felhasználót kell használnia, amelyet az Azure-hoz való csatlakozáskor használt.