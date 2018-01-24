---
title: "Megvalósítása vész-helyreállítási használatával biztonsági mentése és visszaállítása az Azure API Management |} Microsoft Docs"
description: "Útmutató: biztonsági mentése és visszaállítása az Azure API Management katasztrófa utáni helyreállítás végrehajtásához."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: apimpm
ms.openlocfilehash: 3fcd2fc4162cfbf549be979e15745934c2e4c6ff
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vész-helyreállítási szolgáltatás biztonsági mentéssel implementálja, és az Azure API Management visszaállítása

Közzétételét és kezelését az API-kat Azure API Management szolgáltatáson keresztül kiválasztásával készítésének sok hibatűrést és infrastruktúra képességeket, amelyeket egyébként külön kialakítása, megvalósítása és kezelése. Az Azure platformon csökkenti a nagy része egy azon részét, a költség, a lehetséges hibák.

A régió, ahol az API Management szolgáltatás tárolása érintő rendelkezésre állási problémák helyreállítása, készen áll a szolgáltatás egy másik régióban pótlására bármikor kell lennie. Attól függően, hogy a rendelkezésre állási célokat és a helyreállítási idő célkitűzése érdemes lefoglalni egy biztonsági mentési szolgáltatás egy vagy több régióban, majd próbálja meg a konfigurációs és a tartalom szinkronban vannak a aktív szolgáltatás karbantartása. A "biztonsági mentése és visszaállítása" szolgáltatás a szükséges építőelem biztosít végrehajtási vész-helyreállítási stratégiát.

Ez az útmutató bemutatja az Azure Resource Manager-kérelmek hitelesítéséhez szükséges, és biztonsági mentése és visszaállítása az API Management szolgáltatáspéldány.

> [!NOTE]
> A biztonsági mentése és visszaállítása egy API-kezelés szolgáltatáspéldány vész-helyreállítási folyamatot a API-kezelés szolgáltatás példányainak ahhoz hasonló forgatókönyvek esetén átmeneti replikálására is használható.
>
> Minden egyes biztonsági másolat 30 nap múlva lejár. Ha a biztonsági másolat visszaállítása a 30 napos lejárati időszak lejárta után kísérli meg, a visszaállítás sikertelen lesz, és egy `Cannot restore: backup expired` üzenet.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Hitelesítő Azure Resource Manager-kérelmek

