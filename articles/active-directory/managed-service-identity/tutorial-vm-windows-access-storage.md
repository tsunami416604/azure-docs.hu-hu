---
title: Azure Storage eléréséhez használja Windows virtuális gép felügyelt identitás
description: Ez az oktatóanyag végigvezeti az Azure Storage eléréséhez használja Windows virtuális gép felügyelt identitás.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 9ccc94727a18fbcd77f00000531934be57b8e132
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801364"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Oktatóanyag: Azure Storage eléréséhez a Windows virtuális gép felügyelt identitás használatára

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan engedélyezése felügyelt identitás egy Windows rendszerű virtuális gép számára, és Azure Storage eléréséhez használja az identitásukat.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Windows virtuális gép egy új erőforráscsoport létrehozása 
> * Felügyelt identitás Windows virtuális gépre (VM) engedélyezése
> * A tárfiók egy blob-tároló létrehozása
> * A Windows virtuális gép identitásának felügyelt hozzáférést a storage-fiók 
> * Hozzáférés, és hívja az Azure Storage segítségével 

> [!NOTE]
> Az Azure Storage Azure Active Directory-hitelesítés nyilvános előzetes verziójában van.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Windows virtuális gép egy új erőforráscsoport létrehozása

Ebben a szakaszban hozzon létre egy Windows virtuális Gépet, amely egy felügyelt identitás később engedélyezett.

1.  Kattintson a **/ hozzon létre új szolgáltatást** gomb az Azure portál bal felső sarkában található.
2.  Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 
3.  Adja meg a virtuális gép adatait. A **felhasználónév** és **jelszó** létrehozott itt van a hitelesítő adatok használatával jelentkezzen be a virtuális gép.
4.  Válassza ki a megfelelő **előfizetés** a virtuális gép meg a legördülő listában.
5.  Jelölje be egy új **erőforráscsoport** szeretne létrehozni, válassza ki a virtuális gépet **hozzon létre új**. Amikor végzett, kattintson az **OK** gombra.
6.  Adja meg a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. A Beállítások panelen hagyja változatlanul az alapértelmezett beállításokat, és kattintson az **OK** gombra.

    ![Kép helyettesítő szövege](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>A virtuális Gépet felügyelt identitásának engedélyezése

A virtuális gép felügyelt azonosítójának hozzáférési jogkivonatok lekérni az Azure AD anélkül, hogy a hitelesítő adatok kerüljenek a kódot a teszi lehetővé. A színfalak engedélyezése egy virtuális gépet az Azure-portálon kezelt identitásának két dolgot eredményez: regisztrálja a virtuális Gépet az Azure AD-hoz létre egy felügyelt, és konfigurálja a virtuális gép identitását. 

1. Keresse meg az erőforráscsoport a új virtuális gép, és válassza ki a virtuális gépet, az előző lépésben létrehozott.
2. Az a **beállítások** kategória, kattintson a **konfigurációs**.
3. Ahhoz, hogy a felügyelt identitást, válassza ki a **Igen**.
4. Kattintson a **mentése** elemre a konfiguráció alkalmazásához. 

## <a name="create-a-storage-account"></a>Create a storage account 

Ebben a szakaszban hozzon létre egy tárfiókot. 

1. Kattintson a **+ hozzon létre egy erőforrást** gomb az Azure portál bal felső sarkában található.
2. Kattintson a **tárolási**, majd **tárfiók - blob, a fájl, a tábla, a várólista**.
3. A **neve**, adja meg a tárfiók nevét.  
4. **Telepítési modell** és **fiók kind** meg **erőforrás-kezelő** és **Storage (az általános célú v1)**. 
5. Győződjön meg arról a **előfizetés** és **erőforráscsoport** az előző lépésben a virtuális gép létrehozásakor megadott megfelelően.
6. Kattintson a **Create** (Létrehozás) gombra.

    ![Új tárfiók létrehozása](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>A blob-tároló létrehozása és a fájl feltöltése a tárfiókba

Szükséges fájlok blob Storage tárolóban, ezért szüksége lesz egy blob tároló, amely tárolja a fájl létrehozásához. Majd fájlt töltsön fel az új tárfiók a blob-tárolóhoz.

1. Lépjen vissza az újonnan létrehozott tárfiók.
2. A **Blob szolgáltatás**, kattintson a **tárolók**.
3. Kattintson a **+ tároló** az oldal tetején.
4. A **új tároló**, adjon meg egy nevet, a tároló és a **nyilvános hozzáférési szint** tartsa meg az alapértelmezett értéket.

    ![A tároló létrehozása](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Egy fájlt egy szerkesztővel az Ön által választott, hozzon létre *hello world.txt* a helyi számítógépen.  Nyissa meg a fájlt, és adja hozzá a "Hello world szöveg (idézőjelek nélkül)! :) ", és mentse azt. 
6. Töltse fel a fájlt a tároló neve, majd kattintson az újonnan létrehozott tárolóhoz **feltöltése**
7. Az a **feltöltése a blob** ablaktáblán, a **fájlok**, a mappa ikonra, és keresse meg a fájlt **hello_world.txt** a helyi számítógépen, válassza ki a fájlt, majd kattintson a **Feltöltése**.
    ![Szöveg-fájl feltöltése](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>A virtuális gép hozzáférést biztosíthat az Azure Storage-tároló 

A virtuális Gépet felügyelt identitás használhatja az Azure storage-blob adatainak beolvasása.   

1. Lépjen vissza az újonnan létrehozott tárfiók.  
2. Kattintson a **hozzáférés-vezérlés (IAM)** hivatkozás a bal oldali panelen.  
3. Kattintson a **+ Hozzáadás** fölött a lap egy új szerepkör-hozzárendelés hozzáadása a virtuális gép számára.
4. A **szerepkör**, a legördülő listából válassza ki a **Adatolvasó a tárolási Blob (előzetes verzió)**. 
5. A következő legördülő alatt **való hozzáférés hozzárendelése**, válassza a **virtuális gép**.  
6. A következő szerepel-e a megfelelő előfizetést a **előfizetés** legördülő menüből, majd állítsa be **erőforráscsoport** való **összes erőforráscsoport**.  
7. A **válasszon**, válassza ki a virtuális Gépet, és kattintson a **mentése**. 

    ![Engedélyek hozzárendelése](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Szereznie egy hozzáférési jogkivonatot, és hívja az Azure Storage segítségével 

Az Azure Storage natív módon támogatja az Azure AD hitelesítési, így közvetlenül elfogadása jogkivonatot kapott egy felügyelt identitásával. Az Azure Storage-integráció az Azure ad-val része, és biztosítja a kapcsolati karakterlánc-felhasználó hitelesítő adatai eltérnek.

A .net kódot példa kapcsolat létesítése az Azure Storage access token használatával, és olvassa a korábban létrehozott fájl tartalmát. Ez a kód fogja tudni elérni a virtuális gép identitásának felügyelt végpont a virtuális Gépen kell futnia. .NET-keretrendszer 4.6-os vagy újabb is szükséges a hozzáférési token módszer használatával. Cserélje le a `<URI to blob file>` ennek megfelelően. Ezt úgy szerezheti be ezt az értéket navigáljon a fájl létrehozása és feltöltése a blob storage és másolása a **URL-cím** alatt **tulajdonságok** a **áttekintése** lap.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

A válasz tartalmazza a fájl tartalma:

`Hello world! :)`

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan engedélyezése a Windows rendszerű virtuális gép felügyelt identitás Azure Storage eléréséhez.  További információért lásd a Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



