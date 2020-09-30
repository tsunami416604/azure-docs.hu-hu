---
title: Az Azure IoT Model repository koncepcióinak megismerése | Microsoft Docs
description: Megoldás fejlesztőként vagy informatikai szakemberként megismerheti az Azure IoT Model repository alapvető fogalmait.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c82858294054b50d6edae42a3d41e9fcb89ca89d
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577798"
---
# <a name="azure-iot-model-repository"></a>Azure IoT-modell adattár

Az Azure IoT Model repository lehetővé teszi az eszközök építői számára a IoT Plug and Play eszköz modelljeinek felügyeletét és megosztását. Az eszköz modelljei a [digitális Twins modellezési nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)használatával meghatározott JSON ld-dokumentumok. A Model repository szolgáltatásban tárolt modellek a hozzáférés-vezérléssel vagy nyilvánosan, a IoT Plug and Play felhőalapú megoldás integrálásához és fejlesztéséhez szükséges hitelesítés nélkül is megoszthatók a megoldás-fejlesztőknek.

> [!NOTE]
> Az eszközök építői dönthetnek úgy, hogy közvetlenül egy eszközön, modulok használatával vagy egy IoT Edge modulban implementálják a IoT Plug and Play eszköz modelljeit.

A modell tárháza a használatával érhető el:

