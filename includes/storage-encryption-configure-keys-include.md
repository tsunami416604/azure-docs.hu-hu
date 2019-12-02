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
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665906"
---
Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további vezérléséhez az ügyfél által felügyelt kulcsokat a Storage-fiók szintjén lehet megadni.

Az ügyfél által felügyelt kulcsokat egy Azure Key Vault kell tárolni. Létrehozhatja saját kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához. A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. Az Azure Storage titkosításával és a kulcskezelő szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](../articles/storage/common/storage-service-encryption.md). További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
