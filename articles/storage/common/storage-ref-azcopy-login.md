---
title: azcopy bejelentkezés | Microsoft Docs
description: Ez a cikk a azcopy login parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8d2adca661882ea11d04ebe55afe25f7f9c2ef4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219968"
---
# <a name="azcopy-login"></a>azcopy login

Bejelentkezik a Azure Active Directoryba az Azure Storage-erőforrások eléréséhez.

## <a name="synopsis"></a>Áttekintés

Az Azure Storage-erőforrások eléréséhez jelentkezzen be Azure Active Directoryba.

Az Azure Storage-fiókjának engedélyezéséhez hozzá kell rendelnie a **Storage blob-adatközreműködői** szerepkört a felhasználói fiókhoz a Storage-fiók, a szülő erőforráscsoport vagy a szülő-előfizetés kontextusában.

Ez a parancs az operációs rendszer beépített mechanizmusaival gyorsítótárazza az aktuális felhasználó titkosított bejelentkezési adatait.

További információért tekintse meg a példákat.

> [!IMPORTANT]
> Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó fogalmi cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)
- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)
- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Jelentkezzen be interaktív módon az alapértelmezett HRE-bérlői AZONOSÍTÓval a common:

```azcopy
azcopy login
```

Interaktív bejelentkezés egy megadott bérlői AZONOSÍTÓval:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Bejelentkezés egy virtuális gép rendszer által hozzárendelt identitásával (VM):

```azcopy
azcopy login --identity
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának ügyfél-AZONOSÍTÓjának használatával:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának azonosítójával:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatás identitásának erőforrás-azonosítójával:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Jelentkezzen be egyszerű szolgáltatásként egy ügyfél-titkos kulcs használatával. Állítsa a környezeti változót AZCOPY_SPA_CLIENT_SECRET a titkos kulcson alapuló egyszerű szolgáltatás hitelesítéséhez.

```azcopy
azcopy login --service-principal
```

Jelentkezzen be egyszerű szolgáltatásként tanúsítvány és jelszó használatával. Állítsa be a környezeti változót a tanúsítvány-alapú egyszerű szolgáltatás hitelesítéséhez AZCOPY_SPA_CERT_PASSWORD a tanúsítvány jelszavára.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Ügyeljen arra, hogy a/Path/to/my/CERT a PEM-vagy PKCS12/pfx-profil-fájl elérési útjaként kezelje. A AzCopy nem éri el a rendszertanúsítvány-tárolót a tanúsítvány beszerzéséhez.

--Certificate-Path megadása kötelező, ha a tanúsítvány alapú egyszerű szolgáltatás hitelesítését végzi.

## <a name="options"></a>Beállítások

|Beállítás|Description|
|--|--|
|--HRE-végpont|A használandó Azure Active Directory végpont. Az alapértelmezett ( `https://login.microsoftonline.com` ) helyes a nyilvános Azure-felhőben. Adja meg ezt a paramétert a hitelesítéshez a nemzeti felhőben. Lásd: [Azure ad-hitelesítési végpontok](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).
Ez a jelző nem szükséges a Managed Service Identityhoz.|
|--Application-ID karakterlánc|A felhasználó által hozzárendelt identitás alkalmazás-azonosítója. Az egyszerű szolgáltatás hitelesítéséhez szükséges.|
|--Certificate-Path karakterlánc|A tanúsítvány elérési útja az SPN-hitelesítéshez. A tanúsítványalapú szolgáltatásnév hitelesítéséhez szükséges.|
|-h,-– Súgó|Súgótartalom megjelenítése a login parancshoz.|
|--Identity|Jelentkezzen be a virtuális gép identitása, más néven felügyelt szolgáltatás identitása (MSI) használatával.|
|--Identity-Client-ID karakterlánc|A felhasználó által hozzárendelt identitás ügyfél-azonosítója.|
|--Identity-Object-ID karakterlánc|Felhasználó által hozzárendelt identitás objektum-azonosítója.|
|--Identity-Resource-id karakterlánc|A felhasználó által hozzárendelt identitás erőforrás-azonosítója.|
|--szolgáltatás – rendszerbiztonsági tag|Jelentkezzen be SPN-ben (egyszerű szolgáltatásnév) a tanúsítvány vagy a titkos kulcs használatával. Az ügyfél titkos vagy tanúsítványának jelszavát a megfelelő környezeti változóba kell helyezni. `AzCopy env`A környezeti változók nevének és leírásának megjelenítéséhez írja be a következőt:.|
|--Bérlő-azonosító sztring| az OAuth-eszköz interaktív bejelentkezéséhez használandó Azure Active Directory-bérlői azonosító.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Description|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|
|--megbízható-Microsoft-utótagok karakterlánca   |További tartomány-utótagokat határoz meg, amelyekben Azure Active Directory bejelentkezési tokenek küldhetők.  Az alapértelmezett érték: "*. Core.Windows.net;*. core.chinacloudapi.cn; *. Core.cloudapi.de;*. core.usgovcloudapi.net '. Az itt felsorolt beállítások az alapértelmezett értékre kerülnek. A biztonság érdekében itt csak Microsoft Azure-tartományokat helyezhet el. Több bejegyzést pontosvesszővel kell elválasztani.|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
