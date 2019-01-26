---
title: Az Azure AD Graph API használata
description: Az Azure Active Directory (Azure AD) Graph API programozás alapú hozzáférést biztosít az Azure AD-OData REST API-végpontokon keresztül. Alkalmazások a Azure AD Graph API segítségével hajthat végre létrehozása, olvasása, frissítése és törlése a directory-adatok és objektumok (CRUD) műveleteket.
services: active-directory
documentationcenter: n/a
author: CelesteDG
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: sureshja
ms.custom: aaddev
ms.openlocfilehash: da229af181418d84e45f9ade8a8e5af008074f79
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55080828"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Útmutató: Az Azure AD Graph API használata

Az Azure Active Directory (Azure AD) Graph API programozás alapú hozzáférést biztosít az Azure AD-OData REST API-végpontokon keresztül. Alkalmazások a Azure AD Graph API segítségével hajthat végre létrehozása, olvasása, frissítése és törlése a directory-adatok és objektumok (CRUD) műveleteket. Például használhatja az Azure AD Graph API az új felhasználó létrehozása, megtekintése vagy a felhasználó tulajdonságainak frissítése, felhasználó jelszavának módosítására, ellenőrizze a szerepkör alapú hozzáférés érdekében a csoporttagság letiltása vagy a felhasználó törlése. További információ az Azure AD Graph API-funkciók és alkalmazás-forgatókönyvek: [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) és [Azure AD Graph API Előfeltételek](https://msdn.microsoft.com/library/hh974476.aspx).

Ez a cikk az Azure AD Graph API-ra vonatkozik. Hasonló a Microsoft Graph API-val kapcsolatos információk: [a Microsoft Graph API-val](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://developer.microsoft.com/graph) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.

## <a name="how-to-construct-a-graph-api-url"></a>Hogyan hozható létre egy Graph API URL-címe

A Graph API-t directory adatok és objektumok (más szóval erőforrások vagy entitások), amelyre vonatkozóan szeretné CRUD-műveletek végrehajtása eléréséhez használható URL-címek alapján az Open Data (OData) protokollt. A Graph API-ban használt URL-címek négy fő részből állnak: a szolgáltatás legfelső szintű, a bérlőazonosító, az erőforrás elérési útja és a lekérdezési karakterlánc beállítások: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. A példában az alábbi URL-cím érvénybe: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Legfelső szintű szolgáltatás**: Az Azure AD Graph API-t, a szolgáltatás legfelső szintű mindig van https://graph.windows.net.
* **A bérlői azonosító**: Ez a szakasz lehet (regisztrált) ellenőrzött tartomány nevét, az előző példában contoso.com. Azt is lehet a bérlő objektum Azonosítóját vagy futrinka "nevű" vagy "me" alias. További információkért lásd: [Addressing entitásokat és az Azure AD Graph API-műveletek](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Erőforrás elérési útja**: Ebben a szakaszban egy URL-azonosítja az erőforrást kezelni (felhasználók, csoportok, egy adott felhasználó vagy egy adott csoport stb.) A fenti példában a legfelső szintű "csoportok", amely erőforrás-címre. Ha például egy adott entitás is kezelheti "felhasználók / {objectId}" vagy "felhasználók/userPrincipalName".
* **Lekérdezési paramétereket**: A kérdőjel (?) elkülöníti a lekérdezési paraméterek szakaszban az erőforrás elérésiút-szakaszával. Az Azure AD Graph API-ban minden kérelemhez szükséges az "api-verzió" lekérdezési paraméter. Az Azure AD Graph API-t is támogatja a következő OData-lekérdezés beállításai: **$filter**, **$orderby**, **$expand**, **$top**, és **$format**. A következő lekérdezési beállítások jelenleg nem támogatottak: **$count**, **$inlinecount**, és **$skip**. További információkért lásd: [támogatott lekérdezések, szűrők és lapozófájl-beállítások az Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>A Graph API-verziók

Ez a verzió a Graph API-kérelem az "api-verzió" lekérdezési paraméter adható meg. A 1.5-ös és újabb verziók esetén az verzió numerikus érték; API-version = 1.6-os. Régebbi verziók esetében a következő formátumban: éééé-hh-nn; betartó dátum karakterlánc használata például api-version = 2013-11-08. Az előzetes verziójú funkciók használja a következő karakterláncot: "béta"; például api-version = beta. További információ a Graph API-verziók közötti különbségeket: [Azure AD Graph API-k verziókezelése](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API-metaadatok

Vissza az Azure AD Graph API metaadatait tartalmazó fájl, vegye fel a "$metadata" szegmens után az URL-címet a példában a bérlőazonosító, a következő URL-címet adja vissza a metaadatokat a bemutató vállalat: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Megadhatja az URL-cím megtekintéséhez a metaadatok webböngésző címsorába. A visszaadott CSDL metaadat-dokumentum ismerteti az entitások és összetett típusok, azok tulajdonságait és a funkciók és a Graph API-t a kért verziója által elérhetővé tett műveletek. A legújabb verzióra a metaadatokat az api-version paraméter kihagyása adja vissza.

## <a name="common-queries"></a>Gyakori lekérdezések

[Az Azure AD Graph API gyakori lekérdezések](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) gyakori lekérdezések, amelyek használhatók az Azure AD Graph, többek között a címtárban lévő legfelső szintű erőforrások eléréséhez használható lekérdezések és műveletek végrehajtása a címtár-lekérdezések sorolja fel.

Ha például `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` értéket ad vissza a céges információk directory contoso.com.

Vagy `https://graph.windows.net/contoso.com/users?api-version=1.6` felsorolja az összes felhasználói objektum directory contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Az Azure AD Graph Explorer használatával
Az Azure AD Graph Explorer, az Azure AD Graph API segítségével a directory-adatok lekérdezése az alkalmazás elkészítése során.

Az alábbi képernyőképen a jelennének meg az Azure AD Graph Explorer keresse meg, jelentkezzen be, és adja meg a létrehozott kimeneti `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` a bejelentkezett felhasználó összes felhasználó megjelenítéséhez:

![Az Azure AD graph api-tallózó](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Az Azure AD Graph Explorer betöltése**: Betölti az eszközt, navigáljon a [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Kattintson a **bejelentkezési** , és jelentkezzen be az Azure AD Graph Explorer futtatásához a bérlő Azure ad-ben fiókja hitelesítő adataival. Ha az Azure AD Graph Explorer futtatja a saját bérlőn, Ön vagy a rendszergazdához kell jóváhagyást bejelentkezés során. Ha rendelkezik Office 365-előfizetéssel, automatikusan rendelkezik az Azure AD-bérlő. Hitelesítő adatokkal kell bejelentkezni az Office 365-höz, sőt, az Azure AD-felhasználók esetében, és használhatja ezeket a hitelesítő adatokat az Azure AD Graph Explorer.

**Lekérdezés futtatása**: A lekérdezés futtatásához a kérelem szövegmezőbe írja be a lekérdezést, és kattintson a **LEKÉRÉSE** , vagy kattintson a **adja meg** kulcs. Az eredmények jelennek meg a választ a boxban. Ha például `https://graph.windows.net/myorganization/groups?api-version=1.6` a bejelentkezett felhasználó címtárban lévő összes csoport objektumokat sorolja fel.

Vegye figyelembe a következő szolgáltatásokat és az Azure AD Graph Explorer vonatkozó korlátozások:

* Az automatikus kiegészítés funkció az erőforráson állítja be. A funkció működésének megtekintéséhez kattintson a kérelem szövegmezőben (ahol a vállalat URL-címe jelenik meg). Kiválaszthatja egy erőforrást a legördülő listából.
* Kérelem előzményei.
* Támogatja az "me" és "futrinka nevű" címzés aliasok. Használhatja például `https://graph.windows.net/me?api-version=1.6` vissza a felhasználói objektum, a bejelentkezett felhasználó vagy `https://graph.windows.net/myorganization/users?api-version=1.6` minden felhasználót visszaadjon a bejelentkezett felhasználó könyvtárban.
* Támogatja a teljes CRUD-MŰVELETEKKEL kapcsolatos művelet-végrehajtási saját könyvtár használatával `POST`, `GET`, `PATCH` és `DELETE`.
* A válasz a fejlécek szakaszban. Ez a szakasz a lekérdezések futtatásakor előforduló problémák hibaelhárításához használható.
* Egy JSON megjelenítőből a válasz-kibontás és összecsukása képességeket.
* Megjelenítés vagy egy miniatűr fényképre feltöltése nem támogatott.

## <a name="using-fiddler-to-write-to-the-directory"></a>A könyvtárba írást a Fiddler segítségével

A rövid útmutató az alkalmazásában használhatja a Fiddler webes hibakereső eljárás szerint "write" az Azure AD-címtárral végzett műveletek végrehajtása. Például lekérése, és töltse fel a felhasználó profilfényképének (ez nem lehetséges az Azure AD Graph Explorer). További információk, valamint a Fiddler telepítéséhez lásd: [ https://www.telerik.com/fiddler ](https://www.telerik.com/fiddler).

Az alábbi példában a Fiddler webes hibakereső használhatja "MyTestGroup" új biztonsági csoport létrehozása az Azure AD-címtárban.

**Hozzáférési jogkivonat beszerzésére**: Hozzáférhet az Azure AD Graph, az ügyfelek sikeresen hitelesíteni először az Azure AD szükséges. További információkért lásd: [hitelesítési forgatókönyvek az Azure ad-ben](authentication-scenarios.md).

**Állítsa össze és a lekérdezések futtatásához**: Hajtsa végre a következő lépéseket:

1. Nyissa meg a Fiddler webes hibakereső, és váltson át a **Composer** fülre.
2. Mivel a szeretne létrehozni egy új biztonsági csoportot, válassza **Post** HTTP módszert a legördülő menüből. Objektumra műveletek és engedélyeivel kapcsolatos további információkért lásd: [csoport](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) belül a [az Azure AD Graph – REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. A mező mellett található **Post**, írja be a következő kérés URL-cím: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > A tartomány nevét a saját Azure AD-címtár kell helyettesítse be {mytenantdomain}.

4. Az alábbi Post legördülő mezőben írja be a következő HTTP-fejléc:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Helyettesítse be a &lt;a hozzáférési jogkivonat&gt; a hozzáférési jogkivonattal az Azure AD-címtárhoz.

5. Az a **kérelem törzse** mezőbe írja be a következő JSON-ra:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Csoportok létrehozásával kapcsolatos további információkért lásd: [csoport létrehozása](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

További információ az Azure AD-entitások és a gráf által feltárt típusok és Graph azokon végezhető műveleteket kapcsolatos információkat lásd: [az Azure AD Graph – REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet a [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* Tudjon meg többet [az Azure AD Graph API-Engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
