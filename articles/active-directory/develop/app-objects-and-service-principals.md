---
title: Alkalmazások & szolgáltatástagok az Azure AD-ben | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg az alkalmazás és a szolgáltatás egyszerű objektumok közötti kapcsolat az Azure Active Directoryban.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: a636ff15da09bcf1891618d65270376f26fd3239
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885599"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Alkalmazás- és szolgáltatásnév-objektumok az Azure Active Directoryban

Néha az "alkalmazás" kifejezés jelentése félreérthető, ha az Azure Active Directory (Azure AD) környezetében használja. Ez a cikk tisztázza az Azure AD-alkalmazások integrációjának fogalmi és konkrét szempontjait, a regisztráció és a [több-bérlős alkalmazás](developer-glossary.md#multi-tenant-application)hozzájárulásának szemléltetésével.

## <a name="overview"></a>Áttekintés

Az Azure AD-vel integrált alkalmazások olyan következményekkel járnak, amelyek túlmutatnak a szoftver szemponton. "Alkalmazás" gyakran használják fogalmi kifejezésként, amely nem csak az alkalmazás szoftver, hanem az Azure AD-regisztráció és a szerepkör hitelesítési /engedélyezési "beszélgetések" futásidőben.

Definíció szerint egy alkalmazás a következő szerepkörökben működhet:

- [Ügyfélszerepkör](developer-glossary.md#client-application) (erőforrás felhasználása)
- [Erőforrás-kiszolgálói](developer-glossary.md#resource-server) szerepkör (API-k kiteregetése az ügyfelek számára)
- Ügyfél- és erőforrás-kiszolgálói szerepkör egyaránt

Az [OAuth 2.0 engedélyezési engedélyezési folyamat](developer-glossary.md#authorization-grant) határozza meg a beszélgetési protokollt, amely lehetővé teszi, hogy az ügyfél/erőforrás hozzáférjen/megvédje az erőforrás adatait.

A következő szakaszokban láthatja, hogy az Azure AD-alkalmazásmodell egy alkalmazást jelöl tervezési és futási időben.

## <a name="application-registration"></a>Alkalmazásregisztráció

Amikor regisztrál egy Azure AD-alkalmazást az [Azure Portalon,][AZURE-Portal]két objektum jön létre az Azure AD-bérlőben:

- Egy alkalmazásobjektum, és
- Egy szolgáltatásnév-objektum

### <a name="application-object"></a>Alkalmazásobjektum

Az Azure AD-alkalmazást az egyetlen alkalmazásobjektum határozza meg, amely az Azure AD-bérlőben található, ahol az alkalmazás regisztrálva van, az alkalmazás "otthoni" bérlője. A Microsoft Graph [Alkalmazás entitás][MS-Graph-App-Entity] határozza meg az alkalmazásobjektum tulajdonságainak sémáját.

### <a name="service-principal-object"></a>Egyszerű szolgáltatásobjektum

Az Azure AD-bérlő által védett erőforrások eléréséhez a hozzáférést igénylő entitást egy rendszerbiztonsági tagnak kell képviselnie. Ez mind a felhasználókra (egyszerű felhasználó) és az alkalmazásokra (egyszerű szolgáltatásra) igaz.

A rendszerbiztonsági tag határozza meg a hozzáférési szabályzatot és engedélyeket a felhasználó/alkalmazás az Azure AD-bérlőben. Ez lehetővé teszi az alapvető funkciók, például a felhasználó/alkalmazás hitelesítése a bejelentkezés során, és az erőforrás-hozzáférés során engedélyezés.

Ha egy alkalmazás engedélyt kap a bérlő erőforrásainak elérésére (regisztráció vagy [hozzájárulás](developer-glossary.md#consent)esetén), létrejön egy egyszerű szolgáltatásobjektum. A Microsoft Graph [ServicePrincipal entitás][MS-Graph-Sp-Entity] határozza meg a sémát egy egyszerű szolgáltatásobjektum tulajdonságaihoz.

### <a name="application-and-service-principal-relationship"></a>Az alkalmazás és a szolgáltatásnév kapcsolata

Vegye figyelembe az alkalmazás objektumot, mint az alkalmazás *globális* képviseletét az összes bérlő, és a szolgáltatásnév, mint a *helyi* képviselet egy adott bérlőben használható.

Az alkalmazásobjektum szolgál sablonként, amelyből a közös és az alapértelmezett tulajdonságok *származtatása* történik a megfelelő szolgáltatásnév-objektumok létrehozásához. Az alkalmazásobjektum ezért 1:1-es kapcsolattal rendelkezik a szoftveralkalmazással, és 1:több kapcsolata van a hozzá tartozó egyszerű szolgáltatásobjektummal.

Minden olyan bérlőben létre kell hozni egy egyszerű szolgáltatást, ahol az alkalmazást használják, lehetővé téve, hogy identitást hozzon létre a bejelentkezéshez és/vagy a bérlő által védett erőforrásokhoz való hozzáféréshez. Egybérlős alkalmazás csak egy szolgáltatásnévvel rendelkezik (a saját bérlőjében), melynek létrehozása és a használatának engedélyezése az alkalmazás regisztrációja során történik. A több-bérlős webalkalmazás/API is rendelkezik egy egyszerű szolgáltatás létrehozása minden bérlőben, ahol a felhasználó az adott bérlő hozzájárult annak használatához.

> [!NOTE]
> Az alkalmazásobjektumon végrehajtott módosítások is megjelennek a szolgáltatásegyszerű objektumban az alkalmazás otthoni bérlőjében (az a bérlő, ahol regisztrálva volt). Több-bérlős alkalmazások esetén az alkalmazásobjektum módosításai nem jelennek meg a fogyasztói bérlők egyszerű szolgáltatásobjektumaiban, amíg a hozzáférést el nem távolítják az [alkalmazáshozzáférési panelen](https://myapps.microsoft.com) keresztül, és újra meg nem adják.
>
> Azt is vegye figyelembe, hogy a natív alkalmazások alapértelmezés szerint több-bérlősként vannak regisztrálva.

## <a name="example"></a>Példa

Az alábbi ábra az alkalmazás objektuma és a megfelelő egyszerű szolgáltatásobjektumok közötti kapcsolatot mutatja be egy **HR-alkalmazás**nevű minta több-bérlős alkalmazás környezetében. Ebben a példában három Azure AD-bérlő van:

- **Adatum** - A **HR alkalmazást** kifejlesztő cég által használt bérlő
- **Contoso** – a Contoso szervezet által használt bérlő, amely a **HR-alkalmazás** fogyasztója
- **Fabrikam** - A Fabrikam szervezet által használt bérlő, amely szintén felhasználja a **HR alkalmazást**

![Az alkalmazásobjektum és az egyszerű szolgáltatásobjektum közötti kapcsolat](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Ebben a példában:

| Lépés | Leírás |
|------|-------------|
| 1    | Az alkalmazás és a szolgáltatás egyszerű objektumainak létrehozása az alkalmazás otthoni bérlőjében. |
| 2    | Amikor a Contoso és a Fabrikam-rendszergazdák teljes hozzájárulás, egy egyszerű szolgáltatás objektum jön létre a vállalat Azure AD-bérlő, és hozzárendelt e rendszergazda által megadott engedélyeket. Azt is vegye figyelembe, hogy a HR-alkalmazás konfigurálható/tervezhető úgy, hogy a felhasználók egyéni használatra engedélyezze a beleegyezést. |
| 3    | A HR-alkalmazás (Contoso és Fabrikam) fogyasztói bérlői saját egyszerű szolgáltatásobjektummal rendelkeznek. Mindegyik az alkalmazás egy példányának futásidőben történő használatát jelöli, amelyet az adott rendszergazda által jóváhagyott engedélyek szabályoznak. |

## <a name="next-steps"></a>További lépések

- A Microsoft [Graph Intézővel](https://developer.microsoft.com/graph/graph-explorer) lekérdezheti az alkalmazás és a szolgáltatásegyszerű objektumokat is.
- Az alkalmazás objektuma a Microsoft Graph API, az [Azure Portal][AZURE-Portal] alkalmazásjegyzék-szerkesztője vagy az [Azure AD PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)érhető el, az OData-alkalmazás [entitása][MS-Graph-App-Entity]által képviselt.
- Az alkalmazás egyszerű szolgáltatásobjektumát a Microsoft Graph API-n vagy az [Azure AD PowerShell-parancsmagokon](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0)keresztül érheti el, az OData [ServicePrincipal entitása][MS-Graph-Sp-Entity]által képviselt.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
