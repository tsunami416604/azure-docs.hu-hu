---
title: Az Azure Active Directory Graph API |} A Microsoft Docs
description: Egy áttekintése és a rövid útmutató az Azure AD Graph API, amely lehetővé teszi a programozott hozzáférést az Azure ad-hez REST API-végpontokon keresztül.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dkershaw, sureshja
ms.custom: aaddev
ms.openlocfilehash: 2c95e19fe47e23cda6d14fc047fbe6c30100d78e
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56097764"
---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory – Graph API

> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://developer.microsoft.com/graph/) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.

Ez a cikk az Azure AD Graph API-ra vonatkozik. Hasonló a Microsoft Graph API-val kapcsolatos információk: [a Microsoft Graph API-val](https://docs.microsoft.com/graph/use-the-api). 

Az Azure Active Directory Graph API programozás alapú hozzáférést biztosít az Azure ad-hez REST API-végpontokon keresztül. Alkalmazások a Azure AD Graph API segítségével hajthat végre létrehozása, olvasása, frissítése és törlése a directory-adatok és objektumok (CRUD) műveleteket. Például az Azure AD Graph API támogatja a következő gyakori műveletek a felhasználói objektum:

* Új felhasználó létrehozása a könyvtárban
* A felhasználó részletes tulajdonságait, például a csoportok beolvasása
* A felhasználó tulajdonságai, például a hely és a telefonszámot, vagy módosítania kell a jelszavát
* Szerepköralapú hozzáférés egy felhasználó csoporttagságát ellenőrzése
* Egy felhasználói fiókot, vagy a teljes törlése

Ezenfelül más objektumok, például a csoportok és alkalmazások hasonló műveleteket hajthat végre. Azure AD Graph API hívása egy könyvtárban, regisztrálni kell az alkalmazást az Azure ad-ben. Az alkalmazás is hozzáférést kell adni az Azure AD Graph API-hoz. Ez a hozzáférés általában felhasználói vagy rendszergazdai jóváhagyási folyamatot keresztül érhető el.

Az Azure Active Directory Graph API használatának megkezdéséhez tekintse meg a [Azure AD Graph API a rövid útmutató](active-directory-graph-api-quickstart.md), vagy megtekintheti a [interaktív Azure AD Graph API-referenciadokumentáció](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="features"></a>Szolgáltatások

Az Azure AD Graph API a következő szolgáltatásokat biztosítja:

* **REST API-végpontokon**: Az Azure AD Graph API egy olyan REST-alapú szolgáltatás standard HTTP-kérelmek használatával elért végpontokat áll. Az Azure AD Graph API támogatja a kérelmek és válaszok XML- vagy Javascript Object Notation (JSON) tartalomtípusokat. További információkért lásd: [Azure AD Graph REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* **Az Azure AD-hitelesítés**: Minden kérést az Azure AD Graph API-t egy JSON webes jogkivonat (JWT) a kérelem az engedélyezési fejléc hozzáfűzésével kell hitelesíteni. Ez a token igényelve egy kérést az Azure AD-jogkivonat végpontra és érvényes hitelesítő adatokkal. Az engedélyezési kód engedélyezése a Graph meghívásához jogkivonat-beszerzési folyamatot, vagy használhatja az OAuth 2.0 ügyfél-hitelesítési folyamata. További információ [az Azure AD OAuth 2.0-s](https://msdn.microsoft.com/library/azure/dn645545.aspx).
* **Szerepkör-alapú hitelesítést (RBAC)**: Biztonsági csoportok segítségével hajthatók végre RBAC az Azure AD Graph API-ban. Például, ha meg szeretné határozni, hogy egy felhasználó hozzáfér egy adott erőforráshoz, az alkalmazás meghívhatja a [ellenőrizze a csoport tagsága (tranzitív)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/functions-and-actions#checkMemberGroups) művelet, amely igaz vagy hamis értéket ad vissza.
* **Különbözeti lekérdezés**: Különbözeti lekérdezés lehetővé teszi a könyvtár között anélkül, hogy a gyakori lekérdezések az Azure AD Graph API-nak két időszakok nyomon követésére. Az ilyen típusú kérelem csak az előző különbözeti lekérdezés kérést és az aktuális kérelem között végzett módosításokat adja vissza. További információkért lásd: [Azure AD Graph API különbözeti lekérdezés](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query).
* **Címtárbővítmények**: Egyéni tulajdonságokat adhat címtárobjektum, anélkül, hogy egy külső adattár. Például ha az alkalmazás egy Skype-azonosító tulajdonságot igényel minden olyan felhasználóhoz, az új tulajdonság regisztrálhatja a címtárban, és minden user objektum használható lesz. További információkért lásd: [Azure AD Graph API directory sémakiterjesztései](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).
* **Engedélyek hatókörei által védett**: Az Azure AD Graph API engedélyhatókörök, amelyek lehetővé teszik az OAuth 2.0-val az Azure AD-adatokhoz való biztonságos hozzáférést tesz elérhetővé. Ügyfél típusú alkalmazások, beleértve a különböző támogatja:
  
  * felhasználói felületek, amelyek a bejelentkezett felhasználó, (delegált) delegált hozzáférést megadott hitelesítési adatok
  * szolgáltatás/démon az alkalmazásokat, amelyek a háttérben megtalálható-e bejelentkezett felhasználó nélkül működnek, és használja az alkalmazás által meghatározott szerepköralapú hozzáférés-vezérlés
    
    Mindkét delegált és az Alkalmazásengedélyek felel meg az Azure AD Graph API által elérhetővé tett jogosultság, és igényelhetnek keresztül regisztrációs engedélyeket Alkalmazásfunkciók az ügyfélalkalmazások által az [az Azure portal](https://portal.azure.com). [Az Azure AD Graph API engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes) arról nyújt tájékoztatást, mi az az ügyfélalkalmazás által használható.

## <a name="scenarios"></a>Forgatókönyvek

Azure AD Graph API lehetővé teszi, hogy az alkalmazás-forgatókönyvek. A következő forgatókönyvek esetében a leggyakoribb:

* **Üzletági (Egybérlős) alkalmazás**: Ebben a forgatókönyvben egy vállalati fejlesztői működik, az Office 365-előfizetéssel rendelkező szervezetek számára. A fejlesztő állítja össze egy webalkalmazást, amely kommunikál az Azure AD-feladatok végrehajtását, mint a felhasználói licenc hozzárendelése. Ez a feladat az Azure AD Graph API-nak hozzáférésre van szüksége, így a fejlesztő az egybérlős alkalmazást regisztrál az Azure ad-ben, és konfigurálja az olvasási és írási engedélyekkel az Azure AD Graph API-hoz. Az Azure AD Graph API hívása egy token beszerzéséhez saját hitelesítő adatait vagy a felhasználó bejelentkezési jelenleg használja majd az alkalmazás van konfigurálva.
* **(Több-Bérlős) szolgáltatás alkalmazás**: Ebben a forgatókönyvben független szoftverszállító (ISV) fejleszti a felhasználókezelési funkciókat biztosít a más szervezetek, amelyek használják az Azure ad-ben üzemeltetett több-bérlős webes alkalmazást. Ezek a funkciók hozzá kell férniük címtárobjektum, így az alkalmazás az Azure AD Graph API-t kell. A fejlesztő az alkalmazás regisztrálása az Azure ad-ben, konfigurálja, hogy a szükséges olvasási és írási engedélyekkel az Azure AD Graph API-hoz, és majd lehetővé teszi, hogy külső hozzáférés úgy, hogy más szervezetek használják az alkalmazást a könyvtárban adhatnak hozzájárulást. A más szervezetben egy felhasználó hitelesíti magát az alkalmazáshoz, első alkalommal, amikor azok a beleegyezés párbeszédpanelen, az alkalmazás által kért engedélyeket a jelennek meg. Jóváhagyás majd biztosít az alkalmazás megadása azokat, kért engedélyeket a felhasználó Azure AD Graph API-t. A hozzájárulási keretrendszer további információkért lásd: [a hozzájárulási keretrendszer áttekintése](consent-framework.md).

## <a name="next-steps"></a>További lépések

Az Azure Active Directory Graph API használatának megkezdéséhez lásd a következő témaköröket:

* [Az Azure AD Graph API – rövid útmutató](active-directory-graph-api-quickstart.md)
* [Az Azure AD Graph REST dokumentációja](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
