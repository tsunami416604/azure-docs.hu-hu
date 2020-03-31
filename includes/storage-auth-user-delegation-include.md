---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/15/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a4a10797109cb3363027e2445259d06d9aa071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76118111"
---
## <a name="about-the-user-delegation-sas"></a>A felhasználói delegálásról SAS

Egy tárolóhoz vagy blobhoz való hozzáféréss SAS-jogkivonat azure AD-hitelesítő adatokkal vagy fiókkulccsal is biztonságos lehet. Az Azure AD-hitelesítő adatokkal védett SAS-t felhasználói delegálássas-nak nevezzük, mivel a SAS aláírásához használt OAuth 2.0-s jogkivonatot a felhasználó nevében kérik.

A Microsoft azt javasolja, hogy az Azure AD hitelesítő adatait, ha lehetséges, a biztonsági ajánlott eljárás, nem pedig a fiókkulcs használata, amely könnyebben sérülhet. Ha az alkalmazás tervéhez megosztott hozzáférés-aláírások szükségesek, az Azure AD hitelesítő adataival hozzon létre egy felhasználói delegálási SAS-t a nagyobb biztonság érdekében. A felhasználói delegálási SAS-ről további információt a [Felhasználói delegálási SAS létrehozása című](/rest/api/storageservices/create-user-delegation-sas)témakörben talál.

> [!CAUTION]
> Minden olyan ügyfél, amely rendelkezik egy érvényes SAS-hozzáféréssel a tárfiókban lévő adatokhoz, ahogy azt az adott SAS engedélyezi. Fontos, hogy megvédje a SAS-t a rosszindulatú vagy nem szándékos használattól. Körültekintően terjesztheti a SAS-t, és rendelkezik egy tervvel a sérült SAS visszavonásához.

A megosztott hozzáférésű aláírásokról további információt az [Azure Storage-erőforrásokhoz való korlátozott hozzáférés megadása megosztott hozzáférésű aláírások (SAS) használatával](../articles/storage/common/storage-sas-overview.md)című témakörben talál.
