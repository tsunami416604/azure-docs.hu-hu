---
title: Azure Active Directory – Graph API
description: Áttekintés és rövid útmutató az Azure AD Graph API-hoz, amely lehetővé teszi az Azure AD programozott elérését a REST API-végpontokon keresztül.
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
ms.openlocfilehash: 73cdac1a372b42df5a8f52ea09f04ecc40031698
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885718"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API

> [!IMPORTANT]
> Azt javasoljuk, hogy az Azure Active Directory (Azure AD) erőforrások eléréséhez az Azure Active Directory (Azure AD) erőforrások eléréséhez használja a [Microsoft Graph-ot](https://developer.microsoft.com/graph) az Azure Active Directory (Azure AD) erőforrások elérése helyett. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Vannak nagyon korlátozott számú forgatókönyvek, amelyek az Azure AD Graph API továbbra is megfelelő lehet; További információt a [Microsoft Graph vagy az Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) blogbejegyzésében, valamint az [Azure AD Graph-alkalmazások áttelepítése a Microsoft Graphba című témakörben talál.](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)

Ez a cikk az Azure AD Graph API-ra vonatkozik. A Microsoft Graph API-val kapcsolatos hasonló információkért [lásd: A Microsoft Graph API használata](https://docs.microsoft.com/graph/use-the-api).

Az Azure Active Directory Graph API programozás alapú hozzáférést biztosít az Azure AD-hez REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashatnak, frissíthetnek és törölhetnek (CRUD) műveleteket a címtáradatokon és -objektumokon. Az Azure AD Graph API például a következő gyakori műveleteket támogatja egy felhasználói objektumhoz:

* Új felhasználó létrehozása a címtárban
* A felhasználó részletes tulajdonságainak, például a csoportjaiknak a beszereznie
* A felhasználó tulajdonságainak , például tartózkodási helyének és telefonszámának frissítése, vagy jelszó módosítása
* Felhasználó csoporttagságának ellenőrzése szerepköralapú hozzáférés esetén
* Felhasználó fiókjának letiltása vagy teljes törlése

Ezenkívül más objektumokon, például csoportokon és alkalmazásokon is végrehajthat hasonló műveleteket. Az Azure AD Graph API hívásához egy címtárban, az alkalmazás regisztrálva kell lennie az Azure AD.To call Azure AD Graph API on a directory, your application must be registered with Azure AD. Az alkalmazásnak hozzáférést kell biztosítani az Azure AD Graph API-hoz. Ez a hozzáférés általában egy felhasználói vagy rendszergazdai jóváhagyási folyamaton keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg az [Azure AD Graph API rövid útmutatóját,](active-directory-graph-api-quickstart.md)vagy tekintse meg az [interaktív Azure AD Graph API referenciadokumentációját.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

## <a name="features"></a>Szolgáltatások

Az Azure AD Graph API a következő funkciókat biztosítja:

* **REST API-végpontok:** Az Azure AD Graph API egy RESTful szolgáltatás, amely a szabványos HTTP-kérelmek kel elért végpontok ból áll. Az Azure AD Graph API támogatja az XML- vagy Javascript-objektumnotúcés (JSON) tartalomtípusokat a kérelmekhez és válaszokhoz. További információ: [Azure AD Graph REST API-referencia.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* **Hitelesítés az Azure AD-vel:** Az Azure AD Graph API-ra irányuló minden kérést hitelesíteni kell egy JSON webtoken (JWT) hozzáfűzésével a kérelem engedélyezési fejlécében. Ez a jogkivonat az Azure AD token-végpontra vonatkozó kéréssel történik, és érvényes hitelesítő adatokat biztosít. Használhatja az OAuth 2.0 ügyfél hitelesítő adatok folyamat, vagy az engedélyezési kód engedélyezési folyamat beszerezni egy jogkivonatot a Graph hívásához. További információ: [OAuth 2.0 az Azure AD-ben.](https://msdn.microsoft.com/library/azure/dn645545.aspx)
* **Szerepköralapú engedélyezés (RBAC)**: A biztonsági csoportok az RBAC az Azure AD Graph API-ban. Ha például meg szeretné határozni, hogy egy felhasználó rendelkezik-e hozzáféréssel egy adott erőforráshoz, az alkalmazás meghívhatja a [Csoporttagság ellenőrzése (tranzitív)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) műveletet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés:** Különbözeti lekérdezés lehetővé teszi, hogy két időszak között kövesse nyomon a címtár változásait anélkül, hogy gyakori lekérdezéseket kellene végrehajtania az Azure AD Graph API-ra. Az ilyen típusú kérelem csak az előző különbözeti lekérdezési kérelem és az aktuális kérelem között végrehajtott módosításokat adja vissza. További információ: [Azure AD Graph API különbözeti lekérdezés.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)
* **Címtárbővítmények:** A címtárobjektumokhoz külső adattár nélkül is hozzáadhat egyéni tulajdonságokat. Ha például az alkalmazás minden felhasználóhoz skype-azonosító tulajdonságot igényel, regisztrálhatja az új tulajdonságot a címtárban, és az minden felhasználói objektumon használható lesz. További információ: [Azure AD Graph API-címtárséma-bővítmények.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)
* **Engedélyhatókörrel védett:** Az Azure AD Graph API olyan engedélyható köröket tesz elérhetővé, amelyek az OAuth 2.0 használatával biztonságos hozzáférést biztosítanak az Azure AD-adatokhoz. Számos ügyfélalkalmazás-típust támogat, többek között a következőket:
  
  * felhasználói felületek, amelyek delegált hozzáférést kapnak az adatokhoz a bejelentkezett felhasználó (delegált) felhatalmazása révén
  * szolgáltatás/démon alkalmazások, amelyek a háttérben működnek anélkül, hogy bejelentkezett felhasználó jelen lenne, és alkalmazásáltal definiált szerepköralapú hozzáférés-vezérlést használnak
    
    Mind a delegált, mind az alkalmazásengedélyek az Azure AD Graph API által elérhetővé tett jogosultságot képviselnek, és az ügyfélalkalmazások az [Azure Portalon](https://portal.azure.com)az alkalmazásregisztrációs engedélyek funkcióin keresztül igényelhetik. [Az Azure AD Graph API-engedélyhatókörei](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) tájékoztatást adnak arról, hogy mi érhető el az ügyfélalkalmazás számára.

## <a name="scenarios"></a>Forgatókönyvek

Az Azure AD Graph API számos alkalmazási forgatókönyvet tesz lehetővé. A következő forgatókönyvek a leggyakoribbak:

* **Üzletági (egybérlős) alkalmazás:** Ebben az esetben egy vállalati fejlesztő egy Office 365-előfizetéssel rendelkező szervezetnél dolgozik. A fejlesztő egy olyan webalkalmazást hoz össze, amely az Azure AD-vel olyan feladatok végrehajtása érdekében működik, mint például egy licenc hozzárendelése egy felhasználóhoz. Ez a feladat hozzáférést igényel az Azure AD Graph API-hoz, így a fejlesztő regisztrálja az egyetlen bérlős alkalmazást az Azure AD-ben, és konfigurálja az Olvasási és írási engedélyeket az Azure AD Graph API-hoz. Ezután az alkalmazás úgy van konfigurálva, hogy saját hitelesítő adatait vagy a jelenleg bejelentkező felhasználó adatait használja egy jogkivonat beszerzéséhez az Azure AD Graph API-hoz.
* **Szoftver szolgáltatásalkalmazásként (több-bérlős)**: Ebben a forgatókönyvben egy független szoftverszállító (ISV) fejleszt egy üzemeltetett több-bérlős webalkalmazást, amely az Azure AD-t használó más szervezetek számára biztosít felhasználói felügyeleti funkciókat. Ezek a szolgáltatások hozzáférést igényelnek a címtárobjektumokhoz, ezért az alkalmazásnak meg kell hívnia az Azure AD Graph API-t. A fejlesztő regisztrálja az alkalmazást az Azure AD-ben, úgy konfigurálja, hogy az Azure AD Graph API-hoz olvasási és írási engedélyeket igényeljen, majd engedélyezi a külső hozzáférést, hogy más szervezetek is hozzájárulhassanak az alkalmazás használatához a címtárban. Amikor egy másik szervezet felhasználója első alkalommal hitelesíti magát az alkalmazással, az alkalmazás által kért engedélyekkel rendelkező hozzájárulási párbeszédpanel jelenik meg. A hozzájárulás megadása ezután megadja az alkalmazásnak a kért engedélyeket az Azure AD Graph API-hoz a felhasználó címtárában. A hozzájárulási keretrendszerről a [hozzájárulási keretrendszer áttekintése című](consent-framework.md)témakörben olvashat bővebben.

## <a name="next-steps"></a>További lépések

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg az alábbi témaköröket:

* [Útmutató az Azure AD Graph API rövid útmutatójához](active-directory-graph-api-quickstart.md)
* [Az Azure AD Graph REST dokumentációja](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
