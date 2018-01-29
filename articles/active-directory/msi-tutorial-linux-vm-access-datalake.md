---
title: "Azure Data Lake Store eléréséhez használja a felügyelt Linux virtuális gép Szolgáltatásidentitás"
description: "Ez az oktatóanyag bemutatja, hogyan felügyelt szolgáltatás identitásának (MSI) a Linux virtuális gépek Azure Data Lake Store elérésére használhat."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 6bc3f893697ba39c3608f0bc866be89da25596ef
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Azure Data Lake Store eléréséhez használja a felügyelt Linux virtuális gép Szolgáltatásidentitás

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan Szolgáltatásidentitás felügyelt Linux virtuális gép (VM) az Azure Data Lake Store elérésére használhat. Azure automatikusan kezeli a identitásokat tartalmaz, amelyek segítségével MSI hozhatók létre. Használhatja az MSI anélkül, hogy a hitelesítő adatokat beszúrni a kódot az Azure Active Directory (Azure AD-) hitelesítést támogató szolgáltatások felé történő hitelesítésre. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Linux virtuális gép MSI engedélyezése. 
> * A virtuális gép hozzáférést biztosítson az Azure Data Lake Store.
> * Hozzáférés-token beszerzése a Virtuálisgép-azonosítója használatával, és az Azure Data Lake Store hozzáférhetne.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Egy új erőforráscsoportot a Linux virtuális gép létrehozása

Ebben az oktatóanyagban létrehozhatunk egy új Linux virtuális Gépet. A meglévő virtuális MSI is engedélyezheti.

