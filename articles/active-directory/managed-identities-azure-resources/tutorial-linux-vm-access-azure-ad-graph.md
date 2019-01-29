---
title: Az Azure AD Graph API elérése Linux VM-beli, rendszer által hozzárendelt felügyelt identitással
description: Az oktatóanyag azt ismerteti, hogyan férhet hozzá az Azure AD Graph API-hoz egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: priyamo
ms.openlocfilehash: ad2d20bc54a2de5a049beadac779fff47d39d9b4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167954"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>Oktatóanyag: Az Azure AD Graph API elérése Linux VM-beli, rendszer által hozzárendelt felügyelt identitással

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá az Azure AD Graph API-hoz, és kérheti le annak csoporttagságait egy Linux rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásával. Az Azure-erőforrások felügyelt identitásainak kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba.  

Ebben az oktatóanyagban a virtuális gép identitásának Azure AD-csoportbeli tagságát kérdezi le. A csoportadatokra gyakran engedélyezési döntések során van szükség. A virtuális gép felügyelt identitása a háttérben **szolgáltatásnévként** jelenik meg az Azure AD-ben. 

> [!div class="checklist"]
> * Csatlakozás az Azure AD szolgáltatáshoz
> * Virtuális gép identitásának hozzáadása egy csoporthoz az Azure AD-ben 
> * Hozzáférés biztosítása az Azure AD Graph-hoz a virtuális gép identitása számára 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure AD Graph meghívása a használatával

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Annak érdekében, hogy hozzáférést tudjon biztosítani az Azure AD Graph-hoz a virtuális gép identitása számára, a fiókjához hozzá kell rendelnie a **globális rendszergazda** szerepkört az Azure AD-ben.

## <a name="connect-to-azure-ad"></a>Csatlakozás az Azure AD szolgáltatáshoz

Ahhoz, hogy hozzá tudja rendelni a virtuális gépet egy csoporthoz, illetve engedélyezni tudja a csoporttagságok lekérését a virtuális gép számára, csatlakoznia kell az Azure AD-hez.

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>Virtuális gép identitásának hozzáadása egy csoporthoz az Azure AD-ben

Amikor engedélyezte a rendszer által hozzárendelt felügyelt identitást a Linux rendszerű virtuális gépen, az létrehozott egy szolgáltatásnevet az Azure AD-ben.  A virtuális gépet hozzá kell adnia egy csoporthoz. A következő cikkből megtudhatja, hogyan adhatja hozzá virtuális gépét egy csoporthoz az Azure AD-ben:

- [Csoporttagok hozzáadása](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Hozzáférés biztosítása az Azure AD Graph API-hoz a virtuális gép számára

Az Azure-erőforrások felügyelt identitásainak segítségével a kód hozzáférési jogkivonatokat kérhet le az olyan erőforrások felé történő hitelesítéshez, amelyek támogatják az Azure AD-hitelesítést. A Microsoft Azure AD Graph API támogatja az Azure AD-hitelesítést. Ebben a lépésben hozzáférést biztosít az Azure AD Graph-hoz a virtuális gép identitásának szolgáltatásneve számára, hogy az le tudja kérdezni a csoporttagságokat. A szolgáltatásnevek az **alkalmazásengedélyeken** keresztül kapnak hozzáférést a Microsoft vagy az Azure AD Graph-hoz. A biztosítandó alkalmazásengedély típusa attól az entitástól függ, amelyet el szeretne érni a Microsoft vagy az Azure AD Graph-ban.

Ebben az oktatóanyagban a `Directory.Read.All` alkalmazásengedély használatával teszi lehetővé a virtuális gép identitása számára a csoporttagságok lekérdezését. Az engedély megadásához szüksége lesz egy olyan felhasználói fiókra, amely globális rendszergazdai szerepkörrel rendelkezik az Azure AD-ben. Az alkalmazásengedély megadásához általában az Azure Portalon kell megkeresi az alkalmazás regisztrációját, és ott hozzáadni az engedélyt. Az Azure-erőforrások felügyelt identitásai azonban nem alkalmazásobjektumokat regisztrálnak az Azure AD-ben, hanem csak szolgáltatásneveket. Az alkalmazásengedély regisztrálása az Azure AD PowerShell parancssori eszközével történik. 

Azure AD Graph:
- Egyszerű szolgáltatás alkalmazásazonosítója (használja az alkalmazásengedély megadása): 00000002-0000-0000-c000-000000000000
- Erőforrás-azonosító (hozzáférési jogkivonat az Azure-erőforrások felügyelt identitásaiból történő lekérésekor): https://graph.windows.net
- Engedély hatókör-hivatkozást: [Az Azure AD Graph-engedélyek referencia](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>Alkalmazásengedélyek biztosítása a CURL használatával

1. Jogkivonat lekérése a CURL-kérelmek elküldéséhez:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. Kérdezze és jegyezze fel a virtuális gép `objectId` azonosítóját. Erre a további lépésekben lesz szükség, hogy engedélyezhesse a virtuális gép számára a csoporttagsága beolvasását. Cserélje le a(z) `<ACCESS TOKEN>` jogkivonatot az előző lépésben beszerzett hozzáférési jogkivonatra.

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Az Azure AD Graph alkalmazásazonosítóját (00000002-0000-0000-c000-000000000000) használva kérdezze le és jegyezze fel az `odata.type: Microsoft.DirectoryServices.ServicePrincipal` `objectId` azonosítóját és a `Directory.Read.All` alkalmazásszerepkör-engedély `id` azonosítóját.  A(z) `<ACCESS TOKEN>` jogkivonatot cserélje le a korábban lekért hozzáférési jogkivonatra.

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Válasz:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. Ezután az Azure AD Graph API használatával adjon a virtuális gép szolgáltatásnevének olvasási hozzáférést az Azure AD-címtár objektumaihoz.  Az `id` érték a `Directory.Read.All` alkalmazásszerepkör-engedély értéke, a `resourceId` pedig az `odata.type:Microsoft.DirectoryServices.ServicePrincipal` szolgáltatásnév `objectId` azonosítója (az előző lépésben feljegyzett értékek).

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>Hozzáférési jogkivonat beszerzése a virtuális gép identitásával az Azure AD Graph meghívásához 

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](https://msdn.microsoft.com/commandline/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. A portálon lépjen a Linux virtuális gépre, és az **Áttekintés** területen kattintson a **Csatlakozás** gombra.  
2. **Csatlakozzon** a virtuális géphez a választott SSH-ügyféllel. 
3. A terminálablakban a CURL, használatával indítson egy Azure-erőforrások végpont a hozzáférési jogkivonat beszerzése az Azure AD Graph helyi felügyelt identitások.  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   A válasz tartalmazza az Azure AD Graph eléréséhez szükséges hozzáférési jogkivonatot.

   Válasz:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. A virtuális gép szolgáltatásnevének objektumazonosítóját használva (ezt az értéket kérte le a korábbi lépésekben) lekérheti az Azure AD Graph API-ból annak csoporttagságait. Cserélje le `<OBJECT-ID>` a virtuális gép egyszerű szolgáltatás objektumazonosítójú és `<ACCESS-TOKEN>` a korábban kapott hozzáférési jogkivonattal:

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   Válasz:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>További lépések

Az oktatóanyag bemutatta, hogyan érhető el Azure AD Graph a Linux VM-beli, rendszer által hozzárendelt felügyelt identitással.  További információ az AD Graph szolgáltatásról:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
