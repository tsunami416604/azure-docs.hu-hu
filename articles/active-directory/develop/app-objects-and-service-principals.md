---
title: Alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban
description: Ismerje meg az alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directory közötti kapcsolat.
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: f73c4f7f606f264f899aeb6405ac7bfae71e518d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948057"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban

Egyes esetekben az "alkalmazás" kifejezés szerinti is böngésző, ha az Azure Active Directory (Azure AD) környezetben. Ez a cikk értelmezi a fogalmi és konkrét aspektusait az Azure AD integrációja, a regisztráció és az hozzájárulásra vonatkozó olyan bemutatásáért, ahogy egy [több-bérlős alkalmazás](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Áttekintés

Az Azure ad-vel integrált alkalmazás következményeket vonhat, amely a szoftver aspektus. "Alkalmazás" leggyakrabban egy általános kifejezés, nem csak az alkalmazásokat, de is az Azure AD-regisztrációs és a hitelesítés/engedélyezés futásidőben a "beszélgetés" szerepkör.

Definíció szerint egy alkalmazás működhet, ezzel a szerepkörrel:

- [Ügyfél](developer-glossary.md#client-application) szerepkör (felhasználása erőforrás)
- [Erőforrás-kiszolgáló](developer-glossary.md#resource-server) szerepkör (API-k által az ügyfeleknek)
- Ügyfél szerepkör és az erőforrás-kiszolgálói szerepkör

Egy [OAuth 2.0 engedélyezési Grant flow](developer-glossary.md#authorization-grant) a beszélgetés protokoll, amely lehetővé teszi, hogy az ügyfél és az erőforrások hozzáférés/adatok védelme érdekében egy erőforrást, illetve határozza meg.

A következő szakaszokban láthatja, hogyan jelöli az Azure AD-alkalmazásmodell a tervezési idejű és futtatható alkalmazás.

## <a name="application-registration"></a>Alkalmazásregisztráció

Amikor regisztrál az Azure AD-alkalmazást a [az Azure portal][AZURE-Portal], két objektum az Azure AD-bérlőben jönnek létre:

- Alkalmazás objektumot, és
- A szolgáltatásnév-objektum

### <a name="application-object"></a>alkalmazásobjektum

Azure AD-alkalmazást határozza meg az egyik, és csak alkalmazás objektum, amely található az Azure AD-bérlővel, ahol az alkalmazás regisztrálva lett, más néven az alkalmazás "saját" bérlőjén. Az Azure AD Graph [alkalmazás entitás] [ AAD-Graph-App-Entity] határozza meg a sémában az alkalmazás objektum tulajdonságait.

### <a name="service-principal-object"></a>szolgáltatásnév-objektum

Az Azure AD-bérlő által védett erőforrások eléréséhez az entitás hozzáférést igénylő rendszerbiztonsági tag kell képviseli. Ez igaz mind a felhasználók (a felhasználó rendszerbiztonsági tag), és az alkalmazások (egyszerű szolgáltatásnevének).

Az Azure AD-bérlőben a rendszerbiztonsági tag határozza meg a hozzáférési szabályzat és a felhasználó vagy alkalmazás engedélyeit. Ez a alapvető funkcióit, például a felhasználó vagy alkalmazás hitelesítése során be- és erőforrás-hozzáférés során engedélyezési lehetővé teszi.

Ha egy alkalmazás engedélyt kap hozzáférését az erőforrásokhoz a bérlő (regisztráláskor vagy [hozzájárulás](developer-glossary.md#consent)), egy szolgáltatásnév-objektum létrejön. Az Azure AD Graph [ServicePrincipal entitás] [ AAD-Graph-Sp-Entity] határozza meg a sémában a szolgáltatás egyszerű objektum tulajdonságait.

### <a name="application-and-service-principal-relationship"></a>Alkalmazás és szolgáltatás egyszerű kapcsolat

Fontolja meg az alkalmazás-objektumot, a *globális* ábrázolása az alkalmazás használatát az összes bérlőre, és a szolgáltatásnév, mint a *helyi* használatát egy adott bérlő ábrázolását.

Az alkalmazás objektum adattárat biztosít, mely közös a sablon és az alapértelmezett tulajdonságokat, amelyek *származtatott* a megfelelő egyszerű szolgáltatási objektumok létrehozásához. Egy alkalmazás ezért objektumnak az alkalmazás 1:1 kapcsolatot, és a egy annak megfelelő szolgáltatás egyszerű objektumok 1:many kapcsolatokkal.

Egy egyszerű szolgáltatást kell létrehozni az egyes bérlők, ahol az alkalmazás használja, lehetővé teszi a bejelentkezési és/vagy a bérlő által védett erőforrásokhoz való hozzáférés identitást létrehozni. Egy egybérlős alkalmazást csak egy egyszerű szolgáltatással (a saját bérlőjén), létrehozott, és használja az alkalmazás regisztrációja során által jóváhagyott rendelkezik. Egy több-bérlős webes alkalmazás és az API is rendelkezik az egyes bérlők létrehozott egyszerű szolgáltatás ahol a bérlőt a felhasználó hozzájárult annak használatára. 

> [!NOTE]
> Az alkalmazásobjektum végrehajtott módosítások is megjelennek a szolgáltatásnév-objektumot a az alkalmazás saját bérlőjén csak (a bérlő, ahol regisztrálva lett). Több-bérlős alkalmazások, az alkalmazás az objektumot nem tükröződnek az minden olyan fogyasztói bérlők egyszerű Szolgáltatásobjektumok, keresztül eltávolítását a hozzáférést a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) , és újra.
>
> Azt is vegye figyelembe, hogy natív alkalmazásokat regisztrált több-bérlős alapértelmezés szerint.

## <a name="example"></a>Példa

A következő ábra szemlélteti egy alkalmazás alkalmazásobjektum és egyszerű objektumok egy több-bérlős mintaalkalmazás kontextusában nevű megfelelő szolgáltatás közötti kapcsolat **HR alkalmazás**. Ebben a példaforgatókönyvben három Azure AD-bérlőt szerepelnek:

- **Adatum** – a bérlő által kifejlesztett a vállalat által használt a **HR alkalmazás**
- **Contoso** – a bérlő, a Contoso szervezet által használt egy fogyasztói, azaz a **HR alkalmazás**
- **A Fabrikam** – a bérlő használja a Fabrikam szervezet, amely is felhasználja a **HR alkalmazás**

![Az alkalmazás objektum és a egy szolgáltatásnév-objektum közötti kapcsolat](./media/app-objects-and-service-principals/application-objects-relationship.png)

A példában:

| Lépés | Leírás |
|------|-------------|
| 1    | Az a folyamat, az alkalmazás és egyszerű szolgáltatási objektumok létrehozása a az alkalmazás saját bérlőjén. |
| 2    | A Contoso és Fabrikam rendszergazdák befejezése után a jóváhagyás, egy szolgáltatásnév-objektum létrejön a vállalat az Azure AD-bérlőben, és az engedélyeket, a rendszergazda adta meg. Vegye figyelembe, hogy a HR-alkalmazást konfigurált/célja lehet, hogy a hozzájárulási a felhasználók egyéni használatra is. |
| 3    | A fogyasztói bérlők, a HR kérelem (Contoso és Fabrikam) minden egyes rendelkezik saját szolgáltatásnév-objektumot. Minden egyes jelöli használata során az alkalmazás a futásideje, az engedélyek szabályozzák egy példányát hagyta jóvá a megfelelő rendszergazda. |

## <a name="next-steps"></a>További lépések

- Használhatja a [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) mind az alkalmazás és egyszerű szolgáltatási objektumok lekérdezéséhez.
- Egy alkalmazás application objektumot az Azure AD Graph API használatával is elérheti a [az Azure portal] [ AZURE-Portal] alkalmazásjegyzék-szerkesztőben, vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), Az OData jelölt [alkalmazás entitás][AAD-Graph-App-Entity].
- Egy alkalmazás egyszerű szolgáltatási objektumot az Azure AD Graph API keresztül érheti el, vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), határozzák meg az OData [ServicePrincipal entitás] [ AAD-Graph-Sp-Entity].

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
