---
title: Az Azure Active Directory Graph API |} Microsoft Docs
description: "Egy áttekintése és a gyors üzembe helyezési útmutatója a Graph API, amely lehetővé teszi a programozott hozzáférést az Azure AD-REST API-végpontokon keresztül."
services: active-directory
documentationcenter: 
author: viv-liu
manager: mbaldwin
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: viviali
ms.custom: aaddev
ms.openlocfilehash: 79bb96171c00fa62f24113800c3824856febe728
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API
> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://graph.microsoft.io/) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.
> 
> 

Az Azure Active Directory Graph API-val programozott hozzáférést biztosít az Azure AD-REST API-végpontokon keresztül. Alkalmazások végrehajtásához használhatja a Graph API létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek directory adatok és objektumok. A Graph API például olyan felhasználói objektum a következő gyakori műveleteket támogatja:

* Új felhasználó létrehozása a könyvtárban található
* A felhasználó részletes tulajdonságait, például saját csoport
* A felhasználó tulajdonságai, például a hely és a telefonszámot, vagy módosítania kell a jelszavát
* Ellenőrizze a felhasználói csoport tagságát a szerepköralapú hozzáférés
* Egy felhasználói fiókot, vagy a teljes törlése

Felhasználói objektumok mellett más objektumok, például a csoportok és alkalmazások hasonló műveleteket hajthat végre. Egy könyvtárat a Graph API-hívásokat kezdeményezzen, az alkalmazást regisztrálni kell az Azure AD, és úgy, hogy engedélyezi a hozzáférést a könyvtárhoz. Ez általában egy felhasználó vagy rendszergazda hozzájárulási folyamat keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg a [Graph API gyors üzembe helyezési útmutató](active-directory-graph-api-quickstart.md), vagy megtekintheti a [interaktív Graph API-referenciadokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Szolgáltatások
A Graph API a következő szolgáltatásokat biztosítja:

* **REST API-végpontok**: A Graph API egy olyan szabványos HTTP-kérelmek használatával elért végpontokat áll RESTful szolgáltatás. A Graph API támogatja XML- vagy Javascript Object Notation (JSON) tartalomtípusok kérelmeit és válaszait. További információkért lásd: [az Azure AD Graph REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Az Azure AD hitelesítési**: minden kérelemnél a Graph API-val egy JSON webes jogkivonat (JWT) található a kérés hitelesítési fejlécéhez hozzáfűzésével hitelesíteni kell. Ez a token szerzi a kérést továbbítja az Azure AD-jogkivonat végpontjához, és adja meg érvényes hitelesítő adatokat. Az engedélyezési kód engedélyezése szerezni a jogkivonatot, amellyel meghívhatja a grafikon folyamata, vagy használhatja az OAuth 2.0 ügyfél hitelesítő adatok folyamata. További információ [OAuth 2.0, az Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Szerepkör alapú engedélyezési (RBAC)**: biztonsági csoportok segítségével hajtsa végre az RBAC a Graph API-val. Például, ha meg szeretné határozni, hogy egy felhasználó egy adott erőforráshoz való hozzáférés rendelkezik, az alkalmazás meghívhatja a [csoporttagság ellenőrzése (tranzitív)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) művelet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés**: Ha a két időszak szerint anélkül, hogy a Graph API gyakori lekérdezések végrehajtásához közötti könyvtár változásokat ellenőrizni kívánja, akkor is használhatja a különbözeti kérést. Ilyen típusú kérelmet csak a korábbi különbözeti lekérdezés kérelem és a jelenlegi kérelem között végrehajtott módosítások adja vissza. További információkért lásd: [az Azure AD Graph API különbözeti lekérdezés](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Címtárbővítmények**: olvasására vagy írására, directory-objektumok egyedi tulajdonságok igénylő alkalmazást fejleszt, ha regisztrálja, és a Graph API-jával bővítmény értékeket használja. Ha az alkalmazás egy Skype-azonosító tulajdonságot minden olyan felhasználóhoz, például a címtárban regisztrálhatja az új tulajdonságot, és a minden felhasználói objektum elérhető lesz. További információkért lásd: [az Azure AD Graph API Directory-séma bővítményei](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Védi-engedélyhatókörök**: AAD Graph API AAD adataihoz való biztonságos/átadni kívánt hozzájárult e hozzáférés engedélyezése, amely támogatja az ügyfél típusú alkalmazás, beleértve a különböző engedélyhatókörök mutatja:
  
  * egy felhasználói felületet, amely adta rendelkező delegált hozzáférés engedélyezési adatokat (delegált) bejelentkezett felhasználó
  * használó alkalmazás-szerepkörön alapuló hozzáférés-vezérlő megadásához például szolgáltatás/démon ügyfelek (app-szerepkörök)
    
    Mindkét meghatalmazott, és alkalmazás szerepkör engedélyhatókörök határoz meg a Graph API által elérhetővé tett jogosultság és alkalmazás-regisztrációs engedélyek ügyfélalkalmazások kérhető [szolgáltatások az Azure portálon](https://portal.azure.com). Az ügyfelek ellenőrizhetik a engedélyhatókörök nyújtott számukra a hatókör ("szolgáltatáskapcsolódási pont") jogcím delegált jogosultságokkal sikeresen telepítették hozzáférési jogkivonatot kapott vizsgálatával, és a szerepkörök ("-szerepkörök) olvassa be az alkalmazás szerepköri jogosultságok. További információ [az Azure AD Graph API-Engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).

## <a name="scenarios"></a>Forgatókönyvek
A Graph API lehetővé teszi, hogy az alkalmazás-forgatókönyvek. A következő forgatókönyvek esetében a leggyakoribb:

* **Üzletági (Egybérlős) alkalmazás**: Ebben a forgatókönyvben egy vállalati fejlesztői működik egy olyan szervezet, amely olyan Office 365-előfizetéssel rendelkezik. A fejlesztői fejleszt, amely együttműködik az Azure ad-val feladatok elvégzéséhez webalkalmazás ilyen rendel egy licencet a felhasználó. Ez a feladat kell elérnie a Graph API-val, így az egyetlen alkalmazást az Azure AD bérlői, és konfigurálja a fejlesztői regiszterekben olvasási és írási engedéllyel a Graph API. Ezt követően az alkalmazás a saját hitelesítő adatait, vagy azokat, a bejelentkezés jelenleg felhasználó használja a jogkivonatot a Graph API hívása van konfigurálva.
* **(Több-Bérlős) szolgáltatás alkalmazás**: Ebben a forgatókönyvben egy független szoftverszállító (ISV) által fejlesztett üzemeltetett több-bérlős webalkalmazás, amely más Azure AD használó szervezetek felhasználói felügyeleti funkciókat biztosít. Ezek a funkciók címtárobjektumok hozzáférést igényelnek, és így az alkalmazás a Graph API hívása. A fejlesztői regisztrálja az alkalmazást az Azure ad-ben, olvasási és írási engedéllyel a Graph API-val konfigurálja és majd lehetővé teszi, hogy külső hozzáférés úgy, hogy más szervezetek is hozzájárul a címtár alkalmazásával. Amikor egy felhasználó egy másik szervezet hitelesíti magát az alkalmazás első indításakor, azokat az engedélyeket, az alkalmazás egy hozzájárulási párbeszédpanelen jelennek meg.  Biztosítása a hozzájárulási adjon az alkalmazás azokat a kért engedélyeket a Graph API-nak a felhasználó. A hozzájárulási keretrendszerre további információkért lásd: [hozzájárulás keretében áttekintése](active-directory-integrating-applications.md).

## <a name="see-also"></a>Lásd még:
[Az Azure AD Graph API gyors üzembe helyezési útmutató](active-directory-graph-api-quickstart.md)

[AD Graph REST-dokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Az Azure Active Directory fejlesztői útmutatója](active-directory-developers-guide.md)

