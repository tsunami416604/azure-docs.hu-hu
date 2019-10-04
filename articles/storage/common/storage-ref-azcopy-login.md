---
title: azcopy bejelentkezés | Microsoft Docs
description: Ez a cikk a azcopy login parancsra vonatkozó tudnivalókat tartalmazza.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 2938d85becbea738acc21fc7b15991301eef759f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195723"
---
# <a name="azcopy-login"></a>azcopy bejelentkezés

Bejelentkezik a Azure Active Directoryba az Azure Storage-erőforrások eléréséhez.

## <a name="synopsis"></a>Áttekintés

Az Azure Storage-erőforrások eléréséhez jelentkezzen be Azure Active Directoryba.

Az Azure Storage-fiókjának engedélyezéséhez hozzá kell rendelnie a **Storage blob** -adatközreműködői szerepkört a felhasználói fiókhoz a Storage-fiók, a szülő erőforráscsoport vagy a szülő-előfizetés kontextusában.

Ez a parancs az operációs rendszer beépített mechanizmusaival gyorsítótárazza az aktuális felhasználó titkosított bejelentkezési adatait.

További információért tekintse meg a példákat.

> [!IMPORTANT]
> Ha egy környezeti változót a parancssor használatával állít be, akkor ez a változó a parancssori előzményekben olvasható. Érdemes lehet a parancssori előzményekből származó hitelesítő adatokat tartalmazó változókat törölni. Ahhoz, hogy a változók megjelenjenek az előzményekben, egy parancsfájl használatával megkérheti a felhasználótól a hitelesítő adataikat, és beállíthatja a környezeti változót.

```azcopy
azcopy login [flags]
```

## <a name="examples"></a>Példák

Jelentkezzen be interaktív módon az alapértelmezett HRE-bérlői AZONOSÍTÓval a common:

```azcopy
azcopy login
```

Interaktív bejelentkezés egy megadott bérlői AZONOSÍTÓval:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Bejelentkezés a virtuális gép rendszer által hozzárendelt identitásával:

```azcopy
azcopy login --identity
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával a szolgáltatás identitásának ügyfél-azonosítójával:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával a szolgáltatás identitásának azonosítójával:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával a szolgáltatás identitásának erőforrás-azonosítójával:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Jelentkezzen be egyszerű szolgáltatásként egy ügyfél-titkos kulcs használatával. Állítsa be a AZCOPY_SPA_CLIENT_SECRET környezeti változót a Secret-alapú szolgáltatásnév hitelesítéséhez.

```azcopy
azcopy login --service-principal
```

Jelentkezzen be egyszerű szolgáltatásként tanúsítvány és jelszó használatával. Állítsa be a környezeti változót a tanúsítvány AZCOPY_SPA_CERT_PASSWORD a tanúsítvány-alapú egyszerű szolgáltatás hitelesítéséhez.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Ügyeljen arra, hogy a/Path/to/my/CERT a PEM-vagy PKCS12/pfx-profil-fájl elérési útjaként kezelje. A AzCopy nem éri el a rendszertanúsítvány-tárolót a tanúsítvány beszerzéséhez.

--Certificate-Path megadása kötelező, ha a tanúsítvány alapú egyszerű szolgáltatás hitelesítését végzi.

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--Application-ID karakterlánc|A felhasználó által hozzárendelt identitás alkalmazás-azonosítója. Az egyszerű szolgáltatás hitelesítéséhez szükséges.|
|--Certificate-Path karakterlánc|A tanúsítvány elérési útja az SPN-hitelesítéshez. A tanúsítványalapú szolgáltatásnév hitelesítéséhez szükséges.|
|-h,-– Súgó|Súgótartalom megjelenítése a login parancshoz.|
|--Identity|Jelentkezzen be a virtuális gép identitása, más néven felügyelt szolgáltatás identitása (MSI) használatával.|
|--Identity-Client-ID karakterlánc|A felhasználó által hozzárendelt identitás ügyfél-azonosítója.|
|--Identity-Object-ID karakterlánc|Felhasználó által hozzárendelt identitás objektum-azonosítója.|
|--Identity-Resource-id karakterlánc|A felhasználó által hozzárendelt identitás erőforrás-azonosítója.|
|--szolgáltatás – rendszerbiztonsági tag|Jelentkezzen be SPN-ben (egyszerű szolgáltatásnév) a tanúsítvány vagy a titkos kulcs használatával. Az ügyfél titkos vagy tanúsítványának jelszavát a megfelelő környezeti változóba kell helyezni. A `AzCopy env` környezeti változók nevének és leírásának megjelenítéséhez írja be a következőt:.|
|--Bérlő-azonosító sztring| az OAuth-eszköz interaktív bejelentkezéséhez használandó Azure Active Directory-bérlői azonosító.|

## <a name="options-inherited-from-parent-commands"></a>A szülő parancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--Cap-Mbps UInt32|Az adatátviteli sebesség (megabit/másodperc). A pillanatnyi átviteli sebesség a korláttól némileg eltérő lehet. Ha a beállítás értéke nulla, vagy nincs megadva, az átviteli sebesség nem lesz maximális.|
|--output-Type karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: Text, JSON. Az alapértelmezett érték a "text".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
