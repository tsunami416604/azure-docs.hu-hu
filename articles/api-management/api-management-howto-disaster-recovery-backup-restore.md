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
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: e729d163428723d099157ad62d1c89a7ed917900
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711429"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vészhelyreállítás szolgáltatás biztonsági mentése és visszaállítása az Azure API Management szolgáltatásban

Közzététele és kezelése az API-k Azure API Management szolgáltatáson keresztül, akkor még kihasználhatja a hibatűrés és infrastruktúra alapú képességekkel, amelyek egyébként tervezési megvalósítását és kezelését manuálisan kellett. Az Azure platform csökkenti a lehetséges hibák költségeinek töredékéért nagy része.

Helyreállítani a rendelkezésre állási problémák, amelyek befolyásolják a régiót, amelyben az API Management szolgáltatás üzemelteti, készen áll a szolgáltatás egy másik régióban pótlására bármikor lehet. A rendelkezésre állási és helyreállítási idő célok függően érdemes lefoglalni egy vagy több régióban a biztonsági mentési szolgáltatás. Kivonásával is próbálkozhat a konfiguráció és tartalom szinkronban vannak a aktív szolgáltatás fenntartása érdekében. A szolgáltatás "biztonsági mentése és visszaállítása" funkció a vészhelyreállítási stratégia megvalósításához szükséges építőelem biztosít.

Ez az útmutató bemutatja, hogyan Azure Resource Manager-kérések hitelesítéséhez. Azt is bemutatja, hogyan biztonsági mentése és visszaállítása az API Management szolgáltatáspéldányok.

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
    > Ha az Azure Active Directory alapértelmezett címtár nem látható a fiókja számára, forduljon az Azure-előfizetés rendszergazdája, adja meg a szükséges engedélyeket a fiókhoz.
3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **létrehozás** ablak jobb oldalt megjelenik. Ez hol adja meg az AAD-alkalmazás vonatkozó adatokat.
4. Adjon meg egy nevet az alkalmazásnak.
5. Az alkalmazás típusának válassza **natív**.
6. Adja meg például egy helyőrző URL-címet `http://resources` számára a **átirányítási URI-t**szerint kötelező kitölteni, de az értéket később nem használható. Jelölje be a jelölőnégyzetet az alkalmazás mentéséhez.
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

Hívja meg, hogy hozza létre a biztonsági mentést, és állítsa vissza az API-k, mielőtt kell egy token beszerzéséhez. Az alábbi példában a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot kéri le a jogkivonatot.

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

Cserélje le `{tenant id}`, `{application id}`, és `{redirect uri}` az alábbi utasítások szerint:

1. Cserélje le `{tenant id}` a létrehozott Azure Active Directory-alkalmazás Bérlőazonosítója. Az azonosító eléréséhez kattintson **alkalmazásregisztrációk** -> **végpontok**.

    ![Végpontok][api-management-endpoint]
2. Cserélje le `{application id}` a értékkel, úgy ellenőrizheti, hogy a **beállítások** lapot.
3. Cserélje le a `{redirect uri}` értékkel a **átirányítási URI-k** az Azure Active Directory-alkalmazás lapján.

    Ha az érték meg van adva, a példakód kell visszaadnia egy tokent az alábbi példához hasonló:

    ![Jogkivonat][api-management-arm-token]

    > [!NOTE]
    > A jogkivonat a előfordulhat, hogy bizonyos idő elteltével lejár. Hajtsa végre újra az új token létrehozása a kódmintát.

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek meghívása

A REST API-k [Api Management szolgáltatás – biztonsági mentés](/rest/api/apimanagement/apimanagementservice/backup) és [Api Management szolgáltatás - visszaállítási](/rest/api/apimanagement/apimanagementservice/restore).

Előtt hívja meg a következő szakaszok ismertetik a "biztonsági mentés és visszaállítás" műveletek, állítsa be a engedélyezési kérés fejlécében a REST-hívás.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Készítsen biztonsági másolatot egy API Management-szolgáltatás

Biztonsági mentése egy API Management szolgáltatási probléma a következő HTTP-kérelem:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Ahol:

* `subscriptionId` – Az API Management szolgáltatás próbál készítsen biztonsági másolatot tartalmazó előfizetés azonosítója
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

