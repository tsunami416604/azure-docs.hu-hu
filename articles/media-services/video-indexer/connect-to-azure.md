---
title: Hozzon létre egy Video Indexer-fiókot az Azure Portalon
titlesuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan hozhat létre a Video Indexer-fiókot az Azure Portalon.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 11/19/2018
ms.author: juliako
ms.openlocfilehash: 49e05047d58cc5b6bb5e0099c24a131a26dc8af1
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2018
ms.locfileid: "52292365"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Hozzon létre egy kapcsolódik az Azure Video Indexer-fiókot

A Video Indexer-fiók létrehozásakor választhat egy ingyenes próbafiókot (ahol egy bizonyos számú ingyenes indexelési percet kap) vagy egy fizetős lehetőséget (ahol nincs kvótakorlát). Az ingyenes próbaverzióval a Video Indexer akár 600 perc ingyenes indexelést biztosít a webhely felhasználói számára, és akár 2400 perc ingyenes indexelést biztosít az API-felhasználóknak. A fizetős lehetőség létrehozhat egy Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók. Ön az indexelt perceket és a Media Accounttal kapcsolatos díjakat fizeti ki. 

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. 

    Ha nem rendelkezik Azure-előfizetésem, regisztráljon [Azure ingyenes próbaverziója](https://azure.microsoft.com/free/).

* Egy Azure Active Directory (AD) tartományban. 

    Ha nem rendelkezik Azure AD-tartomány, hozzon létre ezt a tartományt az Azure-előfizetésében. További információkért lásd: [az Azure Active Directoryban egyéni tartománynevek kezelése](../../active-directory/users-groups-roles/domains-manage.md)

* Egy felhasználó és az Azure AD-tartomány tagja. Csatlakozás a Video Indexer-fiókot az Azure-bA fogja használni ehhez a taghoz.

    Ennek a felhasználónak meg kell felelnie ezeknek a feltételeknek:

    * Azure AD-felhasználót a munkahelyi vagy iskolai fiókkal, nem egy személyes fiók, mint például az Outlook.com-os, live.com vagy hotmail.com lehet.
        
        ![AAD-felhasználók](./media/create-account/all-aad-users.png)

    *  Az Azure-előfizetésben egy tulajdonosi szerepkör, vagy a közreműködői és a felhasználói hozzáférés rendszergazdája szerepkör tagjának lennie. A felhasználó is hozzáadhatók kétszer, 2 szerepkört. Miután a közreműködő, és egyszer a felhasználói hozzáférés rendszergazdája.

        ![Hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

* Az Azure portal használatával EventGrid erőforrás-szolgáltató regisztrálása.

    Az a [az Azure portal](https://portal.azure.com/), lépjen a **előfizetések** > [. előfizetés] > **ResourceProviders** > **Microsoft.EventGrid**. Ha nem a "Regisztrálva" állapotban lévő, kattintson a **regisztrálása**. Néhány perc alatt regisztrálni vesz igénybe. 

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

1. Nyissa meg a [Video Indexer](https://www.videoindexer.ai/) webhelyét, és jelentkezzen be.

2. Kattintson a **csatlakozás az Azure** gombra:

    ![Csatlakozás az Azure-bA](./media/create-account/connect-to-azure.png)

3. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést. 

    ![a Video Indexer csatlakoztatása az Azure-bA](./media/create-account/connect-vi-to-azure-subscription.png)

4. Egy Azure-régiót választhat a támogatott helyek: USA nyugati RÉGIÓJA 2, Észak-Európában vagy Kelet-Ázsia.
5. A **Azure Media Services-fiók**, válasszon egyet az alábbi lehetőségek közül:

    * Egy új Media Services-fiók létrehozásához válassza **hozzon létre új erőforráscsoportot**. Adja meg az erőforráscsoport nevét.

        Az Azure az előfizetésében, beleértve az új Azure Storage-fiókot hoz létre az új fiók. Új Media Services-fiók egy alapértelmezett kezdeti konfigurációt a egy folyamatos átviteli végponton, és 10 S3 szintű fenntartott egységet tartalmaz.
    * Meglévő Media Services-fiók használatához válassza **használjon meglévő erőforrást**. A fiókok listájából válassza ki a fiókját.

        A Media Services-fiók a Video Indexer-fiókot és ugyanabban a régióban kell rendelkeznie. Indexelési időtartama és az alacsony átviteli sebesség minimalizálása érdekében módosítsa a típusát és a szolgáltatás számára fenntartott egységek száma **10 S3 szintű fenntartott egységet** a Media Services-fiók.
    * A kapcsolat konfigurálásához kattintson a **váltson át manuális konfigurációs**. 
    
        Előfordulhat, hogy szeretné a kapcsolat manuális konfigurálása, ha valamilyen okból az automatikus beállítást nem lehetett végrehajtani, vagy ha a beállítás és konfiguráció eltér a gyakori helyzetek, vagy szeretné engedélyezni a láthatóságát és a beállításokat. 
        
        Az a **Video Indexer csatlakoztatása Azure-előfizetéshez**, adja meg a következő információkat.

        |Beállítás|Leírás|
        |---|---|
        |Video Indexer régiója|A Video Indexer-fiókot régió neve. A jobb teljesítmény és alacsonyabb költségek azt javasoljuk, adja meg a nevét, a régió, ahol az Azure Media Services-erőforrás és Azure Storage-fiókban találhatók. |
        |Az Azure Active Directory (AAD) bérlő|Az Azure AD-bérlővel, például "contoso.onmicrosoft.com" neve. A bérlői kapcsolatos információkat az Azure Portalról kérhető. Vigye a kurzort az egérmutatót a bejelentkezett felhasználó felső sarokban.|
        |Előfizetés azonosítója|Az Azure-előfizetést amellyel ezt a kapcsolatot kell létrehozni. Az előfizetés-azonosító az Azure Portalról kérhető. Kattintson a **minden szolgáltatás** a bal oldali panelen, és keressen a "előfizetések". Válassza ki, **előfizetések** , majd válassza a kívánt Azonosítóját az előfizetések listáját.|
        |Az Azure Media Services erőforráscsoport-név|A név, az erőforráscsoport, amelyben a Media Services-fiók létezik.|
        |Médiaszolgáltatás erőforrás neve|Az Azure Media Services-erőforrás neve.|
        |Alkalmazásazonosító|A megadott Media Services-fiók engedélyeit az Azure AD alkalmazás azonosítója. További információkért lásd: [használata egyszerű szolgáltatásnév hitelesítése](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|
        |Alkalmazáskulcs|További információkért lásd: [használata egyszerű szolgáltatásnév hitelesítése](../../media-services/previous/media-services-portal-get-started-with-aad.md#service-principal-authentication).|

6. Ha elkészült, válassza ki a **Connect**. Ez a művelet eltarthat néhány percig. 

    Miután csatlakozott az Azure-ba, az új Video Indexer-fiókot a számla lista jelenik meg:

    ![Új fiók](./media/create-account/new-account.png)

7. Keresse meg az új fiók: 

    ![Video Indexer-fiókot](./media/create-account/vi-account.png)

## <a name="considerations"></a>Megfontolandó szempontok

A következő Azure Media Services kapcsolódó szempontokat kell figyelembe venni:

* Ha csatlakozott egy új Media Services-fiók, egy új erőforráscsoportot, a Media Services-fiók és a egy Storage-fiók láthatja az Azure-előfizetésében.
* Ha csatlakozott egy új Media Services-fiók, a Video Indexer fogja beállítani az adathordozó **szolgáltatás számára fenntartott egységek** 10 S3 szintű egységre bővíthető:

    ![A Media Services-szolgáltatás számára fenntartott egységek](./media/create-account/ams-reserved-units.png)

* Ha csatlakozik a meglévő Media Services-fiókja, a Video Indexer nem módosítja a meglévő adathordozó **szolgáltatás számára fenntartott egységek** konfigurációja.

    Médiák száma és típusa beállításához szüksége lehet **szolgáltatás számára fenntartott egységek**, a tervezett terhelés alapján. Ne feledje, hogy ha a terhelés túl magas, és nem rendelkezik elegendő egységek vagy a sebességét, videók feldolgozása eredményezhet időtúllépési hibákat.

* Ha csatlakozott egy új Media Services-fiók, a Video Indexer automatikusan elindítja az alapértelmezett **folyamatos átviteli végponton** benne:

    ![A Media Services-streamvégpont](./media/create-account/ams-streaming-endpoint.png)

* Ha csatlakozik a meglévő Media Services-fiókja, a Video Indexer nem módosítja az alapértelmezett Streamvégpont konfiguráció. Ha ott nem fut. **folyamatos átviteli végponton**, nem lesz a Media Services-fiók vagy a Video Indexer-videók.

## <a name="next-steps"></a>További lépések

Programozott módon kezelheti a próbaverziós fiókjához és/vagy a Video Indexer-fiókokkal, amelyek köti össze az Azure-ban utasításait követve: [használható API-khoz](video-indexer-use-apis.md).

Az azonos Azure AD-felhasználót az Azure-ba való kapcsolódáskor használt kell használnia.


