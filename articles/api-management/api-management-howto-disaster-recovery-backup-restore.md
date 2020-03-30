---
title: Vészhelyreállítás megvalósítása biztonsági mentés és visszaállítás használatával az API Management ben
titleSuffix: Azure API Management
description: Ismerje meg, hogyan használhatja a biztonsági mentést és a visszaállítást a vészhelyreállítás végrehajtásához az Azure API Managementben.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: f9758678708338a284a35b45f7f9dd43b9a9017c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335956"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vészhelyreállítás a biztonsági mentés és visszaállítás használatával az Azure API Managementben

Az API-k Azure API Management en keresztüli közzétételével és kezelésével kihasználja a hibatűrést és az infrastruktúra-képességeket, amelyeket egyébként manuálisan tervezne, valósítana meg és kezelne. Az Azure platform a költségek töredékéért csökkenti a potenciális hibák nagy részét.

Az API Management szolgáltatást kiszolgáló régiót érintő rendelkezésre állási problémák helyreállításához bármikor készen kell állnia a szolgáltatás egy másik régióban történő újragenerálására. A helyreállítási idő célkitűzésétől függően érdemes lehet egy vagy több régióban készenléti szolgáltatást tartani. Megpróbálhatja a konfigurációjukat és a tartalmukat az aktív szolgáltatással a helyreállítási pont céljának megfelelően szinkronban tartani. A szolgáltatás biztonsági mentési és visszaállítási funkciói biztosítják a vész-helyreállítási stratégia megvalósításához szükséges építőelemeket.

A biztonsági mentési és visszaállítási műveletek az API Management szolgáltatás konfigurációjának replikálására is használhatók az operatív környezetek között, például fejlesztési és átmeneti állapotok között. Vigyázz, hogy a futásidejű adatok, például a felhasználók és az előfizetések is másolásra kerülnek, ami nem mindig kívánatos.

Ez az útmutató bemutatja, hogyan automatizálhatja a biztonsági mentési és visszaállítási műveleteket, és hogyan biztosíthatja a biztonsági mentési és visszaállítási kérelmek azure Resource Manager általi sikeres hitelesítését.

> [!IMPORTANT]
> A visszaállítási művelet nem módosítja a célszolgáltatás egyéni állomásnév-konfigurációját. Azt javasoljuk, hogy ugyanazt az egyéni állomásnevet és TLS-tanúsítványt használja az aktív és a készenléti szolgáltatásokhoz is, hogy a visszaállítási művelet befejezése után a forgalom átirányítható a készenléti példányra egy egyszerű DNS CNAME módosítással.
>
> A biztonsági mentési művelet nem rögzíti az Azure Portal analytics paneljén megjelenített jelentésekben használt előre összesített naplóadatokat.

> [!WARNING]
> Minden biztonsági mentés 30 nap után lejár. Ha a 30 napos lejárati időszak lejárta után próbál meg biztonsági `Cannot restore: backup expired` másolatot visszaállítani, a visszaállítás üzenettel sikertelen lesz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Az Azure Resource Manager-kérelmek hitelesítése

> [!IMPORTANT]
> A REST API biztonsági mentési és visszaállítási használja az Azure Resource Manager, és egy másik hitelesítési mechanizmus, mint a REST API-k az API-kezelő entitások kezelésére. Ebben a szakaszban található lépések ismertetik, hogyan hitelesítheti az Azure Resource Manager-kérelmeket. További információt az [Azure Resource Manager-kérelmek hitelesítése című témakörben talál.](/rest/api/index)

Az Azure Resource Managert használó erőforrásokon végzett összes feladatot hitelesíteni kell az Azure Active Directoryval a következő lépések végrehajtásával:

-   Alkalmazás hozzáadása az Azure Active Directory-bérlőhöz.
-   Állítsa be a hozzáadott alkalmazás engedélyeit.
-   A kérelmek hitelesítéséhez az Azure Resource Manager token beszerezni.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az API Management szolgáltatáspéldányt tartalmazó előfizetés használatával keresse meg az **Azure Active Directory** (Azure Active Directory > Manage/App registrations) **alkalmazásregisztrációk** lapját.

    > [!NOTE]
    > Ha az Azure Active Directory alapértelmezett könyvtára nem látható a fiókja számára, forduljon az Azure-előfizetés rendszergazdájához, hogy megadja a szükséges engedélyeket a fiókjához.

3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **Létrehozás** ablak a jobb oldalon jelenik meg. Itt adhatja meg az AAD alkalmazás releváns adatait.

