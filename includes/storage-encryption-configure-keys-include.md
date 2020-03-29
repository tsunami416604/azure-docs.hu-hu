---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74895292"
---
Az Azure Storage titkosítja az összes adatot egy tárolófiókban inaktív. Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat a blob- és fájladatok titkosításához.

Az ügyfél által felügyelt kulcsokat egy Azure Key Vaultban kell tárolni. Létrehozhatja saját kulcsait, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat kulcsok létrehozásához. A tárfiók és a key vault kell ugyanabban a régióban, de lehet nek különböző előfizetések. Az Azure Storage titkosításáról és kulcskezeléséről az [Azure Storage titkosítása az inaktív adatokért](../articles/storage/common/storage-service-encryption.md)című témakörben talál további információt. Az Azure Key Vaultról a [Mi az Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
