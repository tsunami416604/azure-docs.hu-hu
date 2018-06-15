---
title: Videó indexelő kapcsolódik az Azure-fiók létrehozása |} Microsoft Docs
description: Ez a cikk bemutatja, hogyan videó indexelő kapcsolódik az Azure-fiók létrehozása.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: ac9093d41a2e70905ea82c6d11f020696488ff27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349862"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Videó indexelő kapcsolódik az Azure-fiók létrehozása

A videó indexelt tartalom próbafiókot használatakor szab a kvóta és videók indexelheti száma. Ez a cikk bemutatja, hogyan hozzon létre egy Azure-előfizetéssel, amely esetben nem a működés felső korlátjának, és használja a használatalapú árképzési kapcsolódó videó indexelő fiókot.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. 

    Ha Azure-előfizetés még nem rendelkezik, regisztráljon [Azure ingyenes próbaverzió](https://azure.microsoft.com/free/).

* Egy Azure Active Directory (AD) tartományhoz. 

    Ha nem rendelkezik az Azure AD-tartomány, hozzon létre ebben a tartományban az Azure-előfizetéshez.

* Egy felhasználó és az Azure AD-tartomány tagjának. Ez a tag videó indexelő fiókja Azure történő csatlakozás során fogja használni.

    Ennek a felhasználónak meg kell felelnie ezeknek a feltételeknek:

    * Egy Azure AD-felhasználó a munkahelyi vagy iskolai fiókkal, nem személyes fiók, például az Outlook.com-os, live.com vagy hotmail.com lehet.
        
        ![minden AAD-felhasználókat](./media/create-account/all-aad-users.png)

    *  Az Azure-előfizetésben egy tulajdonosi szerepkört, vagy a közreműködői és a felhasználói hozzáférés adminisztrátora szerepkör tagjának lennie.

        ![Hozzáférés-vezérlés](./media/create-account/access-control-iam.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

1. Jelentkezzen be, hogy a felhasználó, és kattintson a **csatlakozás az Azure-bA** gombra:

    ![Csatlakozás az Azure szolgáltatáshoz](./media/create-account/connect-to-azure.png)

2. Amikor megjelenik az előfizetések listája, válassza ki a használni kívánt előfizetést. 

    ![Csatlakozás az Azure videó az indexelő szolgáltatáshoz](./media/create-account/connect-vi-to-azure-subscription.png)

3. Egy Azure-régiót választani a támogatott helyek: 2 USA nyugati régiója, Észak-Európa vagy Kelet-Ázsia.
4. A **Azure Media Services-fiók**, ezek a lehetőségek közül választhat:

    * Egy új Media Services-fiók létrehozásához válassza **hozzon létre új erőforráscsoport**. Adja meg az erőforráscsoport nevét.

        Azure hoz létre az új fiókjába az előfizetés, beleértve az új Azure Storage-fiók. Új Media Services-fiók egy alapértelmezett kezdeti konfigurációt a Streamvégponton és 10 S3 fenntartott egységek rendelkezik.
    * Egy meglévő Media Services-fiók használatához válassza **használja a meglévő erőforrás**. A fiókok listájából válassza ki a fiókját.

        A Media Services-fiók ugyanabban a régióban, a videó indexelő fiókkal kell rendelkeznie. Indexelési időtartamát és alacsony átviteli minimalizálása érdekében módosítsa a fenntartott egységek száma és típusa **10 S3 fenntartott egységek** a Media Services-fiók.
    * A kapcsolat kézzel konfigurálásához kattintson a **kézi konfigurálás váltani** hivatkozásra, és adja meg a szükséges adatokat:

    ![Csatlakozás az Azure videó az indexelő szolgáltatáshoz](./media/create-account/connect-vi-to-azure-subscription-2.png)

5. Amikor elkészült, válassza ki a **Connect**. A művelet néhány percet vehet. 

    Miután csatlakozott az Azure-ba, az új videó indexelő fiók fiók listájában jelenik meg:

    ![Új fiók](./media/create-account/new-account.png)

6. Keresse meg az új fiók: 

    ![Videó indexelő fiók](./media/create-account/vi-account.png)

## <a name="considerations"></a>Megfontolandó szempontok

A következő Azure Media Services kapcsolódó érvényesek:

* Ha egy új Media Services-fiókhoz csatlakozik, a látni fogja egy új erőforráscsoportot, a Media Services-fiók és a tárfiókot az Azure-előfizetéshez.
* Ha egy új Media Services-fiókhoz csatlakozik, videó indexelő állítja be az adathordozó **fenntartott egységek** 10 S3 egységnél:

    ![A Media Services fenntartott egységek](./media/create-account/ams-reserved-units.png)

* Ha egy meglévő Media Services-fiókhoz csatlakozik, videó indexelő nem módosítja a meglévő adathordozó **fenntartott egységek** konfigurációs.

    Szükség lehet, úgy, hogy a média száma és típusa **fenntartott egységek**, a tervezett terhelés alapján. Ne feledje, hogy ha a terhelés magas, és nem rendelkezik elegendő egység vagy a sebesség, videók feldolgozása eredményezheti időtúllépési hibák.

* Ha egy új Media Services-fiókhoz csatlakozik, a videó indexelő automatikusan elindul egy **Streamvégponton** benne:

    ![A Media Services streamvégpont](./media/create-account/ams-streaming-endpoint.png)

* Ha egy meglévő Media Services-fiókhoz csatlakozik, videó indexelő nem változtatja meg az adatfolyam-továbbítási végpontok konfigurációs. Ha nem fut a **Streamvégponton**, nem tudnak a Media Services-fiók, vagy videó indexelő nézzenek.

## <a name="use-video-indexer-apis-v2"></a>Videó indexelő használata API-k v2

Programozott módon használhatják a próbafiókkal és/vagy videó indexelő fiókokhoz azure utasításait követve csatlakozó: [használata API-k](video-indexer-use-apis.md).

Az azonos Azure AD-felhasználó, az Azure-ba való kapcsolódáskor használt kell használnia.

## <a name="next-steps"></a>További lépések

[Vizsgálja meg a kimeneti JSON részleteit](video-indexer-output-json-v2.md).

