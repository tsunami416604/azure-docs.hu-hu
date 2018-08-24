---
title: Hozzon létre egy kapcsolódik az Azure Video Indexer-fiókot |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan kapcsolódik az Azure Video Indexer-fiókot létrehozni.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 08/05/2018
ms.author: juliako
ms.openlocfilehash: 64a38ba617a1cc5fe1fdb3473e3cb88a49d89bb0
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42744751"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Hozzon létre egy kapcsolódik az Azure Video Indexer-fiókot

A Video Indexer-fiók létrehozásakor kiválaszthatja a (, ahol megkapja ingyenes indexelési perc bizonyos számú) egy ingyenes próbafiókot vagy egy fizetős lehetőség (Ha nem korlátozza a kvóta). Az ingyenes próbaverziót a Video Indexer legfeljebb 600 perc ingyenes indexeli a webhely számára biztosít, és akár 2400 percnyi ingyenes indexelő API számára. A fizetős lehetőség létrehozhat egy Azure-előfizetéséhez kapcsolódó Video Indexer-fiókot és az Azure Media Services-fiók. Indexelt perc, valamint a kapcsolódó adathordozó-fiók fizet díjak. 

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

1. Jelentkezzen be, hogy a felhasználó, és kattintson a **csatlakozás az Azure** gombra:

    ![Csatlakozás az Azure-bA](./media/create-account/connect-to-azure.png)

2. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést. 

    ![a Video Indexer csatlakoztatása az Azure-bA](./media/create-account/connect-vi-to-azure-subscription.png)

3. Egy Azure-régiót választhat a támogatott helyek: USA nyugati RÉGIÓJA 2, Észak-Európában vagy Kelet-Ázsia.
4. A **Azure Media Services-fiók**, válasszon egyet az alábbi lehetőségek közül:

    * Egy új Media Services-fiók létrehozásához válassza **hozzon létre új erőforráscsoportot**. Adja meg az erőforráscsoport nevét.

        Az Azure az előfizetésében, beleértve az új Azure Storage-fiókot hoz létre az új fiók. Új Media Services-fiók egy alapértelmezett kezdeti konfigurációt a egy folyamatos átviteli végponton, és 10 S3 szintű fenntartott egységet tartalmaz.
    * Meglévő Media Services-fiók használatához válassza **használjon meglévő erőforrást**. A fiókok listájából válassza ki a fiókját.

        A Media Services-fiók a Video Indexer-fiókot és ugyanabban a régióban kell rendelkeznie. Indexelési időtartama és az alacsony átviteli sebesség minimalizálása érdekében módosítsa a típusát és a szolgáltatás számára fenntartott egységek száma **10 S3 szintű fenntartott egységet** a Media Services-fiók.
    * A kapcsolat konfigurálásához kattintson a **váltson át manuális konfigurációs** hivatkozásra, és adja meg a szükséges adatokat:

    ![a Video Indexer csatlakoztatása az Azure-bA](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Ha elkészült, válassza ki a **Connect**. Ez a művelet eltarthat néhány percig. 

    Miután csatlakozott az Azure-ba, az új Video Indexer-fiókot a számla lista jelenik meg:

    ![Új fiók](./media/create-account/new-account.png)

6. Keresse meg az új fiók: 

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


