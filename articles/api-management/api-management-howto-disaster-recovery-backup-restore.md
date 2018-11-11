---
title: Megvalósítása vész-helyreállítási használatával biztonsági mentése és visszaállítása az Azure API Management |} A Microsoft Docs
description: 'Útmutató: biztonsági mentés és vészhelyreállítás az Azure API Management végrehajtásához visszaállítása.'
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: apimpm
ms.openlocfilehash: 0dc7e8836f1e6a11c44f5e0f337015cac53a92d4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51252802"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vészhelyreállítás szolgáltatás biztonsági mentése és visszaállítása az Azure API Management szolgáltatásban

Kiválasztásával tehet közzé és felügyelhet az API-k Azure API Management szolgáltatáson keresztül tart számos hibatűrés és infrastruktúra alapú képességekkel, amelyek egyébként külön tervezéséhez, megvalósításához és kezeléséhez. Az Azure platform csökkenti a lehetséges hibák költségeinek töredékéért nagy része.

Helyreállítani a rendelkezésre állási problémák hatással lenne a régió, ahol az API Management szolgáltatás üzemel, készen áll a szolgáltatás egy másik régióban lévő pótlására bármikor kell lennie. Attól függően, a rendelkezésre állási céljait és a helyreállítási időre vonatkozó célkitűzés érdemes lefoglalni egy biztonsági mentési szolgáltatás egy vagy több régióban, majd próbálja meg a konfiguráció és tartalom szinkronban vannak a aktív szolgáltatás karbantartása. A szolgáltatás "biztonsági mentése és visszaállítása" funkció a vészhelyreállítási stratégia megvalósításához szükséges építőelem biztosít.

Ez az útmutató ismerteti, hogyan hitelesítheti az Azure Resource Manager-kéréseket, és hogyan biztonsági mentése és visszaállítása az API Management szolgáltatáspéldányok.

> [!NOTE]
> A biztonsági mentése és visszaállítása egy API Management-szolgáltatáspéldány vész-helyreállítási folyamatot is használható átmeneti hasonló forgatókönyvek esetén az API Management szolgáltatáspéldányok replikálásához.
>
> Minden egyes biztonsági másolat 30 nap után lejár. Visszaállítás biztonsági másolatból, a 30 napos lejárati időszak lejárta után kísérli meg, ha a visszaállítás sikertelen lesz, és egy `Cannot restore: backup expired` üzenet.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Hitelesítő Azure Resource Manager-kérelmek

