---
title: Webes API-kat megnevező démonalkalmazás létrehozása – Microsoft identity platform | Azure
description: További információ a webes API-kat meghívjaó démonalkalmazások létrehozásáról
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df06c4c55941f4424d6b90d2846af17bf055b2e4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885463"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>Eset: A webes API-kat meghívjaó démonalkalmazás

Ismerje meg mindazt, amire szüksége van egy olyan démonalkalmazás létrehozásához, amely webes API-kat hív meg.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>Áttekintés

Az alkalmazás beszerezhet egy jogkivonatot, hogy hívja meg a webes API-t a saját nevében (nem egy felhasználó nevében). Ez a forgatókönyv démonalkalmazások esetén hasznos. A szabványos OAuth 2.0 [ügyfél hitelesítő adatok megadása.](v2-oauth2-client-creds-grant-flow.md)

![Démonalkalmazások](./media/scenario-daemon-app/daemon-app.svg)

Íme néhány példa a démonalkalmazások használati eseteire:

- Felhasználók kiépítésére vagy felügyeletére, illetve kötegelt folyamatok címtárban történő kezelésére használt webalkalmazások
- A kötegelt feladatokat végző asztali alkalmazások (például Windows-szolgáltatások vagy a Linux démonfolyamatok) vagy a háttérben futó operációsrendszer-szolgáltatás
- Web API-k, amelyeknek a könyvtárakat kell manipulálniuk, nem pedig konkrét felhasználókat

Van egy másik gyakori eset, amikor a nem démonalkalmazások ügyfélhitelesítő adatokat használnak: még akkor is, ha a felhasználók nevében járnak el, technikai okokból hozzá kell férniük egy webes API-hoz vagy egy erőforráshoz a saját identitásuk alatt. Egy példa a titkokhoz való hozzáférés az Azure Key Vaultban vagy egy Azure SQL-adatbázis egy gyorsítótár.

Olyan alkalmazások, amelyek saját identitásuk jogkivonatát szerzik be:

- Bizalmas ügyfélalkalmazások. Ezeknek az alkalmazásoknak, mivel a felhasználóktól függetlenül férnek hozzá az erőforrásokhoz, igazolniuk kell személyazonosságukat. Ők is meglehetősen érzékeny alkalmazások. Az okat jóvá kell hagynia az Azure Active Directory (Azure AD) bérlői rendszergazdák.
- Regisztrált egy titkos (alkalmazásjelszó vagy tanúsítvány) az Azure AD.Have registered a secret (application password or certificate) with Azure AD. Ez a titkos kulcs átadása az Azure AD-hez egy jogkivonat lekéréséhez.

## <a name="specifics"></a>Sajátosságai

> [!IMPORTANT]
>
> - A felhasználók nem használhatják a démonalkalmazásokat. A démonalkalmazás saját identitást igényel. Az ilyen típusú alkalmazás az alkalmazásidentitás használatával kér egy hozzáférési jogkivonatot, és bemutatja az alkalmazásazonosítóját, hitelesítő adatait (jelszavát vagy tanúsítványát), valamint az Azure AD alkalmazásazonosító-URI-ját. A sikeres hitelesítés után a démon kap egy hozzáférési jogkivonatot (és egy frissítési jogkivonatot) a Microsoft identity platform végpontról. Ezt a jogkivonatot ezután a webes API hívására használják (és szükség szerint frissül).
> - Mivel a felhasználók nem tudnak démonalkalmazásokkal együttműködni, a növekményes hozzájárulás nem lehetséges. Az összes szükséges API-engedélyt konfigurálni kell az alkalmazás regisztrációjakor. Az alkalmazás kódja csak statikusan meghatározott engedélyeket kér. Ez azt is jelenti, hogy a démonalkalmazások nem támogatják a növekményes jóváhagyást.

A fejlesztők számára a forgatókönyv végpontok utáni élménye a következő szempontokat rendelkezik:

- A démonalkalmazások csak az Azure AD-bérlőkben működhetnek. Nem lenne értelme, hogy hozzon létre egy démon alkalmazás, amely megpróbálja manipulálni a Microsoft személyes fiókok. Ha Ön egy üzletági (LOB) alkalmazás fejlesztője, a démonalkalmazást a bérlőben hozza létre. Ha ön független járműv, érdemes lehet létrehozni egy több-bérlős démon-alkalmazást. Minden bérlői rendszergazdának beleegyezést kell adnia.
- Az [alkalmazás regisztrációja](./scenario-daemon-app-registration.md)során a válasz URI nincs szükség. Meg kell osztania a titkos kulcsokat vagy tanúsítványokat, vagy aláírt állításokat az Azure AD-vel. Emellett alkalmazásengedélyeket kell kérnie, és rendszergazdai engedélyt kell adnia az alkalmazásengedélyek használatához.
- Az [alkalmazás konfigurációjának](./scenario-daemon-app-configuration.md) ügyfélhitelesítő adatokat kell biztosítania az Azure AD-vel megosztottként az alkalmazás regisztrációja során.
- Az ügyfél hitelesítő adatok folyamatával rendelkező jogkivonat megszerzéséhez használt [hatókörnek](scenario-daemon-acquire-token.md#scopes-to-request) statikus hatókörnek kell lennie.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Démon alkalmazás – alkalmazásregisztráció](./scenario-daemon-app-registration.md)
