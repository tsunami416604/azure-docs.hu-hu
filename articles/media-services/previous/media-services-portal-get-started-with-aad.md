---
title: Ismerkedés az Azure AD-hitelesítés az Azure portal használatával |} A Microsoft Docs
description: Útmutató az Azure portal használata az Azure Active Directory (Azure AD-) hitelesítést használhat az Azure Media Services API eléréséhez.
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: bae134d30bd175656e4da9121bca8dbfcfe7fcdd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229459"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Ismerkedés az Azure AD-hitelesítés az Azure portal használatával

Ismerje meg, hogyan használható az Azure portal eléréséhez az Azure Active Directory (Azure AD-) hitelesítést az Azure Media Services API eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [Azure Media Services-fiók létrehozása az Azure portal használatával](media-services-portal-create-account.md).
- Győződjön meg arról, hogy tekintse át a [elérése az Azure Media Services API Azure AD-hitelesítés – áttekintés](media-services-use-aad-auth-to-access-ams-api.md). 

Az Azure AD-hitelesítés használatakor az Azure Media Services két hitelesítési lehetősége van:

- **Felhasználói hitelesítés**. A személy, aki használja az alkalmazást az való kommunikációhoz a Media Services-erőforrások hitelesítéséhez. Az interaktív alkalmazás először kéri a felhasználótól a hitelesítő adatokat. Ilyen például, a kódolási feladatok figyelésére, vagy az élő adások online közvetítése jogosult felhasználók által használt felügyeleti konzolalkalmazást. 
- **Egyszerű szolgáltatásnév hitelesítése**. Egy szolgáltatás hitelesítéséhez. Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket démonszolgáltatásokat, a középső rétegű services vagy az ütemezett feladatok futtatása: web apps, a függvényalkalmazások, a logic apps, API-k vagy mikroszolgáltatások.

> [!IMPORTANT]
> Jelenleg a Media Services az Azure Access Control service hitelesítési modellt támogatja. Azonban a hozzáférés-vezérlés engedélyezési érvénytelenné válnak 2018. június 1. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="select-the-authentication-method"></a>A hitelesítési módszer kiválasztása

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a Media Services-fiókját.
2. Válassza ki a Media Services API-t kapcsolódás.

    ![Válassza ki a csatlakozási módszer lapja](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Felhasználóhitelesítés

A felhasználó hitelesítési lehetőség használatával csatlakozni a Media Services API-t, az ügyfélalkalmazás van szüksége az Azure AD-token a következő paraméterekkel rendelkező kérelem:  

* Az Azure AD-bérlő végpont
* A Media Services-erőforrás-URI
* A Media Services (natív) alkalmazás Ügyfélazonosítója 
* A Media Services (natív) alkalmazás átirányítási URI 
* Erőforrás URI-t a Media Services – REST

Megtekintheti az értékeket a paraméterek a **Media Services API-hoz felhasználói hitelesítéssel** lapot. 

![Csatlakozás a felhasználói hitelesítés lap](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

A Media Services a Microsoft .NET SDK-val csatlakozik a Media Services API-t, ha a szükséges értékek lesznek elérhetők az SDK részeként. További információkért lásd: [eléréséhez az Azure Media Services API .NET-keretrendszerrel történő hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md).

Ha a Media Services .NET client SDK nem használ, manuálisan kell létrehoznia egy Azure AD-jogkivonatkérelem a korábban említett paraméterek használatával. További információkért lásd: [jogkivonat beszerzése az Azure AD használata az Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

A szolgáltatás egyszerű lehetőség használatával csatlakozni a Media Services API-t, a középső rétegbeli alkalmazás (webes API-t vagy webes alkalmazás) van szüksége az Azure AD-token a következő paraméterekkel rendelkező kérelem:  

* Az Azure AD-bérlő végpont
* A Media Services-erőforrás-URI 
* Erőforrás URI-t a Media Services – REST
* Az Azure AD-alkalmazás értékeire: a **ügyfél-azonosító** és **titkos Ügyfélkód**

Megtekintheti az értékeket a paraméterek a **kapcsolódás a Media Services API-hoz szolgáltatásnévvel** lapot. Ezen a lapon hozzon létre egy új Azure AD-alkalmazást, vagy válasszon ki egy meglévőt. Miután kiválasztotta az Azure AD-alkalmazás, ügyfél-azonosító (Alkalmazásazonosító) beszerzése, és az ügyfél titkos kulcs (kulcs) értékek létrehozásához. 

![Szolgáltatás egyszerű oldal összekapcsolása](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Ha a **szolgáltatásnév** panel nyílik meg, az első Azure AD-alkalmazást, amely megfelel a következő feltételeknek van kiválasztva:

- Egy regisztrált Azure AD-alkalmazást.
- A fiók közreműködői vagy hozzáférés-vezérlés Owner Role-Based engedélyekkel rendelkezik.

Miután hoz létre, vagy válassza ki az Azure AD-alkalmazás, hozzon létre, és másolja ki az ügyfél titkos kulcs (kulcs) és az ügyfél-azonosító (Alkalmazásazonosító). A titkos Ügyfélkód és ügyfél-Azonosítót a hozzáférési jogkivonat ebben a forgatókönyvben beszerzése szükségesek.

Ha nem rendelkezik a tartományban lévő Azure AD-alkalmazások létrehozásához szükséges engedélyek, az Azure AD alkalmazás-vezérlők a panel nem látható, és a egy figyelmeztető üzenet jelenik meg.

Ha a Media Services .NET SDK használatával csatlakozik a Media Services API-t, tekintse meg [eléréséhez az Azure Media Services API .NET-keretrendszerrel történő hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md).

Ha nem a Media Services .NET client SDK-t használ, manuálisan kell létrehoznia egy Azure ad-ben jogkivonat kérése a korábban ismertetett paraméterekkel. További információkért lásd: [jogkivonat beszerzése az Azure AD használata az Azure AD Authentication Library](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Az ügyfél-azonosító és titkos Ügyfélkód

Válasszon ki egy meglévő Azure AD alkalmazást, vagy hozzon létre egy új beállítással után az alábbi gombok jelennek meg:

![Engedélyek és kezelés alkalmazás gomb kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Az Azure AD-alkalmazás paneljén megnyitásához kattintson a **alkalmazás kezeléséhez**. Az a **alkalmazás kezeléséhez** panelen megtekintheti az alkalmazás ügyfél-azonosító (Alkalmazásazonosító). Ügyfél titkos kulcs (kulcs) létrehozásához válassza **kulcsok**.

![Alkalmazás panelen kulcs lehetőség kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Engedélyek és az alkalmazás kezelése

Miután kiválasztotta az Azure AD-alkalmazás, kezelheti az alkalmazás és az engedélyek. Az Azure AD-alkalmazás más alkalmazás-hozzáférési beállításához kattintson **kezelheti az engedélyeiket**. Kattintson a felügyeleti feladatokat, például a kulcsok és a válasz URL-címek módosítása, vagy szerkesztheti az alkalmazás jegyzékfájlját, **alkalmazás kezeléséhez**.

### <a name="edit-the-apps-settings-or-manifest"></a>Az alkalmazás beállításainak szerkesztése vagy manifest

Az alkalmazás beállításait, vagy a jegyzékfájl szerkesztéséhez kattintson **alkalmazás kezeléséhez**.

![Alkalmazások lap kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlokat tölthet fel a fiók](media-services-portal-upload-files.md).
