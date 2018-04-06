---
title: Az Azure Active Directory Graph API |} Microsoft Docs
description: Az Azure AD Graph API-hoz, amely lehetővé teszi a programozott hozzáférést az Azure AD-REST API-végpontokon keresztül áttekintése és a gyors üzembe helyezési útmutató.
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: c0f5110fe73fb48cf6cf5307de08045bd843cb5a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API
> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://graph.microsoft.io/) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.
> 
> 

Az Azure Active Directory Graph API-val programozott hozzáférést biztosít az Azure AD-REST API-végpontokon keresztül. Alkalmazások az Azure AD Graph API használható létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek directory adatok és objektumok. Például az Azure AD Graph API a következő gyakori műveleteket, olyan felhasználói objektum támogatja:

* Új felhasználó létrehozása a könyvtárban található
* A felhasználó részletes tulajdonságait, például saját csoport
* A felhasználó tulajdonságai, például a hely és a telefonszámot, vagy módosítania kell a jelszavát
* Ellenőrizze a felhasználói csoport tagságát a szerepköralapú hozzáférés
* Egy felhasználói fiókot, vagy a teljes törlése

Ezenfelül más objektumok, például a csoportok és alkalmazások hasonló műveleteket hajthat végre. Címtár Azure AD Graph API-hívásokat kezdeményezzen, regisztrálni kell az alkalmazást az Azure ad-val. Az alkalmazáshoz is hozzáférést kell rendelni a Azure AD Graph API-hoz. A hozzáférés általában egy felhasználó vagy rendszergazda hozzájárulási folyamat keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg a [az Azure AD Graph API gyors üzembe helyezési útmutató](active-directory-graph-api-quickstart.md), vagy megtekintheti a [interaktív Azure AD Graph API-referenciadokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Szolgáltatások
Az Azure AD Graph API a következő szolgáltatásokat biztosítja:

* **REST API-végpontok**: Azure AD Graph API egy olyan szabványos HTTP-kérelmek használatával elért végpontokat áll RESTful szolgáltatás. Az Azure AD Graph API támogatja XML- vagy Javascript Object Notation (JSON) tartalomtípusok kérelmeit és válaszait. További információkért lásd: [az Azure AD Graph REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Az Azure AD hitelesítési**: Azure AD Graph API kérelmek egy JSON webes jogkivonat (JWT) található a kérés hitelesítési fejlécéhez hozzáfűzésével hitelesíteni kell. Ez a token szerzi a kérést továbbítja az Azure AD-jogkivonat végpontjához, és adja meg érvényes hitelesítő adatokat. Az engedélyezési kód engedélyezése szerezni a jogkivonatot, amellyel meghívhatja a grafikon folyamata, vagy használhatja az OAuth 2.0 ügyfél hitelesítő adatok folyamata. További információ [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Szerepkör alapú engedélyezési (RBAC)**: biztonsági csoportok használhatók az Azure AD Graph API RBAC végrehajtásához. Például, ha meg szeretné határozni, hogy egy felhasználó egy adott erőforráshoz való hozzáférés rendelkezik, az alkalmazás meghívhatja a [csoporttagság ellenőrzése (tranzitív)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) művelet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés**: különbözeti lekérdezés lehetővé teszi egy könyvtárat, anélkül, hogy így gyakori lekérdezések az Azure AD Graph API két időszakok közötti változásainak követése. Ilyen típusú kérelmet csak a korábbi különbözeti lekérdezés kérelem és a jelenlegi kérelem között végrehajtott módosítások adja vissza. További információkért lásd: [az Azure AD Graph API különbözeti lekérdezés](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Címtárbővítmények**: ou egyéni tulajdonságokat adhat hozzá címtárobjektumok anélkül, hogy egy külső adattároló. Ha az alkalmazás egy Skype-azonosító tulajdonságot minden olyan felhasználóhoz, például a címtárban regisztrálhatja az új tulajdonságot, és minden user objektum használható lesz. További információkért lásd: [az Azure AD Graph API Directory-séma bővítményei](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Védi-engedélyhatókörök**: Azure AD Graph API-val tesz elérhetővé, amelyek lehetővé teszik az OAuth 2.0 verziót használja az Azure AD-adatokhoz való biztonságos hozzáférés engedélyhatókörök. Ügyfél típusú alkalmazás, beleértve a különböző támogatja:
  
  * felhasználói felületek, amelyek hozzáférő delegált engedélyezési adatokat (delegált) bejelentkezett felhasználó
  * szolgáltatás/démon alkalmazások, hogy a háttérben, anélkül, hogy a bejelentkezett felhasználó műveletet, és használhatja alkalmazás által meghatározott szerepköralapú hozzáférés-vezérlő
    
    Mindkét meghatalmazott, és Alkalmazásengedélyek felel meg az Azure AD Graph API által elérhetővé tett jogosultság, és igényelhet ügyfélalkalmazások alkalmazás regisztrációs engedélyek funkciók a révén a [Azure-portálon](https://portal.azure.com). [Az Azure AD Graph API-Engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) bemutatja, mi az az ügyfélalkalmazás által használható.

## <a name="scenarios"></a>Forgatókönyvek
Az Azure AD Graph API lehetővé teszi, hogy az alkalmazás-forgatókönyvek. A következő forgatókönyvek esetében a leggyakoribb:

* **Üzletági (Egybérlős) alkalmazás**: Ebben a forgatókönyvben egy vállalati fejlesztői működik egy olyan szervezet, amely olyan Office 365-előfizetéssel rendelkezik. A fejlesztői fejleszt, amely együttműködik az Azure ad-val feladatok elvégzéséhez webalkalmazás ilyen rendel egy licencet a felhasználó. Ezt a feladatot az Azure AD Graph API hozzáférésre van szüksége, így a fejlesztők a egybérlős alkalmazás regisztrálása az Azure ad-ben, és konfigurálja olvasási és írási engedéllyel az Azure AD Graph API. Szerezzen be egy tokent a Azure AD Graph API hívása saját hitelesítő adatait vagy a bejelentkezés jelenleg felhasználó használja majd az alkalmazás van konfigurálva.
* **(Több-Bérlős) szolgáltatás alkalmazás**: Ebben a forgatókönyvben egy független szoftverszállító (ISV) által fejlesztett üzemeltetett több-bérlős webalkalmazás, amely más Azure AD használó szervezetek felhasználói felügyeleti funkciókat biztosít. Ezek a funkciók címtárobjektumok hozzáférést igényelnek, és így az alkalmazás Azure AD Graph API hívása. A fejlesztői regisztrálja az alkalmazást az Azure ad-ben, olvasási és írási engedéllyel az Azure AD Graph API-val konfigurálja és majd lehetővé teszi, hogy külső hozzáférés úgy, hogy más szervezetek is hozzájárul a címtár alkalmazásával. Amikor egy felhasználó egy másik szervezet hitelesíti magát az alkalmazás első indításakor, azokat az engedélyeket, az alkalmazás egy hozzájárulási párbeszédpanelen jelennek meg.  Biztosítása a hozzájárulási adjon az alkalmazás azokat a kért Azure AD Graph API-nak a felhasználó engedélye. A hozzájárulási keretrendszerre további információkért lásd: [hozzájárulás keretében áttekintése](active-directory-integrating-applications.md).

## <a name="see-also"></a>Lásd még:
[Az Azure AD Graph API gyors üzembe helyezési útmutató](active-directory-graph-api-quickstart.md)

[Az Azure AD Graph REST dokumentációja](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

