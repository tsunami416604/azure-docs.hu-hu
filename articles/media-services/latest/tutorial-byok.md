---
title: Ügyfél által felügyelt kulcsok vagy saját kulcs használata (BYOK) Media Services
description: Útmutató a Media Services Storage-fiókkal rendelkező ügyfelek által felügyelt kulcsok használatához
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326143"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Oktatóanyag: az ügyfél által felügyelt kulcsok használata vagy a saját kulcs (BYOK) használata Media Services

Az 2020-05-01 API-val egy ügyfél által felügyelt RSA-kulcsot használhat olyan Media Services-fiókkal, amely rendelkezik rendszerfelügyelt identitással.  Ez az oktatóanyag egy Poster-gyűjteményt és-környezetet tartalmaz, amely REST-kérelmeket küld az Azure-szolgáltatásoknak.  Használt szolgáltatások:

- Azure Active Directory alkalmazás regisztrálása a Poster-ban
- Microsoft Graph API
- Azure Storage tárterület
- Azure Key Vault
- Media Services

Ebben az oktatóanyagban megtanulhatja, hogyan használhatja a Poster-t:

> [!div class="checklist"]
> * Jogkivonatok beszerzése az Azure-szolgáltatásokkal való használathoz.
> * Hozzon létre egy erőforráscsoportot és egy Storage-fiókot.
> * Media Services-fiók létrehozása rendszerfelügyelt identitással
> * Hozzon létre egy Key Vault, amely a Storage-fiókkal használható RSA-(ügyfél által felügyelt) kulcs tárolására szolgál.
> * Frissítse a Media Services fiókot az RSA-kulcs és a Storage-fiók használatára.
> * Használjon változókat a Poster-ben.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Egy egyszerű szolgáltatásnév regisztrálása a megfelelő engedélyekkel.
- A [Poster](https://www.postman.com)telepítése.
- Az oktatóanyaghoz tartozó Poster-gyűjtemény letöltése az [Azure-mintákhoz: Media-Services-Customer-Managed-Keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Egyszerű szolgáltatásnév regisztrálása a szükséges engedélyekkel

[Egyszerű szolgáltatásnév létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).  A szolgáltatás egyszerű titkának beszerzéséhez tekintse meg a [2. lehetőséget](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) .  Ne feledje, hogy a titkos kulcs valahol a portál titkos oldalának meghagyása után nem lesz elérhető.

Az egyszerű szolgáltatásnak az alábbi engedélyekkel kell rendelkeznie az oktatóanyagban fellépő feladatok végrehajtásához.

![az egyszerű szolgáltatáshoz szükséges engedélyek](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Poster telepítése

Ha még nem telepítette az Azure-beli Poster-t, a következő címen érheti el: [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>A gyűjtemény letöltése és importálása

Az oktatóanyaghoz tartozó Poster-gyűjtemény letöltése az [Azure-mintákhoz: Media-Services-Customer-Managed-Keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>A gyűjtemény és a környezet telepítése

1. Poster futtatása.
1. Válassza az **Importálás** lehetőséget.
1. Válassza a **fájlok feltöltése**lehetőséget.
1. Navigáljon a gyűjtemény és a környezeti fájlok mentéséhez.
1. Válassza ki a gyűjteményt és a környezeti fájlt.
1. Válassza a **Megnyitás** lehetőséget.  (Egy figyelmeztetés jelenik meg, hogy a fájlok nem lesznek importálva API-ként, hanem gyűjteményekként.  Ez rendben van.  Ezt szeretné.)
1. Ez a gyűjtemény most megjelenik a gyűjtemények BYOK.
1. A környezeti változók ekkor megjelennek a környezetekben.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>A gyűjtemény REST API-kéréseinek megismerése

A gyűjtemény a következő REST API kérelmeket biztosítja. A kérelmeket a legtöbbjük által megadott sorozatban el kell juttatni a tesztelési szkripteket, amelyek dinamikusan hoznak létre globális változókat a következő (vagy azt követő) kérelemhez a sorozatban. Nem szükséges manuálisan létrehozni a globális változókat.

A Poster-ben ezek a változók `{{ }}` zárójelek között találhatók.  Például: `{{bearerToken}}`.

1. HRE-jogkivonat beolvasása – a teszt a *bearerToken* globális változót állítja be
2. Graph-token beszerzése – a teszt a *graphToken* globális változót állítja be
3. Egyszerű szolgáltatásnév részleteinek beolvasása – a teszt a *servicePrincipalObjectId* globális változót állítja be
4. Storage-fiók létrehozása – a teszt az *storageAccountId* globális változót állítja be
5. Media Services fiók létrehozása rendszerfelügyelt identitással – a test beállítja a globális változó *principalId*
6. Key Vault létrehozása, hozzáférés biztosítása az egyszerű szolgáltatásnév számára – a test beállítja a globális változó *keyVaultId*
7. Key Vault token beszerzése – a test beállítja a globális változó *keyVaultToken*
8. RSA-kulcs létrehozása a Key vaultban – a test beállítja a globális változó *keyId*
9. Frissítse a Media Services fiókot a kulcs a Storage-fiókkal való használatához – nincs tesztelési parancsfájl ehhez a kéréshez.

## <a name="define-environment-variables"></a>Környezeti változók definiálása

1. Váltson a letöltött környezetre a környezetek legördülő lista kiválasztásával.
1. Hozza létre a környezeti változókat a Poster-ben. Ezeket a zárójelek között szereplő változókként is használják `{{ }}` .  Például: `{{tenantId}}`.

    * *tenantId* = a bérlő azonosítója
    * *servicePrincipalId* = a kedvenc metódusával létrehozott szolgáltatásnév azonosítója: portál, parancssori felület stb.
    * *servicePrincipalSecret* = az egyszerű szolgáltatáshoz létrehozott titok
    * *előfizetés* = az előfizetés azonosítója
    * *storageName* = a tárhelyhez adni kívánt név
    * *accountName* = a használni kívánt Media Service-fiók neve
    * *keyVaultName* = a használni kívánt Key Vault neve
    * *resourceLocation* = CentralUS (vagy ha valaha is el szeretné helyezni az erőforrásokat).  Ez a gyűjtemény csak a CentralUS-mel lett tesztelve.)
    * *resourceGroup* = az erőforráscsoport neve

    Az alábbi változók szabványosak az Azure-erőforrások használatához, így nem kell módosítani őket.

    * *armResource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainSuffix* = `vault.azure.net`

## <a name="send-the-requests"></a>A kérések elküldése

Miután meghatározta a környezeti változókat, futtathatja a kérelmeket egy időben a fenti sorozatban, vagy a Poster futtatásával futtathatja a gyűjteményt.

## <a name="change-the-key"></a>A kulcs módosítása

A Media Services automatikusan észlelni fogja, hogy mikor módosult a kulcs.  Ennek teszteléséhez hozzon létre egy másik verziót ugyanahhoz a kulcshoz. A Media Servicesnak 15 percnél rövidebbnek kell lennie a kulcs észleléséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a létrehozott erőforrások használatát, és **nem kívánja tovább használni a számlázást**, törölje őket.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan...
> [!div class="nextstepaction"]
> [Távoli fájl kódolása URL-cím alapján és a videó továbbítása a REST-tel](stream-files-tutorial-with-rest.md)
