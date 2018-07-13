---
title: Egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez
description: Ez az oktatóanyag bemutatja, hogyan lehet egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a35d99ae6fd41ae840c5e2710135c6a2f840be65
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007723"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Egy Windows virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan használható a Felügyeltszolgáltatás-identitás (MSI) egy Windows virtuális gép (VM) egy Azure Data Lake Store eléréséhez. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi szolgáltatások, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy a hitelesítő adatok beszúrása a kódot a hitelesítéshez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy Windows virtuális gép az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést adni egy Azure Data Lake Store
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és használhatja azokat egy Azure Data Lake Store eléréséhez

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben az oktatóanyagban létrehozunk egy új Windows virtuális Gépet.  A meglévő virtuális gép MSI is engedélyezheti.

1. Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3. Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gépet.
4. Válassza ki a megfelelő **előfizetés** a virtuális gép a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** , amelyben a virtuális gép létrehozásához, válassza ki a **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások oldalon hagyja változatlanul az alapértelmezett beállításokat, majd kattintson **OK**.

   ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez 

Virtuális gép MSI lehetővé teszi, hogy a hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. MSI engedélyezéséhez jelzi az Azure a virtuális gép létrehozása egy felügyelt identitás. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepíti, és lehetővé teszi, hogy az Azure Resource Manager MSI.

1. Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.  
2. A bal oldali navigációs sávon kattintson **konfigurációs**. 
3. Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem. 
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.  
   ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, és a virtuális gépen ellenőrizze, hogy mely bővítmények, kattintson a **bővítmények**. Ha az MSI engedélyezve van, majd **ManagedIdentityExtensionforWindows** megjelenik a listában.

   ![Kép ALT szövege](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>A virtuális gép hozzáférést biztosítani az Azure Data Lake Store

Most már képes a virtuális gép hozzáférést a fájlokhoz és mappákhoz az Azure Data Lake Store a.  Ebben a lépésben egy meglévő Data Lake Store használata, vagy hozzon létre egy újat.  Hozzon létre egy új Data Lake Store az Azure portal használatával, kövesse a [Azure Data Lake Store a rövid útmutató](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Még nincsenek rövid útmutatók, amelyek használják az Azure CLI-vel és az Azure PowerShell-lel a [Azure Data Lake Store dokumentációja](~/articles/data-lake-store/data-lake-store-overview.md).

A Data Lake Store a hozzon létre egy új mappát, és engedélyezi a virtuális gép MSI olvasási, írási és futtat fájlokat abban a mappában:

1. Az Azure Portalon kattintson a **Data Lake Store** a bal oldali navigációs menüben.
2. A Data Lake Store ehhez az oktatóanyaghoz használni kívánt gombra.
3. Kattintson a **adatkezelő** a parancssávon.
4. A Data Lake Store gyökérmappájában van kiválasztva.  Kattintson a **hozzáférés** a parancssávon.
5. Kattintson a **Hozzáadás** parancsra.  Az a **kiválasztása** mezőbe írja be például a virtuális gép neve **DevTestVM**.  Jelölje be a virtuális gép a keresési eredmények között, majd kattintson a **kiválasztása**.
6. Kattintson a **engedélyek kiválasztása**.  Válassza ki **olvasási** és **Execute**, hozzáadása **Ez a mappa**, és adja hozzá **csak egy engedéllyel**.  Kattintson az **OK** gombra.  Az engedély sikeresen fel kell venni.
7. Zárja be a **hozzáférés** panelen.
8. A jelen oktatóanyag esetében hozzon létre egy új mappát.  Kattintson a **új mappa** a parancssávon, és adja meg az új mappa nevét, például **TestFolder**.  Kattintson az **OK** gombra.
9. Kattintson a létrehozott mappát, majd kattintson a **hozzáférés** a parancssávon.
10. Hasonló a 5. lépésében kattintson a **Hozzáadás**, a a **kiválasztása** mezőben adja meg a virtuális gép nevét, válasszon ki, és kattintson **kiválasztása**.
11. Hasonló a 6. lépés: kattintson a **engedélyek kiválasztása**, jelölje be **olvasási**, **írási**, és **Execute**, hozzá **mappa**, és adja hozzá **hozzáférési engedély bejegyzés és alapértelmezett engedély bejegyzés**.  Kattintson az **OK** gombra.  Az engedély sikeresen fel kell venni.

A virtuális gép MSI most már elvégezheti a fájlok minden művelet a létrehozott mappába.  A Data Lake Store, a hozzáférés kezeléséről további információt Ez a cikk a [hozzáférés-vezérlés a Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>A virtuális gép MSI használata hozzáférési jogkivonatot kapjon, és az Azure Data Lake Store fájlrendszer meghívására

Az Azure Data Lake Store natív módon támogatja az Azure AD-hitelesítést, így közvetlenül fogadja a hozzáférési jogkivonatok beszerezni az MSI használatával.  A Data Lake Store fájlrendszer hitelesítésre küldeni a Data Lake Store fájlrendszer-végpontra, az engedélyeztetési fejléc a következő formátumban: "< ACCESS_TOKEN_VALUE > tulajdonosi" az Azure AD által kiállított hozzáférési jogkivonat.  Az Azure AD-hitelesítés a Data Lake Store-támogatással kapcsolatos további tudnivalókért olvassa el a [hitelesítés a Data Lake Store az Azure Active Directoryval](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> A Data Lake Store-fájlrendszer ügyféloldali SDK-k még nem támogatják a Felügyeltszolgáltatás-identitást.  Ebben az oktatóanyagban frissül a támogatási az SDK-val való felvételekor.

Ebben az oktatóanyagban hitelesítést a REST API, PowerShell-lel, hogy a REST-kérelmek a Data Lake Store-fájlrendszer. A virtuális gép MSI használata a hitelesítéshez, szüksége, hogy a kéréseket a virtuális gépről.

1. A portálon lépjen a **virtuális gépek**, lépjen a Windows virtuális gépre, és a a **áttekintése** kattintson **Connect**.
2. Adja meg a **felhasználónév** és **jelszó** számára, amelyhez hozzáadta a Windows virtuális gép létrehozásakor. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolat** nyissa meg a virtuális géppel **PowerShell** a távoli munkamenet. 
4. A PowerShell-lel `Invoke-WebRequest`, indítson egy a helyi MSI-végpontot a hozzáférési jogkivonat beszerzése az Azure Data Lake Store.  A Data Lake Store az erőforrás-azonosító "https://datalake.azure.net/".  A Data Lake nem pontosan egyezik az erőforrás-azonosítója, és a záró perjellel fontos.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   A válasz egy JSON-objektumból átalakítása egy PowerShell-objektumot. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Bontsa ki a hozzáférési jogkivonatot a válaszból.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Használja a PowerShell "Invoke-WebRequest", indítson egy a Data Lake Store REST-végpont a gyökérmappában található mappák listáját.  Ez az egyik egyszerű módja, ellenőrizze, hogy minden helyesen van-e beállítva.  Fontos, az engedélyezési fejléc a "Tulajdonos" string "B" változtatáshoz rendelkezik.  A Data Lake Store a neve annak a **áttekintése** szakasz a Data Lake Store panel az Azure Portalon.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   A sikeres válasz a következőhöz hasonló:

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

6. Most kipróbálhatja, ha fájlt tölt fel a Data Lake Store.  Először hozzon létre egy fájlt a feltöltéshez.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. A PowerShell-lel `Invoke-WebRequest`, indítson egy, a korábban létrehozott mappába töltse fel a fájlt a Data Lake Store REST-végpont.  A kérelem két lépést tart.  Az első lépésben kérés, és hol kell feltölteni a fájlt egy átirányítási beolvasása.  A második lépésben ténylegesen feltöltheti a fájlt.  Állítsa be annak a mappának a nevét, és megfelelően fájlt, ha ebben az oktatóanyagban használt különböző értékeket, mint a vágólapra. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Nézze meg az értéket, ha `$HdfsRedirectResponse` a következő választ hasonlóan kell kinéznie:

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

   A feltöltés egy kérést küld az átirányítási végpont:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   A sikeres válasz néz ki:

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

Egyéb Data Lake Store fájlrendszer API-k fűzze hozzá a fájlokhoz használja, töltse le a fájlokat, és több.

Gratulálunk!  Virtuális gép MSI használata a Data Lake Store-fájlrendszer már hitelesítve.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- A felügyeleti műveletek Data Lake Store az Azure Resource Managert használja.  További információk a virtuális gép MSI használata a hitelesítést a Resource Managerhez, [egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata a Resource Manager eléréséhez](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Tudjon meg többet [hitelesítés a Data Lake Store az Azure Active Directoryval](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Tudjon meg többet [fájlrendszerműveletek az Azure Data Lake Store REST API-val](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) vagy a [WebHDFS FileSystem API-k](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Tudjon meg többet [hozzáférés-vezérlés a Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.