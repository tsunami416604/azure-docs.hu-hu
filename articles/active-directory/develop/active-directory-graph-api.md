---
title: Azure Active Directory – Graph API
description: Az Azure AD Graph API áttekintése és gyors útmutatója, amely lehetővé teszi a programozott hozzáférést az Azure AD-hez REST API végpontokon keresztül.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: da99468b1582c4acab192ad3b96761172aa69580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89068660"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API

> [!IMPORTANT]
> Azt javasoljuk, hogy az Azure AD Graph API helyett az [Microsoft Graph](https://developer.microsoft.com/graph) használja az Azure Active Directory (Azure ad) erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott számú forgatókönyv áll rendelkezésre, amelyek esetében az Azure AD Graph API továbbra is megfelelő lehet; További információkért tekintse meg az [Microsoft Graph vagy az Azure ad Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) blogbejegyzését, és [telepítse át az Azure ad Graph-alkalmazásokat a Microsoft Graphra](/graph/migrate-azure-ad-graph-planning-checklist).

Ez a cikk az Azure AD Graph APIre vonatkozik. A Microsoft Graph API-val kapcsolatos hasonló információk: [a Microsoft Graph API használata](/graph/use-the-api).

Az Azure Active Directory Graph API programozás alapú hozzáférést biztosít az Azure AD-hez REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashat, frissíthetnek és törölhetnek (szifilisz) műveleteket a címtáradatokat és az objektumokat. Például az Azure AD Graph API a következő általános műveleteket támogatja egy felhasználói objektumhoz:

* Új felhasználó létrehozása egy címtárban
* A felhasználó részletes tulajdonságainak (például a csoportok) beolvasása
* Frissítheti a felhasználó tulajdonságait, például a helyüket és a telefonszámot, vagy megváltoztathatja a jelszavát
* Felhasználói csoporttagság keresése szerepköralapú hozzáféréshez
* Felhasználói fiók letiltása vagy teljes törlése

Emellett hasonló műveleteket is végrehajthat más objektumokon, például csoportokon és alkalmazásokon. Az Azure AD-Graph API címtárban való meghívásához az alkalmazást regisztrálni kell az Azure AD-ben. Az alkalmazásnak hozzáférést kell biztosítania az Azure AD Graph APIhoz is. Ez a hozzáférés általában egy felhasználói vagy rendszergazdai beleegyező folyamaton keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg az [Azure ad Graph API rövid útmutatóját](./microsoft-graph-intro.md), vagy tekintse meg az [interaktív Azure ad Graph API dokumentációját](/previous-versions/azure/ad/graph/api/api-catalog).

## <a name="features"></a>Szolgáltatások

Az Azure AD Graph API a következő funkciókat biztosítja:

* **REST API végpontok**: az Azure ad Graph API egy REST-alapú szolgáltatás, amely a szabványos HTTP-kérések segítségével elérhető végpontokból áll. Az Azure AD Graph API támogatja az XML-vagy JavaScript-objektumok (JSON) tartalomtípusát a kérelmekhez és a válaszokhoz. További információ: az [Azure ad Graph REST API referenciája](/previous-versions/azure/ad/graph/api/api-catalog).
* **Hitelesítés az Azure ad-vel**: az azure ad Graph API minden kérését hitelesíteni kell egy JSON web token (JWT) a kérelem engedélyezési fejlécében való hozzáfűzésével. Ezt a jogkivonatot az Azure AD jogkivonat-végpontjának kérésével, valamint érvényes hitelesítő adatok megadásával szerezték be. Használhatja a OAuth 2,0 ügyfél-hitelesítő adatok folyamatát, vagy az engedélyezési kód lehetővé teszi a flow számára, hogy megszerezze a tokent a gráf meghívásához. További információ [: OAuth 2,0 az Azure ad-ben](/previous-versions/azure/dn645545(v=azure.100)).
* **Szerepköralapú hitelesítés (RBAC)**: a biztonsági csoportok használatával végezhető el a RBAC az Azure AD-Graph API. Ha például meg szeretné állapítani, hogy egy felhasználó rendelkezik-e hozzáféréssel egy adott erőforráshoz, az alkalmazás meghívhatja a csoporttagság [(tranzitív)](/previous-versions/azure/ad/graph/api/functions-and-actions#checkMemberGroups) műveletet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés**: a különbözeti lekérdezés lehetővé teszi egy könyvtár változásainak nyomon követését két időszak között anélkül, hogy gyakori lekérdezéseket kellene végeznie az Azure AD-Graph API. Ez a típusú kérelem csak az előző különbözeti lekérdezési kérelem és a jelenlegi kérelem közötti módosításokat adja vissza. További információ: [Azure AD Graph API különbözeti lekérdezés](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-differential-query).
* **Directory-bővítmények**: egyéni tulajdonságokat adhat hozzá a címtár-objektumokhoz külső adattár megkövetelése nélkül. Ha például az alkalmazás minden felhasználóhoz egy Skype ID tulajdonságot igényel, akkor regisztrálhatja az új tulajdonságot a címtárban, és minden felhasználói objektumon használható lesz. További információ: [Azure AD Graph API Directory sémakezelő bővítmények](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Védettek az engedélyek hatókörei**: az azure ad Graph API olyan jogosultsági hatóköröket tesz elérhetővé, amelyek lehetővé teszik az Azure ad-adatelérést az OAuth 2,0 használatával. Számos ügyfélalkalmazás-típust támogat, többek között a következőket:
  
  * azok a felhasználói felületek, amelyek a bejelentkezett felhasználó hitelesítése útján kapnak hozzáférést az adatokhoz (delegált)
  * a háttérben működő Service/Daemon-alkalmazások bejelentkezett felhasználó nélkül, és az alkalmazás által meghatározott szerepköralapú hozzáférés-vezérlés használata
    
    A delegált és az alkalmazásra vonatkozó engedélyek egyaránt az Azure AD-Graph API által közzétett jogosultságot jelentenek, és az ügyfélalkalmazások a [Azure Portal](https://portal.azure.com)alkalmazás-regisztrációs engedélyek funkciói által igényelhetők. Az [Azure AD Graph API engedély-hatókörök](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes) az ügyfélalkalmazás által használható információkról nyújtanak információt.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure AD Graph API számos alkalmazási forgatókönyvet tesz lehetővé. A leggyakoribb forgatókönyvek a következők:

* Üzletági **(Egybérlős) alkalmazás**: ebben a forgatókönyvben a vállalati fejlesztő egy Office 365-előfizetéssel rendelkező szervezet számára működik. A fejlesztő olyan webalkalmazást épít ki, amely együttműködik az Azure AD-vel olyan feladatok elvégzéséhez, mint például a licencek felhasználóhoz rendelése. Ehhez a feladathoz hozzáférést kell adni az Azure AD-Graph APIhoz, így a fejlesztő regisztrálja az egybérlős alkalmazást az Azure AD-ben, és az olvasási és írási engedélyeket konfigurálja az Azure AD Graph APIhoz. Ezután az alkalmazás úgy van konfigurálva, hogy a saját hitelesítő adatait használja, vagy a jelenleg bejelentkezett felhasználó számára, hogy jogkivonatot szerezzen be az Azure AD Graph API meghívásához.
* **Szoftveres szolgáltatásalkalmazás (több-bérlős)**: ebben a forgatókönyvben egy független SZOFTVERGYÁRTÓ (ISV) olyan üzemeltetett több-bérlős webalkalmazást fejleszt, amely az Azure ad-t használó más szervezeteknek biztosít felhasználói felügyeleti funkciókat. Ezeknek a szolgáltatásoknak hozzáférésre van szükségük a címtár objektumaihoz, így az alkalmazásnak meg kell hívnia az Azure AD Graph API. A fejlesztő regisztrálja az alkalmazást az Azure AD-ben, úgy konfigurálja, hogy írási és olvasási jogosultságot kér az Azure AD Graph APIhoz, majd engedélyezi a külső hozzáférést, hogy más szervezetek is beleférjenek az alkalmazáshoz a címtárában. Ha egy másik szervezet felhasználója először hitelesíti az alkalmazást, az alkalmazás által kért engedélyekkel rendelkező belefoglalási párbeszédablak jelenik meg. A beleegyezés megadása után az alkalmazás a felhasználó címtárában adja meg a kért engedélyeket az Azure AD-Graph APInak. További információ az engedélyezési keretrendszerről: [az engedélyezési keretrendszer áttekintése](consent-framework.md).

## <a name="next-steps"></a>Következő lépések

A Azure Active Directory Graph API használatának megkezdéséhez tekintse meg a következő témaköröket:

* [Azure AD Graph API rövid útmutató](./microsoft-graph-intro.md)
* [Azure AD Graph – REST dokumentáció](/previous-versions/azure/ad/graph/api/api-catalog)
