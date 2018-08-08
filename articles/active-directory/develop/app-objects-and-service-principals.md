---
title: Az Azure Active Directory-alkalmazás és egyszerű szolgáltatási objektumok
description: Ismerteti azokat a alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directory közötti kapcsolat
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
services: active-directory
editor: ''
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: 057465567217cff080b189bcdabee3042f41468d
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595875"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Alkalmazás és egyszerű szolgáltatási objektumok Azure Active Directoryban (Azure AD)
Egyes esetekben az "alkalmazás" kifejezés szerinti is böngésző használatakor az Azure AD környezetében. Ez a cikk célja az, hogy értelmezését elméleti és konkrét az Azure AD integrációja, a regisztráció olyan bemutatásáért, és a vonatkozó beleegyezés egy [több-bérlős alkalmazás](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Áttekintés
Az Azure ad-vel integrált alkalmazás következményeket vonhat, amely a szoftver aspektus. "Alkalmazás" leggyakrabban egy általános kifejezés, nem csak az alkalmazásokat, de is az Azure AD-regisztrációs és a hitelesítés/engedélyezés futásidőben a "beszélgetés" szerepkör. Definíció szerint egy alkalmazás működhet a egy [ügyfél](developer-glossary.md#client-application) szerepkör (használ egy erőforrást), egy [erőforrás-kiszolgáló](developer-glossary.md#resource-server) szerepkör (az ügyfelek illetéktelenül elért fiók(ok) API), vagy akár mindkettőt. Határozza meg a beszélgetési protokoll egy [OAuth 2.0 engedélyezési Grant flow](developer-glossary.md#authorization-grant), így az ügyfél és az erőforrások hozzáférési és adatok védelme érdekében az erőforrás jelölik. Most nézzük meg a mélyebb szintű, és tekintse meg, hogyan jelöli az Azure AD-alkalmazásmodell a tervezési idejű és futtatható alkalmazás. 

## <a name="application-registration"></a>Alkalmazásregisztráció
Amikor regisztrál az Azure AD-alkalmazást a [az Azure portal][AZURE-Portal], két objektum az Azure AD-bérlőben jönnek létre: egy alkalmazásobjektumot, és a egy szolgáltatásnév-objektumot.

#### <a name="application-object"></a>alkalmazásobjektum
Azure AD-alkalmazást határozza meg az egyik, és csak alkalmazás objektum, amely található az Azure AD-bérlővel, ahol az alkalmazás regisztrálva lett, más néven az alkalmazás "saját" bérlőjén. Az Azure AD Graph [alkalmazás entitás] [ AAD-Graph-App-Entity] határozza meg a sémában az alkalmazás objektum tulajdonságait. 

#### <a name="service-principal-object"></a>szolgáltatásnév-objektum
Az Azure AD-bérlő által védett erőforrások eléréséhez az entitás hozzáférést igénylő rendszerbiztonsági tag kell képviseli. Ez igaz mind a felhasználók (a felhasználó rendszerbiztonsági tag), és az alkalmazások (egyszerű szolgáltatásnevének). A rendszerbiztonsági tagot az adott bérlőn belüli határozza meg a hozzáférési szabályzat és a felhasználó vagy alkalmazás engedélyeit. Ez a alapvető funkcióit, például a felhasználó vagy alkalmazás hitelesítése során be- és erőforrás-hozzáférés során engedélyezési lehetővé teszi.

Ha egy alkalmazás engedélyt kap hozzáférését az erőforrásokhoz a bérlő (regisztráláskor vagy [hozzájárulás](developer-glossary.md#consent)), egy szolgáltatásnév-objektum létrejön. Az Azure AD Graph [ServicePrincipal entitás] [ AAD-Graph-Sp-Entity] határozza meg a sémában a szolgáltatás egyszerű objektum tulajdonságait. 

#### <a name="application-and-service-principal-relationship"></a>Alkalmazás és szolgáltatás egyszerű kapcsolat
Fontolja meg az alkalmazás-objektumot, a *globális* ábrázolása az alkalmazás használatát az összes bérlőre, és a szolgáltatásnév, mint a *helyi* használatát egy adott bérlő ábrázolását. Az alkalmazás objektum adattárat biztosít, mely közös a sablon és az alapértelmezett tulajdonságokat, amelyek *származtatott* a megfelelő egyszerű szolgáltatási objektumok létrehozásához. Egy alkalmazás ezért objektumnak az alkalmazás 1:1 kapcsolatot, és a egy annak megfelelő szolgáltatás egyszerű objektumok 1:many kapcsolatokkal.

Egy egyszerű szolgáltatást kell létrehozni az egyes bérlők, ahol az alkalmazás használja, lehetővé teszi a bejelentkezési és/vagy a bérlő által védett erőforrásokhoz való hozzáférés identitást létrehozni. Egy egybérlős alkalmazást csak egy egyszerű szolgáltatással (a saját bérlőjén), létrehozott, és használja az alkalmazás regisztrációja során által jóváhagyott rendelkezik. Egy több-bérlős webes alkalmazás és az API is rendelkezik az egyes bérlők létrehozott egyszerű szolgáltatás ahol a bérlőt a felhasználó hozzájárult annak használatára. 

> [!NOTE]
> Az alkalmazásobjektum végrehajtott módosítások is megjelennek a szolgáltatásnév-objektumot a az alkalmazás saját bérlőjén csak (a bérlő, ahol regisztrálva lett). Több-bérlős alkalmazások, az alkalmazás az objektumot nem tükröződnek az minden olyan fogyasztói bérlők egyszerű Szolgáltatásobjektumok, mindaddig, amíg a rendszer eltávolítja a hozzáférés-n keresztül a [alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) , és újra.
><br>  
> Azt is vegye figyelembe, hogy natív alkalmazásokat regisztrált több-bérlős alapértelmezés szerint.
> 
> 

## <a name="example"></a>Példa
A következő ábra szemlélteti egy alkalmazás alkalmazásobjektum és egyszerű objektumok egy több-bérlős mintaalkalmazás kontextusában nevű megfelelő szolgáltatás közötti kapcsolat **HR alkalmazás**. Ebben a forgatókönyvben három Azure AD-bérlőt szerepelnek: 

* **Adatum** – a bérlő által kifejlesztett a vállalat által használt a **HR alkalmazás**
* **Contoso** – a bérlő, a Contoso szervezet által használt egy fogyasztói, azaz a **HR alkalmazás**
* **A Fabrikam** – a bérlő használja a Fabrikam szervezet, amely is felhasználja a **HR alkalmazás**

![Az alkalmazás objektum és a egy szolgáltatásnév-objektum közötti kapcsolat](./media/app-objects-and-service-principals/application-objects-relationship.png)

Az előző ábrán az 1. lépés az a folyamat az alkalmazás és egyszerű szolgáltatási objektumok létrehozása a az alkalmazás saját bérlőjén.

2. lépésben a Contoso és Fabrikam rendszergazdák befejezése után a jóváhagyás, egyszerű létrejön a vállalat az Azure AD-bérlőben, és az engedélyeket, a rendszergazda adta meg. Vegye figyelembe, hogy a HR-alkalmazást konfigurált/célja lehet, hogy a hozzájárulási a felhasználók egyéni használatra is.

3. lépésben a fogyasztói bérlők, a HR kérelem (Contoso és Fabrikam) minden egyes rendelkezik saját szolgáltatásnév-objektumot. Minden egyes jelöli használata során az alkalmazás a futásideje, az engedélyek szabályozzák egy példányát hagyta jóvá a megfelelő rendszergazda.

## <a name="next-steps"></a>További lépések
Az alkalmazásobjektum alkalmazás elérhető lesz az Azure AD Graph API-n keresztül a [az Azure portal] [ AZURE-Portal] alkalmazásjegyzék-szerkesztőben, vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), mint Az OData által képviselt [alkalmazás entitás][AAD-Graph-App-Entity].

Egy alkalmazás egyszerű szolgáltatási objektumot az Azure AD Graph API-n keresztül érhető el vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), határozzák meg az OData [ServicePrincipal entitás] [ AAD-Graph-Sp-Entity].

A [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) akkor hasznos, ha az alkalmazás és az egyszerű szolgáltatási objektumok lekérdezéséhez.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