> [!IMPORTANT]
> A REST API, a biztonsági mentés és visszaállítás Azure Resource Managert használja, és az API Management entitások kezeléséhez, mint a REST API-k másik hitelesítési mechanizmussal rendelkezik. A jelen szakaszban ismertetett lépések bemutatják, hogyan Azure Resource Manager-kérések hitelesítéséhez. További információkért lásd: [hitelesítése az Azure Resource Manager-kérelmek](https://msdn.microsoft.com/library/azure/dn790557.aspx).

Minden olyan feladat hivatkozása, amelyeket Ön az erőforrások az Azure Resource Manager használatával kell hitelesíteni, az Azure Active Directoryval az alábbi lépéseket követve:

* Vegye fel egy alkalmazást az Azure Active Directory-bérlőhöz.
* Az alkalmazáshoz hozzáadott engedélyeket.
* A token lekérése az Azure Resource Manager-kérelmek hitelesítéséhez.

### <a name="create-an-azure-active-directory-application"></a>Az Azure Active Directory-alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az előfizetés, amely tartalmazza az API Management szolgáltatáspéldányt, nyissa meg azt a **alkalmazásregisztrációk** lapján **Azure Active Directory** (Azure Active Directory > kezelés/alkalmazásregisztrációk).

    > [!NOTE]
    > Ha nem látható a fiókja számára az Azure Active Directory alapértelmezett címtár, lépjen kapcsolatba az Azure-előfizetés rendszergazdája, adja meg a szükséges engedélyeket a fiókhoz.
3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **létrehozás** ablak jobb oldalt megjelenik. Ez hol adja meg az AAD-alkalmazás vonatkozó adatokat.
4. Adjon meg egy nevet az alkalmazásnak.
5. Az alkalmazás típusának válassza **natív**.
6. Adja meg például egy helyőrző URL-címet `http://resources` számára a **átirányítási URI-t**szerint kötelező kitölteni, de az értéket később nem használja. Jelölje be a jelölőnégyzetet az alkalmazás mentéséhez.
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-application"></a>Alkalmazás hozzáadása

1. Az alkalmazás létrehozása után kattintson a **beállítások**.
2. Kattintson a **szükséges engedélyek**.
3. Kattintson a **+ Hozzáadás**.
4. Nyomja meg **API kiválasztása**.
5. Válasszon **Windows** **Azure Service Management API**.
6. Nyomja meg **kiválasztása**. 

    ![Engedélyek hozzáadása](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kattintson a **delegált engedélyek** az újonnan hozzáadott alkalmazás mellett jelölje be a **hozzáférés az Azure Service Management (előzetes verzió)**.
8. Nyomja meg **kiválasztása**.
9. Kattintson a **engedélyeket**.

### <a name="configuring-your-app"></a>Az alkalmazás konfigurálása

Előtt hozza létre a biztonsági mentést, és állítsa vissza az API-k meghívása, a jogkivonat beszerzéséhez szükséges. Az alábbi példában a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot kéri le a jogkivonatot.

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

Cserélje le `{tentand id}`, `{application id}`, és `{redirect uri}` az alábbi utasítások szerint:

1. Cserélje le `{tenant id}` a létrehozott Azure Active Directory-alkalmazás bérlőazonosítója. Az azonosító eléréséhez kattintson **alkalmazásregisztrációk** -> **végpontok**.

    ![Végpontok][api-management-endpoint]
2. Cserélje le `{application id}` a értékkel, úgy ellenőrizheti, hogy a **beállítások** lapot.
3. Cserélje le a `{redirect uri}` értékkel a **átirányítási URI-k** az Azure Active Directory-alkalmazás lapján.

    Ha az érték meg van adva, a példakód kell visszaadnia egy tokent az alábbi példához hasonló:

    ![Jogkivonat][api-management-arm-token]

    > [!NOTE]
    > A jogkivonat a előfordulhat, hogy bizonyos idő elteltével lejár. Hajtsa végre újra az új token létrehozása a kódmintát.

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek meghívása

A REST API-k [Api Management szolgáltatás – biztonsági mentés](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_backup) és [Api Management szolgáltatás - visszaállítási](https://docs.microsoft.com/rest/api/apimanagement/apimanagementservice/apimanagementservice_restore).

Előtt hívja meg a következő szakaszok ismertetik a "biztonsági mentés és visszaállítás" műveletek, állítsa be a engedélyezési kérés fejlécében a REST-hívás.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Készítsen biztonsági másolatot egy API Management-szolgáltatás
Biztonsági mentése egy API Management szolgáltatási probléma a következő HTTP-kérelem:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Ahol:

* `subscriptionId` – az API Management szolgáltatás próbált készítsen biztonsági másolatot tartalmazó előfizetés azonosítója
* `resourceGroupName` – az Azure API Management-szolgáltatás, az erőforráscsoport neve
* `serviceName` – az API Management-szolgáltatás neve végez biztonsági másolatának létrehozása idején már megadott
* `api-version` -lecserélése `2018-06-01-preview`

A kérés törzsét adja meg a céloldali Azure storage-fiók neve, a hozzáférési kulcsot, a blobtároló neve és a biztonsági másolatának neve:


```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Az értékét állítsa be a `Content-Type` a kérelem fejlécében `application/json`.

Biztonsági mentés egy hosszú ideig futó művelet végrehajtása több percet is igénybe vehet.  Ha a kérelem sikeres volt, és a biztonsági mentési folyamatból elindítva, kap egy `202 Accepted` válasz állapotkódja a egy `Location` fejléc.  Győződjön meg arról, a "GET" kérelmek az URL-címét a `Location` fejléc a művelet állapotának megállapítása. A biztonsági mentés van folyamatban, miközben továbbra is megjelenik a "202-es elfogadva" állapotkódot. Válaszkód `200 OK` azt jelzi, hogy a biztonsági mentési művelet sikeres befejezését.

Vegye figyelembe a következő korlátozásokkal, amikor egy biztonsági mentési kérelmet.

* **Tároló** a kérelem törzsében megadott **léteznie kell**.
* Biztonsági mentés folyamatban, amíg **ne próbáljon szolgáltatás felügyeleti műveleteket sem** Termékváltozat frissítése vagy alacsonyabb szintű, a tartomány nevét módosítása, például stb.
* Visszaállítása egy **biztonsági mentés csak 30 napig garantáltan** időpontjában a létrehozása óta.
* **Használati adatok** elemzési jelentések készítéséhez **lehetőség nem része** a biztonsági mentéshez. Használat [Azure API Management REST API] [ Azure API Management REST API] való rendszeres időközönkénti lekérése az elemzési jelentésekkel, az adatvesztés megelőzése érdekében.
* A gyakoriságot, amellyel a szolgáltatás biztonsági mentések végrehajtása a helyreállításipont-célkitűzés hatással. Azt minimalizálása érdekében a javaslat van rendszeres biztonsági mentések végrehajtására, valamint az API Management szolgáltatás a fontos módosítások elvégzése után igény szerinti biztonsági mentések végrehajtásához.
* **Módosítások** biztonsági mentés közben a szolgáltatás konfigurációs (például API-k, szabályzatok, fejlesztői portál megjelenésének) végzett művelet van folyamatban **nem része lehet a biztonsági mentéshez, és ezért elvész**.

### <a name="step2"> </a>API Management szolgáltatás visszaállítása
Az API Management visszaállítása egy korábban létrehozott biztonsági másolat szolgáltatás győződjön meg arról, a következő HTTP-kérelem:

```
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Ahol:

* `subscriptionId` -a visszaállítani kívánt biztonsági másolat az API Management szolgáltatást tartalmazó előfizetés azonosítója
* `resourceGroupName` -az erőforráscsoport, amely tartalmazza a visszaállítani kívánt biztonsági másolat az Azure API Management-szolgáltatás neve
* `serviceName` – az API Management szolgáltatás visszaállított be a létrehozása idején már megadott neve
* `api-version` -lecserélése `2018-06-01-preview`

A kérelem törzsében adja meg a biztonsági mentési fájl helyét, amely, az Azure storage-fiók neve, hozzáférési kulcsot, blobtároló neve és biztonsági másolatának neve:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Az értékét állítsa be a `Content-Type` a kérelem fejlécében `application/json`.

Visszaállítás egy hosszú ideig futó művelet, amely előfordulhat, hogy 30 vagy több percig eltarthat végrehajtásához. Ha a kérelem sikeres volt, és a visszaállítási folyamat elindítva, kap egy `202 Accepted` válasz állapotkódja a egy `Location` fejléc. Győződjön meg arról, a "GET" kérelmek az URL-címét a `Location` fejléc a művelet állapotának megállapítása. Amíg a visszaállítás folyamatban van, továbbra is megjelenik a "202-es elfogadva" állapotkódot. Válaszkód `200 OK` azt jelzi, hogy a visszaállítási művelet sikeres befejezését.

> [!IMPORTANT]
> **A Termékváltozat** a szolgáltatás be visszaállított **egyeznie kell** a Termékváltozat a visszaállítandó biztonsági másolat szolgáltatás.
>
> **Módosítások** végzett a szolgáltatási konfiguráció (például API-k, szabályzatok, fejlesztői portál megjelenésének) visszaállítása közben a művelet van folyamatban **felülíródnak**.

> [!NOTE]
> Biztonsági mentési és visszaállítási műveletek is elvégezhető a PowerShell-lel *Backup-azurermapimanagement parancsmagok* és *Restore-azurermapimanagement parancsmagok* parancsokat jelölik.

## <a name="next-steps"></a>További lépések

Tekintse meg a biztonsági mentési vagy visszaállítási folyamat különböző forgatókönyvek a következő forrásokat.

* [Az Azure API Management-fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatizálhatja API Management biztonsági mentése és visszaállítása a Logic Apps használatával](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Az Azure API Management: Biztonsági mentése és visszaállítása konfigurációs](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *által Stuart részletes megközelítés nem egyezik meg a hivatalos útmutatást, de ez érdekes.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
