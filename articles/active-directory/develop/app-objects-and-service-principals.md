---
title: Alkalmazások az Azure AD-ben & egyszerű szolgáltatások | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg az alkalmazás és a szolgáltatás egyszerű objektumai közötti kapcsolattal Azure Active Directoryban.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cf436881a74cffd0053718413ce83a148fa019d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918202"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban

Előfordulhat, hogy az "alkalmazás" kifejezés jelentése nem értelmezhető, ha Azure Active Directory (Azure AD) környezetében használják. Ez a cikk az Azure AD-alkalmazások integrációjának fogalmi és konkrét szempontjait mutatja be, és egy [több-bérlős alkalmazás](developer-glossary.md#multi-tenant-application)regisztrálását és beleegyezőjét ismerteti.

## <a name="overview"></a>Áttekintés

Az Azure AD-vel integrált alkalmazások a szoftver aspektusán túlmutató következményekkel járnak. Az "alkalmazás" kifejezést gyakran használják fogalmi feltételként, amely nem csupán az alkalmazás szoftverére vonatkozik, hanem az Azure AD-regisztrációra és a "beszélgetések" a hitelesítés/engedélyezés "beszélgetésekhez" is.

Definíció szerint az alkalmazások a következő szerepkörökben működhetnek:

- [Ügyfél](developer-glossary.md#client-application) -szerepkör (erőforrás-fogyasztás)
- [Erőforrás-kiszolgálói](developer-glossary.md#resource-server) szerepkör (API-k kimutatása az ügyfeleknek)
- Az ügyfél-szerepkör és az erőforrás-kiszolgálói szerepkör is

Az [OAuth 2,0 engedélyezési folyamata](developer-glossary.md#authorization-grant) meghatározza a beszélgetési protokollt, amely lehetővé teszi, hogy az ügyfél/erőforrás hozzáférjen az erőforrás adataihoz, illetve megvédje azokat.

A következő részekben azt láthatja, hogy az Azure AD-alkalmazás modellje hogyan jelent egy alkalmazást a tervezéskor és a futási időben.

## <a name="application-registration"></a>Alkalmazásregisztráció

Ha egy Azure AD-alkalmazást regisztrál a [Azure Portal][AZURE-Portal], két objektum jön létre az Azure ad-bérlőben:

- Egy alkalmazás-objektum és
- Egy egyszerű szolgáltatásnév

### <a name="application-object"></a>Application objektum

Egy Azure AD-alkalmazást egy és csak egy Application objektum definiál, amely abban az Azure AD-bérlőben található, ahol az alkalmazás regisztrálva van, vagyis az alkalmazás "Home" bérlője. A Microsoft Graph [alkalmazás entitása][MS-Graph-App-Entity] határozza meg az Application objektum tulajdonságainak sémáját.

### <a name="service-principal-object"></a>egyszerű szolgáltatásnév objektum

Az Azure AD-bérlő által védett erőforrások eléréséhez a hozzáférést igénylő entitást egy rendszerbiztonsági tagnek kell képviselnie. Ez a felhasználó (egyszerű felhasználónév) és az alkalmazások (egyszerű szolgáltatásnév) esetében is igaz.

A rendszerbiztonsági tag meghatározza az Azure AD-bérlőben a felhasználóhoz/alkalmazáshoz tartozó hozzáférési szabályzatot és engedélyeket. Ez lehetővé teszi az olyan alapvető funkciók használatát, mint a felhasználó/alkalmazás hitelesítése a bejelentkezéskor és az erőforrás-hozzáférés engedélyezésekor.

Ha egy alkalmazás engedélyt kap a bérlő erőforrásainak elérésére (regisztráció vagy [beleegyezés](developer-glossary.md#consent)esetén), létrejön egy egyszerű szolgáltatásnév objektum. A Microsoft Graph [ServicePrincipal entitás][MS-Graph-Sp-Entity] definiálja az egyszerű szolgáltatásnév objektumának sémáját.

### <a name="application-and-service-principal-relationship"></a>Az alkalmazás és a szolgáltatásnév kapcsolata

Tekintse át az Application objektumot az alkalmazás *globális* ábrázolásának az összes bérlőn való használatra, valamint az egyszerű szolgáltatásnév, amely egy adott bérlőn való használatra *helyi* ábrázolás.

Az alkalmazásobjektum szolgál sablonként, amelyből a közös és az alapértelmezett tulajdonságok *származtatása* történik a megfelelő szolgáltatásnév-objektumok létrehozásához. Az Application objektumnak ezért van 1:1 kapcsolata a szoftver alkalmazással, és egy 1: sok kapcsolat van a hozzá tartozó szolgáltatásnév-objektummal.

Minden olyan bérlőn létre kell hozni egy szolgáltatásnevet, amelyben az alkalmazás használatban van, ami lehetővé teszi, hogy személyazonosságot hozzon létre a bejelentkezéshez és/vagy a bérlő által védett erőforrásokhoz való hozzáféréshez. Egybérlős alkalmazás csak egy szolgáltatásnévvel rendelkezik (a saját bérlőjében), melynek létrehozása és a használatának engedélyezése az alkalmazás regisztrációja során történik. A több-bérlős webalkalmazások/API-k egy egyszerű szolgáltatásnevet is létrehoznak minden olyan bérlőn, ahol az adott bérlő felhasználója beleegyezett a használatára.

> [!NOTE]
> Az alkalmazás-objektumon végrehajtott módosításokat a rendszer csak az alkalmazás otthoni bérlője (a bérlő, ahol regisztrálva van) egyszerű szolgáltatásnév objektumában is megjeleníti. A több-bérlős alkalmazások esetében az Application objektum módosításai nem jelennek meg a fogyasztói bérlők egyszerű szolgáltatásaiban, amíg el nem távolítja a hozzáférést az [alkalmazás-hozzáférési paneljén](https://myapps.microsoft.com) , és nem ad vissza engedélyt.
>
> Azt is vegye figyelembe, hogy a natív alkalmazások alapértelmezés szerint több-bérlőként vannak regisztrálva.

## <a name="example"></a>Példa

Az alábbi ábrán az alkalmazás alkalmazás-objektuma és a hozzá tartozó egyszerű szolgáltatásnév objektumok közötti kapcsolat látható az **HR**-alkalmazás nevű minta több-bérlős alkalmazás kontextusában. Ebben a példában három Azure AD-bérlő található:

- **Adatum** – a **HR-alkalmazást** fejlesztő vállalat által használt bérlő
- **Contoso** – a contoso szervezet által használt bérlő, amely a **HR-alkalmazás** fogyasztója
- **Fabrikam** – a fabrikam-szervezet által használt bérlő, amely a **HR-alkalmazást** is használja

![Kapcsolat az alkalmazás-objektum és az egyszerű szolgáltatásnév objektum között](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Ebben a példában a következő forgatókönyvben:

| Lépés: | Leírás |
|------|-------------|
| 1    | Az alkalmazás és a szolgáltatás egyszerű objektumainak létrehozásának folyamata az alkalmazás kezdőlapjának bérlője. |
| 2    | Ha a contoso és a fabrikam rendszergazdája teljes körű beleegyezik, a rendszer létrehoz egy egyszerű szolgáltatásnév-objektumot a vállalat Azure AD-bérlőben, és hozzárendeli a rendszergazda által megadott engedélyeket. Azt is vegye figyelembe, hogy a HR-alkalmazás konfigurálható/úgy lett kialakítva, hogy engedélyezze a felhasználók számára az egyéni használatra való hozzájárulásukat. |
| 3    | A HR-alkalmazás (contoso és Fabrikam) fogyasztói bérlői mindegyike saját egyszerű szolgáltatásnév-objektummal rendelkezik. Mindegyik azt jelenti, hogy az alkalmazás egy példányát futásidőben, a megfelelő rendszergazda által eljuttatott engedélyek szabályozzák. |

## <a name="next-steps"></a>Következő lépések

- Az alkalmazás és a szolgáltatás egyszerű objektumainak lekérdezéséhez használhatja a [Microsoft Graph Explorert](https://developer.microsoft.com/graph/graph-explorer) is.
- Az alkalmazás alkalmazás-objektumát a Microsoft Graph API, a [Azure Portal][AZURE-Portal] Application manifest Editor vagy az [Azure ad PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)segítségével érheti el, ahogyan azt a OData [alkalmazási entitása][MS-Graph-App-Entity]képviseli.
- A Microsoft Graph API-n vagy az [Azure ad PowerShell-parancsmagokon](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)keresztül elérheti egy alkalmazás egyszerű szolgáltatásának objektumát, amelyet a OData [ServicePrincipal-entitása][MS-Graph-Sp-Entity]is képvisel.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
