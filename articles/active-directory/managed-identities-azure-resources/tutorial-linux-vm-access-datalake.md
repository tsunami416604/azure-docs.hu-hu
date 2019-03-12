---
title: Az Azure Data Lake Storage elérése Linux VM-beli, rendszer által hozzárendelt felügyelt identitással
description: Ez az oktatóanyag bemutatja, hogyan használhat Linux VM-beli, rendszer által hozzárendelt felügyelt identitást az Azure Data Lake Storage eléréséhez.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1d0540ccfb9b4aac03b750b5c0002055bdd536a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57546738"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Oktatóanyag: Az Azure Data Lake Storage elérése Linux VM-beli, rendszer által hozzárendelt felügyelt identitással

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan lehet egy rendszer által hozzárendelt felügyelt identitás használata Linux rendszerű virtuális gép (VM) az Azure Data Lake Store eléréséhez. Az alábbiak végrehajtásának módját ismerheti meg: 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés engedélyezése a virtuális gép számára egy Azure Data Lake Store-hoz.
> * Hozzáférési jogkivonat lekérése a virtuális gép rendszer által hozzárendelt felügyelt identitásának használatával az Azure Data Lake Storage eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Hozzáférés engedélyezése a virtuális gép számára egy Azure Data Lake Store-hoz

Most hozzáférést adhat a virtuális gépnek az Azure Data Lake Store-on lévő fájlokhoz és mappákhoz. Ehhez a lépéshez meglévő Data Lake Store-példányt is használhat, de újat is létrehozhat. Ha Data Lake Store-példányt szeretne létrehozni az Azure Portalon, kövesse az [Azure Data Lake Store rövid útmutatónak](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) a lépéseit. Az [Azure Data Lake Store dokumentációjában](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

A Data Lake Storage-ban hozzon létre egy új mappát, és adjon engedélyt a Linux VM-beli, rendszer által hozzárendelt felügyelt identitás számára a mappában lévő fájlok olvasásához, írásához és futtatásához:

1. Az Azure Portalon válassza a bal oldali panelen lévő **Data Lake Store** lehetőséget.
2. Válassza ki a használni kívánt Data Lake Store-példányt.
3. A parancssávon válassza ki az **Adatkezelő** elemet.
4. A Data Lake Store-példány gyökérmappája van kiválasztva. A parancssávon válassza a **Hozzáférés** elemet.
5. Válassza a **Hozzáadás** lehetőséget.  A **Kiválasztás** mezőbe írja be a virtuális gép nevét (például **DevTestVM**). Válassza ki a virtuális gépet a keresési eredményekből, majd kattintson a **Kiválasztás** gombra.
6. Kattintson az **Engedélyek kiválasztása** elemre.  Válassza ki az **Olvasás** és a **Végrehajtás** lehetőséget, adja hozzá **ehhez a mappához**, és adja hozzá **csak hozzáférési engedélyként**. Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.
7. Zárja be a **Hozzáférés** panelt.
8. Ebben az oktatóanyagban egy új mappát hozunk létre. Válassza ki a parancssáv **Új mappa** elemét, és adjon nevet az új mappának (például **TestFolder**).  Kattintson az **OK** gombra.
9. Válassza ki a létrehozott mappát, majd válassza ki a parancssávon a **Hozzáférés** lehetőséget.
10. Az 5. lépéshez hasonlóan válassza a **Hozzáadás** lehetőséget. A **Kiválasztás** mezőbe írja be a virtuális gép nevét. Válassza ki a virtuális gépet a keresési eredményekből, majd kattintson a **Kiválasztás** gombra.
11. A 6. lépéshez hasonlóan válassza az **Engedélyek kiválasztása** lehetőséget. Válassza ki az **Olvasás**, **Írás** és **Végrehajtás** elemeket, adja hozzá **ehhez a mappához**, és adja hozzá **hozzáférési engedély és engedély bejegyzéseként**. Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.

Az Azure-erőforrások felügyelt identitásai mostantól az összes műveletet el tudják végezni a létrehozott mappában lévő fájlokon. További információkat a Data Lake Store hozzáférésének kezeléséről [a hozzáférés-vezérlés Data Lake Store-ban való használatával kapcsolatos](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control) cikkben talál.

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Hozzáférési jogkivonat lekérése és a Data Lake Store-fájlrendszer meghívása

Az Azure Data Lake Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja az Azure-erőforrások felügyelt identitásai használatával beszerzett hozzáférési jogkivonatokat. A Data Lake Store-fájlrendszer hitelesítéséhez az Azure AD által kiadott hozzáférési jogkivonatot kell küldenie a Data Lake Store-fájlrendszer végpontjának. A hozzáférési jogkivonat egy „Bearer \<HOZZÁFÉRÉSI_JOGKIVONAT_ÉRTÉKE\>” formátumú engedélyeztetési fejléc.  Az Azure AD-hitelesítés Data Lake Store általi támogatásáról további információt a [Data Lake Store-ral és az Azure Active Directoryval való hitelesítést](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory) ismertető cikkben talál.

Ebben az oktatóanyagban a Data Lake Store-fájlrendszer REST API-jában fog hitelesíteni a cURL használatával REST-kérések elindításához.

> [!NOTE]
> A Data Lake Storage-fájlrendszer ügyféloldali SDK-i még nem támogatják az Azure-erőforrások felügyelt identitásait.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](https://msdn.microsoft.com/commandline/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. A portálon tallózzon a Linux rendszerű virtuális géphez. Az **Áttekintés** területen válassza a **Csatlakozás** lehetőséget.  
2. Csatlakozzon a virtuális géphez a választott SSH-ügyféllel. 
3. A terminálablakban a cURL használatával intézzen egy kérést az Azure-erőforrások helyi felügyeltidentitási végpontjára, hogy lekérjen egy hozzáférési jogkivonatot a Data Lake Storage-fájlrendszerhez. A Data Lake Store erőforrás-azonosítója „https://datalake.azure.net/”.  Mindenképpen bele kell foglalni a záró perjelet az erőforrás-azonosítóba.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   A sikeres válasz visszaadja a Data Lake Store-ban végzett hitelesítéshez használt hozzáférési jogkivonatot:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. A cURL használatával intézzen egy kérést a Data Lake Store fájlrendszer REST-végpontjához a gyökérmappában lévő mappák listázása érdekében. Így egyszerűen ellenőrizhető, hogy minden megfelelően van-e konfigurálva. Másolja ki az előző lépésből a hozzáférési jogkivonat értékét. Fontos, hogy az engedélyeztetési fejléc „Bearer” sztringjében a „B” betű nagy legyen. A Data Lake Store-példány nevét az Azure Portalon, a **Data Lake Store** panel **Áttekintés** szakaszában találja.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   A sikeres válasz így néz ki:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Most megpróbálhat feltölteni egy fájlt a Data Lake Store-példányba. Először hozza létre a feltölteni kívánt fájlt.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. A cURL használatával intézzen egy kérést a Data Lake Store-fájlrendszer REST-végpontjához, hogy feltöltse a fájlt a korábban létrehozott mappába. Az átirányítás a feltöltési művelet része, amelyet a cURL automatikusan követ. 

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

A Data Lake Store-fájlrendszer más API-jaival fájlokhoz végezhet hozzáfűzést, fájlokat tölthet le, és egyéb műveleteket is elvégezhet.

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan használhat Linux VM-beli, rendszer által hozzárendelt felügyelt identitást az Azure Data Lake Storage eléréséhez. További információ az Azure Data Lake Store-ról:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
