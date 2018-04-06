---
title: Az Azure AD Graph API a gyors üzembe helyezés |} Microsoft Docs
description: Az Azure Active Directory Graph API-val programozott hozzáférést biztosít az Azure AD-OData REST API-végpontokon keresztül. Alkalmazások az Azure AD Graph API használható létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek directory adatok és objektumok.
services: active-directory
documentationcenter: n/a
author: mtillman
manager: mtillman
editor: ''
tags: ''
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: d195d808e07b872c11379f13b6e89794da39f70e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="quickstart-for-the-azure-ad-graph-api"></a>Az Azure AD Graph API a gyors üzembe helyezés
Az Azure Active Directory (AD) Graph API-val programozott hozzáférést biztosít az Azure AD-OData REST API-végpontokon keresztül. Alkalmazások az Azure AD Graph API használható létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek directory adatok és objektumok. Például használhatja az Azure AD Graph API új felhasználó létrehozása, megtekintése vagy felhasználó tulajdonságai, jelszó módosítása, ellenőrizze a szerepköralapú hozzáférési csoportok tagságát letiltja, vagy törölje a felhasználót. Azure AD Graph API-funkciókat és az alkalmazás-forgatókönyvek további információkért lásd: [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) és [Azure AD Graph API Előfeltételek](https://msdn.microsoft.com/library/hh974476.aspx). 

> [!IMPORTANT]
> Az Azure AD Graph API helyett ajánljuk a [Microsoft Graph](https://developer.microsoft.com/graph) használatát az Azure Active Directory erőforrásainak eléréséhez. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Nagyon korlátozott azon forgatókönyvek száma, amelyeknél az Azure AD Graph API használata még elegendő. További információért tekintse meg a [Microsoft Graph vagy Azure AD Graph](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph) blogbejegyzést az Office fejlesztői központjában.
> 
> 

## <a name="how-to-construct-a-graph-api-url"></a>Hogyan hozható létre egy grafikonon API URL-címe
Graph API-ban hozzáférhet a címtáradatok és objektumokat (más szóval erőforrásokat és entitások) amely CRUD műveletek végrehajtásához használhatja az Open Data (OData) protokollon alapuló URL-címeket. A Graph API-ban használt URL-címeket négy fő részből állnak: szolgáltatás a legfelső szintű, a bérlő azonosítója, az erőforrás elérési útja és a lekérdezési karakterlánc lehetőségek: `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. A következő URL-címének példánál: `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

* **Szolgáltatás legfelső szintű**: az Azure AD Graph API-val, a szolgáltatás legfelső szintű mindig van https://graph.windows.net.
* **A bérlői azonosító**: Ez a szakasz lehet (regisztrált) ellenőrzött tartomány nevét, az előző példában contoso.com. Azt is egy bérlő objektum azonosítója vagy futrinka "nevű" vagy "me" alias. További információkért lásd: [címzési entitásokat és az Azure AD Graph API műveletek](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview).
* **Erőforrás elérési útja**: Ez a szakasz egy URL-cím azonosítja az erőforrás kezelni (felhasználók, csoportok, egy adott felhasználó vagy egy adott csoport stb.) A fenti példában, akkor a legfelső szintű "csoportok" erőforrás beállított címre. Is megoldható, egy adott entitás, például "felhasználók / {objectId}" vagy "felhasználók/userPrincipalName tulajdonsághoz".
* **Lekérdezési paramétert**: A kérdőjel (?) elválasztja a lekérdezési paraméterek szakaszban az erőforrás elérésiút-szakasszal. Minden Azure AD Graph API-kérésnél a "api-verzió" lekérdezési paraméter megadása kötelező. Az Azure AD Graph API-t is támogatja a következő OData-lekérdezés beállításai: **$filter**, **$orderby**, **$expand argumentum**, **$top**, és **$format**. A következő lekérdezési lehetőségek jelenleg nem támogatottak: **$count**, **$inlinecount**, és **$skip**. További információkért lásd: [támogatott lekérdezések, szűrők és beállítások lapozás Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Graph API-verziók
A Graph API-kérelem verzióját a "api-verzió" lekérdezési paraméter adható meg. Az 1.5-ös vagy újabb verzió az numerikus verzió érték; az API-version 1.6-os =. Korábbi verzióihoz dátum-karakterláncnak a következő formátumban: éééé-hh-nn; megfelelő használata például az api-version = 2013-11-08. Az előzetes verziójú funkciók használja a következő karakterláncot: "béta"; például az api-version = béta. A Graph API-verziók közötti különbségekről további információkért lásd: [Azure AD Graph API Versioning](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Graph API-metaadatok
Térjen vissza az Azure AD Graph API metaadatfájl, vegye fel a "$metadata" szegmens után az URL-címe például a bérlő-azonosító, a következő URL-címet adja vissza a metaadatokat egy bemutató vállalati: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Megadhatja az URL-cím megtekintéséhez a metaadatok webböngésző címsorába. A visszaadott CSDL metaadat-dokumentum ismerteti az entitásokat és komplex típusok, azok tulajdonságait, és a funkciók és műveletek jelennek meg, ha a kért Graph API verziója. A legújabb verziót a metaadatokat az api-version paraméter kihagyása adja vissza.

## <a name="common-queries"></a>Általános lekérdezések
[Az Azure AD Graph API általános lekérdezések](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) együtt az Azure AD Graph, beleértve, hogy a címtárban található legfelső szintű erőforrások eléréséhez használható lekérdezéseket és műveleteinek elvégzéséhez a címtár lekérdezések közös lekérdezések sorolja fel.

Például `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` értéket ad vissza a vállalati címtár contoso.com adatait.

Vagy `https://graph.windows.net/contoso.com/users?api-version=1.6` felsorolja az összes felhasználói objektum directory contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Az Azure AD Graph Explorerrel
Az Azure AD Graph Explorer, az Azure AD Graph API segítségével a directory adatait kéri az alkalmazás létrehozása.

Az alábbi képernyőfelvételen mutatunk, ha volt az Azure AD Graph Explorer keresse meg, jelentkezzen be, és adja meg a kimeneti `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` a felhasználók megjelennek a bejelentkezett felhasználó:

![Az Azure AD graph api explorer](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Az Azure AD Graph Explorer betölteni**: betölteni az eszközt, navigáljon a [ https://graphexplorer.azurewebsites.net/ ](https://graphexplorer.azurewebsites.net/). Kattintson a **bejelentkezési** , és jelentkezzen be az Azure AD Graph Explorer futtatni a bérlője Azure AD fiók hitelesítő adatait. Azure AD Graph Explorer futtatni saját bérlőt, ha Ön vagy a rendszergazda kell hozzájárulás bejelentkezés során. Ha olyan Office 365-előfizetéssel rendelkezik, automatikusan rendelkezik az Azure AD-bérlő. A hitelesítő adatok használatával jelentkezzen be Office 365, valójában az Azure AD-fiókok, és az Azure AD Graph Explorer használhatja ezeket a hitelesítő adatokat.

**Lekérdezés futtatása**: futtatni a lekérdezést, a kérelem szövegmezőbe írja be a lekérdezést, és kattintson a **beolvasása** , vagy kattintson a **adja meg** kulcs. Az eredmények jelennek meg a válasz mezőbe. Például `https://graph.windows.net/myorganization/groups?api-version=1.6` a bejelentkezett felhasználó az összes csoport objektumok listája.

Vegye figyelembe az alábbi szolgáltatások és az Azure AD Graph Explorer korlátozásai:

* Erőforrás automatikus kiegészítési funkció beállítása. Ezt a funkciót látja, kattintson a kérelem szövegmezőben (Ha a vállalat URL-címe akkor jelenik meg). Kiválaszthatja, hogy egy erőforrás, a legördülő listából.
* Kérelem előzményei.
* Támogatja a "me" és "futrinka nevű" címzési aliasok. Használhat például `https://graph.windows.net/me?api-version=1.6` vissza a user objektum a bejelentkezett felhasználó vagy `https://graph.windows.net/myorganization/users?api-version=1.6` a bejelentkezett felhasználó az összes felhasználó kinyeréséhez.
* Támogatja a saját directory használatának mellőzését teljes CRUD-műveleteket `POST`, `GET`, `PATCH` és `DELETE`.
* A válasz fejlécek szakasz. Ez a szakasz segítségével lekérdezések futtatásakor előforduló problémák elhárítása érdekében.
* Bontsa ki és összecsukása képességekkel a válasz egy JSON megjelenítőből.
* Megjelenítés vagy a miniatűr fényképre feltöltése nem támogatott.

## <a name="using-fiddler-to-write-to-the-directory"></a>Számára a könyvtárba írást Fiddler segítségével
A gyors üzembe helyezési útmutató alkalmazásában használhatja a Fiddler webes hibakereső gyakorlat "write" az Azure AD-címtár műveleteket végez. Például lekérni, és töltse fel a felhasználó profilképet (ez nem lehetséges az Azure AD Graph Explorer). További információt, és a Fiddler telepítéséhez lásd: [ http://www.telerik.com/fiddler ](http://www.telerik.com/fiddler).

Az alábbi példában segítségével Fiddler webes Debugger létrehoz egy új biztonsági csoportot "MyTestGroup" az az Azure AD-címtár.

**Szerezze be a hozzáférési token**: Azure AD Graph szeretne használni, az ügyfelek sikeresen hitelesített először az Azure AD szükséges. További információkért lásd: [hitelesítési forgatókönyvek az Azure AD](active-directory-authentication-scenarios.md).

**Írása, és futtassa a lekérdezést**: hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Fiddler webes hibakereső, és váltson a **szerkesztő** fülre.
2. Mivel a kívánt hozzon létre egy új biztonsági csoportot, válassza **Post** HTTP módszert a legördülő menüből. Objektum műveletek és engedélyekkel kapcsolatos további információkért lásd: [csoport](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) belül a [az Azure AD Graph REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. A mező melletti **Post**, írja be a következő kérelem URL-címe: `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`.
   
   > [!NOTE]
   > {Mytenantdomain} kell helyettesítse a saját Azure AD-címtár a tartomány nevét.
   > 
   > 
4. A közvetlenül alatt Post legördülő mezőben írja be a következő HTTP-fejléc:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Helyettesítő a &lt;a hozzáférési token&gt; az Azure AD-címtárát hozzáférési jogkivonatot.
   > 
   > 
5. Az a **Request body** mezőbe írja be a következő JSON:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    Csoportok létrehozásával kapcsolatos további információkért lásd: [csoport létrehozása](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

További információk az Azure AD entitásokat és a grafikon által típusok és végrehajtható rajtuk Graph műveleteire vonatkozó adatokat, lásd: [az Azure AD Graph REST API-referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>További lépések
* További információ a [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* További információ [az Azure AD Graph API-Engedélyhatókörök](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

