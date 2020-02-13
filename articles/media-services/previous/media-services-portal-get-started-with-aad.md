---
title: Az Azure AD-hitelesítés használatának első lépései a Azure Portal használatával | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Portal az Azure Active Directory (Azure AD) hitelesítés elérésére a Azure Media Services API felhasználásához.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 4428187c985c1004c88f2ac20b0e5811803cce2a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162769"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Az Azure AD-hitelesítés használatának első lépései a Azure Portal használatával

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-from-v2-to-v3.md)

Megtudhatja, hogyan érheti el a Azure Portal az Azure Active Directory (Azure AD) hitelesítés eléréséhez a Azure Media Services API eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók szükséges. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](media-services-portal-create-account.md).
- Győződjön meg arról, hogy áttekinti a [Azure Media Services API-t az Azure ad-hitelesítés áttekintésével](media-services-use-aad-auth-to-access-ams-api.md). 

Ha az Azure AD-hitelesítést Azure Media Services használatával használja, két hitelesítési lehetőség közül választhat:

- **Felhasználói hitelesítés**. Az alkalmazást használó személy hitelesítése Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Erre példa egy olyan felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak. 
- **Egyszerű szolgáltatás hitelesítése**. Szolgáltatás hitelesítése. Az ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok futtatására szolgálnak: webalkalmazások, Function apps, Logic apps, API-k vagy egy szolgáltatás.

> [!IMPORTANT]
> Jelenleg a Media Services támogatja az Azure Access Control szolgáltatás hitelesítési modelljét. A Access Control engedélyezése azonban a 2018. június 1-jén elavulttá válik. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="select-the-authentication-method"></a>Válassza ki a hitelesítési módszert

1. A [Azure Portal](https://portal.azure.com/)válassza ki Media Services-fiókját.
2. Válassza ki, hogyan szeretne csatlakozni a Media Services API-hoz.

    ![Válassza ki a hálózati mód lapot](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Felhasználóhitelesítés

Ha a felhasználói hitelesítési lehetőség használatával szeretne csatlakozni a Media Services API-hoz, az ügyfélalkalmazás a következő paraméterekkel rendelkező Azure AD-tokent kell kérnie:  

* Azure AD-bérlői végpont
* Erőforrás-URI Media Services
* Media Services (natív) alkalmazás ügyfél-azonosítója 
* Media Services (natív) alkalmazás átirányítási URI-ja 
* Erőforrás URI-ja REST Media Services

A paraméterek értékeit a **Media Services API felhasználói hitelesítés** lapjával szerezheti be. 

![Kapcsolat felhasználói hitelesítéssel oldal](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

Ha a Media Services API-hoz csatlakozik a Media Services Microsoft .NET SDK használatával, a szükséges értékek az SDK részeként elérhetők lesznek. További információ: [Az Azure ad-hitelesítés használata a Azure Media Services API .net-tel való eléréséhez](media-services-dotnet-get-started-with-aad.md).

Ha nem használja a Media Services .NET Client SDK-t, manuálisan kell létrehoznia egy Azure AD-jogkivonat-kérelmet a korábban tárgyalt paraméterek használatával. További információ: az Azure [ad-hitelesítési függvénytár használata az Azure ad-jogkivonat beszerzéséhez](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Ha az egyszerű szolgáltatásnév beállítással szeretne csatlakozni a Media Services API-hoz, a középső rétegbeli alkalmazásnak (webes API-nak vagy webalkalmazásnak) olyan Azure AD-jogkivonatot kell igényelnie, amely a következő paraméterekkel rendelkezik:  

* Azure AD-bérlői végpont
* Erőforrás-URI Media Services 
* Erőforrás URI-ja REST Media Services
* Azure AD-alkalmazás értékei: az **ügyfél-azonosító** és az **ügyfél titka**

A paraméterek értékeit a **kapcsolódás Media Services API-hoz egyszerű szolgáltatásnév** lapon érheti el. Ezen a lapon hozhat létre új Azure AD-alkalmazást, vagy kijelölhet egy meglévőt. Az Azure AD-alkalmazás kiválasztása után beszerezheti az ügyfél-azonosítót (alkalmazás-azonosítót), és létrehozhatja az ügyfél titkos (kulcs) értékeit. 

![Az egyszerű szolgáltatásnév oldalának összekapcsolása](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Amikor megnyílik az **egyszerű szolgáltatásnév** panel, az első Azure ad-alkalmazás van kiválasztva, amely megfelel az alábbi feltételeknek:

- Ez egy regisztrált Azure AD-alkalmazás.
- Közreműködői vagy tulajdonosi szerepkörön alapuló Access Control engedélyekkel rendelkezik a fiókhoz.

Egy Azure AD-alkalmazás létrehozása vagy kiválasztása után létrehozhatja és átmásolhatja az ügyfél titkos kulcsát (a kulcsot) és az ügyfél-azonosítót (az alkalmazás AZONOSÍTÓját). Ebben az esetben az ügyfél titkos és ügyfél-azonosítója szükséges a hozzáférési jogkivonat lekéréséhez.

Ha nem rendelkezik az Azure AD-alkalmazások tartományba való létrehozásához szükséges engedélyekkel, a panel Azure AD-alkalmazás vezérlői nem jelennek meg, és egy figyelmeztető üzenet jelenik meg.

Ha a Media Services .NET SDK használatával csatlakozik a Media Services API-hoz, tekintse meg [Az Azure ad-hitelesítés használata a Azure Media Services API .net-tel való eléréséhez](media-services-dotnet-get-started-with-aad.md)című témakört.

Ha nem használja a Media Services .NET Client SDK-t, manuálisan kell létrehoznia egy Azure AD-jogkivonat-kérelmet a korábban tárgyalt paraméterek használatával. További információ: az Azure [ad-hitelesítési függvénytár használata az Azure ad-jogkivonat beszerzéséhez](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Az ügyfél-azonosító és az ügyfél titkos kulcsának beolvasása

Miután kiválasztott egy meglévő Azure AD-alkalmazást, vagy kiválasztja az új létrehozása lehetőséget, a következő gombok jelennek meg:

![Engedélyek kezelése gomb és alkalmazás kezelése gomb](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Az Azure AD-alkalmazás panel megnyitásához kattintson az **alkalmazás kezelése**lehetőségre. Az **alkalmazás kezelése** panelen beszerezheti az alkalmazás ügyfél-azonosítóját (alkalmazásspecifikus azonosítóját). Az ügyfél titkos kulcsának létrehozásához válassza a **kulcsok**lehetőséget.

![Alkalmazás panel-kulcsok kezelése lehetőség](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Engedélyek és az alkalmazás kezelése

Az Azure AD-alkalmazás kiválasztása után kezelheti az alkalmazást és az engedélyeket. Ha be szeretné állítani az Azure AD-alkalmazást más alkalmazások elérésére, kattintson az **engedélyek kezelése**lehetőségre. A felügyeleti feladatokhoz, például a kulcsok és a válasz URL-címeinek módosításához, vagy az alkalmazás jegyzékfájljának szerkesztéséhez kattintson az **alkalmazás kezelése**lehetőségre.

### <a name="edit-the-apps-settings-or-manifest"></a>Az alkalmazás beállításainak vagy jegyzékfájljának szerkesztése

Az alkalmazás beállításainak vagy jegyzékfájljának szerkesztéséhez kattintson az **alkalmazás kezelése**lehetőségre.

![Alkalmazás kezelése lap](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>Következő lépések

Ismerkedés a [fájlok feltöltésével a fiókjába](media-services-portal-upload-files.md).
