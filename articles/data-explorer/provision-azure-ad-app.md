---
title: Azure AD-alkalmazás létrehozása az Azure Data Explorerben
description: Ismerje meg, hogyan hozhat létre egy Azure AD-alkalmazást az Azure Data Explorerben.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550512"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Az Azure Active Directory-alkalmazások regisztrációjának létrehozása az Azure Data Explorerben

Az Azure Active Directory (Azure AD) alkalmazáshitelesítés olyan alkalmazásokhoz, például egy felügyelet nélküli szolgáltatáshoz vagy egy ütemezett folyamathoz használatos, amelyeknek a felhasználó nélkül kell hozzáférniük az Azure Data Explorerhez. Ha egy alkalmazás, például egy webalkalmazás használatával csatlakozik egy Azure Data Explorer-adatbázishoz, az egyszerű szolgáltatáshitelesítés használatával kell hitelesítenie magát. Ez a cikk ismerteti, hogyan hozhat létre és regisztrálhat egy Azure AD egyszerű szolgáltatást, majd engedélyezi, hogy hozzáférjen egy Azure Data Explorer-adatbázishoz.

## <a name="create-azure-ad-application-registration"></a>Azure AD-alkalmazásregisztráció létrehozása

Az Azure AD-alkalmazások hitelesítése egy alkalmazás létrehozását és regisztrálását igényel az Azure AD-vel. Egy egyszerű szolgáltatás automatikusan létrejön, amikor az alkalmazás regisztrációja jön létre egy Azure AD-bérlőben. 

