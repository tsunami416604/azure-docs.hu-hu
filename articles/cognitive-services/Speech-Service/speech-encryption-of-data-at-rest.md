---
title: A beszédfelismerési szolgáltatás inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési szolgáltatás inaktív adatok titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: c2e52fbab8d984f7442d8a336e90e9f22c0bf061
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198671"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>A beszédfelismerési szolgáltatás inaktív adatok titkosítása

A beszédfelismerési szolgáltatás automatikusan titkosítja az adatait, amikor azt megőrzi a felhőben. A beszédfelismerési szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Alapértelmezés szerint az adatai biztonságban vannak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás kihasználása érdekében.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A Custom Speech és az egyéni hang használatakor a beszédfelismerési szolgáltatás a következő Felhőbeli adattárolást is elvégezheti:  

* Beszédfelismerési adat – csak akkor, ha a nyomkövetést bekapcsolja az egyéni végponthoz
* Feltöltött képzések és tesztelési célú adatfeldolgozás

Alapértelmezés szerint az adatai a Microsoft tárolójában tárolódnak, és az előfizetése a Microsoft által felügyelt titkosítási kulcsokat használja. Lehetősége van saját Storage-fiók előkészítésére is. Az áruházhoz való hozzáférést a felügyelt identitás kezeli, és a beszédfelismerési szolgáltatás nem tud közvetlenül hozzáférni a saját adataihoz, például a beszédfelismerési adatokhoz, a testreszabási adatokhoz és az egyéni modellekhez.

A felügyelt identitással kapcsolatos további információkért lásd: [Mik a felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>Saját tároló (BYOS) használata a testreszabáshoz és a naplózáshoz

Ha hozzáférést szeretne kérni a saját tárterületének eléréséhez, töltse ki és küldje el a [beszédfelismerési szolgáltatást – a saját tároló (BYOS) kérelmi űrlapját](https://aka.ms/cogsvc-cmk). A jóváhagyást követően létre kell hoznia a saját Storage-fiókját a testreszabáshoz és a naplózáshoz szükséges adattároláshoz. Storage-fiók hozzáadásakor a Speech Service-erőforrás engedélyezi a rendszer által hozzárendelt felügyelt identitást. Ha engedélyezve van a rendszerhez rendelt felügyelt identitás, az erőforrás regisztrálva lesz Azure Active Directory (HRE). A regisztrálást követően a felügyelt identitás hozzáférést kap a Storage-fiókhoz. A felügyelt identitásokról itt talál további információt. A felügyelt identitással kapcsolatos további információkért lásd: [Mik a felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

> [!IMPORTANT]
> Ha letiltja a rendszerhez rendelt felügyelt identitásokat, a Storage-fiókhoz való hozzáférés el lesz távolítva. Ez azt eredményezi, hogy a beszédfelismerési szolgáltatás azon részei, amelyek hozzáférést igényelnek a Storage-fiókhoz a működésének leállásához.  

A beszédfelismerési szolgáltatás jelenleg nem támogatja a Ügyfélszéf. Az ügyféladatok azonban a BYOS használatával is tárolhatók, így a [Ügyfélszéfhoz](../../security/fundamentals/customer-lockbox-overview.md)hasonló adatellenőrzéseket érhet el. Ne feledje, hogy a beszédfelismerési szolgáltatás adatmennyisége és feldolgozása abban a régióban történik, ahol a beszédfelismerési erőforrás létrejött. Ez a nyugalmi állapotban tárolt adatokra és a tranzitban tárolt adatokra is vonatkozik. A testreszabási funkciók, például a Custom Speech és az egyéni hang használatakor az összes ügyfél-adat átvitele, tárolása és feldolgozása ugyanabban a régióban történik, ahol a BYOS (ha használatban van) és a Speech Service-erőforrás található.

> [!IMPORTANT]
> A Microsoft **nem** használja fel az ügyféladatokat, hogy javítsa a beszédfelismerési modelljeit. Emellett, ha a végpont-naplózás le van tiltva, és nem használ testreszabást, akkor a rendszer nem tárolja az ügyféladatokat. 

## <a name="next-steps"></a>Következő lépések

* [Beszédfelismerési szolgáltatás – saját tároló (BYOS) kérésének űrlapja](https://aka.ms/cogsvc-cmk)
* [Mik azok a felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
