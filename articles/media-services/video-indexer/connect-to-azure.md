---
title: Hozzon létre egy Video Indexer-fiókot az Azure Portalon
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan hozhat létre a Video Indexer-fiókot az Azure Portalon.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: affa6f9a808543401b7d57812c7d2bef4324a83c
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58894217"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Hozzon létre egy kapcsolódik az Azure Video Indexer-fiókot

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőséggel hozzon létre egy Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók. Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók. A témakör lépéseit ismerteti az Azure-hoz kapcsolódáshoz a automatikus (alapértelmezett) folyamat használatával. Azt is bemutatja, hogyan kapcsolódhat az Azure-manuálisan (speciális).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

    Ha nem rendelkezik Azure-előfizetésem, regisztráljon [Azure ingyenes próbaverziója](https://azure.microsoft.com/free/).

* Egy Azure Active Directory (AD) tartományban.

    Ha nem rendelkezik Azure AD-tartomány, hozzon létre ezt a tartományt az Azure-előfizetésében. További információkért lásd: [az Azure Active Directoryban egyéni tartománynevek kezelése](../../active-directory/users-groups-roles/domains-manage.md)

* Egy felhasználó és az Azure AD-tartomány tagja. Csatlakozás a Video Indexer-fiókot az Azure-bA fogja használni ehhez a taghoz.

    Ez a felhasználó munkahelyi vagy iskolai fiókkal, nem egy személyes fiók, mint például az Outlook.com-os, live.com vagy hotmail.com az Azure AD-felhasználót kell lennie.

    ![AAD-felhasználók](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>További Előfeltételek az automatikus folyamat

Egy felhasználó és az Azure AD-tartomány tagja. Csatlakozás a Video Indexer-fiókot az Azure-bA fogja használni ehhez a taghoz.

Ennek a felhasználónak kell lennie az Azure-előfizetésében sem tagja egy **tulajdonosa** szerepkör, illetve mindkettőt **közreműködői** és **felhasználói hozzáférés rendszergazdája** szerepköröket. A felhasználó is hozzáadhatók kétszer, 2 szerepkört. Miután a közreműködő, és egyszer a felhasználói hozzáférés rendszergazdája.

![Hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>További Előfeltételek manuális folyamat

Az Azure portal használatával EventGrid erőforrás-szolgáltató regisztrálása.

Az a [az Azure portal](https://portal.azure.com/), lépjen a **előfizetések**-> [. előfizetés] ->**ResourceProviders**. 

Keresse meg **Microsoft.Media** és **Microsoft.EventGrid**. Ha nem a "Regisztrálva" állapotban lévő, kattintson a **regisztrálása**. Néhány perc alatt regisztrálni vesz igénybe.

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.

2. Kattintson a **hozzon létre új fiókot** gombra:

    ![Csatlakozás az Azure-bA](./media/create-account/connect-to-azure.png)

3. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést.

    ![a Video Indexer csatlakoztatása az Azure-bA](./media/create-account/connect-vi-to-azure-subscription.png)

4. Válasszon egy Azure-régióban támogatott helyek: 2. nyugati RÉGIÓJA, Észak-Európában vagy Kelet-Ázsia.
5. A **Azure Media Services-fiók**, válasszon egyet az alábbi lehetőségek közül:

    * Egy új Media Services-fiók létrehozásához válassza **hozzon létre új erőforráscsoportot**. Adja meg az erőforráscsoport nevét.

        Az Azure az előfizetésében, beleértve az új Azure Storage-fiókot hoz létre az új fiók. Új Media Services-fiók egy alapértelmezett kezdeti konfigurációt a egy folyamatos átviteli végponton, és 10 S3 szintű fenntartott egységet tartalmaz.
    * Meglévő Media Services-fiók használatához válassza **használjon meglévő erőforrást**. A fiókok listájából válassza ki a fiókját.

        A Media Services-fiók a Video Indexer-fiókot és ugyanabban a régióban kell rendelkeznie. 

        > [!NOTE]
        > Az indexelő időtartama és az alacsony átviteli sebesség minimalizálása érdekében azt javasoljuk a típusa és száma [szolgáltatás számára fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) való **10 S3 szintű fenntartott egységet** a Media Services-fiók. Lásd: [szolgáltatás számára fenntartott egységek módosítása a portálon](../previous/media-services-portal-scale-media-processing.md).

    * A kapcsolat konfigurálásához kattintson a **váltson át manuális konfigurációs** hivatkozásra.

        Részletes információkért lásd: a [manuálisan csatlakoztatása az Azure-](#connect-to-azure-manually-advanced-option) (speciális beállítás) a következő szakasz.
6. Ha elkészült, válassza ki a **Connect**. Ez a művelet eltarthat néhány percig. 

    Miután csatlakozott az Azure-ba, az új Video Indexer-fiókot a számla lista jelenik meg:

    ![Új fiók](./media/create-account/new-account.png)

7. Tallózással keresse meg az új fiók

## <a name="connect-to-azure-manually-advanced-option"></a>Csatlakozás az Azure-ban manuálisan (speciális beállítás)

Nem sikerült kapcsolódni az Azure-ba, megpróbálhat manuálisan történő csatlakoztatásával a probléma elhárításához.

> [!NOTE]
> Erősen javasoljuk, hogy rendelkezik a következő három fiókoknak ugyanabban a régióban: a Video Indexer-fiókot, amely kapcsolódik a Media Services-fiók, valamint az Azure storage-fiók ugyanabban a Media Services-fiók csatlakozik.

### <a name="create-and-configure-a-media-services-account"></a>Egy Media Services-fiók létrehozása és konfigurálása

1. Használja a [Azure](https://portal.azure.com/) létrehozása az Azure Media Services-fiók, leírtak szerint a portálon [hozzon létre egy fiókot](../previous/media-services-portal-create-account.md).

    Válasszon egy tárfiókot a Media Services-fiók létrehozásakor **StorageV2** a fiók típusa és **Georedundáns tárolást (RGS)** replikációs mezők esetében.

    ![új AMS-fiók](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Ellenőrizze, hogy írja le a Media Services-erőforrás és a fiók nevével. Szüksége lesz rá a következő szakasz lépéseit.

2. Állítsa be az típusa és száma [szolgáltatás számára fenntartott egységek](../previous/media-services-scale-media-processing-overview.md ) való **10 S3 szintű fenntartott egységet** a Media Services-fiókban létrehozott. Lásd: [szolgáltatás számára fenntartott egységek módosítása a portálon](../previous/media-services-portal-scale-media-processing.md).
3. A Video Indexer webes alkalmazásban a videók lejátszásához, el kell indítania az alapértelmezett **folyamatos átviteli végponton** az új Media Services-fiók.

    Az új Media Services-fiókban kattintson **Streamvégpontok**. Válassza ki a folyamatos átviteli végponton, és nyomja le a kezdő.

    ![új AMS-fiók](./media/create-account/create-ams-account2.png)

4. A Video Indexer Media Services API-hitelesítésre egy AD-alkalmazást kell létrehozni. Az alábbi lépések útmutatóban ismertetett, az Azure AD-hitelesítési folyamat [használatának első lépései az Azure AD-hitelesítés az Azure Portalon](../previous/media-services-portal-get-started-with-aad.md):

    1. Válassza ki az új Media Services-fiók **API-hozzáférés**.
    2. Válassza ki [szolgáltatás egyszerű hitelesítési módszer](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).
    3. Az ügyfél-Azonosítóját és ügyfélkulcsát, beolvasása, leírtak szerint a [az ügyfél-azonosító és titkos Ügyfélkód](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret) szakaszban.

        Kiválasztása után **beállítások**->**kulcsok**, adjon hozzá **leírása**, nyomja le az ENTER **mentése**, a kulcs értékét tölti fel a rendszer.

        Ha a kulcs érvényességének lejárta a fiók tulajdonosától kell forduljon az ügyfélszolgálathoz a Video Indexer a kulcs megújítását.

        > [!NOTE]
        > Ügyeljen arra, hogy a kulcs értékét, és az alkalmazás azonosítóját. Szüksége lesz rá a következő szakasz lépéseit.

### <a name="connect-manually"></a>Csatlakozás manuálisan

Az a **Video Indexer csatlakoztatása Azure-előfizetéshez** párbeszédpanelén a [Video Indexer](https://www.videoindexer.ai/) lapon válassza ki a **váltson át manuális konfigurációs** hivatkozás.

A párbeszédpanelen adja meg a következő információkat:

|Beállítás|Leírás|
|---|---|
|Video Indexer-fiók régiója|A Video Indexer-fiókot régió neve. A jobb teljesítmény és alacsonyabb költségek azt javasoljuk, adja meg a nevét, a régió, ahol az Azure Media Services-erőforrás és Azure Storage-fiókban találhatók. |
|Az Azure Active Directory (AAD) bérlő|Az Azure AD-bérlővel, például "contoso.onmicrosoft.com" neve. A bérlői kapcsolatos információkat az Azure Portalról kérhető. Vigye a kurzort a jobb felső sarokban a bejelentkezett felhasználó neve. A neve, jobb oldalán található **tartomány**.|
|Előfizetés azonosítója|Az Azure-előfizetést amellyel ezt a kapcsolatot kell létrehozni. Az előfizetés-azonosító az Azure Portalról kérhető. Kattintson a **minden szolgáltatás** a bal oldali panelen, és keressen a "előfizetések". Válassza ki **előfizetések** , majd válassza a kívánt Azonosítóját az előfizetések listáját.|
|Azure Media Services-erőforráscsoport neve|Az erőforráscsoport, amelyben létrehozta a Media Services-fiók neve.|
|Media Services-szolgáltatási erőforrás neve|Az Azure Media Services-fiók, amely az előző szakaszban létrehozott neve.|
|Alkalmazásazonosító|Az Azure AD alkalmazás Azonosítót (az engedélyeket a megadott Media Services-fiók), amely az előző szakaszban létrehozott.|
|Alkalmazáskulcs|Az Azure AD alkalmazás kulcs az előző szakaszban létrehozott. |

## <a name="considerations"></a>Megfontolandó szempontok

A következő Azure Media Services kapcsolódó szempontokat kell figyelembe venni:

* Automatikus csatlakozás, ha jelenik meg egy új erőforráscsoportot, a Media Services-fiók és a egy Storage-fiókot az Azure-előfizetésében.
* Automatikus csatlakozás, ha a Video Indexer állítja be, az adathordozó **szolgáltatás számára fenntartott egységek** 10 S3 szintű egységre bővíthető:

    ![A Media Services-szolgáltatás számára fenntartott egységek](./media/create-account/ams-reserved-units.png)

* Ha csatlakoztat egy meglévő Media Services-fiókhoz, a Video Indexer nem módosítja a meglévő adathordozó **szolgáltatás számára fenntartott egységek** konfigurációja.

   Szüksége lehet típusával és számával, a Media szolgáltatás számára fenntartott egységeket, módosítsa a tervezett terhelés alapján. Ne feledje, hogy ha a terhelés túl magas, és nem rendelkezik elegendő egységek vagy a sebességét, videók feldolgozása eredményezhet időtúllépési hibákat.

* Ha csatlakozik egy új Media Services-fiók, a Video Indexer automatikusan elindítja az alapértelmezett **folyamatos átviteli végponton** benne:

    ![A Media Services-streamvégpont](./media/create-account/ams-streaming-endpoint.png)

    Streamvégpontok jelentős indítási időt igénybe. Ezért fog csatlakozni a fiók Azure-ban, mindaddig, amíg a videók streamelt, és a Video Indexer webalkalmazás terméknél kezdve néhány percig is eltarthat.

* Ha csatlakoztat egy meglévő Media Services-fiókhoz, a Video Indexer nem módosítja az alapértelmezett Streamvégpont konfiguráció. Ha ott nem fut. **folyamatos átviteli végponton**, nem lesz a Media Services-fiók vagy a Video Indexer-videók.

## <a name="next-steps"></a>További lépések

Programozott módon kezelheti a próbaverziós fiókjához és/vagy a Video Indexer-fiókokkal, amelyek köti össze az Azure-ban utasításait követve: [Az API-kkal](video-indexer-use-apis.md).

Az azonos Azure AD-felhasználót az Azure-ba való kapcsolódáskor használt kell használnia.