Biztonsági mentés egy hosszú ideig futó művelet végrehajtásához több mint egy percet is igénybe vehet.  Ha a kérelem sikeres volt, és a biztonsági mentési folyamatának megkezdését, kap egy `202 Accepted` válasz állapotkódja a egy `Location` fejléc.  Győződjön meg arról, a "GET" kérelmek az URL-címét a `Location` fejléc a művelet állapotának megállapítása. A biztonsági mentés van folyamatban, miközben továbbra is megjelenik a "202-es elfogadva" állapotkódot. Válaszkód `200 OK` azt jelzi, hogy a biztonsági mentési művelet sikeres befejezését.

Amikor egy biztonsági mentési kérelmet, vegye figyelembe a következő korlátozásokkal:

* **Tároló** a kérelem törzsében megadott **léteznie kell**.
* Biztonsági mentés van folyamatban, amíg **elkerülése érdekében a service management változásai** például Termékváltozat frissítése vagy alacsonyabb szintű, módosítsa a tartománynevet, és egyéb.
* Visszaállítása egy **biztonsági mentés csak 30 napig garantáltan** időpontjában a létrehozása óta.
* **Használati adatok** elemzési jelentések készítéséhez **nem szerepeltetett** a biztonsági mentéshez. Használat [Azure API Management REST API] [ Azure API Management REST API] való rendszeres időközönkénti lekérése az elemzési jelentésekkel, az adatvesztés megelőzése érdekében.
* A gyakoriságot, amellyel a szolgáltatás biztonsági mentések végrehajtása a helyreállításipont-célkitűzés hatással. Azt minimalizálása érdekében javasoljuk a végrehajtási rendszeres biztonsági mentést, és igény szerinti biztonsági mentéseket hajt végre, az API Management szolgáltatás módosítása után.
* **Módosítások** biztonsági mentés közben a szolgáltatás konfigurációját, (például API-k, szabályzatok és fejlesztői portál megjelenésének) végzett művelet van folyamatban **elvesznek, és előfordulhat, hogy ki kell zárni a biztonsági mentés**.

### <a name="step2"> </a>API Management szolgáltatás visszaállítása

API Management szolgáltatás biztonsági másolatból történő visszaállítását egy korábban létrehozott, győződjön meg a következő HTTP-kérelem:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Ahol:

* `subscriptionId` -Az API Management-szolgáltatás, hogy visszaállítása biztonsági másolatból történő társításához előfizetés azonosítója
* `resourceGroupName` – az Azure API Management szolgáltatás, hogy visszaállítása biztonsági másolatból történő társításához az erőforráscsoport neve
* `serviceName` – az API Management szolgáltatás visszaállított be a létrehozáskor megadott neve
* `api-version` -lecserélése `2018-06-01-preview`

A kérés törzsét adja meg a biztonsági mentési fájl helyét. Ez azt jelenti, hogy adja hozzá az Azure storage-fiók neve, a hozzáférési kulcsot, a blobtároló neve és a biztonsági másolatának neve:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Az értékét állítsa be a `Content-Type` a kérelem fejlécében `application/json`.

Visszaállítás egy hosszú ideig futó művelet, amely előfordulhat, hogy 30 vagy több percig eltarthat végrehajtásához. Ha a kérelem sikeres volt, és a visszaállítási folyamat kezdődött, kap egy `202 Accepted` válasz állapotkódja a egy `Location` fejléc. Győződjön meg arról, a "GET" kérelmek az URL-címét a `Location` fejléc a művelet állapotának megállapítása. Amíg a visszaállítás folyamatban van, továbbra is megjelenik a "202-es elfogadva" állapotkódot. Válaszkód `200 OK` azt jelzi, hogy a visszaállítási művelet sikeres befejezését.

> [!IMPORTANT]
> **A Termékváltozat** a szolgáltatás be visszaállított **egyeznie kell** a Termékváltozat a visszaállítandó biztonsági másolat szolgáltatás.
>
> **Módosítások** végzett a szolgáltatási konfiguráció (például API-k, szabályzatok, fejlesztői portál megjelenésének) visszaállítása közben a művelet van folyamatban **felülíródnak**.

<!-- Dummy comment added to suppress markdown lint warning -->

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