4. Adjon nevet az alkalmazásnak.
5. Az alkalmazás típushoz válassza a **Natív**lehetőséget.
6. Adjon meg egy helyőrző URL-címet, például `http://resources` az **átirányítási URI-hoz,** mivel ez egy kötelező mező, de az értéket később nem használja. Az alkalmazás mentéséhez kattintson a jelölőnégyzetre.
7. Kattintson **a Létrehozás gombra.**

### <a name="add-an-application"></a>Alkalmazás hozzáadása

1. Az alkalmazás létrehozása után kattintson az **API-engedélyek**elemre.
2. Kattintson **a + Engedély hozzáadása gombra.**
4. Nyomja **le a Microsoft API-k kiválasztásával**.
5. Válassza az **Azure Service Management**lehetőséget.
6. Nyomja **le a Kijelölés gombot.**

    ![Engedélyek hozzáadása](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kattintson a **Delegált engedélyek** elemre az újonnan hozzáadott alkalmazás mellett, jelölje be az **Access Azure Service Management (előzetes verzió) jelölőnégyzetet.**
8. Nyomja **le a Kijelölés gombot.**
9. Kattintson **az Engedélyek megadása gombra.**

### <a name="configuring-your-app"></a>Az alkalmazás konfigurálása

A biztonsági mentést generáló és restaurálandó API-k hívása előtt be kell szereznie egy jogkivonatot. A következő példa a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet csomagot használja a jogkivonat beolvasásához.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Cserélje `{tenant id}` `{application id}`ki `{redirect uri}` a , , és a következő utasításokat használja:

1. Cserélje `{tenant id}` le a létrehozott Azure Active Directory-alkalmazás bérlői azonosítóját. Az azonosítót az **Alkalmazásregisztrációk** -> **végpontjai**elemre kattintva érheti el.

    ![Végpontok][api-management-endpoint]

2. Cserélje `{application id}` le a **Beállítások** lapra való navigálással megkapandó értékre.
3. Cserélje `{redirect uri}` le az Azure Active **Directory-alkalmazás Átirányítási URI-k** lapján szereplő értékre.

    Az értékek megadása után a példakódnak a következő példához hasonló jogkivonatot kell visszaadnia:

    ![Jogkivonat][api-management-arm-token]

    > [!NOTE]
    > A token egy bizonyos időszak után lejár. Új jogkivonat létrehozásához hajtsa végre újra a kódmintát.

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek hívása

A REST API-k [API-kezelő szolgáltatás - Biztonsági mentés](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) i és [Api-kezelési szolgáltatás – Visszaállítás.](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

A következő szakaszokban ismertetett "biztonsági mentési és visszaállítási" műveletek hívása előtt állítsa be a REST-hívás engedélyezési kérelem fejlécét.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>API-felügyeleti szolgáltatás biztonsági szolgálatának biztonsági szolgálata

Az API Management szolgáltatás biztonsági rendszerének biztonsági és biztonsági szolgálata a következő HTTP-kérést adja ki:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

ahol:

-   `subscriptionId`- Annak az előfizetésnek az azonosítója, amely rendelkezik a biztonsági másolatot próbál tartalmazó API Management szolgáltatásról
-   `resourceGroupName`- az Azure API Management szolgáltatás erőforráscsoportjának neve
-   `serviceName`- annak az API Management szolgáltatásnak a neve, amelyről a létrehozáskor megadott biztonsági másolatot készít
-   `api-version`- csere`2018-06-01-preview`

A kérelem törzsében adja meg a cél Azure storage-fiók nevét, a hozzáférési kulcsot, a blobtároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Állítsa a `Content-Type` kérelemfejléc értékét `application/json`a értékre.

A biztonsági mentés egy hosszú ideig futó művelet, amely több mint egy percet is igénybe vehet. Ha a kérés sikeres volt, és a `202 Accepted` biztonsági mentési `Location` folyamat megkezdődött, egy fejléccel rendelkező válaszállapotkódot kap. A "GET" kérelmek et `Location` a fejlécben lévő URL-címre a művelet állapotának megismeréséhez. Amíg a biztonsági mentés folyamatban van, továbbra is "202 Elfogadott" állapotkódot kap. A válaszkód `200 OK` a biztonsági mentési művelet sikeres befejezését jelzi.

Biztonsági mentési vagy visszaállítási kérelem készítésekor vegye figyelembe az alábbi korlátozásokat:

-   **A** kérelemtörzsben megadott **tárolónak léteznie kell**.
-   Amíg a biztonsági mentés folyamatban van, **kerülje a felügyeleti módosításokat a szolgáltatásban,** például a Termékváltozat frissítését vagy visszaminősítését, a tartománynév módosítását stb.
-   A biztonsági mentés visszaállítása **csak 30 napig garantált** a létrehozása óta.
-   **Az** elemzési jelentések létrehozásához használt használati adatok **nem szerepelnek** a biztonsági mentésben. Az [Azure API Management REST API-val][azure api management rest api] rendszeres időközönként lekérheti az elemzési jelentéseket a megőrzésérdekében.
-   Ezenkívül a következő elemek nem képezik a biztonsági mentési adatok részét: egyéni tartomány TLS/SSL tanúsítványok és az ügyfél által feltöltött köztes vagy főtanúsítványok, a fejlesztői portál tartalma és a virtuális hálózati integráció beállításai.
-   A szolgáltatás biztonsági mentések végrehajtásának gyakorisága befolyásolja a helyreállítási pont célját. Minimalizálása érdekében azt javasoljuk, hogy rendszeres biztonsági mentéseket hajtson végre, és igény szerinti biztonsági mentéseket hajtson végre az API Management szolgáltatás módosítása után.
-   **A** szolgáltatás konfigurációján végrehajtott módosítások (például API-k, házirendek és fejlesztői portálmegjelenések) a biztonsági mentés iművelete során **kizárhatók a biztonsági mentésből, és elvesznek.**
-   **Engedélyezze** a hozzáférést a vezérlősíkról az Azure Storage-fiókhoz, ha a [tűzfal][azure-storage-ip-firewall] engedélyezve van. Az ügyfélnek meg kell nyitnia az [Azure API Management Control Plane IP-címeit][control-plane-ip-address] a tárfiókban a biztonsági mentéshez vagy a visszaállításhoz. 

> [!NOTE]
> Ha egy API Management szolgáltatásból próbál biztonsági mentést/visszaállítást végezni egy olyan tárfiók használatával, amelyen engedélyezve van a [tűzfal,][azure-storage-ip-firewall] ugyanabban az Azure-régióban, akkor ez nem fog működni. Ennek az az oka, hogy az Azure Storage-nak érkező kérelmek nem snated egy nyilvános IP-cím compute > (Azure Api Management vezérlő sík). Régiók közötti tárolási kérelem lesz SNATed.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>API-felügyeleti szolgáltatás visszaállítása

Ha egy API Management szolgáltatást szeretne visszaállítani egy korábban létrehozott biztonsági másolatból, tegye a következő HTTP-kérelmet:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

ahol:

-   `subscriptionId`- Annak az előfizetésnek az azonosítója, amely azt az API Management szolgáltatást tartalmazza, amelybe biztonsági másolatot készít,
-   `resourceGroupName`- annak az erőforráscsoportnak a neve, amely az Azure API Management szolgáltatást tartalmazza, amelybe biztonsági másolatot készít
-   `serviceName`- az API Management szolgáltatás nak a létrehozáskor megadott helyére történő visszaállítása
-   `api-version`- csere`2018-06-01-preview`

A kérelem törzsében adja meg a biztonsági másolat helyét. Ez azt, hogy adja hozzá az Azure storage-fiók nevét, a hozzáférési kulcsot, a blobtároló nevét és a biztonsági másolat nevét:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Állítsa a `Content-Type` kérelemfejléc értékét `application/json`a értékre.

A visszaállítás egy hosszú ideig futó művelet, amely akár 30 vagy több percet is igénybe vehet. Ha a kérelem sikeres volt, és a `202 Accepted` visszaállítási folyamat `Location` megkezdődött, egy fejléccel ellátott válaszállapotkódot kap. A "GET" kérelmek et `Location` a fejlécben lévő URL-címre a művelet állapotának megismeréséhez. Amíg a visszaállítás folyamatban van, továbbra is megkapja a "202 Elfogadott" állapotkódot. A visszaállítási `200 OK` művelet sikeres befejezését jelző válaszkód.

> [!IMPORTANT]
> A visszaállított szolgáltatás **termékváltozatának** **meg kell egyeznie** a visszaállított biztonsági rendszer termékváltozatával.
>
> **A** szolgáltatás konfigurációján végrehajtott módosítások (például API-k, házirendek, fejlesztői portál megjelenése) a visszaállítási művelet folyamatban van, **felülírhatók.**

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> A biztonsági mentési és visszaállítási műveletek a PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) és [_a Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) parancsokkal is elvégezhetők.

## <a name="next-steps"></a>További lépések

Tekintse meg a következő forrásokat a biztonsági mentési/visszaállítási folyamat különböző forgatókönyveihez.

-   [Azure API-felügyeleti fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Az API Management biztonsági mentésének és helyreállításának automatizálása a Logic Apps használatával](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: A konfiguráció](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    biztonsági mentése és visszaállítása_A Stuart által részletezett megközelítés nem felel meg a hivatalos útmutatásnak, de érdekes._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