> [!IMPORTANT]
> A REST API-t a biztonsági mentési és visszaállítási Azure Resource Managert használja, és egy különböző hitelesítési eljárást, mint a REST API-k az API Management entitások kezeléséhez. Ebben a szakaszban a lépések azt ismertetik, hogyan Azure Resource Manager-kérelmek hitelesítéséhez szükséges. További információkért lásd: [kérelmek hitelesítéséhez az Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Minden olyan feladat, amelyeknek a erőforrásokat az Azure Resource Manager használatával kell hitelesíteni, az Azure Active Directoryban az alábbi lépéseket követve:

* Hozzáadhat egy alkalmazást az Azure Active Directory-bérlő.
* Állítsa be az alkalmazás hozzáadott.
* A token beolvasása az Azure Resource Manager kérések hitelesítése.

### <a name="create-an-azure-active-directory-application"></a>Egy Azure Active Directory-alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az előfizetés, amely tartalmazza az API Management szolgáltatáspéldány, nyissa meg azt a **App regisztrációk** fülre.

    > [!NOTE]
    > Ha az Azure Active Directory alapértelmezett címtár nem látható a fiókjához, lépjen kapcsolatba a fiókhoz a szükséges engedélyek megadására az Azure-előfizetés rendszergazdája.
3. Kattintson az **Új alkalmazásregisztráció** elemre.

    A **létrehozása** ablak jobb oldalt megjelenik. Ez az aad-ben app vonatkozó információkat rögzítheti.
4. Adjon meg egy nevet az alkalmazásnak.
5. Az alkalmazástípus kiválasztása **natív**.
6. Írjon be egy helyőrző URL-címet, például `http://resources` a a **átirányítási URI-**, mert a mezőt kötelező kitölteni, de az érték nem használatos később. Jelölje be a jelölőnégyzetet az alkalmazás menteni.
7. Kattintson a **Create** (Létrehozás) gombra.

### <a name="add-an-application"></a>Alkalmazás felvétele

1. Az alkalmazás létrehozása után kattintson **beállítások**.
2. Kattintson a **szükséges engedélyek**.
3. Kattintson a **+ Hozzáadás**.
4. Nyomja le az **API kiválasztása**.
5. Válasszon **Windows** **Azure Szolgáltatáskezelési API**.
6. Nyomja le az **válasszon**. 

    ![Engedélyek hozzáadása](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Kattintson a **delegált engedélyek** az újonnan hozzáadott alkalmazás mellett jelölje be a **Access Azure Service Management (preview)**.
8. Nyomja le az **válasszon**.

### <a name="configuring-your-app"></a>Az alkalmazás konfigurálása

Előtt hívja az API-kat, hogy a biztonsági mentés és állítsa vissza azt, fontos szolgáltatáshitelesítést egy token. Az alábbi példában a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-csomagot a jogkivonatot lekérdezni.

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
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Cserélje le `{tentand id}`, `{application id}`, és `{redirect uri}` a következő útmutatás szerint:

1. Cserélje le `{tenant id}` , bérlői azonosító: a létrehozott Azure Active Directory-alkalmazást. Az azonosító eléréséhez kattintson **App regisztrációk** -> **végpontok**.

    ![Végpontok][api-management-endpoint]
2. Cserélje le `{application id}` való elérhetővé értékét a **beállítások** lap.
3. Cserélje le a URL-CÍMÉT a **átirányítási URI-azonosítók** lapon látható az Azure Active Directory-alkalmazást.

    Ha az értékek vannak megadva, a Kódpélda kell visszaadnia jogkivonat a következőhöz hasonló:

    ![Jogkivonat][api-management-arm-token]

## <a name="calling-the-backup-and-restore-operations"></a>A biztonsági mentési és visszaállítási műveletek meghívása

Előtt hívja meg a "biztonsági mentése és visszaállítása" műveleteket az alábbi szakaszok ismertetik, állítsa be a REST-hívást a hitelesítési kérelem fejlécéhez.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"></a>Készítsen biztonsági másolatot egy API-kezelés szolgáltatás
Biztonsági mentése egy API-kezelés szolgáltatás a probléma a következő HTTP-kérelem:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Ahol:

* `subscriptionId`-próbál készítsen biztonsági másolatot az API Management szolgáltatást tartalmazó előfizetés azonosítója
* `resourceGroupName`-karakterlánc 'Api - alapértelmezett-{szolgáltatás-régió}' formájában ahol `service-region` azonosítja az Azure-régió, ahol az API Management szolgáltatást, akkor próbált biztonsági mentési üzemelteti, például`North-Central-US`
* `serviceName`-a szolgáltatás nevét, az API Management végez biztonsági másolatának meg egyszerre, ahol létrehozták
* `api-version`-cseréje`2014-02-14`

A kérelem törzsében adja meg a cél az Azure storage-fiók neve, a hozzáférési kulcsot, a blob-tároló neve és a biztonsági másolat neve:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Állítsa a `Content-Type` a kérelem fejlécében `application/json`.

Biztonsági mentés egy hosszú ideig futó művelet, amely több percet is igénybe vehet.  Ha a kérelem sikeres volt, és a biztonsági mentési folyamat kezdeményezték, akkor kap egy `202 Accepted` a válasz állapotkódja egy `Location` fejléc.  Ellenőrizze a "GET" kéréseket az URL-címet a `Location` fejlécet a művelet állapotának megállapítása. A biztonsági mentés van folyamatban, miközben továbbra is megjelenik a '202 elfogadott' állapotkód. Válaszkód `200 OK` jelzi a biztonsági mentési művelet sikeres befejezését.

Vegye figyelembe a következő korlátozások vonatkoznak, amikor egy biztonsági mentési kérést.

* **Tároló** a kérés törzsében megadott **léteznie kell**.
* Biztonsági mentés folyamatban, amíg **nem szabadna megpróbálniuk a szolgáltatás felügyeleti műveleteket sem** SKU frissítése vagy alacsonyabb szintre való visszalépést, a tartomány kiszolgálónév-változás, például stb.
* A visszaállítás egy **biztonsági mentés csak 30 napig garantáltan** időpontjában a létrehozása óta.
* **Használati adatok** elemzési jelentések létrehozásához használt **nincs megadva** a biztonsági mentésben. Használjon [Azure API Management REST API] [ Azure API Management REST API] rendszeresen beolvasása az analytics-jelentések meg van őrizve.
* A gyakoriság, amellyel szolgáltatás biztonsági mentések végrehajtása a helyreállítási időkorlát hatással. Az minimalizálása érdekében a javaslat van rendszeres biztonsági mentések végrehajtására, valamint az API Management szolgáltatás fontos módosítások elvégzése után igény szerinti biztonsági mentést végez.
* **Módosítások** biztonsági mentés közben a szolgáltatás konfigurációs (például API-k, házirendek, fejlesztői portál megjelenését) végzett művelet van folyamatban **előfordulhat, hogy nem kell a biztonsági mentésben szereplő, és ezért elvész**.

### <a name="step2"></a>Egy API-kezelés szolgáltatás visszaállítása
Az API Management visszaállítása egy korábban létrehozott biztonsági másolat szolgáltatás ellenőrizze a következő HTTP-kérelem:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Ahol:

* `subscriptionId`-a biztonsági másolatból történő visszaállítását végzi az API Management szolgáltatást tartalmazó előfizetés azonosítója
* `resourceGroupName`-karakterlánc 'Api - alapértelmezett-{szolgáltatás-régió}' formájában ahol `service-region` azonosítja az Azure-régió, ahol az API Management szolgáltatás visszaállítását végzi, a biztonsági másolat tárolása, például`North-Central-US`
* `serviceName`-a API-kezelés szolgáltatás a visszaállítandó meg, ahol létrehozták egyszerre neve
* `api-version`-cseréje`2014-02-14`

A kérelem törzsében adja meg a biztonságimásolat-fájl helye, van, az Azure storage-fiók neve, a hozzáférési kulcsot, a blob-tároló neve és biztonsági másolat neve:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Állítsa a `Content-Type` a kérelem fejlécében `application/json`.

Visszaállítás egy hosszú ideig futó művelet, amely előfordulhat, hogy 30 vagy több percig tarthat befejezéséhez. Ha a kérelem sikeres volt, és a visszaállítási folyamat kezdeményezték, akkor kap egy `202 Accepted` a válasz állapotkódja egy `Location` fejléc. Ellenőrizze a "GET" kéréseket az URL-címet a `Location` fejlécet a művelet állapotának megállapítása. Amíg a helyreállítás van folyamatban, azután is megkapják az '202 elfogadott' állapotkód. Válaszkód `200 OK` jelzi a visszaállítási művelet sikeres befejezését.

> [!IMPORTANT]
> **A Termékváltozat** a szolgáltatás a visszaállítandó **egyeznie kell** a visszaállítandó biztonsági másolat szolgáltatás a Termékváltozat.
>
> **Módosítások** végzett a szolgáltatási konfiguráció (például API-k, házirendek, fejlesztői portál megjelenését) visszaállítása közben a művelet van folyamatban **felülíródnak**.
>
>

## <a name="next-steps"></a>További lépések
Tekintse meg a biztonsági mentési/visszaállítási folyamat két különböző forgatókönyvek a következő Microsoft-blogjaihoz.

* [Az Azure API Management fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Az Azure API Management: Biztonsági mentése, és konfiguráció visszaállítása](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Stuart által részletes módszer nem egyezik meg a hivatalos útmutatást, de érdemes.

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
