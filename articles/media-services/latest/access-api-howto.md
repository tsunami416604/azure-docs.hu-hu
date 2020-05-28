---
title: Ismerkedés az Azure AD-hitelesítéssel
description: Megtudhatja, hogyan érheti el Azure Active Directory (Azure AD) hitelesítését a Azure Media Services API felhasználásához.
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
ms.date: 05/21/2020
ms.author: juliako
ms.openlocfilehash: 9788c4663908497b51fbaaf7f824125d857e7c81
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774415"
---
# <a name="get-credentials-to-access-media-services-api"></a>Hitelesítő adatok beszerzése Media Services API-hoz való hozzáféréshez  

Ha Azure AD-hitelesítést használ a Azure Media Services API eléréséhez, két hitelesítési lehetőség közül választhat:

- **Egyszerű szolgáltatásnév hitelesítése** (ajánlott)

    Szolgáltatás hitelesítése. Az ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok futtatására szolgálnak: webalkalmazások, Function apps, Logic apps, API-k vagy egy szolgáltatás.
- **Felhasználói hitelesítés**

    Az alkalmazást használó személy hitelesítése Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Erre példa egy olyan felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak. 

Ez a cikk a hitelesítő adatok Media Services API-hoz való hozzáférésének lépéseit ismerteti. Válasszon a következő lapok közül.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](create-account-howto.md).

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

### <a name="api-access"></a>API-hozzáférés 

Az **API-hozzáférés** lapon kiválaszthatja az API-hoz való kapcsolódáshoz használni kívánt hitelesítési módszert. Az oldalon az API-hoz való kapcsolódáshoz szükséges értékeket is megadja.

1. A [Azure Portal](https://portal.azure.com/)válassza ki Media Services-fiókját.
2. Válassza ki, hogyan szeretne csatlakozni a Media Services API-hoz.
3. A **kapcsolódás Media Services API**-hoz területen válassza ki azt a Media Services API-verziót, amelyhez csatlakozni szeretne (v3 a szolgáltatás legújabb verziója).

### <a name="service-principal-authentication--recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)

Egy Azure Active Directory (Azure AD) alkalmazás és titok használatával hitelesíti a szolgáltatást. Ez a Media Services API-t hívó középső szintű szolgáltatások esetében ajánlott. Ilyenek például a Web Apps, a functions, a Logic Apps, az API-k és a-szolgáltatások. Ez az ajánlott hitelesítési módszer.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Az Azure AD-alkalmazás és a titkos kód kezelése

A **HRE-alkalmazás és a titkos kód kezelése** szakasz lehetővé teszi, hogy új Azure ad-alkalmazást válasszon ki vagy hozzon létre, és létrehoz egy titkos kulcsot. Biztonsági okokból a titkos kód nem jeleníthető meg a panel bezárása után. Az alkalmazás az alkalmazás AZONOSÍTÓját és a titkos kulcsot használja a Media Services érvényes jogkivonat beszerzéséhez.

Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel az alkalmazás Azure AD-bérlőben való regisztrálásához és az alkalmazás az Azure-előfizetésben lévő szerepkörhöz való hozzárendeléséhez. További információ: [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

#### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

A **kapcsolódás Media Services API** -hoz a szolgáltatás egyszerű alkalmazásának összekapcsolásához használt értékeket adja meg. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

### <a name="user-authentication"></a>Felhasználóhitelesítés

Ezzel a beállítással lehet hitelesíteni egy alkalmazottat vagy egy olyan Azure Active Directory tagját, aki egy alkalmazást használ az Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a felhasználó hitelesítő adatainak megadására. Ezt a hitelesítési módszert csak felügyeleti alkalmazásokhoz lehet használni.

#### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

Másolja a hitelesítő adatait a felhasználói alkalmazás összekapcsolásához a **kapcsolódás Media Services API-hoz** szakaszhoz. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>További lépések

[Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével](stream-files-tutorial-with-api.md).
