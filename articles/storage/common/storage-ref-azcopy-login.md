---
title: azcopy bejelentkezés | Microsoft dokumentumok
description: Ez a cikk az azcopy bejelentkezési parancsra vonatkozó referenciainformációkat tartalmaz.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d07d1a706635a7f269a9a51769ae6f8bbf57df3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295409"
---
# <a name="azcopy-login"></a>azcopy login

Bejelentkezik az Azure Active Directoryba az Azure Storage-erőforrások eléréséhez.

## <a name="synopsis"></a>Áttekintés

Jelentkezzen be az Azure Active Directoryba az Azure Storage-erőforrások eléréséhez.

Az Azure Storage-fiókhoz való jogosultsághoz hozzá kell rendelnie a **Storage Blob Data Contributor** szerepkört a felhasználói fiókhoz a Storage-fiók, a fölérendelt erőforráscsoport vagy a fölérendelt erőforrás-csoport vagy a fölérendelt előfizetés környezetében.

Ez a parancs gyorsítótárazza a titkosított bejelentkezési adatokat az aktuális felhasználó számára az operációs rendszer beépített mechanizmusai használatával.

További információkért olvassa el a példákat.

> [!IMPORTANT]
> Ha egy környezeti változót a parancssorból állít be, az a változó olvasható lesz a parancssori előzményekben. Fontolja meg a parancssori előzmények hitelesítő adatait tartalmazó változók törlését. Ha meg szeretné tartani, hogy a változók ne jelenjenek meg az előzményekben, parancsfájl segítségével kérheti a felhasználótól a hitelesítő adatait, és beállíthatja a környezeti változót.

```azcopy
azcopy login [flags]
```

## <a name="related-conceptual-articles"></a>Kapcsolódó koncepcionális cikkek

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)
- [Adatok átvitele az AzCopy és blob tárhellyel](storage-use-azcopy-blobs.md)
- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)
- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)

## <a name="examples"></a>Példák

Jelentkezzen be interaktívan, ha az alapértelmezett AAD-bérlői azonosító közös:

```azcopy
azcopy login
```

Jelentkezzen be interaktívan egy megadott bérlőazonosítóval:

```azcopy
azcopy login --tenant-id "[TenantID]"
```

Jelentkezzen be egy virtuális gép rendszeráltal hozzárendelt identitásával:

```azcopy
azcopy login --identity
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás ügyfélazonosítójával:

```azcopy
azcopy login --identity --identity-client-id "[ServiceIdentityClientID]"
```

Jelentkezzen be egy virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás objektumazonosítójával:

```azcopy
azcopy login --identity --identity-object-id "[ServiceIdentityObjectID]"
```

Jelentkezzen be a virtuális gép felhasználó által hozzárendelt identitásával és a szolgáltatásidentitás erőforrás-azonosítójával:

```azcopy
azcopy login --identity --identity-resource-id "/subscriptions/<subscriptionId>/resourcegroups/myRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID"
```

Bejelentkezés egyszerű szolgáltatásnévként egy ügyféltitkos adat használatával. Állítsa be a környezeti változó AZCOPY_SPA_CLIENT_SECRET az ügyféltitkos kulcsot a titkos névsori szolgáltatás hitelesítési.

```azcopy
azcopy login --service-principal
```

Jelentkezzen be egyszerű szolgáltatásként egy tanúsítvánnyal és jelszóval. Állítsa be a környezeti változóAZCOPY_SPA_CERT_PASSWORD a tanúsítvány jelszavát a tanúsítvány-alapú szolgáltatás egyszerű hitelesítés.

```azcopy
azcopy login --service-principal --certificate-path /path/to/my/cert
```

Győződjön meg róla, hogy a /path/to/my/cert-et PEM vagy PKCS12 fájl elérési útjaként kezeli. Az AzCopy nem éri el a rendszer tanúsítványtárolóját a tanúsítvány beszerzéséhez.

--tanúsítvány-elérési út kötelező a tanúsítványalapú egyszerű szolgáltatáshitelesítés i.

## <a name="options"></a>Beállítások

|Beállítás|Leírás|
|--|--|
|--aad-végpont|A használandó Azure Active Directory-végpont. Az alapértelmezett`https://login.microsoftonline.com`( ) helyes a nyilvános Azure-felhőben. Ezt a paramétert akkor állítsa be, ha nemzeti felhőben hitelesít. Lásd: [Azure AD hitelesítési végpontok.](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)
Ez a jelző nem szükséges a felügyelt szolgáltatás identitásához.|
|--alkalmazás-id karakterlánc|A felhasználó által hozzárendelt identitás alkalmazásazonosítója. A szolgáltatásnév hitelesítési elemhez szükséges.|
|--tanúsítvány-elérési út karakterlánc|Az SPN-hitelesítés tanúsítványának elérési útja. Tanúsítványalapú egyszerű szolgáltatáshitelesítéshez szükséges.|
|-h, --segítség|A bejelentkezési parancs súgótartalmának megjelenítése.|
|--identitás|virtuális gép identitásával, más néven felügyelt szolgáltatásidentitással (MSI) jelentkezhet be.|
|--identitás-ügyfél-azonosító karakterlánc|A felhasználó által hozzárendelt identitás ügyfélazonosítója.|
|--identity-object-id karakterlánc|A felhasználó által hozzárendelt identitás objektumazonosítója.|
|--identitás-erőforrás-azonosító karakterlánc|A felhasználó által hozzárendelt identitás erőforrásazonosítója.|
|--szolgáltatás-fő|Jelentkezzen be az EGYSZERŰ szolgáltatáson keresztül (egyszerű szolgáltatásnév) egy tanúsítvány vagy egy titkos név használatával. Az ügyféltitkos kulcsot vagy a tanúsítvány jelszavát a megfelelő környezeti változóba kell helyezni. Írja `AzCopy env` be a környezeti változók nevét és leírását.|
|--bérlő-azonosító karakterlánc| az Azure active directory-bérlői azonosítót az OAuth-eszköz interaktív bejelentkezéséhez.|

## <a name="options-inherited-from-parent-commands"></a>Szülőparancsoktól örökölt beállítások

|Beállítás|Leírás|
|---|---|
|--sapka-mbps uint32|Az átviteli sebesség felső határa megabit/másodpercben. A pillanatonkénti átviteli kapacitás kissé eltérhet a kupaktól. Ha ez a beállítás nulla, vagy nincs megadva, az átviteli áteresztőmód nem lesz korlátozva.|
|--kimenet-típusú karakterlánc|A parancs kimenetének formátuma. A lehetőségek a következők: szöveg, json. Az alapértelmezett érték a "szöveg".|

## <a name="see-also"></a>Lásd még

- [azcopy](storage-ref-azcopy.md)
