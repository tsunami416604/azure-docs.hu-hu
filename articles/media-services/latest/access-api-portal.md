---
title: Az Azure AD-hitelesítés használatának első lépései a Azure Portal használatával
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
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: 03643ea3f96448e5ec9693cd8ce9589472e8e3f2
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082744"
---
# <a name="use-the-azure-portal-to-get-credentials-to-access-media-services-api"></a>Hitelesítő adatok beszerzése a Azure Portal használatával Media Services API eléréséhez  

Ha Azure AD-hitelesítést használ a Azure Media Services API eléréséhez, két hitelesítési lehetőség közül választhat:

- **Egyszerű szolgáltatás hitelesítése**. Szolgáltatás hitelesítése. Az ezt a hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek a Daemon Services, a közepes szintű szolgáltatások vagy az ütemezett feladatok futtatására szolgálnak: webalkalmazások, Function apps, Logic apps, API-k vagy egy szolgáltatás.
- **Felhasználói hitelesítés**. Az alkalmazást használó személy hitelesítése Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a hitelesítő adatok megadására. Erre példa egy olyan felügyeleti konzol alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő adatfolyamok figyelésére használnak. 

Ez a cikk bemutatja, hogyan kérheti le a hitelesítő adatokat a Media Services API eléréséhez a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval](https://azure.microsoft.com/pricing/free-trial/). 
- Egy Media Services-fiók szükséges. További információ: [Azure Media Services fiók létrehozása a Azure Portal használatával](create-account-portal.md).

## <a name="api-access"></a>API-hozzáférés 

Az **API-hozzáférés** lapon kiválaszthatja az API-hoz való kapcsolódáshoz használni kívánt hitelesítési módszert. Az oldalon az API-hoz való kapcsolódáshoz szükséges értékeket is megadja.

1. A [Azure Portal](https://portal.azure.com/)válassza ki Media Services-fiókját.
2. Válassza ki, hogyan szeretne csatlakozni a Media Services API-hoz.
3. A **kapcsolódás Media Services API**-hoz területen válassza ki azt a Media Services API-verziót, amelyhez csatlakozni szeretne (v3 a szolgáltatás legújabb verziója).

## <a name="service-principal-authentication--recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)

Egy Azure Active Directory (Azure AD) alkalmazás és titok használatával hitelesíti a szolgáltatást. Ez a Media Services API-t hívó középső szintű szolgáltatások esetében ajánlott. Ilyenek például a Web Apps, a functions, a Logic Apps, az API-k és a-szolgáltatások. Ez az ajánlott hitelesítési módszer.

### <a name="manage-your-azure-ad-app-and-secret"></a>Az Azure AD-alkalmazás és a titkos kód kezelése

A **HRE-alkalmazás és a titkos kód kezelése** szakasz lehetővé teszi, hogy új Azure ad-alkalmazást válasszon ki vagy hozzon létre, és létrehoz egy titkos kulcsot. Biztonsági okokból a titkos kód nem jeleníthető meg a panel bezárása után. Az alkalmazás az alkalmazás AZONOSÍTÓját és a titkos kulcsot használja a Media Services érvényes jogkivonat beszerzéséhez.

Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel az alkalmazás Azure AD-bérlőben való regisztrálásához és az alkalmazás az Azure-előfizetésben lévő szerepkörhöz való hozzárendeléséhez. További információ: [szükséges engedélyek](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

A **kapcsolódás Media Services API** -hoz a szolgáltatás egyszerű alkalmazásának összekapcsolásához használt értékeket adja meg. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

## <a name="user-authentication"></a>Felhasználóhitelesítés

Ezzel a beállítással lehet hitelesíteni egy alkalmazottat vagy egy olyan Azure Active Directory tagját, aki egy alkalmazást használ az Media Services erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználót a felhasználó hitelesítő adatainak megadására. Ezt a hitelesítési módszert csak felügyeleti alkalmazásokhoz lehet használni.

### <a name="connect-to-media-services-api"></a>Kapcsolódás Media Services API-hoz

Másolja a hitelesítő adatait a felhasználói alkalmazás összekapcsolásához a **kapcsolódás Media Services API-hoz** szakaszhoz. Szöveges értékeket is letölthet, vagy átmásolhatja a JSON-vagy XML-blokkokat.

## <a name="next-steps"></a>Következő lépések

[Oktatóanyag: videók feltöltése, kódolása és továbbítása a Media Services v3 segítségével](stream-files-tutorial-with-api.md).
