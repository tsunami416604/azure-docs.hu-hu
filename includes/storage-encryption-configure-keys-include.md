---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61593532"
---
Az Azure Storage támogatja a titkosítás inaktív állapotban a Microsoft által felügyelt kulcsokkal vagy felhasználó által kezelt kulcsokkal. Ügyfél által felügyelt kulcsokat hozhat létre, elforgatása, tiltsa le, és visszavonhatja a hozzáférés-vezérlés lehetővé teszik.

Az Azure Key Vault használatával a kulcsok kezelése és naplózása a kulcshasználat. A saját kulcsok létrehozása és a key vaultban tárolja őket, vagy az Azure Key Vault API-k segítségével kulcsok létrehozásához. A storage-fiók és a key vault ugyanabban a régióban kell lennie, de különböző előfizetésekhez is lehetnek. Azure Key Vaulttal kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../articles/key-vault/key-vault-overview.md)