- [Azure IoT Model adattár](https://aka.ms/iotmodelrepo) -portál
- [Az Azure IoT Model repository REST API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Azure CLI-IoT Model adattár parancsai](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Nyilvános modellek

A modell tárházában tárolt nyilvános digitális kettős modellek mindenki számára elérhetők, hogy hitelesítés nélkül használják fel és integrálják az alkalmazásaikat. Emellett a nyilvános modellekkel az eszköz-építők és a megoldások fejlesztői számára nyitott ökoszisztémák is megoszthatják és használhatják a IoT Plug and Play eszköz modelljeit.

Tekintse meg a [modell közzététele a](#publish-a-model) **vállalati modellekben** című szakaszt a modell közzétételéhez a modell-adattárban, hogy azok nyilvánosak legyenek.

Nyilvános modell megtekintése a Model repository portál használatával:

1. Nyissa meg az [Azure IoT Model adattár-portált](https://aka.ms/iotmodelrepo).

1. Válassza a **nyilvános modellek megtekintése**lehetőséget.

    ![Nyilvános modellek megtekintése](./media/concepts-model-repository/public-models.png)

Ha a REST API használatával programozottan szeretné megtekinteni a nyilvános modellt, tekintse meg a modell REST API dokumentáció [beszerzése](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) című témakört.

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Egy nyilvános modell parancssori felülettel való megtekintéséhez tekintse meg az Azure CLI [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) parancsot.

## <a name="company-models"></a>Vállalati modellek

A vállalati modell tárháza az Azure IoT Model adattárának bérlője, amely a vállalaton vagy szervezeten belüli felhasználók által készített digitális kettős modelleket hozhat létre és kezelhet. A vállalati modellek csak a vállalat vagy szervezet hitelesített felhasználói számára érhetők el. A modell-tárház bérlői rendszergazdája engedélyeket rendelhet hozzá, és szabályozhatja a vállalat vagy szervezet más felhasználóinak hozzáférését a vállalati modell tárházában lévő modellekhez.

### <a name="set-up-your-company-model-repository"></a>A vállalati modell tárházának beállítása

A modell tárházának eléréséhez használja *munkahelyi vagy iskolai Azure Active Directory (Azure ad) fiókját* . Ha a szervezet már rendelkezik Azure AD-Bérlővel, az Azure AD-bérlőből felhasználói fiók (ok) és egyszerű szolgáltatásnév használható.

Az Azure AD-bérlő beállításával és a felhasználók vagy szolgáltatások Azure AD-bérlőben való létrehozásával kapcsolatos további információkért lásd: [További információ](#additional-information) szakasz.

- Ha Ön a szervezet első felhasználója a modell-adattár eléréséhez vagy a portálra való bejelentkezéshez, megkapja a **bérlői rendszergazda** szerepkört. Ez a szerepkör lehetővé teszi, hogy szerepköröket rendeljen a szervezet adattár-bérlője más felhasználóihoz.

- Más szerepköröket egy **bérlői rendszergazda** , például **olvasási modellek** vagy **modellek létrehozásával**rendelhet hozzá.

### <a name="understand-access-management"></a>A hozzáférés-kezelés ismertetése

A következő táblázat összefoglalja a vállalati modell tárházának támogatott képességeit és a hozzájuk kapcsolódó engedélyeket:

| Képesség  | Engedély| Leírás|
|-------------|-----------|------------|
|Modellek olvasása|Modellek olvasása|Alapértelmezés szerint a vállalati bérlő összes felhasználója megtekintheti a vállalati modelljeiket. Emellett a felhasználó megtekintheti a többi vállalat által mások számára megosztott privát modell (eke) t is.|
|Hozzáférés kezelése|Hozzáférés kezelése|Kezelheti a felhasználói szerepkör-hozzárendelést (Hozzáadás vagy Eltávolítás) a szervezet többi felhasználója számára.|
|Modellek létrehozása|Modellek létrehozása|Modellek létrehozása a vállalati modell adattárában.|
|Modellek közzététele|Modellek közzététele|Tegye közzé a modelleket, hogy bárki megtekintse a modellt.|

A következő táblázat összefoglalja a támogatott szerepköröket és azok képességeit a modell adattárában, amely a hozzáférés-kezeléshez használható.

|Szerepkör|Képesség|
|----|----------|
|TenantAdministrator|Hozzáférés kezelése, modellek olvasása|
|Létrehozó|Modellek létrehozása, modellek olvasása|
|Publisher|Modellek közzététele, modellek olvasása|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Biztonsági jogkivonat átadása a vállalati modellek REST APIhoz való elérésekor

Ha a REST API-kat a privát vagy megosztott vállalati modellek kezelésére hívja meg, akkor JWT formátumban kell megadnia egy engedélyezési jogkivonatot a felhasználóhoz vagy az egyszerű szolgáltatáshoz. Tekintse meg a [További információk](#additional-information) című szakaszt, amelyből megtudhatja, hogyan KÉRHET le JWT tokent egy felhasználóhoz vagy egyszerű szolgáltatáshoz.

A JWT jogkivonatot az API engedélyezési HTTP-fejlécében kell átadni a vállalati modellek vagy a megosztott modellek célzása során. A nyilvános modellek célzásakor nincs szükség az JWT tokenre.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Vállalat vagy megosztott modellek megtekintése

Az adattár-bérlő *olvasói* szerepkörének tagjának kell lennie, vagy a modellt meg kell osztani Önnel, hogy beolvassa a modellt. Megtekintheti az Önnel megosztott, közzé nem tett modellek listáját, valamint az Önnel megosztott közzétett modellek listáját. Alapértelmezés szerint a felhasználók elolvashatják a vállalat modelljeit, a többi vállalat által megosztott modelleket, valamint az összes nyilvános modellt.

Vállalat vagy közös modell megtekintése a portál használatával:

1. Jelentkezzen be az [Azure IoT Model adattár-portálra](https://aka.ms/iotmodelrepo).

1. **Vállalati modellek** kibontása – **Közzététel** nélkül a bal oldali panelen

    ![Vállalati modellek megtekintése](./media/concepts-model-repository/view-company-models.png)

1. Megosztott modellek kibontása **– Közzététel** nélkül a bal oldali panelen

    ![Megosztott modellek megtekintése](./media/concepts-model-repository/view-shared-models.png)

Ha a REST API használatával szeretne megtekinteni egy vállalatot vagy közös modellt, tekintse meg a [modell Beolvasása](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API dokumentációját. Tekintse meg a [biztonsági jogkivonatok átadása a vállalati modellekhez való hozzáféréskor REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) információt arról, hogyan lehet a http-KÉRELEMben JWT-hitelesítési fejlécet továbbítani.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-09-30").ConfigureAwait(false);
```

Ha a parancssori felület használatával szeretné megtekinteni a vállalati modellt vagy a megosztott modellt, tekintse meg az Azure CLI [Get Model](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true) parancsot.

### <a name="manage-roles"></a>Szerepkörök kezelése

A bérlői rendszergazda szerepköröket rendelhet hozzá az adattár-bérlőben található felhasználókhoz, így saját modelleket hozhat létre a vállalat vagy szervezet számára, a modelleket tehet közzé vagy szerepköröket kezelhet más felhasználók számára.

Felhasználó hozzáadása modell-tárház bérlői szerepkörhöz a portál használatával:

1. Jelentkezzen be az [Azure IoT Model adattár-portálra](https://aka.ms/iotmodelrepo).

1. Válassza a **hozzáférés-kezelés** lehetőséget a bal oldali ablaktáblán, majd válassza a **+ Hozzáadás**lehetőséget. Az **engedély hozzáadása** panelen írja be annak a felhasználónak a munkahelyi címeit, amelyet hozzá szeretne adni a szerepkörhöz:

    ![Munkahelyi címek hozzáadása](./media/concepts-model-repository/add-user.png)

1. Válassza ki azt a szerepkört, amelyhez hozzá szeretné adni a felhasználót a **szerepkör** legördülő listából. Ezután válassza a **Mentés**lehetőséget:

    ![Szerepkör kiválasztása](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Modell feltöltése

A modellnek a vállalati modell tárházba való feltöltéséhez az adattár bérlője **létrehozói** szerepkörének tagjának kell lennie.

Ezek a modellek nincsenek közzétéve, és alapértelmezés szerint csak a szervezeten belüli felhasználók férhetnek hozzá. Egy vagy több közzé nem tett modellt is megoszthat külső felhasználókkal.

A feltöltött modellek nem változtathatók meg.

Ezeknek a modelleknek a modell-azonosítóinak globálisan egyedinek kell lenniük az összes feltöltött modell összes tárház-bérlője között.

Modell feltöltése a portál használatával:

1. Jelentkezzen be az [Azure IoT Model adattár-portálra](https://aka.ms/iotmodelrepo).

1. Bontsa ki a **vállalati modellek** elemet a bal oldali ablaktáblán, és válassza a **modell létrehozása**lehetőséget. Ezután válassza a **JSON importálása**lehetőséget.

    ![Modell létrehozása](./media/concepts-model-repository/create-model.png)

1. Válassza ki a feltölteni kívánt fájlt. Ha a portál sikeresen érvényesíti a modellt, válassza a **Mentés**lehetőséget.

Modell feltöltéséhez a REST API használatával tekintse meg a [Model API létrehozása](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) című témakört. Tekintse meg a [biztonsági jogkivonatok átadása a vállalati modellekhez való hozzáféréskor REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) információt arról, hogyan lehet a http-KÉRELEMben JWT-hitelesítési fejlécet továbbítani.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-09-30", httpContent).ConfigureAwait(false);
```

Ha a parancssori felület használatával szeretne feltölteni egy modellt, tekintse meg az Azure CLI [modell létrehozása](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true) parancsot.

### <a name="publish-a-model"></a>Modell közzététele

Modell közzétételéhez a következő követelményeknek kell teljesülniük:

1. A modell közzétételéhez a szervezetnek a [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) tagjának kell lennie. A partner Center-fiók létrehozásával kapcsolatban lásd: [partner Center-fiók létrehozása](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). A fiók jóváhagyása után közzéteheti a modelleket. További információ: a [partner Center gyakori kérdései](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. A felhasználónak az adattár bérlője *közzétevői* szerepkör tagjának kell lennie.

A szervezeten belüli felhasználók által létrehozott és közzétett modellek *közzétett modellként*láthatók. Ezek a modellek nyilvánosak, és a **nyilvános modellekben**bárki megtalálhatja őket.

Modell közzététele a portál használatával:

1. Jelentkezzen be az [Azure IoT Model adattár-portálra](https://aka.ms/iotmodelrepo).

2. Bontsa ki a **vállalati modellek** elemet a bal oldali ablaktáblán, és válassza ki a közzétenni kívánt modellt. Ezután válassza a **Publish** (Közzététel) lehetőséget.

    ![Modell közzététele](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Ha értesítést kap arról, hogy nem rendelkezik Microsoft-partneri (MPN-) AZONOSÍTÓval, kövesse az értesítés regisztrációs lépéseit. További információt a szakasz elején található követelmények című témakörben talál.

Ha a REST API használatával szeretne közzétenni egy modellt, tekintse meg a [modell közzététele](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) REST API dokumentációját. Adja meg a lekérdezési karakterlánc paramétert `update-metadata=true` egy modell közzétételéhez a REST API használatával. Tekintse meg a [biztonsági jogkivonatok átadása a vállalati modellekhez való hozzáféréskor REST API](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) információt arról, hogyan lehet a http-KÉRELEMben JWT-hitelesítési fejlécet továbbítani.

Ha a parancssori felületen szeretné közzétenni a modelleket, tekintse meg a [modell közzététele](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true) az Azure CLI-ben című témakört.

> [!NOTE]
> A minősítési tesztek futtatása előtt közzé kell tenni a modelleket a modell adattárában. További információ: [IoT Plug and Play-eszközök tanúsítása](howto-certify-device.md).

### <a name="share-a-model"></a>Modell megosztása

Megoszthatja a külső szervezetek felhasználóival létrehozott céges modelleket. Ily módon lehetővé teheti a közreműködők számára, hogy megtekintsék és fejlesszenek a saját vállalati modelljeivel rendelkező megoldásokat.

Előfordulhat például, hogy egy eszköz gyártója a vállalat vagy a szervezet számára szeretné megőrizni a saját modelleket. Lehetnek olyan ügyfelek, akiknek szükségük van arra, hogy az eszköz képességei bizalmasak maradjanak.

A modellek megosztása több vállalat vagy szervezet számára lehetővé teszi a nem nyilvános modellekhez való biztonságos hozzáférést.

Vállalati modell megosztása a portál használatával:

- Ha Ön a modell létrehozója, a **megosztás** és a gombokkal **való** megosztása aktív, ha megtekinti a modellt a **vállalati modellek** szakaszban.

    ![Modell megosztása](./media/concepts-model-repository/share-model.png)

- A modell külső felhasználóval való megosztásához válassza a **megosztás**lehetőséget. A **modell megosztása** panelen adja meg a külső felhasználó e-mail-címét, majd válassza a **Mentés**lehetőséget.

- Ha meg szeretné tekinteni azokat a felhasználókat, akikkel megosztotta a modellt, válassza a **megosztott a**következővel lehetőséget.

- Ha meg szeretné szüntetni a modell megosztását egy adott felhasználóval, válassza ki a felhasználót a **megosztott a** (z) ablaktáblán a felhasználók listájából. Ezután válassza az **Eltávolítás** lehetőséget, és erősítse meg, ha a rendszer kéri.

    ![Megosztás leállítása](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>További információ

Az alábbi témakörök hasznosak lehetnek az Azure AD használatakor:

- Új Azure AD-bérlő létrehozásához tekintse meg [az új bérlő létrehozása az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)című témakört. A legtöbb szervezet már rendelkezik Azure AD-Bérlővel.

- Ha felhasználókat vagy vendég felhasználókat szeretne hozzáadni egy Azure AD-bérlőhöz, tekintse meg a [felhasználók hozzáadása vagy törlése az Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory)-ben című témakört.

- Egyszerű szolgáltatás Azure AD-bérlőhöz való hozzáadásával kapcsolatban tekintse meg a [hogyan használható a portál egy Azure ad-alkalmazás és egyszerű szolgáltatásnév létrehozásához, amely hozzáférhet az erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

- Ha szeretné megtudni, hogyan szerezhet be JWT tokent az Azure AD-ből a REST API-k hívásakor, tekintse meg [a jogkivonat beszerzése az Azure ad-ből az ügyfélalkalmazástól érkező kérések engedélyezéséhez](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)című témakört.

## <a name="next-steps"></a>További lépések

A javasolt következő lépés a [IoT Plug and Play architektúra](concepts-architecture.md)áttekintése.
