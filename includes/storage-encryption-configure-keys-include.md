---
title: fájlbefoglalás
description: fájlbefoglalás
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74895292"
---
Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozásához megadhatja az ügyfél által felügyelt kulcsokat a blobok és a fájlok titkosításához.

Az ügyfél által felügyelt kulcsokat egy Azure Key Vault kell tárolni. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. Az Azure Storage titkosításával és a kulcskezelő szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](../articles/storage/common/storage-service-encryption.md). További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
