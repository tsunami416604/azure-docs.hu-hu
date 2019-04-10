---
title: SharePoint-fájlok – QnA Maker
titleSuffix: Azure Cognitive Services
description: Védett Sharepoint-adatforrások hozzáadása a Tudásbázis feldúsítani a Tudásbázis kérdésekkel és válaszokkal, előfordulhat, hogy az Active Directoryban kell védeni.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: 671d76b6c0a5a2cdac5797668fb0e5651b5823e0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281743"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>A Tudásbázis egy védett Sharepoint-adatforrás hozzáadása

Védett Sharepoint-adatforrások hozzáadása a Tudásbázis feldúsítani a Tudásbázis kérdésekkel és válaszokkal, előfordulhat, hogy az Active Directoryban kell védeni. 

A Tudásbázis, mint a QnA Maker manager védett Sharepoint-dokumentum hozzáadásakor kérje meg az Active Directory számára a QnA Maker. Miután ezt az engedélyt kap az Active Directory managerből QnA Maker hozzáférés a Sharepointhoz, nem kell újra kell megadni. Minden ezt követő dokumentum Hozzáadás a Tudásbázis következő, nem kell engedélyezési, amennyiben az ugyanazon a Sharepoint-erőforrás található. 

Ha a QnA Maker Tudásbázis-kezelő nem az Active Directory-kezelőt, szüksége lesz az Active Directory-kezelővel, ez a folyamat befejezéséhez kommunikációhoz.

## <a name="add-supported-file-types-to-knowledge-base"></a>Támogatott fájltípusok Tudásbázis hozzáadása

Adhat hozzá az összes QnA Maker által támogatott [fájltípusok](../Concepts/data-sources-supported.md) a Sharepoint-kiszolgáló a tudásbázist. Előfordulhat, hogy biztosítania [engedélyek](#permissions) Ha a fájl erőforrás védett.

1. A Sharepoint server, válassza ki a Fájl menü három pontot ábrázoló `...`.
1. A fájl URL-Címének másolása.

    ![A fájl három pont menü, majd másolja az URL-címet kapni a Sharepoint-fájl URL-címe.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. A QnA Maker Portal a a **beállítások** lapon [adja hozzá az URL-cím](edit-knowledge-base.md#add-datasource) a Tudásbázisban. 

## <a name="permissions"></a>Engedélyek

Engedélyek megadása történik, ha a Sharepoint-kiszolgáló egy védett fájlt adnak hozzá egy Tudásbázis. Attól függően, hogyan van beállítva a Sharepoint fel és az engedélyeket ehhez szükség volt a fájl hozzáadása a személy:

* Nincsenek további lépések szükségesek: a fájl hozzáadása a személy rendelkezik a szükséges összes engedélyt.
* mindkét lépést [Tudásbázis-kezelő](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) és [Active Directory kezelő](#active-directory-manager-grant-file-read-access-to-qna-maker).

Tekintse meg az alábbi lépéseket. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Tudásbázis-kezelő: a QnA Maker portál Sharepoint-adatforrás hozzáadása

Ha a **QnA Maker manager** ad hozzá egy védett Sharepoint-dokumentum Tudásbázis, a Tudásbázis-kezelő kezdeményezi, amely az Active Directory kezelő el kell végeznie engedélyt kér.

A kérelem egy előugró ablak, Active Directory-fiókkal történő hitelesítést kezdődik. 

![Felhasználói fiók hitelesítéséhez](../media/add-sharepoint-datasources/authenticate-user-account.png)

A QnA Maker kezelő kiválasztja a fiókot, ha az Active Directory-rendszergazda kap egy üzenetet arról, hogy ő kell hozzáférnie a QnA Maker app (nem a QnA Maker manager) a Sharepoint-erőforrás. Az Active Directory kezelő kell ehhez a minden Sharepoint-erőforrás, de nem minden dokumentum az adott erőforráshoz. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Az Active directory kezelő: fájl olvasási jogot a QnA Maker

Az Active Directory-kezelő (nem a QnA Maker manager) van szüksége a hozzáférési jogot a Sharepoint-erőforrás eléréséhez kiválasztásával a QnA Maker [ezt a hivatkozást](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) engedélyezéséhez a QnA Maker Portal Sharepoint nagyvállalati alkalmazás fájl olvasása engedélyek. 

![Az Azure Active Directory manager engedélyt ad a interaktív módon](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központban való hozzáférés engedélyezése

1. Az Active Directory-kezelő jelentkezik be az Azure Portalon, és megnyílik  **[vállalati alkalmazások](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**. 

1. Keresse meg `QnAMakerPortalSharepoint` válassza ki a QnA Maker alkalmazást. 

    [![SVállalati alkalmazások listájában QnAMakerPortalSharepoint számára eresés](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. A **biztonsági**, lépjen a **engedélyek**. Válassza ki **biztosítson rendszergazdai jóváhagyás szervezet**. 

    [![Saz Active Directory-rendszergazda dönthet a hitelesített felhasználó](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Válassza ki a jogosult engedélyeket az Active Directory bejelentkezési fiókot. 


  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) or [updating a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A tudásbázist másokkal közös használatához](collaborate-knowledge-base.md)