---
title: "Az Azure Active Directory-alkalmazás és szolgáltatás egyszerű objektumok"
description: "A leírását, az alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directory közötti kapcsolat"
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: b00acc192e868a7c1ade9fc68cf4d3ca04f1a070
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2017
---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Alkalmazás és szolgáltatás egyszerű objektumok az Azure Active Directory (Azure AD)
Egyes esetekben "alkalmazás" szerinti is böngésző használata az Azure AD a környezetben. Ez a cikk célja az, hogy értelmezését elméleti és konkrét alkalmazások integrálása az Azure AD, a regisztráció olyan bemutatásáért, és hozzájárulás az egy [több-bérlős alkalmazás](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Áttekintés
Integrálva van az Azure AD alkalmazás, amely a szoftver aspektus implications van. "Alkalmazás" gyakran használatos általános kifejezés, nem csak az alkalmazásokat, de is az Azure AD-regisztrációval és a hitelesítési/engedélyezési futásidőben a "beszélgetés" szerepkör. Definíció, egy alkalmazás működhet a [ügyfél](active-directory-dev-glossary.md#client-application) szerepkör (fel erőforrás), egy [erőforrás-kiszolgáló](active-directory-dev-glossary.md#resource-server) szerepkör (API az ilyen ügyfelek), vagy akár mindkét. Határozza meg a beszélgetési protokoll egy [OAuth 2.0 Hitelesítésengedélyezési folyamat](active-directory-dev-glossary.md#authorization-grant), amely lehetővé teszi az ügyfél és az erőforrások hozzáférés vagy az erőforrás adatainak védelméhez rendre. Most pedig ugorjunk a mélyebb szintű, és tekintse meg, hogyan az Azure AD alkalmazás-modell jelenti az alkalmazást tervezéskor és futásidejű. 

## <a name="application-registration"></a>alkalmazás regisztrálása
Amikor regisztrál az Azure AD alkalmazás a [Azure-portálon][AZURE-Portal], két objektum jön létre az Azure AD-bérlőn: alkalmazásobjektum, és egy szolgáltatás egyszerű objektum.

#### <a name="application-object"></a>alkalmazásobjektum
Egy Azure AD-alkalmazást határozza meg a egy és csak alkalmazás objektum, amely az Azure AD-bérlő, ahol az alkalmazás regisztrálva lett található, az alkalmazás "otthoni" bérlői néven ismert. Az Azure AD Graph [alkalmazás entitás] [ AAD-Graph-App-Entity] határozza meg a sémában az alkalmazás tulajdonságait. 

#### <a name="service-principal-object"></a>szolgáltatás egyszerű objektum
Az Azure AD-bérlő által védett erőforrások eléréséhez az entitás hozzáférést igénylő rendszerbiztonsági tag kell képviselnie. Ez igaz mind a felhasználót (egyszerű), és az alkalmazások (egyszerű szolgáltatásnév). A rendszerbiztonsági tagot a hozzáférési házirend és a felhasználó/alkalmazáshoz tartozó engedélyek definiálja a bérlőre. Ez lehetővé teszi, hogy az alapvető funkcióit, például a hitelesítés a felhasználói kérelem során bejelentkezési és hitelesítési erőforrások elérése során.

Ha egy alkalmazás engedélyt kap hozzáférését az erőforrásokhoz a bérlő (regisztrálásakor vagy [hozzájárulás](active-directory-dev-glossary.md#consent)), egy szolgáltatás egyszerű objektum jön létre. Az Azure AD Graph [szolgáltatásnév entitás] [ AAD-Graph-Sp-Entity] határozza meg a szolgáltatás egyszerű objektum tulajdonságainak sémáját.  

#### <a name="application-and-service-principal-relationship"></a>Alkalmazás és szolgáltatás egyszerű kapcsolat
Fontolja meg az application objektum, mint a *globális* használható egyetlen bérlő számára, és a szolgáltatás egyszerű, mint az alkalmazás ábrázolását a *helyi* használatát egy adott bérlő ábrázolását. Az alkalmazás objektum működik, mely közös sablont és az alapértelmezett tulajdonságok vannak *származtatott* megfelelő szolgáltatás egyszerű objektumok létrehozására használható. Egy alkalmazás ezért objektumnak az alkalmazás 1:1 kapcsolatot, és egy annak megfelelő szolgáltatás egyszerű objektumok 1:many kapcsolata.

Egy egyszerű szolgáltatást kell létrehozni minden bérlőhöz, ahol az alkalmazás szolgál, lehetővé téve számára egy azonosságának bejelentkezési és/vagy éppen a bérlő által védett erőforrásokhoz való hozzáférés. A single-bérlő alkalmazásnak csak egy egyszerű (az otthoni bérlői), a létrehozott és átadni kívánt hozzájárult e használatra regisztrációja során. Egy több-bérlős webes alkalmazás/API is rendelkezik a szolgáltatásnevet létrehozni az egyes bérlők ahol bérlőre a felhasználó hozzájárult használatát.  

> [!NOTE]
> Az application objektum módosítások is megjelennek a szolgáltatás alapvető célja az alkalmazás otthoni bérlői csak (a bérlő, ahol regisztrálva volt). Több-bérlős alkalmazásokhoz, az objektum módosításai nem tükröződnek az bármely fogyasztói bérlők szolgáltatás egyszerű objektumok, a hozzáférés keresztül eltávolításáig a [alkalmazás hozzáférési Panel](https://myapps.microsoft.com) , és újra.
><br>  
> Ne feledje, hogy natív alkalmazások nyilvántartott több-bérlős alapértelmezés szerint.
> 
> 

## <a name="example"></a>Példa
A következő diagram azt ábrázolja, egy alkalmazás alkalmazásobjektum és egyszerű, több-bérlős mintaalkalmazás környezetében objektumok a megfelelő szolgáltatás közötti kapcsolat **HR app**. Három Azure AD-bérlő szerepelnek ebben a forgatókönyvben: 

* **Adatum** – a bérlő által kifejlesztett a vállalat által használt a **HR-alkalmazás**
* **Contoso** -a bérlő, a Contoso szervezet által használt ügyféllel, vagyis a **HR-alkalmazás**
* **Fabrikam** – a bérlő használja a Fabrikam szervezet, amely is használ a **HR-alkalmazás**

![Az application objektum és a szolgáltatás egyszerű objektum közötti kapcsolat](./media/active-directory-application-objects/application-objects-relationship.png)

Az előző ábrán az 1. lépés az alkalmazás és szolgáltatás egyszerű objektumok az alkalmazás otthoni bérlő létrehozásának folyamatán.

2. lépésben a Contoso és Fabrikam rendszergazda jóváhagyását, befejeződése után a szolgáltatás egyszerű objektum a vállalat az Azure AD-bérlő létrehozása és a engedélyeket a rendszergazda adott. Ne feledje, hogy a HR-alkalmazást konfigurált/célja lehet hozzájárulás engedélyezéséhez felhasználók egyéni használatra.

3. lépésben, a fogyasztó a bérlők a HR-alkalmazás (Contoso és Fabrikam) minden rendelkezik saját szolgáltatás egyszerű objektum. Minden egyes jelöli az alkalmazás futásidőben, az engedélyek által szabályozott példányának használatát átadni kívánt hozzájárult e a megfelelő rendszergazda.

## <a name="next-steps"></a>Következő lépések
Egy alkalmazás alkalmazásobjektum Azure AD Graph API-n keresztül elérhető legyen a [Azure portal] [ AZURE-Portal] application manifest-szerkesztőben vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), mint Az OData által képviselt [alkalmazás entitás][AAD-Graph-App-Entity].

Az alkalmazás fő szolgáltatásobjektum az Azure AD Graph API-n keresztül érhető el vagy [Azure AD PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), OData képviselt [szolgáltatásnév entitás] [ AAD-Graph-Sp-Entity].

A [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) akkor hasznos, ha az alkalmazás és a szolgáltatás egyszerű objektumok lekérdezése.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com
