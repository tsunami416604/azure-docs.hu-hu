---
title: Az Azure Data Lake Storage elérése Windows VM-beli felügyeltszolgáltatás-identitással
description: Ez az oktatóanyag bemutatja, hogyan használhat Windows VM-beli felügyeltszolgáltatás-identitást az Azure Data Lake Storage eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: f5d4a5e26ecf4bde286a5163bf5ec7da492e474d
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247913"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-data-lake-store"></a>Oktatóanyag: Az Azure Data Lake Storage elérése Windows VM-beli felügyeltszolgáltatás-identitással

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan használhat felügyeltszolgáltatás-identitást egy Windows rendszerű virtuális gépen (VM) egy Azure Data Lake Storage eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Felügyeltszolgáltatás-identitás engedélyezése Windows rendszerű virtuális gépen 
> * Hozzáférés engedélyezése a virtuális gép számára egy Azure Data Lake Store-hoz
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd hozzáférés egy Azure Data Lake Store-hoz a jogkivonat segítségével

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Egy Windows rendszerű virtuális gép létrehozása egy új erőforráscsoportban

Ebben az oktatóanyagban egy új Windows VM-et fog létrehozni.  A meglévő virtuális gépeken is engedélyezheti a felügyeltszolgáltatás-identitást.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3. Adja meg a virtuális gép adatait. Az itt létrehozott **felhasználónév** és **jelszó** alkotják a virtuális gépre való bejelentkezéshez használt hitelesítő adatokat.
4. Válassza ki a megfelelő **előfizetést** a virtuális géphez a legördülő menüben.
5. A virtuális gép létrehozásához használni kívánt új **erőforráscsoport** kiválasztásához válassza az **Új létrehozása** lehetőséget. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a virtuális gép méretét. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások lapon hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

   ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-service-identity-on-your-vm"></a>Felügyeltszolgáltatás-identitás engedélyezése a virtuális gépen 

A VM-beli felügyeltszolgáltatás-identitással anélkül kérhet le hozzáférési jogkivonatokat az Azure AD-ből, hogy hitelesítő adatokat kellene a kódba illesztenie. A felügyeltszolgáltatás-identitás engedélyezése arra utasítja az Azure-t, hogy hozzon létre egy felügyelt identitást a virtuális géphez. A háttérben a felügyeltszolgáltatás-identitás engedélyezésének két következménye van: regisztrálja a virtuális gépet az Azure Active Directoryban a felügyelt identitása létrehozásához, és konfigurálja az identitást a virtuális gépen.

