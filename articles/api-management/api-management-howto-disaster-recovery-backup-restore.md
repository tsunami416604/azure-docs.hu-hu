---
title: "Megvalósítása vész-helyreállítási használatával biztonsági mentése és visszaállítása az Azure API Management |} Microsoft Docs"
description: "Útmutató: biztonsági mentése és visszaállítása az Azure API Management katasztrófa utáni helyreállítás végrehajtásához."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 6f10be3c-f796-4a6c-bacd-7931b6aa82af
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 51ec88ae2a4f10b68c7d74324c3a3a25d2893891
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Vész-helyreállítási szolgáltatás biztonsági mentéssel implementálja, és az Azure API Management visszaállítása
Közzétételét és kezelését az API-kat Azure API Management szolgáltatáson keresztül kiválasztásával készítésének sok hibatűrést és infrastruktúra képességeket, amelyeket egyébként külön kialakítása, megvalósítása és kezelése. Az Azure platformon csökkenti a nagy része egy azon részét, a költség, a lehetséges hibák.

Rendelkezésre állási helyreállítás érintő a régió, ahol az API Management szolgáltatás tárolása, hogy a szolgáltatás egy másik régióban pótlására bármikor készen kell lennie. Attól függően, hogy a rendelkezésre állási célokat és a helyreállítási idő célkitűzése érdemes lefoglalni egy biztonsági mentési szolgáltatás egy vagy több régióban, majd próbálja meg a konfigurációs és a tartalom szinkronban vannak a aktív szolgáltatás karbantartása. A szolgáltatás biztonsági mentési és visszaállítási funkciót biztosít a szükséges építőelem végrehajtási vész-helyreállítási stratégiát.

Ez az útmutató bemutatja, hogyan Azure Resource Manager-kérelmek hitelesítéséhez szükséges, és hogyan biztonsági mentése és visszaállítása az API Management szolgáltatáspéldány.

