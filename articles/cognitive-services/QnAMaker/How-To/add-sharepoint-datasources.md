---
title: SharePoint-fájlok – QnA Készítő
description: Biztonságos SharePoint-adatforrások hozzáadása a tudásbázishoz, hogy a tudásbázist az Active Directoryval biztonságos kérdésekkel és válaszokkal gazdagíthassa.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294881"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Biztonságos SharePoint-adatforrás hozzáadása a tudásbázishoz

Biztonságos felhőalapú SharePoint-adatforrások hozzáadása a tudásbázishoz, hogy a tudásbázist az Active Directoryval biztonságos kérdésekkel és válaszokkal gazdagíthassa.

Ha biztonságos SharePoint-dokumentumot ad hozzá a tudásbázishoz, a QnA Maker kezelőjeként az Active Directory engedélyét kell kérnie a QnA Maker hez. Miután az Active Directory-kezelő engedélyt adott a QnA Makernek a SharePoint eléréséhez, nem kell újra megadni. A tudásbázis minden további dokumentumkiegészítése nem igényel engedélyezést, ha ugyanabban a SharePoint-erőforrásban található.

Ha a QnA Maker tudásbázis-kezelője nem az Active Directory-kezelő, a folyamat befejezéséhez kommunikálnia kell az Active Directory kezelőjével.

## <a name="prerequisites"></a>Előfeltételek

* Felhőalapú SharePoint – A QnA Maker a Microsoft Graph segítségével használja az engedélyeket. Ha a SharePoint a helyszínen található, nem tud kivonatolni a SharePointból, mert a Microsoft Graph nem tudja meghatározni az engedélyeket.
* URL formátum - A QnA Maker csak a megosztásra létrehozott és formátumú SharePoint-URL-eket támogatja`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Támogatott fájltípusok hozzáadása a tudásbázishoz

A SharePoint-webhelyről az összes QnA Maker által támogatott [fájltípust](../Concepts/content-types.md) hozzáadhatja a tudásbázishoz. Előfordulhat, hogy [engedélyeket kell adnia,](#permissions) ha a fájlerőforrás biztonságos.

1. A SharePoint-webhelyet kiszolgáló tárban válassza ki a `...`fájl három pontra eső menüjét.
1. Másolja a fájl URL-címét.

   ![A SharePoint-fájl URL-címének bejelölésével jelölje ki a fájl három pontra eső menüjét, majd másolja az URL-címet.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. A QnA Maker portál **Beállítások** lapján [adja hozzá az URL-címet](manage-knowledge-bases.md#edit-knowledge-base) a tudásbázishoz.

### <a name="images-with-sharepoint-files"></a>Képek SharePoint-fájlokkal

Ha a fájlok képeket is tartalmaznak, azok nem lesznek kibontva. Hozzáadhatja a képet a QnA Maker portálról, miután a fájlt QnA párokba bontotta.

Adja hozzá a képet a következő jelszintaxissal:

```markdown
![Explanation or description of image](URL of public image)
```

A szögletes zárójelben `[]`lévő szöveg , magyarázza a képet. A zárójelben `()`lévő URL-cím a képre mutató közvetlen hivatkozás.

Amikor teszteli a QnA-párt az interaktív tesztpanelen, a QnA Maker portálon, a kép jelenik meg a markdown szöveg helyett. Ez érvényesíti a lemezképet nyilvánosan lehívható az ügyfél-alkalmazás.

## <a name="permissions"></a>Engedélyek

Az engedélyek megadása akkor történik, ha egy SharePoint-kiszolgálóról származó védett fájlt adnak hozzá egy tudásbázishoz. A SharePoint beállításától és a fájlt hozzáadó személy engedélyeitől függően ez a következőket teheti szükségessé:

* nincs további lépés - a fájlt hozzáadó személy rendelkezik az összes szükséges engedéllyel.
* [tudásbáziskezelő](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) és az [Active Directory-kezelő lépéseit.](#active-directory-manager-grant-file-read-access-to-qna-maker)

Lásd az alábbi lépéseket.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Tudásbázis-kezelő: SharePoint-adatforrás hozzáadása a QnA Maker portálon

Amikor a **QnA Maker-kezelő** biztonságos SharePoint-dokumentumot ad hozzá egy tudásbázishoz, a tudásbázis-kezelő engedélyt kér, amelyet az Active Directory-kezelőnek el kell végeznie.

A kérelem egy előugró ablakkal kezdődik, amely hitelesíti magát egy Active Directory-fiókban.

![Felhasználói fiók hitelesítése](../media/add-sharepoint-datasources/authenticate-user-account.png)

Miután a QnA Maker-kezelő kiválasztotta a fiókot, az Azure Active Directory rendszergazdája értesítést kap arról, hogy engedélyeznie kell a QnA Maker alkalmazás (nem a QnA Maker-kezelő) hozzáférést a SharePoint-erőforráshoz. Az Azure Active Directory-kezelő nek ezt kell tennie minden SharePoint-erőforrásesetében, de nem az adott erőforrás összes dokumentuma esetében.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory manager: fájlolvasási hozzáférés megadása a QnA Maker számára

Az Active Directory-kezelőnek (nem a QnA Maker-kezelőnek) hozzáférést kell biztosítania a QnA Maker számára a SharePoint-erőforrás eléréséhez, ha ezt a [hivatkozást](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) választja, és engedélyezi a QnA Maker Portal SharePoint vállalati alkalmazás fájlolvasási engedélyekkel való engedélyezését.

![Az Azure Active Directory-kezelő interaktív engedélyt ad](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

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
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Hozzáférés megadása az Azure Active Directory felügyeleti központból

1. Az Active Directory-kezelő bejelentkezik az Azure Portalra, és megnyitja **[a vállalati alkalmazásokat.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**

1. Keresse `QnAMakerPortalSharePoint` meg a QnA Maker alkalmazást.

    [![A QnAMakerPortalSharePoint keresése az Enterprise alkalmazáslistájában](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. A **Biztonság csoportban**nyissa meg **az Engedélyek (Engedélyek) (Engedélyek) (Engedélyek) (Engedélyek) csoportban.** Válassza **a Rendszergazdai hozzájárulás megadása a Szervezethez**lehetőséget.

    [![Hitelesített felhasználó kiválasztása az Active Directory rendszergazdájához](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Jelöljön ki egy bejelentkezési fiókot, amely engedéllyel rendelkezik az Active Directory számára.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

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

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Közös munka a tudásbázison](collaborate-knowledge-base.md)
