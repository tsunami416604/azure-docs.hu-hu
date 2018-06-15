---
title: Ismerkedés az Azure AD-alapú hitelesítés az Azure portál használatával |} Microsoft Docs
description: 'Útmutató: Azure Active Directory (Azure AD-) hitelesítést használ az Azure Media Services API eléréséhez használja az Azure-portálon.'
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 6267dd8dca4c932d4a4d96b34a8eaa26f6a59c20
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790327"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Ismerkedés az Azure AD-alapú hitelesítés az Azure portál használatával

Útmutató az Azure portál Azure Active Directory (Azure AD-) hitelesítés az Azure Media Services API eléréséhez elérésére használhat.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nincs fiókja, kezdje egy [Azure ingyenes próbát](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információkért lásd: [Azure Media Services-fiók létrehozása az Azure-portál használatával](media-services-portal-create-account.md).
- Győződjön meg arról, hogy tekintse át a [eléréséhez Azure Media Services API-t az Azure AD hitelesítési áttekintés](media-services-use-aad-auth-to-access-ams-api.md). 

Az Azure AD-alapú hitelesítés használatakor az Azure Media Services hitelesítési két lehetőség közül választhat:

- **Felhasználó hitelesítése**. Hitelesítés a személy, aki használja az alkalmazás interakciót Media Services-erőforrásokkal. Az interaktív alkalmazás először kell kérni a felhasználót a hitelesítő adatokat. Példa: egy kódolási feladatok figyelése, vagy live streaming jogosult felhasználók által használt felügyeleti konzol alkalmazást. 
- **Szolgáltatás egyszerű hitelesítési**. A szolgáltatás hitelesítéséhez. Ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyeket démon szolgáltatások, a középső rétegbeli szolgáltatások vagy az ütemezett feladatok futtatása: webalkalmazások, függvény alkalmazások, a logic apps, API-k vagy egy mikroszolgáltatási.

> [!IMPORTANT]
> A Media Services jelenleg az Azure hozzáférés-vezérlés szolgáltatásmodell-hitelesítés. Azonban a 2018. június 1 elavulttá válik hozzáférés-vezérlés engedélyt. Javasoljuk, hogy mielőbb térjen át az Azure AD-hitelesítési modellre.

## <a name="select-the-authentication-method"></a>A hitelesítési módszer kiválasztása

1. Az a [Azure-portálon](https://portal.azure.com/), válassza ki a Media Services-fiókját.
2. Válassza ki a Kapcsolódás a Media Services API.

    ![Válassza ki a csatlakozási módszer lapja](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started01.png)

## <a name="user-authentication"></a>Felhasználóhitelesítés

A Media Services API segítségével csatlakozni a felhasználói hitelesítést választja, az ügyfélalkalmazás van szüksége, kérje az Azure AD jogkivonatban az alábbi paraméterekkel rendelkezik:  

* Azure AD-bérlő végpont
* A Media Services erőforrás URI azonosítója
* A Media Services (natív) alkalmazás ügyfél-azonosító 
* Media Services (natív) alkalmazás átirányítási URI 
* Erőforrás URI azonosítója a többi Media Services szolgáltatásokhoz

Kaphat az értékeket a paraméterek a **Media Services API és a felhasználói hitelesítés** lap. 

![Csatlakozás a felhasználói hitelesítés lap](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started02.png)

A Media Services Microsoft .NET SDK használatával kapcsolódik a Media Services API-t, ha a szükséges értékeket rendelkezésére álljanak a SDK részeként. További információkért lásd: [elérni az Azure Media Services API a .NET hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md).

Ha nem használja a Media Services .NET SDK ügyfél, manuálisan kell létrehoznia az Azure AD-jogkivonatkérelem a korábban tárgyalt paraméterek használatával. További információkért lásd: [hogyan használható az Azure AD Authentication Library az Azure AD-token beszerzése](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="service-principal-authentication"></a>Egyszerű szolgáltatásnév hitelesítése

Csatlakozás a Media Services API a szolgáltatás egyszerű lehetőséget, a középső rétegbeli (webes API-t vagy webalkalmazás) kell kérjen egy Azure AD-tokent, amely a következő paraméterekkel rendelkezik:  

* Azure AD-bérlő végpont
* A Media Services erőforrás URI azonosítója 
* Erőforrás URI azonosítója a többi Media Services szolgáltatásokhoz
* Az Azure AD alkalmazás értékeket: a **ügyfél-azonosító** és **ügyfélkulcs**

Kaphat az értékeket a paraméterek a **kapcsolódás a Media Services API-t a szolgáltatás egyszerű** lap. Ezen a lapon hozzon létre egy új Azure AD-alkalmazást, vagy válasszon ki egy meglévőt. Miután kiválasztotta az Azure AD-alkalmazáshoz, az ügyfél-azonosítót (Alkalmazásazonosító), és az ügyfél titkos (kulcs) értékek generálásához. 

![Csatlakozás szolgáltatás egyszerű lap](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started04.png)

Ha a **egyszerű** panel nyílik meg, az első Azure AD-alkalmazás, amely megfelel a következő elemet:

- Egy regisztrált Azure AD-alkalmazást.
- A fiók közreműködői vagy Owner Role-Based hozzáférés-vezérlés engedéllyel rendelkezik.

Miután hoz létre, vagy válasszon ki egy Azure AD-alkalmazást, hozhat létre, és másolja át egy ügyfélkulcsot (kulcs) és az ügyfél-azonosító (Alkalmazásazonosító). A titkos ügyfélkulcs és ügyfél-azonosító eléréséhez az ebben a forgatókönyvben token szükségesek.

Ha nem rendelkezik engedélyekkel az Azure AD-alkalmazásai létrehozására a tartományban, a panel az Azure AD alkalmazás vezérlők nem láthatók, és megjelenik egy figyelmeztető üzenet.

Ha a Media Services .NET SDK használatával kapcsolódik a Media Services API-t, tekintse meg [elérni az Azure Media Services API a .NET hitelesítés használata az Azure AD](media-services-dotnet-get-started-with-aad.md).

Ha nem használ a Media Services .NET SDK ügyfél, manuálisan kell létrehoznia egy Azure AD-jogkivonatkérelem, a korábban tárgyalt paraméterek használatával. További információkért lásd: [hogyan használható az Azure AD Authentication Library az Azure AD-token beszerzése](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="get-the-client-id-and-client-secret"></a>Az ügyfél-azonosító és a titkos ügyfélkódot beolvasása

Válasszon ki egy meglévő Azure AD-alkalmazást, vagy hozzon létre egy újat választja, a következő gombok jelennek meg:

![Engedélyek és kezelés alkalmazás gomb kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-manage.png)

Az Azure AD alkalmazás panel megnyitásához kattintson **alkalmazás kezeléséhez**. Az a **alkalmazás kezeléséhez** panelen kaphat az alkalmazás ügyfél-azonosító (Alkalmazásazonosító). Egy ügyfélkulcsot (kulcs) létrehozásához, jelölje be **kulcsok**.

![Alkalmazás panel kulcsok beállítás kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started06.png) 

### <a name="manage-permissions-and-the-application"></a>Engedélyek és az alkalmazás kezelése

Miután kiválasztotta az Azure AD-alkalmazás, kezelheti az alkalmazás és az engedélyek. Más alkalmazások elérése az Azure AD alkalmazás beállításához kattintson **kezeli az engedélyeket**. A fájlkezelési feladatok, például megváltoztatni a kulcsok és a válasz URL-címek, illetve módosíthatja az alkalmazás jegyzékében, kattintson a **alkalmazás kezeléséhez**.

### <a name="edit-the-apps-settings-or-manifest"></a>Az alkalmazás beállításainak módosítása vagy manifest

Az alkalmazás beállításai vagy jegyzékfájl szerkesztéséhez kattintson **alkalmazás kezeléséhez**.

![Alkalmazások lap kezelése](./media/media-services-portal-get-started-with-aad/media-services-portal-get-started05.png)

## <a name="next-steps"></a>További lépések

Ismerkedés a [fájlok feltöltése a fiókjához](media-services-portal-upload-files.md).
