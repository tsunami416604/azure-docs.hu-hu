---
title: Webes API-kat hívó Daemon-alkalmazás (áttekintés) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó Daemon-alkalmazást
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae0912203f2427694d2a9b8611966a55e1e6889e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056383"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Forgatókönyv: Webes API-kat meghívó Daemon-alkalmazás

A webes API-kat meghívó Daemon-alkalmazások létrehozásához szükséges tudnivalók.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Áttekintés

Az alkalmazás képes jogkivonatot beszerezni egy webes API saját nevében történő meghívására (nem a felhasználó nevében). Ez a forgatókönyv a Daemon-alkalmazások esetében hasznos. A standard OAuth 2,0 [ügyfél hitelesítő adatait](v2-oauth2-client-creds-grant-flow.md) használja.

![Démonalkalmazások](./media/scenario-daemon-app/daemon-app.svg)

Íme néhány példa a Daemon-alkalmazások használati eseteire:

- A felhasználók kiépítéséhez vagy felügyeletéhez használt webalkalmazások, illetve a Batch-folyamatok egy címtárban
- Asztali alkalmazások (például Windows-szolgáltatások vagy Linux rendszeren futó démonok), amelyek kötegelt feladatokat hajtanak végre, vagy egy, a háttérben futó operációsrendszer-szolgáltatás.
- Webes API-k, amelyeknek a címtárakat kell kezelniük, nem meghatározott felhasználóknak

Van egy másik gyakori eset, amikor a nem Daemon-alkalmazások az ügyfél hitelesítő adatait használják: még akkor is, ha a felhasználók nevében cselekszenek, a webes API-t vagy az erőforrást az identitásuk technikai okokból való eléréséhez kell elérniük. Ilyen például a kulcstartóhoz vagy egy Azure SQL Database-adatbázishoz való hozzáférés a gyorsítótárhoz.

Olyan alkalmazások, amelyek jogkivonatot szerzik be a saját identitásuk számára:

- Bizalmas ügyfélalkalmazások. Ezek az alkalmazások, mivel a felhasználóktól függetlenül férnek hozzá az erőforrásokhoz, bizonyítaniuk kell identitásukat. Emellett meglehetősen érzékeny alkalmazások, amelyeket az Azure Active Directory (Azure AD) bérlői rendszergazdáinak jóvá kell hagyniuk.
- Titkos (alkalmazás jelszava vagy tanúsítvány) van regisztrálva az Azure AD-ben. Ezt a titkot a rendszer átadja az Azure AD-nek a jogkivonat beszerzésére irányuló hívása során.

## <a name="specifics"></a>Sajátosságai

> [!IMPORTANT]
>
> - A felhasználói beavatkozás nem lehetséges egy Daemon-alkalmazásban. Egy démon-alkalmazáshoz saját identitás szükséges. Az ilyen típusú alkalmazás hozzáférési jogkivonatot kér az alkalmazás identitásával, és megjeleníti az alkalmazás AZONOSÍTÓját, hitelesítő adatait (jelszavát vagy tanúsítványát), valamint az alkalmazás AZONOSÍTÓjának URI-JÁT az Azure AD-hez. A sikeres hitelesítés után a démon egy hozzáférési jogkivonatot (és egy frissítési jogkivonatot) kap a Microsoft Identity platform-végponttól, amelyet a rendszer a webes API meghívására használ (és szükség szerint frissíti).
> - Mivel a felhasználói beavatkozás nem lehetséges, a növekményes közreműködés nem lehetséges. Az összes szükséges API-engedélyt be kell állítani az alkalmazás regisztrálásakor, és az alkalmazás kódja csak statikusan meghatározott engedélyeket kér. Ez azt is jelenti, hogy a Daemon-alkalmazások nem támogatják a növekményes hozzájárulásukat.

A fejlesztők számára a forgatókönyv teljes körű tapasztalata a következő szempontokat öleli fel:

- A Daemon-alkalmazások csak az Azure AD-bérlők esetében működhetnek. Nem érdemes olyan Daemon-alkalmazást létrehozni, amely megkísérli a személyes Microsoft-fiókok kezelését. Ha Ön üzletági (LOB) alkalmazás fejlesztője, a démoni alkalmazást a bérlőben hozza létre. Ha Ön ISV-t használ, érdemes lehet több-bérlős démon alkalmazást létrehoznia. Minden bérlői rendszergazdának meg kell adnia.
- Az [alkalmazás regisztrálása](./scenario-daemon-app-registration.md)során a **Válasz URI-ja** nem szükséges. Meg kell osztania a titkokat, a tanúsítványokat vagy az aláírt állításokat az Azure AD-vel, és meg kell kérnie az alkalmazások engedélyeit, és rendszergazdai jóváhagyást kell adnia az alkalmazás engedélyeinek használatához.
- Az [alkalmazás konfigurációjának](./scenario-daemon-app-configuration.md) az Azure ad-ben megosztott ügyfél-hitelesítő adatokat kell megadnia az alkalmazás regisztrációja során.
- Az ügyfél hitelesítő adataival rendelkező token beszerzéséhez használt [hatókörnek](scenario-daemon-acquire-token.md#scopes-to-request) statikus hatókörre van szüksége.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Daemon App – alkalmazás regisztrálása](./scenario-daemon-app-registration.md)
