---
title: Az inaktív adatok beszédszolgáltatás-titkosítása
titleSuffix: Azure Cognitive Services
description: Az inaktív adatok beszédszolgáltatás-titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372358"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Az inaktív adatok beszédszolgáltatás-titkosítása

A Beszédszolgáltatás automatikusan titkosítja az adatokat, ha azok a felhőben vannak megőrizve. A beszédszolgáltatások titkosítása védi az adatokat, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-cognitive-services-encryption"></a>A Cognitive Services titkosítása

Az adatok titkosítása és visszafejtése [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) kompatibilis [256 bites AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítással történik. A titkosítás és a visszafejtés transzparens, ami azt jelenti, hogy a titkosítás és a hozzáférés kezelése az Ön számára. Az adatok alapértelmezés szerint biztonságosak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás előnyeinek kihasználásához.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

Az egyéni beszédfelismerés és az egyéni hang használata esetén a Beszédszolgáltatás a következő adatokat tárolhatja a felhőben:  

* Beszédkövetési adatok – csak akkor, ha bekapcsolja az egyéni végpont nyomkövetését
* Feltöltött képzési és vizsgálati adatok

Alapértelmezés szerint az adatok a Microsoft tárhelyén tárolódnak, és az előfizetés microsoftáltal felügyelt titkosítási kulcsokat használ. Azt is lehetősége van arra, hogy készítse el a saját tárfiók. Az áruházhoz való hozzáférést a felügyelt identitás kezeli, és a beszédszolgáltatás nem tud közvetlenül hozzáférni a saját adataihoz, például a beszédkövetési adatokhoz, a testreszabási betanítási adatokhoz és az egyéni modellekhez.

A felügyelt identitásról a Felügyelt identitások ról további információt a [Felügyelt identitások című témakörben talál.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Saját tárhely (BYOS) elhozása testreszabáshoz és naplózáshoz

Ha hozzáférést szeretne kérni saját tárhelyéhez, töltse ki és küldje el a [Beszédszolgáltatást – hozza magával saját tárhely (BYOS) igénylőlapot.](https://aka.ms/cogsvc-cmk) A jóváhagyást követően létre kell hoznia saját tárfiókot a testreszabáshoz és a naplózáshoz szükséges adatok tárolásához. Tárfiók hozzáadásakor a beszédszolgáltatás-erőforrás engedélyezi a rendszer hez rendelt felügyelt identitást. Miután a rendszer hez rendelt felügyelt identitás engedélyezve van, ez az erőforrás regisztrálva lesz az Azure Active Directory (AAD) regisztrálva. A regisztráció után a felügyelt identitás hozzáférést kap a tárfiókhoz. A felügyelt identitásokról itt olvashat bővebben. A felügyelt identitásról a Felügyelt identitások ról további információt a [Felügyelt identitások című témakörben talál.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Ha letiltja a rendszer hez rendelt felügyelt identitások, a tárfiókhoz való hozzáférés törlődik. Ez azt eredményezi, hogy a beszédfelismerési szolgáltatás azon részei, amelyek hozzáférést igényelnek a tárfiókhoz, leállnak.  

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A BYOS jelenleg a következő régiókban érhető el:

* USA déli középső régiója
* USA nyugati régiója, 2.
* USA keleti régiója

## <a name="next-steps"></a>További lépések

* [Beszédszolgáltatás - saját tárhely (BYOS) kéreleműrlap](https://aka.ms/cogsvc-cmk)
* [Mik azok a felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
