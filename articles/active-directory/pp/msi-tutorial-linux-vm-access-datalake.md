---
title: Egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez
description: Ez az oktatóanyag bemutatja, hogyan egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez.
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
ms.openlocfilehash: 358827722e8d77cd91410fae842ad2ba99967d98
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610354"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Egy Linux rendszerű virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata Azure Data Lake Store eléréséhez

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Az oktatóanyag bemutatja, hogyan használható a Felügyeltszolgáltatás-identitás (MSI) egy Linux rendszerű virtuális gépek (VM) egy Azure Data Lake Store eléréséhez. Felügyelt Szolgáltatásidentitások Azure automatikusan kezeli, és lehetővé teszi szolgáltatások, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy a hitelesítő adatok beszúrása a kódot a hitelesítéshez. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Egy Linux rendszerű virtuális gépen az MSI engedélyezéséhez 
> * A virtuális gép hozzáférést adni egy Azure Data Lake Store
> * A virtuális gép identitásának használatával hozzáférési jogkivonatot kapjon, és használhatja azokat egy Azure Data Lake Store eléréséhez

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Linux rendszerű virtuális gép létrehozása az új erőforráscsoport

Ebben az oktatóanyagban létrehozunk egy új Linux rendszerű virtuális Gépet. A meglévő virtuális gép MSI is engedélyezheti.

1. Kattintson a **erőforrás létrehozása** a az Azure portal bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**válassza **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik jelentkezzen be a virtuális Gépre.

   ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Válasszon egy **előfizetés** a virtuális gép a legördülő listában.
5. Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gép **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6. Válassza ki a méretet a virtuális gép számára. További méretek megjelenítéséhez válassza ki a **az összes megtekintése** vagy a lemez típusát támogatott szűrő módosítása. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen az MSI engedélyezéséhez

A virtuális gép MSI lehetővé teszi hozzáférési tokenek beszerzése az Azure ad-ből anélkül, hogy hitelesítő adatok üzembe a kódot kellene. Valójában MSI engedélyezéséhez két dolgot eredményez: a virtuális Gépen az MSI-Virtuálisgép-bővítmény telepítése, és lehetővé teszi az Azure Resource Manager MSI.  

1. Válassza ki a **virtuális gép** , hogy szeretné-e az MSI engedélyezéséhez.
2. A bal oldali navigációs sávon kattintson **konfigurációs**.
3. Látja **Felügyeltszolgáltatás-identitás**. Regisztráljon, és az MSI engedélyezéséhez, válassza ki a **Igen**, ha szeretné letiltani, válassza a nem.
4. Győződjön meg arról, hogy kattintson **mentése** a konfiguráció mentéséhez.

   ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Ha szeretné ellenőrizni, hogy a rendszer milyen kiterjesztésű **Linux rendszerű virtuális gép**, kattintson a **bővítmények**. Ha az MSI engedélyezve van, a **ManagedIdentityExtensionforLinux** megjelenik a listán.

   ![Kép ALT szövege](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

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

A virtuális gép MSI most már elvégezheti a fájlok minden művelet a létrehozott mappába.  A Data Lake Store, a hozzáférés kezeléséről további információt Ez a cikk a [hozzáférés-vezérlés a Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>A virtuális gép MSI használata hozzáférési jogkivonatot kapjon, és az Azure Data Lake Store fájlrendszer meghívására

Az Azure Data Lake Store natív módon támogatja az Azure AD-hitelesítést, így közvetlenül fogadja a hozzáférési jogkivonatok beszerezni az MSI használatával.  A Data Lake Store fájlrendszer hitelesítésre a Data Lake Store fájlrendszer-végpontra, az engedélyeztetési fejléc a következő formátumban az Azure AD által kiállított hozzáférési jogkivonat küldése "tulajdonosi \<ACCESS_TOKEN_VALUE\>".  Az Azure AD-hitelesítés a Data Lake Store-támogatással kapcsolatos további tudnivalókért olvassa el a [hitelesítés a Data Lake Store az Azure Active Directoryval](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

Ebben az oktatóanyagban hitelesítést a CURL használatával, hogy a többi REST API-kérelmek a Data Lake Store-fájlrendszer.

> [!NOTE]
> A Data Lake Store-fájlrendszer ügyféloldali SDK-k még nem támogatják a Felügyeltszolgáltatás-identitást.  Ebben az oktatóanyagban frissül a támogatási az SDK-val való felvételekor.

A lépések elvégzéséhez szüksége van egy SSH-ügyfél. Ha Windows használ, használhatja az SSH-ügyfél a a [a Linux Windows alrendszere](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [használata SSH-kulcsok az Azure-ban a Windows hogyan](~/articles/virtual-machines/linux/ssh-from-windows.md), vagy [létrehozása és a nyilvános és titkos ssh-kulcs használata Linux rendszerű virtuális gépekhez az Azure-ban](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. A portálon lépjen a Linux rendszerű virtuális gépre, majd a a **áttekintése**, kattintson a **Connect**.  
2. **Csatlakozás** az SSH-ügyféllel a választott a virtuális géphez. 
3. A terminálablakban a CURL, használatával indítson egy a helyi MSI-végpontot, hogy a Data Lake Store-fájlrendszer szükséges hozzáférési jogkivonat beszerzése.  A Data Lake Store az erőforrás-azonosító "https://datalake.azure.net/."  Fontos a záró törtvonalat erőforrás-azonosítója.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   A sikeres válasz adja vissza a hozzáférési jogkivonatot a Data Lake Store hitelesítésre használható:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. A Data Lake Store fájlrendszer REST-végpont a gyökérmappában található mappák listáját egy kérelmet a CURL használatával, végezze el.  Ez az egyik egyszerű módja, ellenőrizze, hogy minden helyesen van-e beállítva.  Másolja a hozzáférési jogkivonat értékét az előző lépésben.  Fontos, az engedélyezési fejléc a "Tulajdonos" string "B" változtatáshoz rendelkezik.  A Data Lake Store a neve annak a **áttekintése** szakasz a Data Lake Store panel az Azure Portalon.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   A sikeres válasz a következőhöz hasonló:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Most kipróbálhatja, ha fájlt tölt fel a Data Lake Store.  Először hozzon létre egy fájlt a feltöltéshez.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. A Data Lake Store fájlrendszer REST-végpont feltölteni a fájlt a mappába a korábban létrehozott egy kérelmet a CURL használatával, végezze el.  A feltöltés beletartozik az átirányítást, és a CURL automatikusan követi az átirányítást. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    A sikeres válasz a következőhöz hasonló:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
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