1. Válassza ki azt a **virtuális gépet**, amelyen engedélyezni szeretné a felügyeltszolgáltatás-identitást.  
2. A bal oldali navigációs sávban kattintson a **Konfigurálás** elemre. 
3. Megjelenik a **felügyeltszolgáltatás-identitás**. A felügyeltszolgáltatás-identitás regisztrálásához és engedélyezéséhez kattintson az **Igen**, a letiltásához a Nem gombra. 
4. Mindenképp kattintson a **Mentés** gombra a konfiguráció mentéséhez.  
   ![Helyettesítő képszöveg](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha ellenőrizni szeretné, hogy milyen bővítmények találhatók meg ezen a virtuális gépen, kattintson a **Bővítmények** lehetőségre. Ha a felügyeltszolgáltatás-identitás engedélyezett, akkor a **ManagedIdentityExtensionforWindows** megjelenik a listában.

   ![Helyettesítő képszöveg](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Hozzáférés engedélyezése a virtuális gép számára egy Azure Data Lake Store-hoz

Most hozzáférést adhat a virtuális gépnek az Azure Data Lake Store-on lévő fájlokhoz és mappákhoz.  Ehhez a lépéshez meglévő Data Lake Store-t is használhat, de újat is létrehozhat.  Ha új Data Lake Store-t szeretne létrehozni az Azure Portalon, kövesse ennek az [Azure Data Lake Store rövid útmutatónak](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) a lépéseit. Az [Azure Data Lake Store dokumentációjában](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

A Data Lake Storage-ban hozzon létre egy új mappát, és adjon engedélyt a VM felügyeltszolgáltatás-identitásnak a mappában lévő fájlok olvasásához, írásához és futtatásához:

1. Az Azure Portalon kattintson a bal oldali navigációs sávban lévő **Data Lake Store** elemre.
2. Kattintson az oktatóanyaghoz használni kívánt Data Lake Store-ra.
3. Kattintson a parancssávon az **Adatkezelő** lehetőségre.
4. A Data Lake Store gyökérmappája van kiválasztva.  Kattintson a **Hozzáférés** elemre a parancssávon.
5. Kattintson a **Hozzáadás** parancsra.  A **Kiválasztás** mezőbe írja be a virtuális gép nevét (például **DevTestVM**).  Kattintson a virtuális gép kiválasztásához a keresési eredmények közül, majd kattintson a **Kiválasztás** lehetőségre.
6. Kattintson az **Engedélyek kiválasztása** elemre.  Válassza ki az **Olvasás** és a **Végrehajtás** lehetőséget, adja hozzá **ehhez a mappához**, és adja hozzá **csak hozzáférési engedélyként**.  Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.
7. Zárja be a **Hozzáférés** panelt.
8. Ebben az oktatóanyagban egy új mappát hozunk létre.  Kattintson a parancssáv **Új mappa** elemére, és adjon nevet az új mappának (például **TestFolder**).  Kattintson az **OK** gombra.
9. Kattintson a létrehozott mappára, majd kattintson a **Hozzáférés** gombra a parancssávon.
10. Az 5. lépéshez hasonlóan kattintson a **Hozzáadás** lehetőségre, a **Kiválasztás** mezőbe írja be a virtuális gép nevét, válassza ki, és kattintson a **Kiválasztás** elemre.
11. A 6. lépéshez hasonlóan kattintson az **Engedélyek kiválasztása** lehetőségre, válassza az **Olvasás**, **Írás** és **Végrehajtás** elemet, adja hozzá **ehhez a mappához**, és adja hozzá **hozzáférési és alapértelmezett engedély bejegyzéseként**.  Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.

A VM-beli felügyeltszolgáltatás-identitás mostantól az összes műveletet el tudja végezni a létrehozott mappában lévő fájlokon.  További információkat a Data Lake Store hozzáférésének kezeléséről [a hozzáférés-vezérlés Data Lake Store-ban való használatával kapcsolatos](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control) cikkben talál.

## <a name="get-an-access-token-using-the-vm-managed-service-identity-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Hozzáférési jogkivonat lekérése a VM-beli felügyeltszolgáltatás-identitással, majd az Azure Data Lake Storage-fájlrendszer hívása a jogkivonat segítségével

Az Azure Data Lake Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja a felügyeltszolgáltatás-identitás használatával beszerzett hozzáférési jogkivonatokat.  A Data Lake Store-fájlrendszer hitelesítéséhez az Azure AD által kiadott hozzáférési jogkivonatot kell küldenie a Data Lake Store-fájlrendszer végpontjának egy engedélyeztetési fejlécben, „Bearer <HOZZÁFÉRÉSI_JOGKIVONAT_ÉRTÉKE>” formátumban.  Az Azure AD-hitelesítés Data Lake Store általi támogatásáról további információt a [Data Lake Store-ral és az Azure Active Directoryval való hitelesítést](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory) ismertető cikkben talál.

> [!NOTE]
> A Data Lake Store-fájlrendszer ügyféloldali SDK-i még nem támogatják a Managed Service Identityt.  Ez az oktatóanyag frissülni fog, mihelyt az SDK általi támogatás kibővül.

Ebben az oktatóanyagban a Data Lake Store-fájlrendszer REST API-jában fog hitelesíteni a PowerShell használatával REST-kérések elindításához. Ha a VM-beli felügyeltszolgáltatás-identitást hitelesítéshez szeretné használni, a virtuális gépről kell indítania a kéréseket.

1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben. 
4. A Powershell `Invoke-WebRequest` parancsával küldjön kérést a helyi felügyeltszolgáltatás-identitási végpontra, hogy lekérjen egy hozzáférési jogkivonatot az Azure Data Lake Storage-hoz.  A Data Lake Store erőforrás-azonosítója „https://datalake.azure.net/”.  A Data Lake az erőforrás-azonosítót pontosan egyezteti, így a záró perjel nem hagyható el.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konvertálja a választ JSON-objektumból PowerShell-objektummá. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Nyerje ki a hozzáférési jogkivonatot a válaszból.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. A PowerShell „Invoke-WebRequest” parancsával intézzen egy kérést a Data Lake Store REST-végpontjához a gyökérmappában lévő mappák listázása érdekében.  Így egyszerűen ellenőrizhető, hogy minden megfelelően van-e konfigurálva.  Fontos, hogy az engedélyeztetési fejléc „Bearer” sztringjében a „B” betű nagy legyen.  A Data Lake Store nevét az Azure Portalon, a Data Lake Store panel **Áttekintés** szakaszában ellenőrizheti.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   A sikeres válasz így néz ki:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Most megpróbálhat feltölteni egy fájlt a Data Lake Store-ba.  Először hozza létre a feltölteni kívánt fájlt.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. A PowerShell `Invoke-WebRequest` parancsával intézzen egy kérést a Data Lake Store REST-végpontjához, hogy feltöltse a fájlt a korábban létrehozott mappába.  Ez a kérés két lépésből áll.  Az első lépésben elindít egy kérést, és a rendszer átirányítja oda, ahová a fájlt fel kell tölteni.  A második lépésben ténylegesen feltölti a fájlt.  Ne feledje megfelelően beállítani a mappa és a fájl nevét, ha az oktatóanyagtól eltérő értékeket használt. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   A `$HdfsRedirectResponse` értékének a következő válaszhoz hasonlóan kell kinéznie:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   A feltöltés befejezéséhez küldjön egy kérést az átirányítási végponthoz:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   A sikeres válasz nagyjából így néz ki:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

A Data Lake Store-fájlrendszer más API-jaival fájlokhoz végezhet hozzáfűzést, fájlokat tölthet le, és egyéb műveleteket is elvégezhet.

Gratulálunk!  Hitelesítést végzett a Data Lake Storage-fájlrendszeren egy VM-beli felügyeltszolgáltatás-identitással.

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan használhat felügyeltszolgáltatás-identitásokat Windows rendszerű virtuális gépeken egy Azure Data Lake Store eléréséhez. További információ az Azure Data Lake Store-ról:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