> [!NOTE]
> A biztonsági mentése és visszaállítása egy API-kezelés szolgáltatáspéldány vész-helyreállítási folyamatot a API-kezelés szolgáltatás példányainak ahhoz hasonló forgatókönyvek esetén átmeneti replikálására is használható.
>
> Vegye figyelembe, hogy minden egyes biztonsági másolat 30 nap múlva lejár. Ha a biztonsági másolat visszaállítása a 30 napos lejárati időszak lejárta után kísérli meg, a visszaállítás sikertelen lesz, és egy `Cannot restore: backup expired` üzenet.
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Hitelesítő Azure Resource Manager-kérelmek
> [!IMPORTANT]
> A REST API-t a biztonsági mentési és visszaállítási Azure Resource Managert használja, és egy különböző hitelesítési eljárást, mint a REST API-k az API Management entitások kezeléséhez. Ebben a szakaszban a lépések azt ismertetik, hogyan Azure Resource Manager-kérelmek hitelesítéséhez szükséges. További információkért lásd: [kérelmek hitelesítéséhez az Azure Resource Manager](http://msdn.microsoft.com/library/azure/dn790557.aspx).
>
>

Minden olyan feladat, amelyeknek az Azure Resource Manager használatával erőforrások az Azure Active Directoryban az alábbi lépéseket követve hitelesíteni kell.

* Hozzáadhat egy alkalmazást az Azure Active Directory-bérlő.
* Állítsa be az alkalmazás hozzáadott.
* A token beolvasása az Azure Resource Manager kérések hitelesítése.

Az első lépés, ha az Azure Active Directory-alkalmazást. Jelentkezzen be a [klasszikus Azure portál](http://manage.windowsazure.com/) példányt, és keresse meg az előfizetést, amely tartalmazza az API-kezelés szolgáltatás a **alkalmazások** fülre az alapértelmezett Azure Active Directoryban.

> [!NOTE]
> Ha az Azure Active Directory alapértelmezett címtár nem látható a fiókjához, lépjen kapcsolatba a fiókhoz a szükséges engedélyek megadására az Azure-előfizetés rendszergazdája.

![Azure Active Directory-alkalmazás létrehozása][api-management-add-aad-application]

Kattintson a **Hozzáadás**, **a szerveztem által fejlesztett alkalmazás hozzáadása**, és válassza a **natív ügyfélalkalmazás**. Adjon meg egy leíró nevet, és kattintson a Tovább nyílra. Írjon be egy helyőrző URL-címet, például `http://resources` a a **átirányítási URI-**, mert a mezőt kötelező kitölteni, de az érték nem használatos később. Jelölje be a jelölőnégyzetet az alkalmazás menteni.

Ha az alkalmazás menti, kattintson **konfigurálása**, görgessen le a **egyéb alkalmazások engedélyei** szakaszt, és kattintson a **alkalmazás hozzáadása**.

![Engedélyek hozzáadása][api-management-aad-permissions-add]

Válassza ki **Windows** **Azure szolgáltatásfelügyeleti API** és a jelölőnégyzet bejelölésével vegye fel az alkalmazást.

![Engedélyek hozzáadása][api-management-aad-permissions]

Kattintson a **delegált engedélyek** mellett az újonnan hozzáadott **Windows** **Azure szolgáltatásfelügyeleti API** alkalmazást, jelölje be a **Azure szolgáltatás Management (preview)**, és kattintson a **mentése**.

![Engedélyek hozzáadása][api-management-aad-delegated-permissions]

Előtt hívja az API-kat, hogy a biztonsági mentés és állítsa vissza azt, fontos szolgáltatáshitelesítést egy token. Az alábbi példában a [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) nuget-csomagot a jogkivonatot lekérdezni.

```c#
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

Cserélje le `{tentand id}`, `{application id}`, és `{redirect uri}` az alábbi utasításokat.

Cserélje le `{tenant id}` együtt az imént létrehozott Azure Active Directory-alkalmazás a bérlő azonosítója. Az azonosító eléréséhez kattintson **végpontok megtekintése**.

![Végpontok][api-management-aad-default-directory]

![Végpontok][api-management-endpoint]

Cserélje le `{application id}` és `{redirect uri}` használatával a **ügyfél-azonosító** és az URL-CÍMÉT a **átirányítási URI-azonosítók** szakaszban az Azure Active Directory-alkalmazás **konfigurálása** fülre.

![Erőforrások][api-management-aad-resources]

Ha az értékek vannak megadva, a Kódpélda kell visszaadnia jogkivonatot az alábbi példához hasonló.

![Token][api-management-arm-token]

Előtt hívja meg az alábbi szakaszok ismertetik a biztonsági mentési és visszaállítási műveletek, állítsa be a REST-hívást a hitelesítési kérelem fejlécéhez.

```c#
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

## <a name="step1"></a>Készítsen biztonsági másolatot egy API-kezelés szolgáltatás
Biztonsági mentése egy API-kezelés szolgáltatás a probléma a következő HTTP-kérelem:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

Ahol:

* `subscriptionId`-a kívánt API Management szolgáltatást tartalmazó előfizetés azonosítója a biztonsági mentés
* `resourceGroupName`-karakterlánc 'Api - alapértelmezett-{szolgáltatás-régió}' formájában ahol `service-region` azonosítja az Azure-régió, ahol az API Management szolgáltatást, akkor próbált biztonsági mentési üzemelteti, pl.`North-Central-US`
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

Biztonsági mentés egy hosszú ideig futó művelet, amely több percet is igénybe vehet.  Ha a kérelem sikeres volt, és a biztonsági mentési folyamat kezdeményezett kapni fog egy `202 Accepted` a válasz állapotkódja egy `Location` fejléc.  Ellenőrizze a "GET" kéréseket az URL-címet a `Location` fejlécet a művelet állapotának megállapítása. Miközben folyamatban van a biztonsági mentés fog továbbra is megjelenik egy '202 elfogadott' állapotkód. Válaszkód `200 OK` jelzi a biztonsági mentési művelet sikeres befejezését.

Amikor egy biztonsági mentési kérést vegye figyelembe a következő korlátozások vonatkoznak.

* **Tároló** a kérés törzsében megadott **léteznie kell**.
* Biztonsági mentés folyamatban, amíg **nem szabadna megpróbálniuk a szolgáltatás felügyeleti műveleteket sem** SKU frissítése vagy alacsonyabb szintre való visszalépést, a tartomány kiszolgálónév-változás, például stb.
* A visszaállítás egy **biztonsági mentés csak 30 napig garantáltan** időpontjában a létrehozása óta.
* **Használati adatok** elemzési jelentések létrehozásához használt **nincs megadva** a biztonsági mentésben. Használjon [Azure API Management REST API] [ Azure API Management REST API] rendszeresen beolvasása az analytics-jelentések meg van őrizve.
* A gyakoriság, amellyel elvégezhető a szolgáltatás biztonsági mentések hatással lesz a helyreállításipont-célkitűzést. Minimalizálása érdekében azt javasoljuk a rendszeres biztonsági mentések végrehajtására, valamint a igény szerinti biztonsági mentést végez az API Management szolgáltatás fontos módosítások elvégzése után.
* **Módosítások** a szolgáltatás konfigurációs (pl. API-k, házirendek, fejlesztői portál megjelenését) biztonsági mentés közben végzett művelet van folyamatban **előfordulhat, hogy nem kell a biztonsági mentésben szereplő, és ezért elvész**.

## <a name="step2"></a>Egy API-kezelés szolgáltatás visszaállítása
Az API Management visszaállítása egy korábban létrehozott biztonsági másolat szolgáltatás ellenőrizze a következő HTTP-kérelem:

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

Ahol:

* `subscriptionId`-a biztonsági másolatból történő visszaállítását végzi az API Management szolgáltatást tartalmazó előfizetés azonosítója
* `resourceGroupName`-karakterlánc 'Api - alapértelmezett-{szolgáltatás-régió}' formájában ahol `service-region` pl. azonosítja az Azure-régió, ahol az API Management szolgáltatás visszaállítását végzi, a biztonsági másolat tárolása,`North-Central-US`
* `serviceName`-a API-kezelés szolgáltatás a visszaállítandó meg, ahol létrehozták egyszerre neve
* `api-version`-cseréje`2014-02-14`

A kérelem törzsében adja meg a biztonságimásolat-fájl helyét, azaz az Azure storage-fiók neve, a hozzáférési kulcsot, a blob-tároló neve és biztonsági másolat neve:

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

Állítsa a `Content-Type` a kérelem fejlécében `application/json`.

Visszaállítás egy hosszú ideig futó művelet, amely előfordulhat, hogy 30 vagy több percig tarthat befejezéséhez.  Ha a kérelem sikeres volt, és a visszaállítási folyamat kezdeményezett kapni fog egy `202 Accepted` a válasz állapotkódja egy `Location` fejléc.  Ellenőrizze a "GET" kéréseket az URL-címet a `Location` fejlécet a művelet állapotának megállapítása. A visszaállítás közben, továbbra is megkapják '202 elfogadott' állapotkód. Válaszkód `200 OK` tájékoztatja a visszaállítási művelet sikeres befejezését.

> [!IMPORTANT]
> **A Termékváltozat** a szolgáltatás a visszaállítandó **egyeznie kell** a visszaállítandó biztonsági másolat szolgáltatás a Termékváltozat.
>
> **Módosítások** végzett a szolgáltatási konfiguráció (pl. API-k, házirendek, fejlesztői portál megjelenését) visszaállítása közben a művelet van folyamatban **felülíródnak**.
>
>

## <a name="next-steps"></a>Következő lépések
Tekintse meg a biztonsági mentési/visszaállítási folyamat két különböző forgatókönyvek a következő Microsoft-blogjaihoz.

* [Az Azure API Management fiókok replikálása](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
  * Köszönjük, hogy a Gisela saját hozzájárulás a cikkhez.
* [Az Azure API Management: Biztonsági mentése, és konfiguráció visszaállítása](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Stuart által részletes módszer nem egyezik meg a hivatalos útmutatást de nagyon fontos.

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