1. Válassza ki a **új** gomb az Azure portál bal felső sarkában.
2. Válassza a **Számítás**, majd az **Ubuntu Server 16.04 LTS** elemet.
3. Adja meg a virtuális gép adatait. A **hitelesítési típus**, jelölje be **nyilvános SSH-kulcs** vagy **jelszó**. A létrehozott hitelesítő adatok lehetővé teszik-e jelentkezni a virtuális gép.

   !["Alapvető beállítások" ablaktáblán a virtuális gép létrehozása](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Az a **előfizetés** listára, válassza ki a virtuális gép előfizetés.
5. Egy új erőforráscsoportot, amelyet létre kell hozni a virtuális gép kiválasztásához jelölje be **erőforráscsoport** > **hozzon létre új**. Amikor elkészült, válassza **OK**.
6. Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A beállítások panelen hagyja az alapértelmezett beállításokat, és válassza ki **OK**.

## <a name="enable-msi-on-your-vm"></a>A virtuális Gépen lévő MSI engedélyezése

A virtuális gép MSI segítségével hozzáférési jogkivonatok lekérni az Azure AD anélkül, hogy a hitelesítő adatok be a kódját. Az MSI-Virtuálisgép-bővítmény engedélyezése MSI telepíti a virtuális Gépet, és lehetővé teszi, hogy az Azure Resource Manager MSI.  

1. A **virtuális gép**, válassza ki a MSI engedélyezze a kívánt virtuális gépet.
2. A bal oldali panelen válassza ki a **konfigurációs**.
3. Látni **identitás**. Regisztrálja és MSI engedélyezéséhez jelölje be **Igen**. Ha le kell tiltani, jelölje be **nem**.
   !["Az Azure Active Directoryban regisztrálja" kiválasztása](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Kattintson a **Mentés** gombra.
5. Ha azt szeretné ellenőrizni a Linux virtuális Gépet, válassza ki a rendszer mely bővítmények **bővítmények**. Ha engedélyezve van a MSI, **ManagedIdentityExtensionforLinux** listájában jelenik meg.

   ![Kiterjesztések listája](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>A virtuális gép hozzáférési jogot az Azure Data Lake Store

Most is a virtuális gép hozzáférést a fájlokhoz és mappákhoz az Azure Data Lake Store. Ez a lépés a meglévő Data Lake Store-példány használata, vagy hozzon létre egy újat. Data Lake Store-példány létrehozása az Azure portál használatával, kövesse a [Azure Data Lake Store gyors üzembe helyezés](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Is vannak, amelyek a Azure CLI és az Azure PowerShell quickstarts a [Azure Data Lake Store-dokumentáció](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

A Data Lake Store hozzon létre egy új mappát, és MSI engedélyt olvasási, írási és futtat fájlokat abban a mappában:

1. Válassza ki az Azure-portálon **Data Lake Store** a bal oldali ablaktáblán.
2. Válassza ki a használni kívánt Data Lake Store-példányt.
3. Válassza ki **adatkezelő** a parancssávon.
4. A gyökérmappában található azon a Data Lake Store-példány van kiválasztva. Válassza ki **hozzáférés** a parancssávon.
5. Válassza a **Hozzáadás** lehetőséget.  Az a **válasszon** mezőbe írja be például a nevét, a virtuális gép – **DevTestVM**. Válassza ki a virtuális Gépet a találatok között, és kattintson **válasszon**.
6. Kattintson a **engedélyként válassza**.  Válassza ki **olvasási** és **Execute**, hozzáadása **Ez a mappa**, és adja hozzá **csak olyan hozzáférési engedélyek**. Válassza ki **Ok**.  Az engedély sikeresen hozzá kell adni.
7. Zárja be a **hozzáférés** ablaktáblán.
8. Ebben az oktatóanyagban hozzon létre egy új mappát. Válassza ki **új mappa** a parancssávon, és adja meg az új mappa nevét – például **TestFolder**.  Válassza ki **Ok**.
9. Válassza ki a mappát, amely hozott létre, és válassza ki **hozzáférés** a parancssávon.
10. 5 select. lépés hasonlít **Hozzáadás**. Az a **válasszon** mezőbe írja be a virtuális gép nevét. Válassza ki a virtuális Gépet a találatok között, és kattintson **válasszon**.
11. 6 select. lépés hasonlít **Select engedélyeket**. Válassza ki **olvasási**, **írási**, és **Execute**, adja hozzá **Ez a mappa**, és adja hozzá **egy hozzáférési engedélybejegyzés és egy alapértelmezett engedélybejegyzés**. Válassza ki **Ok**.  Az engedély sikeresen hozzá kell adni.

MSI most elvégezhetnek a fájl a mappában létrehozott. A Data Lake Store történő hozzáférés felügyeletéhez további információkért lásd: [hozzáférés-vezérlés a Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Szereznie egy hozzáférési jogkivonatot, és hívja meg a Data Lake Store-fájlrendszer

Azure Data Lake Store natív módon támogatja az Azure AD hitelesítési, így közvetlenül elfogadása jogkivonatot kapott MSI keresztül. A Data Lake Store-fájlrendszer hitelesítést végeznek, az Azure AD ki a Data Lake Store-fájl rendszer végpontot a hozzáférési tokent kell küldenie. A hozzáférési token formátumú az authorization fejlécet "tulajdonosi \<ACCESS_TOKEN_VALUE\>".  Az Azure AD-alapú hitelesítés Data Lake Store-támogatással kapcsolatos további tudnivalókért lásd: [Azure Active Directory használatával a Data Lake Store hitelesítési](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

Ebben az oktatóanyagban bejelentkezik a Data Lake Store-fájlrendszer szükséges a REST API REST-kérelmeket a cURL használatával.

> [!NOTE]
> Az ügyfél SDK-k a Data Lake Store-fájlrendszer még nem támogatják a felügyelt identitás.

Lépések elvégzéséhez szüksége van egy SSH-ügyfél. Windows használ, ha az SSH-ügyfél a használhatja a [Linux rendszerhez készült Windows alrendszer](https://msdn.microsoft.com/commandline/wsl/about). Ha az SSH-ügyfél kulcsok konfigurálása segítségre van szüksége, tekintse meg [SSH használata a Windows Azure-kulcsok](../virtual-machines/linux/ssh-from-windows.md) vagy [létrehozása, és az SSH nyilvános és titkos kulcsból álló kulcspárt használata a Linux virtuális gépek Azure-ban](../virtual-machines/linux/mac-create-ssh-keys.md).

1. A portálon tallózással keresse meg a Linux virtuális Gépet. A **áttekintése**, jelölje be **Connect**.  
2. Csatlakoztassa a virtuális Gépet az SSH-ügyfél az Ön által választott használatával. 
3. A Terminálszolgáltatások ablakban cURL, használatával indítson egy lekérdezést a helyi MSI-végpont a Data Lake Store-fájlrendszer olyan hozzáférési jogkivonatot beolvasni. A Data Lake Store erőforrás-azonosító "https://datalake.azure.net/".  Fontos a záró perjelet az erőforrás-azonosító.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   A sikeres válasz a hozzáférési jogkivonat Data Lake Store-hitelesítést használó adja vissza:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. CURL használatával indítson egy lekérdezést a Data Lake Store fájlrendszert REST-végpont a gyökérmappában található mappák listázásához. Ez egyszerű módja ellenőrizze, hogy minden megfelelően van konfigurálva. A hozzáférési token értékének másolja az előző lépésben. Fontos, hogy rendelkezik-e a "Tulajdonos" karakterlánc a Authorization fejlécet a beruházási "b". Megtalálhatja a Data Lake Store-példány nevét a **áttekintése** szakasza a **Data Lake Store** ablaktáblán az Azure portálon.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   A sikeres válasz így néz ki:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Most megpróbálhatja, a fájl feltöltése a Data Lake Store-példányra. Először hozzon létre a fájlt a feltöltéshez.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. CURL használatával indítson egy lekérdezést a Data Lake Store fájlrendszert REST-végpont lehet feltölteni a fájlt a korábban létrehozott mappába. A feltöltés magában foglalja az átirányítást, és a cURL követi automatikusan az átirányítást. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    A sikeres válasz így néz ki:

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

Más API-k használatával a Data Lake Store-fájlrendszer, fűzze hozzá a fájlokhoz, a letöltendő fájlok, és több.

Gratulálunk! MSI-fájl használata a Linux virtuális gépek által hitelesített már a Data Lake Store fájlrendszerhez.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](../active-directory/msi-overview.md).
- A műveleteket Data Lake Store az Azure Resource Manager.  Az MSI hitelesítésére az erőforrás-kezelő használatával további információkért lásd: [egy Linux virtuális gép felügyelt szolgáltatás Identity (MSI) használatával férjenek hozzá a Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- További információ [hitelesítés az Azure Active Directory használatával a Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- További információ [fájl rendszerműveletekről az Azure Data Lake Store REST API használatával](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) vagy a [WebHDFS fájlrendszer API-k](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- További információ [hozzáférés-vezérlés a Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.