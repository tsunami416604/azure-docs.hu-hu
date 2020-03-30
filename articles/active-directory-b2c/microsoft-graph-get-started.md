---
title: Erőforrások kezelése a Microsoft Graph segítségével
titleSuffix: Azure AD B2C
description: Készüljön fel az Azure AD B2C-erőforrások Microsoft Graph-mal való kezelésére egy olyan alkalmazás regisztrálásával, amely megkapta a szükséges Graph API-engedélyeket.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184347"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Az Azure AD B2C kezelése a Microsoft Graph segítségével

[A Microsoft Graph][ms-graph] lehetővé teszi az Azure AD B2C-bérlőn belüli erőforrások nagy részének kezelését, beleértve az ügyfél-felhasználói fiókokat és az egyéni szabályzatokat. A [Microsoft Graph API-t][ms-graph-api]hívó parancsfájlok vagy alkalmazások írásával automatizálhatja a bérlői felügyeleti feladatokat, például:

* Meglévő felhasználói tároló áttelepítése egy Azure AD B2C-bérlőre
* Egyéni szabályzatok üzembe helyezése azure-folyamattal az Azure DevOps-ban, és egyéni szabályzatkulcsok kezelése
* Felhasználói regisztráció üzemeltetése a saját oldalán, és felhasználói fiókok létrehozása az Azure AD B2C könyvtárban a színfalak mögött
* A jelentkezések regisztrálásának automatizálása
* Naplónaplók beszerzése

A következő szakaszok segítségével felkészülhet a Microsoft Graph API használatával az Azure AD B2C címtárban lévő erőforrások kezelésének automatizálásához.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API interakciós módok

A Microsoft Graph API-val való munka során két kommunikációs mód használható az Azure AD B2C-bérlő erőforrásainak kezeléséhez:

* **Interaktív** – Egyszeri futtatásra alkalmas feladatok esetén a B2C-bérlőben rendszergazdai fiókot kell használnia a felügyeleti feladatok végrehajtásához. Ebben a módban a rendszergazdának a hitelesítő adataival kell bejelentkeznie a Microsoft Graph API hívása előtt.

* **Automatikus** – Ütemezett vagy folyamatosan futtatott feladatok esetén ez a módszer egy olyan szolgáltatásfiókot használ, amelyet a felügyeleti feladatok végrehajtásához szükséges engedélyekkel konfigurál. A "szolgáltatásfiók" az Azure AD B2C-ben egy olyan alkalmazás regisztrálásával, amelyet az alkalmazások és parancsfájlok az *alkalmazás (ügyfél) azonosítója* és az OAuth 2.0 ügyfél hitelesítő adatok megadása használatával történő hitelesítéshez használnak. Ebben az esetben az alkalmazás úgy működik, mint a Microsoft Graph API hívására, nem pedig a rendszergazdai felhasználó, mint a korábban leírt interaktív módszer.

Az **automatikus interakciós** forgatókönyvet a következő szakaszokban látható alkalmazásregisztráció létrehozásával engedélyezheti.

## <a name="register-management-application"></a>Regiszterkezelő alkalmazás

Ahhoz, hogy a parancsfájlok és alkalmazások kommunikálhassanak a [Microsoft Graph API-val][ms-graph-api] az Azure AD B2C-erőforrások kezeléséhez, létre kell hoznia egy alkalmazásregisztrációt az Azure AD B2C-bérlőben, amely megadja a szükséges API-engedélyeket.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>API-hozzáférés biztosítása

Ezután adja meg a regisztrált alkalmazás engedélyeket a bérlői erőforrások manipulálására a Microsoft Graph API-hoz való hívások on keresztül.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Ügyféltitok létrehozása

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Most már rendelkezik egy alkalmazás, amely engedéllyel *rendelkezik az*Azure AD B2C-bérlőben lévő felhasználók létrehozására, *olvasására,* *frissítésére*és *törlésére.* Folytassa a következő szakaszra a *jelszófrissítési* engedélyek hozzáadásához.

## <a name="enable-user-delete-and-password-update"></a>Felhasználótörlésének és jelszófrissítésének engedélyezése

Az *olvasási és írási címtáradatok* engedély **nem** tartalmazza a felhasználók törlésének lehetőségét vagy a felhasználói fiókok jelszavainak frissítését.

Ha az alkalmazásnak vagy parancsfájlnak törölnie kell a felhasználókat, vagy frissítenie kell a jelszavukat, rendelje hozzá a *Felhasználói rendszergazdai* szerepkört az alkalmazáshoz:

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és használja a **Directory + Előfizetés** szűrőt az Azure AD B2C bérlőre való váltáshoz.
1. Keresse meg és válassza az **Azure AD B2C**lehetőséget.
1. A **Kezelés csoportban**válassza a **Szerepkörök és rendszergazdák lehetőséget.**
1. Válassza a **Felhasználói rendszergazda szerepkört.**
1. Válassza **a Hozzárendelések hozzáadása**lehetőséget.
1. A **Kijelölés** szövegmezőbe írja be a korábban regisztrált alkalmazás nevét, például *managementapp1*. Válassza ki az alkalmazást, amikor megjelenik a keresési eredmények között.
1. Válassza a **Hozzáadás** lehetőséget. Az engedélyek teljes propagálása eltarthat néhány percig.

## <a name="next-steps"></a>További lépések

Most, hogy regisztrálta a felügyeleti alkalmazást, és megadta neki a szükséges engedélyeket, az alkalmazások és szolgáltatások (például az Azure Pipelines) használhatják a hitelesítő adatait és engedélyeit a Microsoft Graph API-val való együttműködéshez.

* [A Microsoft Graph által támogatott B2C-műveletek](microsoft-graph-operations.md)
* [Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph segítségével](manage-user-accounts-graph-api.md)
* [Naplózási naplók beszereznie az Azure AD jelentéskészítési API-val](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
