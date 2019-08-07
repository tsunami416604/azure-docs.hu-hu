---
title: Azure Active Directory Graph API | Microsoft Docs
description: Az Azure AD Graph API áttekintése és gyors útmutatója, amely lehetővé teszi a programozott hozzáférést az Azure AD-hez REST API végpontokon keresztül.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f2e3d73b336d41210d80d15b57462dd144b8e45
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835396"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API

> [!IMPORTANT]
>
> Február 2019-én elkezdtük a Azure Active Directory Graph API korábbi verzióinak elavulttá tételét a Microsoft Graph API javára. 
>
> A részleteket, a frissítéseket és az időkereteket lásd: [Microsoft Graph vagy az Azure ad Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) az Office fejlesztői központban.
>
> A továbbítást követően az alkalmazásoknak a Microsoft Graph API-t kell használniuk. 



Ez a cikk az Azure AD Graph APIre vonatkozik. A Microsoft Graph API-val kapcsolatos hasonló információk: [a Microsoft Graph API használata](https://docs.microsoft.com/graph/use-the-api). 

A Azure Active Directory Graph API programozott hozzáférést biztosít az Azure AD-hez REST API végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashat, frissíthetnek és törölhetnek (szifilisz) műveleteket a címtáradatokat és az objektumokat. Például az Azure AD Graph API a következő általános műveleteket támogatja egy felhasználói objektumhoz:

* Új felhasználó létrehozása egy címtárban
* A felhasználó részletes tulajdonságainak (például a csoportok) beolvasása
* Frissítheti a felhasználó tulajdonságait, például a helyüket és a telefonszámot, vagy megváltoztathatja a jelszavát
* Felhasználói csoporttagság keresése szerepköralapú hozzáféréshez
* Felhasználói fiók letiltása vagy teljes törlése

Emellett hasonló műveleteket is végrehajthat más objektumokon, például csoportokon és alkalmazásokon. Az Azure AD-Graph API címtárban való meghívásához az alkalmazást regisztrálni kell az Azure AD-ben. Az alkalmazásnak hozzáférést kell biztosítania az Azure AD Graph APIhoz is. Ez a hozzáférés általában egy felhasználói vagy rendszergazdai beleegyező folyamaton keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg az [Azure ad Graph API](active-directory-graph-api-quickstart.md)rövid útmutatóját, vagy tekintse meg az [interaktív Azure ad Graph API dokumentációját](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Szolgáltatások

Az Azure AD Graph API a következő funkciókat biztosítja:

* **REST API végpontok**: Az Azure AD Graph API egy REST-szolgáltatás, amely a szabványos HTTP-kérések használatával elért végpontokból áll. Az Azure AD Graph API támogatja az XML-vagy JavaScript-objektumok (JSON) tartalomtípusát a kérelmekhez és a válaszokhoz. További információ: az [Azure ad Graph REST API referenciája](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Hitelesítés az Azure ad-vel**: Az Azure AD Graph API minden kérését hitelesíteni kell egy JSON Web Token (JWT) a kérelem engedélyezési fejlécében való hozzáfűzésével. Ezt a jogkivonatot az Azure AD jogkivonat-végpontjának kérésével, valamint érvényes hitelesítő adatok megadásával szerezték be. Használhatja a OAuth 2,0 ügyfél-hitelesítő adatok folyamatát, vagy az engedélyezési kód lehetővé teszi a flow számára, hogy megszerezze a tokent a gráf meghívásához. További információ [: OAuth 2,0 az Azure ad-ben](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Szerepköralapú hitelesítés (RBAC)** : A biztonsági csoportok az Azure AD Graph API RBAC végrehajtásához használhatók. Ha például meg szeretné állapítani, hogy egy felhasználó rendelkezik-e hozzáféréssel egy adott erőforráshoz, az alkalmazás meghívhatja a csoporttagság [(tranzitív)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) műveletet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés**: A különbözeti lekérdezés lehetővé teszi, hogy két időszakon belül követheti nyomon a címtár változásait anélkül, hogy az Azure AD-Graph API gyakori lekérdezéseket kellene végeznie. Ez a típusú kérelem csak az előző különbözeti lekérdezési kérelem és a jelenlegi kérelem közötti módosításokat adja vissza. További információ: [Azure AD Graph API különbözeti lekérdezés](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Címtárszolgáltatás**-bővítmények: A címtár-objektumokhoz egyéni tulajdonságokat adhat hozzá anélkül, hogy külső adattárra lenne szükség. Ha például az alkalmazás minden felhasználóhoz egy Skype ID tulajdonságot igényel, akkor regisztrálhatja az új tulajdonságot a címtárban, és minden felhasználói objektumon használható lesz. További információ: [Azure AD Graph API Directory sémakezelő bővítmények](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **A jogosultsági hatókörök biztosítják**: Az Azure AD Graph API olyan jogosultsági hatóköröket tesz elérhetővé, amelyek lehetővé teszik a biztonságos hozzáférést az Azure AD-adataihoz az OAuth 2,0 használatával. Számos ügyfélalkalmazás-típust támogat, többek között a következőket:
  
  * azok a felhasználói felületek, amelyek a bejelentkezett felhasználó hitelesítése útján kapnak hozzáférést az adatokhoz (delegált)
  * a háttérben működő Service/Daemon-alkalmazások bejelentkezett felhasználó nélkül, és az alkalmazás által meghatározott szerepköralapú hozzáférés-vezérlés használata
    
    A delegált és az alkalmazásra vonatkozó engedélyek egyaránt az Azure AD-Graph API által közzétett jogosultságot jelentenek, és az ügyfélalkalmazások a [Azure Portal](https://portal.azure.com)alkalmazás-regisztrációs engedélyek funkciói által igényelhetők. Az [Azure AD Graph API engedély-hatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) az ügyfélalkalmazás által használható információkról nyújtanak információt.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure AD Graph API számos alkalmazási forgatókönyvet tesz lehetővé. A leggyakoribb forgatókönyvek a következők:

* Üzletági **(Egybérlős) alkalmazás**: Ebben a forgatókönyvben a vállalati fejlesztő olyan szervezet számára működik, amely Office 365-előfizetéssel rendelkezik. A fejlesztő olyan webalkalmazást épít ki, amely együttműködik az Azure AD-vel olyan feladatok elvégzéséhez, mint például a licencek felhasználóhoz rendelése. Ehhez a feladathoz hozzáférést kell adni az Azure AD-Graph APIhoz, így a fejlesztő regisztrálja az egybérlős alkalmazást az Azure AD-ben, és az olvasási és írási engedélyeket konfigurálja az Azure AD Graph APIhoz. Ezután az alkalmazás úgy van konfigurálva, hogy a saját hitelesítő adatait használja, vagy a jelenleg bejelentkezett felhasználó számára, hogy jogkivonatot szerezzen be az Azure AD Graph API meghívásához.
* **Szoftveres szolgáltatásalkalmazás (több-bérlős)** : Ebben a forgatókönyvben egy független szoftvergyártó (ISV) olyan üzemeltetett több-bérlős webalkalmazást fejleszt, amely az Azure AD-t használó más szervezetek számára biztosít felhasználói felügyeleti funkciókat. Ezeknek a szolgáltatásoknak hozzáférésre van szükségük a címtár objektumaihoz, így az alkalmazásnak meg kell hívnia az Azure AD Graph API. A fejlesztő regisztrálja az alkalmazást az Azure AD-ben, úgy konfigurálja, hogy írási és olvasási jogosultságot kér az Azure AD Graph APIhoz, majd engedélyezi a külső hozzáférést, hogy más szervezetek is beleférjenek az alkalmazáshoz a címtárában. Ha egy másik szervezet felhasználója először hitelesíti az alkalmazást, az alkalmazás által kért engedélyekkel rendelkező belefoglalási párbeszédablak jelenik meg. A beleegyezés megadása után az alkalmazás a felhasználó címtárában adja meg a kért engedélyeket az Azure AD-Graph APInak. További információ az engedélyezési keretrendszerről: [az engedélyezési keretrendszer áttekintése](consent-framework.md).

## <a name="next-steps"></a>További lépések

A Azure Active Directory Graph API használatának megkezdéséhez tekintse meg a következő témaköröket:

* [Azure AD Graph API rövid útmutató](active-directory-graph-api-quickstart.md)
* [Azure AD Graph – REST dokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
