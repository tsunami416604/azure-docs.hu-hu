---
title: Postás konfigurálása az Azure Media Services v3 REST API-hívásaihoz
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Postman t az Azure Media Services (AMS) REST API-k hívására.
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
ms.date: 12/05/2019
ms.author: juliako
ms.openlocfilehash: 872dad95fc5b536c51e251612f40439da020a059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779637"
---
# <a name="configure-postman-for-media-services-v3-rest-api-calls"></a>Postás konfigurálása a Media Services v3 REST API-hívásaihoz

Ez a cikk bemutatja, hogyan konfigurálhatja **a Postman** t az Azure Media Services (AMS) REST API-k hívására. A cikk bemutatja, hogyan importálhatja a környezetet és a gyűjteményfájlokat a **Postman be.** A gyűjtemény az Azure Media Services (AMS) REST API-kat hívó HTTP-kérelmek csoportosított definícióit tartalmazza. A környezeti fájl azokat a változókat tartalmazza, amelyeket a gyűjtemény használ.

A fejlesztés megkezdése előtt tekintse át [a Fejlesztés a Media Services v3 API-jával](media-services-apis-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Győződjön meg arról, hogy nem emlékszik az erőforráscsoport nevére és a Media Services-fiók nevére. 
- Az [API-k eléréséhez](access-api-cli-how-to.md) szükséges információk beszerezni
- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Kód** a REST plugin vagy **Telerik Fiddler**. 

> [!IMPORTANT]
> Tekintse át [az elnevezési konvenciókat.](media-services-apis-overview.md#naming-conventions)

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Postman konfigurálása

### <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Nyissa meg a **Postman** alkalmazást.
2. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Környezet felügyelete](./media/develop-with-postman/postman-import-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
2. Keresse meg az `Azure Media Service v3 Environment.postman_environment.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le.
6. Az **Azure Media Service v3 Environment** környezet hozzáadása megtörténik.

    > [!Note]
    > Frissítse a hozzáférési változókat a fenti **Hozzáférés a Media Services API-hoz** szakaszban található értékekkel.

7. Kattintson duplán a kiválasztott fájlra, és írja be az API elérésének lépéseiben lekért értékeket.
8. Zárja be a párbeszédpanelt.
9. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3.postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3.postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

Mielőtt elkezdené az AMS v3-erőforrások kezelésére, be kell szereznie és be kell állítania az Azure AD token szolgáltatást javaskivonat-hitelesítéshez.

1. A Postman alkalmazás bal oldali ablakában válassza az "1. lépés: AAD hitelesítéstoken beszereznie" lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének.  

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="troubleshooting"></a>Hibaelhárítás 

* Ha az alkalmazás meghibásodik a "HTTP 504: Gateway Timeout" használatával, győződjön meg arról, hogy a helyváltozó nincs kifejezetten beállítva a Media Services-fiók várható helyétől eltérő értékre. 
* Ha "A fiók nem található" hibaüzenetet kap, ellenőrizze azt is, hogy a Body JSON üzenetben lévő helytulajdonság be van-e állítva arra a helyre, ahol a Media Services-fiók található. 

## <a name="see-also"></a>Lásd még

- [Fájlok feltöltése Media Services-fiókba – REST](upload-files-rest-how-to.md)
- [Szűrők létrehozása a Media Services szolgáltatással – REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-alapú REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>További lépések

- [Fájlok streamelése rest segítségével.](stream-files-tutorial-with-rest.md)  
- [Oktatóanyag: Távoli fájl kódolása URL alapján és streamelés - REST](stream-files-tutorial-with-rest.md)