1. Jelentkezzen be az Azure `Azure Active Directory` [Portalra,](https://portal.azure.com) és nyissa meg a panelt

    ![Válassza ki az Azure Active Directoryt a portál menüből](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Válassza ki az **alkalmazásregisztrációk panelt,** és válassza **az Új regisztráció lehetőséget**

    ![Új alkalmazásregisztráció indítása](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Töltse ki a következő adatokat: 

    * **Név** 
    * **Támogatott fióktípusok**
    * **URI-web** > **átirányítása**
        > [!IMPORTANT] 
        > Az alkalmazástípusnak **webnek**kell lennie. Az URI nem kötelező, és ebben az esetben üresen marad.
    * **Regisztráció** kiválasztása

    ![Új alkalmazásregisztráció regisztrálása](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Válassza ki az **Áttekintés panelt,** és másolja az **alkalmazásazonosítót**.

    > [!NOTE]
    > Az adatbázis eléréséhez az alkalmazásazonosítóra van szükség ahhoz, hogy engedélyezze a szolgáltatásnév számára.

    ![Alkalmazásregisztrációs azonosító másolása](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. A **Tanúsítványok & titkos kulcsok** panelen válassza az **Új ügyféltitok** lehetőséget.

    ![Ügyféltitok létrehozásának megkezdése](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Ez a cikk az alkalmazás hitelesítő adatainak ügyféltitkos adatait ismerteti.  X509-es tanúsítvánnyal is hitelesítheti az alkalmazást. Válassza **a Tanúsítvány feltöltése lehetőséget,** és kövesse az utasításokat a tanúsítvány nyilvános részének feltöltéséhez.

1. Adjon meg egy leírást, a lejárati lejárati és válassza **a Hozzáadás lehetőséget.**

    ![Ügyféltitkos paraméterek megadása](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Másolja a kulcs értékét.

    > [!NOTE]
    > Ha elhagyja ezt a lapot, a kulcs érték nem lesz elérhető.  Az adatbázis ügyfélhitelesítő adatainak konfigurálásához kulcsra lesz szüksége.

    ![Ügyfél titkos kulcsértékének másolása](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Az alkalmazás létrejön. Ha csak egy hivatalos Azure Data Explorer-erőforráshoz kell hozzáférnie, például az alábbi programozott példában, hagyja ki a következő szakaszt. A delegált engedélyek támogatásáról az [alkalmazásregisztrációdelegált engedélyek konfigurálása](#configure-delegated-permissions-for-the-application-registration)című témakörben nyújt be.

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Delegált engedélyek konfigurálása az alkalmazásregisztrációhoz

Ha az alkalmazásnak a hívó felhasználó hitelesítő adataival kell hozzáférnie az Azure Data Explorerhez, konfigurálja az alkalmazásregisztrációdelegált engedélyeket. Ha például egy webes API-t hoz fel az Azure Data Explorer eléréséhez, és hitelesíteni szeretné magát az *API-t hívó* felhasználó hitelesítő adataival.  

1. Az **API-engedélyek** panelen válassza az **Engedély hozzáadása**lehetőséget.
1. Válassza ki **a szervezet által használt API-kat.** Keresse meg és válassza az **Azure Data Explorer**lehetőséget.

    ![Azure Data Explorer API-engedély hozzáadása](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. A **Delegált engedélyek**párbeszédpanelen jelölje be a **user_impersonation** jelölőnégyzetet, és adja hozzá **az engedélyeket**

    ![Delegált engedélyek kiválasztása a felhasználói megszemélyesítéssel](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Egyszerű szolgáltatáshozzáférés megadása egy Azure Data Explorer-adatbázishoz

Most, hogy a szolgáltatás egyszerű alkalmazás regisztráció ja, meg kell adnia a megfelelő egyszerű hozzáférést az Azure Data Explorer-adatbázishoz. 

1. A [Webes felhasználói felületen](https://dataexplorer.azure.com/)csatlakozzon az adatbázishoz, és nyisson meg egy lekérdezéslapot.

1. Hajtsa végre a következő parancsot:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Példa:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Az utolsó paraméter egy olyan karakterlánc, amely megjegyzésként jelenik meg, amikor lekérdezi az adatbázishoz társított szerepköröket.
    
    > [!NOTE]
    > Az alkalmazás regisztrációja létrehozása után előfordulhat, hogy több perces késéssel, amíg az Azure Data Explorer hivatkozhat rá. Ha a parancs végrehajtásakor hibaüzenet jelenik meg, hogy az alkalmazás nem található, várjon, és próbálkozzon újra.

További információt a [biztonsági szerepkörek kezelési](/azure/kusto/management/security-roles) és [betöltési engedélyei című témakörben talál.](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)  

## <a name="using-application-credentials-to-access-a-database"></a>Adatbázis eléréséhez alkalmazáshitelesítő adatok használata

Az alkalmazás hitelesítő adataival programozott módon érheti el az adatbázist az [Azure Data Explorer ügyfélkódtár](/azure/kusto/api/netfx/about-kusto-data.md)használatával.

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Adja meg az alkalmazásazonosítót és a korábban létrehozott egyszerű alkalmazásregisztráció (egyszerű szolgáltatás) kulcsát.

További információ: [Hitelesítés az Azure AD for Azure Data Explorer hozzáférés](/azure/kusto/management/access-control/how-to-authenticate-with-aad) és az Azure Key Vault használata a [.NET Core webalkalmazással.](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="invalid-resource-error"></a>Érvénytelen erőforráshiba

Ha az alkalmazást az Azure Data Explorer-hozzáférés felhasználóinak vagy alkalmazásainak hitelesítésére használja, delegált engedélyeket kell beállítania az Azure Data Explorer szolgáltatásalkalmazáshoz. Deklarálja az alkalmazást, amely hitelesítheti a felhasználókat vagy alkalmazásokat az Azure Data Explorer-hozzáféréshez. Ha ezt nem teszi meg, a hitelesítési kísérlet esetén a következőhöz hasonló hiba lép fel:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Az [Azure Data Explorer szolgáltatásalkalmazás delegált engedélyeinek beállítására](#configure-delegated-permissions-for-the-application-registration)vonatkozó utasításokat kell követnie.

### <a name="enable-user-consent-error"></a>Felhasználói hozzájárulási hiba engedélyezése

Az Azure AD-bérlői rendszergazda olyan szabályzatot hozhat, amely megakadályozza, hogy a bérlői felhasználók beleegyezést adjanak az alkalmazásokhoz. Ez a helyzet a következőhöz hasonló hibát eredményez, amikor egy felhasználó megpróbál bejelentkezni az alkalmazásba:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Kapcsolatba kell lépnie az Azure AD-rendszergazdával, hogy megadja a jóváhagyást a bérlő összes felhasználója számára, vagy engedélyeznie kell a felhasználó beleegyezését az adott alkalmazáshoz.

## <a name="next-steps"></a>További lépések

* A támogatott kapcsolati karakterláncok listájáról a [Kusto-kapcsolati karakterláncok](/azure/kusto/api/connection-strings/kusto.md) című témakörben található.
