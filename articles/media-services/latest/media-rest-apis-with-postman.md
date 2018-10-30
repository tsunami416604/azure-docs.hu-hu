---
title: Postman konfigurálása az Azure Media Services REST API-hívások
description: Ismerje meg, a Media Services REST API-hívások a Postman konfigurálása.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2018
ms.author: juliako
ms.openlocfilehash: b110a57526d6e23ba53afce0a91d735fad6d247c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215786"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>A Media Services REST API-hívások a Postman konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja **Postman** így is használható az Azure Media Services (AMS) REST API-k meghívására. A cikk bemutatja, hogyan környezet és a gyűjtemény fájlok importálását **Postman**. A gyűjtemény, amely az Azure Media Services (AMS) REST API-k hívása a HTTP-kérések csoportosított definíciókat tartalmazza. A környezeti változókat, amelyek a gyűjteményt tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 
- Szükséges információk [API-k elérése](access-api-cli-how-to.md)
- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Code** REST beépülő modullal vagy **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman konfigurálása

Ebben a szakaszban konfiguráljuk a Postmant.

### <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Nyissa meg a **Postmant**.
2. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Környezet felügyelete](./media/develop-with-postman/postman-import-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
2. Keresse meg az `Azure Media Service v3 Environment.postman_environment.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le.
6. Az **Azure Media Service v3 Environment** környezet hozzáadása megtörténik.

    > [!Note]
    > Frissítse a hozzáférési változókat a fenti **Hozzáférés a Media Services API-hoz** szakaszban található értékekkel.

7. Kattintson duplán a kiválasztott fájlra, és írja be az [API elérésének](#access-the-media-services-api) lépéseiben lekért értékeket.
8. Zárja be a párbeszédpanelt.
9. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3.postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3.postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

Mielőtt elkezdené az AMS v3 erőforrások kezelésére szolgáló, és állítsa be a tokent az Azure-AD a szolgáltatásnév hitelesítési szüksége.

1. A Postman bal ablakában válassza a „Step 1: Get AAD Auth token” (1. lépés: AAD-hitelesítési jogkivonat lekérése) lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének. Az „AccessToken” értéket beállító kód megtekintéséhez kattintson a **Tests** (Tesztek) fülre. 

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="next-steps"></a>További lépések

[REST-tel fájlok Stream](stream-files-tutorial-with-rest.md).  
