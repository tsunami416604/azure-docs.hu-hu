---
title: Az Azure AD Graph API használata
description: Az Azure Active Directory (Azure AD) Graph API programozott hozzáférést biztosít az Azure AD-hez az OData REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashatnak, frissíthetnek és törölhetnek (CRUD) műveleteket a címtáradatokon és -objektumokon.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 9dc268a9-32e8-402c-a43f-02b183c295c5
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: ryanwi
ms.reviewer: sureshja
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 701aadcbfe03c82f6a4c341ef9698f8a1bf1a347
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476590"
---
# <a name="how-to-use-the-azure-ad-graph-api"></a>Útmutató: Az Azure AD Graph API használata

> [!IMPORTANT]
> Azt javasoljuk, hogy az Azure Active Directory (Azure AD) erőforrások eléréséhez az Azure Active Directory (Azure AD) erőforrások eléréséhez használja a [Microsoft Graph-ot](https://developer.microsoft.com/graph) az Azure Active Directory (Azure AD) erőforrások elérése helyett. A fejlesztési energiáinkat mostantól a Microsoft Graph-ra koncentráljuk, az Azure AD Graph API-hoz nem tervezünk további fejlesztéseket. Vannak nagyon korlátozott számú forgatókönyvek, amelyek az Azure AD Graph API továbbra is megfelelő lehet; További információt a [Microsoft Graph vagy az Azure AD Graph](https://developer.microsoft.com/office/blogs/microsoft-graph-or-azure-ad-graph/) blogbejegyzésében, valamint az [Azure AD Graph-alkalmazások áttelepítése a Microsoft Graphba című témakörben talál.](https://docs.microsoft.com/graph/migrate-azure-ad-graph-overview)

Az Azure AD Graph API programozott hozzáférést biztosít az Azure AD-hez az OData REST API-végpontokon keresztül. Az alkalmazások az Azure AD Graph API használatával hozhatnak létre, olvashatnak, frissíthetnek és törölhetnek (CRUD) műveleteket a címtáradatokon és -objektumokon. Az Azure AD Graph API használatával például új felhasználót hozhat létre, megtekintheti vagy frissítheti a felhasználó tulajdonságait, módosíthatja a felhasználó jelszavát, ellenőrizheti a csoporttagságot a szerepköralapú hozzáféréshez, letilthatja vagy törölheti a felhasználót. Az Azure AD Graph API-funkciókkal és alkalmazásforgatókönyvekkel kapcsolatos további információkért tekintse meg az [Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) és az Azure [AD Graph API előfeltételeit.](https://msdn.microsoft.com/library/hh974476.aspx) Az Azure AD Graph API csak munkahelyi vagy iskolai/szervezeti fiókokkal működik.

Ez a cikk az Azure AD Graph API-ra vonatkozik. A Microsoft Graph API-val kapcsolatos hasonló információkért [lásd: A Microsoft Graph API használata](https://developer.microsoft.com/graph/docs/concepts/use_the_api).

## <a name="how-to-construct-a-graph-api-url"></a>Graph API URL-címének létrehozása

A Graph API-ban a CRUD-műveletek et végrehajtani kívánt címtáradatok és -objektumok (más szóval erőforrások vagy entitások) eléréséhez használhatja az Open Data (OData) protokollon alapuló URL-címeket. A Graph API-ban használt URL-címek négy fő részből állnak: szolgáltatásgyökér, `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`bérlőazonosító, erőforráselérési út és lekérdezési karakterlánc beállításai: . Vegyünk egy példát `https://graph.windows.net/contoso.com/groups?api-version=1.6`a következő URL-cím: .

* **Szolgáltatás gyökér:** Az Azure AD Graph API-ban a szolgáltatás gyökér mindig. https://graph.windows.net
* **Bérlői azonosító**: Ez a szakasz lehet ellenőrzött (regisztrált) tartománynév, az előző példában contoso.com. Ez lehet egy bérlői objektum azonosítója vagy a "saját szervezet" vagy "én" alias. További információ: [Címzés entitások és műveletek az Azure AD Graph API-ban.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)
* **Erőforrás elérési útja**: Az URL-cím nek ez a szakasza azonosítja a kapcsolatba lépő erőforrást (felhasználók, csoportok, egy adott felhasználó vagy egy adott csoport stb.) A fenti példában a legfelső szintű "csoportok" az adott erőforráskészlet kezelésére. Megcímezhet egy adott entitást is, például "users/{objectId}" vagy "users/userPrincipalName".
* **Lekérdezési paraméterek**: A kérdőjel (?) elválasztja az erőforrás elérési út szakaszát a lekérdezési paraméterek szakasztól. Az "api-version" lekérdezési paraméter az Azure AD Graph API minden kéréséhez szükséges. Az Azure AD Graph API a következő OData-lekérdezési beállításokat is támogatja: **$filter,** **$orderby,** **$expand**, **$top**és **$format.** A következő lekérdezési beállítások jelenleg nem támogatottak: **$count**, **$inlinecount**és **$skip**. További információt a [Támogatott lekérdezések, szűrők és lapozási beállítások az Azure AD Graph API-ban című témakörben talál.](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options)

## <a name="graph-api-versions"></a>Graph API-verziók

A Graph API-kérelem verzióját az "api-version" lekérdezési paraméterben adhatja meg. Az 1.5-ös és újabb verziókhoz numerikus verzióértéket kell használni; api-version=1.6. A korábbi verziókhoz olyan dátumkarakterláncot használ, amely megfelel az YYYY-MM-DD formátumnak; például api-version=2013-11-08. Az előnézeti funkciókhoz használja a "beta" karakterláncot; például api-version=beta. A Graph API-verziók közötti különbségekről az [Azure AD Graph API verziószámozása](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning)című témakörben talál további információt.

## <a name="graph-api-metadata"></a>Graph API metaadatai

Az Azure AD Graph API metaadatfájljának visszaadására adja hozzá a "$metadata" szegmenst az URL-címbérlői azonosítója után: Például a következő URL-cím egy bemutató vállalat metaadatait adja vissza: `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. . Ezt az URL-címet a webböngésző címsorába írhatja be a metaadatok megtekintéséhez. A visszaadott CSDL-metaadat-dokumentum ismerteti az entitásokat és az összetett típusokat, azok tulajdonságait, valamint a kért Graph API-verzió által elérhetővé tett függvényeket és műveleteket. Az api-version paraméter elhagyása metaadatokat ad vissza a legújabb verzióhoz.

## <a name="common-queries"></a>Gyakori lekérdezések

[Az Azure AD Graph API gyakori lekérdezések](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) felsorolja a gyakori lekérdezések, amelyek az Azure AD Graph, beleértve a lekérdezéseket, amelyek segítségével a címtárban a legfelső szintű erőforrások eléréséhez, és lekérdezéseket a címtárban műveletek végrehajtásához.

Például `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` a címtár contoso.com vállalati adatait adja vissza.

Vagy `https://graph.windows.net/contoso.com/users?api-version=1.6` felsorolja az összes felhasználói objektumot a könyvtárban contoso.com.

## <a name="using-the-azure-ad-graph-explorer"></a>Az Azure AD Graph Explorer használata
Használhatja az Azure AD Graph Explorer az Azure AD Graph API-t a címtáradatok lekérdezése az alkalmazás létrehozása során.

A következő képernyőkép az a kimenet, amelyet látni szeretne, ha az Azure `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` AD Graph Explorert szeretné megnyitni, bejelentkezni, és beírni a bejelentkezett felhasználó könyvtárában lévő összes felhasználó megjelenítéséhez:

![Példa kimenetre az Azure AD Graph API Explorerben](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Az Azure AD Graph Explorer**betöltése: [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)Az eszköz betöltéséhez keresse meg a t. Kattintson **a Bejelentkezés** és bejelentkezés az Azure AD-fiók hitelesítő adataival az Azure AD Graph Explorer futtatásához a bérlőn. Ha az Azure AD Graph Explorert saját bérlője ellen futtatja, önnek vagy a rendszergazdának a bejelentkezés során beleegyezését kell adnia. Ha Office 365-előfizetéssel rendelkezik, automatikusan rendelkezik egy Azure AD-bérlővel. Az Office 365-be való bejelentkezéshez használt hitelesítő adatok valójában Azure AD-fiókok, és ezeket a hitelesítő adatokat használhatja az Azure AD Graph Explorer használatával.

**Lekérdezés futtatása**: Lekérdezés futtatásához írja be a lekérdezést a kérelem mezőbe, és kattintson a **GET** gombra, vagy kattintson az **enter billentyűre.** Az eredmények a válaszmezőben jelennek meg. Például `https://graph.windows.net/myorganization/groups?api-version=1.6` a bejelentkezett felhasználó könyvtárában lévő összes csoportobjektum ot listázza.

Vegye figyelembe az Azure AD Graph Explorer következő szolgáltatásait és korlátait:

* Automatikus kiegészítési képesség az erőforráskészleteken. A funkció megtekintéséhez kattintson a kérelem szövegmezőjére (ahol a vállalat URL-címe megjelenik). A legördülő listából kiválaszthat egy erőforráskészletet.
* A kérelem előzményei.
* Támogatja az aliasokat megszólító "én" és "saját szervezet" címet. Például visszaadhatja `https://graph.windows.net/me?api-version=1.6` a bejelentkezett felhasználó felhasználói objektumát, vagy `https://graph.windows.net/myorganization/users?api-version=1.6` visszaküldheti a bejelentkezett felhasználó könyvtárában lévő összes felhasználót.
* Támogatja a teljes CRUD-műveleteket `POST`a `GET` `PATCH` saját `DELETE`könyvtárával szemben a használatával, a és a .
* Válaszfejlécek szakasz. Ez a szakasz a lekérdezések futtatásakor felmerülő problémák elhárítására szolgál.
* JSON-megjelenítő a kibontási és összecsukási képességekkel rendelkező válaszhoz.
* Nem támogatja a miniatűr fénykép megjelenítését vagy feltöltését.

## <a name="using-fiddler-to-write-to-the-directory"></a>A Fiddler használata a könyvtárba való íráshoz

Ez a rövid útmutató alkalmazásában használhatja a Fiddler webhibakereső az Azure AD-címtár "írási" műveletek végrehajtásának. Például lekaphatja és feltöltheti egy felhasználó profilképét (ami az Azure AD Graph Explorer rel nem lehetséges). További információ és a Fiddler [https://www.telerik.com/fiddler](https://www.telerik.com/fiddler)telepítése a témakörben található.

Az alábbi példában a Fiddler Web Debugger használatával hozzon létre egy új biztonsági csoportot "MyTestGroup" az Azure AD könyvtárban.

**Hozzáférési jogkivonat beszerzése:** Az Azure AD Graph eléréséhez az ügyfeleknek először sikeresen hitelesíteniük kell magukat az Azure AD-ben. További információ: [Authentication scenarios for Azure AD.](authentication-scenarios.md)

**Lekérdezés összeállítása és futtatása**: Hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Fiddler Web Debugger alkalmazást, és váltson a **Zeneszerző** lapra.
2. Ha új biztonsági csoportot szeretne létrehozni, válassza a **Legördülő** menü Közzététel HTTP-módszerként parancsát. A csoportobjektumműveleteiről és engedélyeiről további információt a [Csoportosítás](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#group-entity) az [Azure AD Graph REST API-n](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)belüli hivatkozásban talál.
3. A **Post (Post)** mezőben írja be `https://graph.windows.net/{mytenantdomain}/groups?api-version=1.6`a következő kérelem URL-címét: .
   
   > [!NOTE]
   > {mytenantdomain} helyett a saját Azure AD-címtár tartománynevét kell helyettesítenie.

4. A Közvetlenül a Lehúzás könyvelése alatti mezőbe írja be a következő HTTP fejlécet:
   
    ```
   Host: graph.windows.net
   Authorization: Bearer <your access token>
   Content-Type: application/json
   ```
   
   > [!NOTE]
   > Helyettesítse &lt;a&gt; hozzáférési jogkivonatot az Azure AD-címtár hozzáférési jogkivonatával.

5. A **Kérelem törzsmezőbe** írja be a következő JSON-t:
   
    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
   ```
   
    A csoportok létrehozásáról a [Csoport létrehozása című](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup)témakörben talál további információt.

Az Azure AD-entitásokról és a Graph által elérhetővé tett típusokról, valamint a Graph-szal rajtuk végrehajtható műveletekről az [Azure AD Graph REST API-hivatkozáscímű témakörben](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)talál további információt.

## <a name="next-steps"></a>További lépések

* További információ az [Azure AD Graph API-ról](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
* További információ az [Azure AD Graph API-engedélyhatóköreiről](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)
