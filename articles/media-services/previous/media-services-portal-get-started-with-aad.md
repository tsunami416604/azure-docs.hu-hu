---
title: Az Azure AD-hitelesítés első lépései az Azure Portal használatával| Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja az Azure Portalhasználatával az Azure Active Directory (Azure AD) hitelesítést az Azure Media Services API-használatához.
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
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330668"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Bevezetés az Azure AD-hitelesítés használatába az Azure Portalon

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Megtudhatja, hogyan használhatja az Azure Portalhasználatával az Azure Active Directory (Azure AD) hitelesítésének eléréséhez az Azure Media Services API eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure-fiók. Ha nem rendelkezik fiókkal, kezdje egy [ingyenes Azure-próbaverzióval.](https://azure.microsoft.com/pricing/free-trial/) 
- Egy Media Services-fiók. További információ: [Azure Media Services-fiók létrehozása az Azure Portal használatával](media-services-portal-create-account.md)című témakörben talál.

Ha Az Azure AD-hitelesítést az Azure Media Services szolgáltatással használja, két hitelesítési lehetőség közül választhat:

- **Egyszerű szolgáltatáshitelesítés**. Szolgáltatás hitelesítése. A hitelesítési módszert gyakran használó alkalmazások olyan alkalmazások, amelyek démonszolgáltatásokat, középső rétegbeli szolgáltatásokat vagy ütemezett feladatokat futtatnak: webalkalmazások, függvényalkalmazások, logikai alkalmazások, API-k vagy mikroszolgáltatások.
- **Felhasználói hitelesítés**. Hitelesítse azt a személyt, aki az alkalmazást használja a Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználónak a hitelesítő adatokat. Egy példa egy felügyeleti konzol alkalmazás által használt jogosult felhasználók kódolási feladatok figyelésére vagy élő streamelés. 

## <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Ezen a lapon kiválaszthatja az API-hoz való csatlakozáshoz használni kívánt hitelesítési módszert. Az oldal az API-hoz való csatlakozáshoz szükséges értékeket is tartalmazza.

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a Media Services-fiókot.
2. Válassza ki, hogyan csatlakozhat a Media Services API-hoz.
3. A **Csatlakozás a Media Services API-hoz csoportban**válassza ki azt a Media Services API-verziót, amelyhez csatlakozni szeretne.

## <a name="service-principal-authentication--recommended"></a>Egyszerű szolgáltatáshitelesítés (ajánlott)

Egy Azure Active Directory (Azure AD) alkalmazás használatával hitelesíti a szolgáltatást, és titkos. Ez a Media Services API-t hívó középső rétegbeli szolgáltatások számára ajánlott. Ilyenek például a webalkalmazások, funkciók, logikai alkalmazások, API-k és mikroszolgáltatások. Ez az ajánlott hitelesítési módszer.

### <a name="manage-your-azure-ad-app-and-secret"></a>Az Azure AD-alkalmazás és a titkos

Az **AAD-alkalmazás és a titkos** funkció kezelése parancatussal új Azure AD-alkalmazást választhat ki vagy hozhat létre, és létrehozhat egy titkos kulcsot. Biztonsági okokból a titok nem jeleníthető meg a fűrészlap bezárása után. Az alkalmazás az alkalmazásazonosítót és a titkos hitelesítést használja a médiaszolgáltatások érvényes jogkivonatának beszerzéséhez.

Győződjön meg arról, hogy rendelkezik-e megfelelő engedélyekkel egy alkalmazás regisztrálásához az Azure AD-bérlővel, és rendelje hozzá az alkalmazást egy szerepkörhöz az Azure-előfizetésben. További információ: [Required permissions](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

### <a name="connect-to-media-services-api"></a>Csatlakozás a Media Services API-hoz

A **Media Services hez való csatlakozás API** olyan értékeket biztosít, amelyeket az egyszerű szolgáltatásalkalmazás csatlakoztatásához használ. Szöveges értékeket kaphat, vagy másolhatja a JSON- vagy XML-blokkokat.

## <a name="user-authentication"></a>Felhasználóhitelesítés

Ez a beállítás egy alkalmazott vagy egy Azure Active Directory-tag hitelesítésére használható, aki egy alkalmazást használ a Media Services-erőforrásokkal való interakcióhoz. Az interaktív alkalmazásnak először meg kell kérnie a felhasználóhitelesítő adatait. Ezt a hitelesítési módszert csak felügyeleti alkalmazásokhoz szabad használni.

### <a name="connect-to-media-services-api"></a>Csatlakozás a Media Services API-hoz

Másolja a hitelesítő adatokat a felhasználói alkalmazás csatlakoztatásához a Csatlakozás a **Media Services API-hoz** szakaszból. Szöveges értékeket kaphat, vagy másolhatja a JSON- vagy XML-blokkokat.

## <a name="next-steps"></a>További lépések

Kezdje el feltölteni a [fájlokat a fiókjába.](media-services-portal-upload-files.md)
