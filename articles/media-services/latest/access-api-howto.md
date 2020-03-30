---
title: Első lépések az Azure AD-hitelesítéssel
description: Megtudhatja, hogyan érheti el az Azure Active Directory (Azure AD) hitelesítést az Azure Media Services API felhasználásához.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/15/2020
ms.author: juliako
ms.openlocfilehash: 6e1ab30e8b4cf44f7d1f82fd94fb9bf854915557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79478252"
---
# <a name="get-credentials-to-access-media-services-api"></a>Hitelesítő adatok beszereznie a Media Services API-hoz való hozzáféréshez  

Ha az Azure Media Services API eléréséhez Azure AD-hitelesítést használ, két hitelesítési lehetőség közül választhat:

- **Egyszerű szolgáltatáshitelesítés** (ajánlott)

    Szolgáltatás hitelesítése. A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak: webalkalmazások, függvényalkalmazások, logikai alkalmazások, API-k vagy mikroszolgáltatások.
- **Felhasználóhitelesítés**

    Hitelesítse azt a személyt, aki az alkalmazást használja a Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználónak a hitelesítő adatokat. Egy példa egy felügyeleti konzol alkalmazás által használt jogosult felhasználók kódolási feladatok figyelésére vagy élő streamelés. 

Ez a cikk a Media Services API-hoz való hozzáférés hez szükséges hitelesítő adatok beszerzésének lépéseit ismerteti. Válasszon az alábbi lapok közül.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval.](https://azure.microsoft.com/pricing/free-trial/) 
- Egy Media Services-fiók. További információ: [Azure Media Services-fiók létrehozása az Azure Portal használatával](create-account-howto.md)című témakörben talál.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

### <a name="api-access"></a>API-hozzáférés 

Az **API-hozzáférési** lap lehetővé teszi az API-hoz való csatlakozáshoz használni kívánt hitelesítési módszer kiválasztását. Az oldal az API-hoz való csatlakozáshoz szükséges értékeket is tartalmazza.

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a Media Services-fiókot.
2. Válassza ki, hogyan csatlakozhat a Media Services API-hoz.
3. A Csatlakozás a **Media Services API-hoz csoportban**válassza ki azt a Media Services API-verziót, amelyhez csatlakozni szeretne (a V3 a szolgáltatás legújabb verziója).

### <a name="service-principal-authentication--recommended"></a>Egyszerű szolgáltatáshitelesítés (ajánlott)

Egy Azure Active Directory (Azure AD) alkalmazás használatával hitelesíti a szolgáltatást, és titkos. Ez a Media Services API-t hívó középső rétegbeli szolgáltatások számára ajánlott. Ilyenek például a webalkalmazások, funkciók, logikai alkalmazások, API-k és mikroszolgáltatások. Ez az ajánlott hitelesítési módszer.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Az Azure AD-alkalmazás és a titkos

Az **AAD-alkalmazás és a titkos** funkció kezelése parancatussal új Azure AD-alkalmazást választhat ki vagy hozhat létre, és létrehozhat egy titkos kulcsot. Biztonsági okokból a titok nem jeleníthető meg a fűrészlap bezárása után. Az alkalmazás az alkalmazásazonosítót és a titkos hitelesítést használja a médiaszolgáltatások érvényes jogkivonatának beszerzéséhez.

Győződjön meg arról, hogy rendelkezik-e megfelelő engedélyekkel egy alkalmazás regisztrálásához az Azure AD-bérlővel, és rendelje hozzá az alkalmazást egy szerepkörhöz az Azure-előfizetésben. További információ: [Required permissions](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Csatlakozás a Media Services API-hoz

A **Media Services hez való csatlakozás API** olyan értékeket biztosít, amelyeket az egyszerű szolgáltatásalkalmazás csatlakoztatásához használ. Szöveges értékeket kaphat, vagy másolhatja a JSON- vagy XML-blokkokat.

### <a name="user-authentication"></a>Felhasználóhitelesítés

Ez a beállítás egy alkalmazott vagy egy Azure Active Directory-tag hitelesítésére használható, aki egy alkalmazást használ a Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználóhitelesítő adatait. Ezt a hitelesítési módszert csak felügyeleti alkalmazásokhoz szabad használni.

#### <a name="connect-to-media-services-api"></a>Csatlakozás a Media Services API-hoz

Másolja a hitelesítő adatokat a felhasználói alkalmazás csatlakoztatásához a Csatlakozás a **Media Services API-hoz** szakaszból. Szöveges értékeket kaphat, vagy másolhatja a JSON- vagy XML-blokkokat.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>További lépések

[Oktatóanyag: Videók feltöltése, kódolása és streamelése a Media Services v3 segítségével.](stream-files-tutorial-with-api.md)
