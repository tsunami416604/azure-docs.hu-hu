---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75371776"
---
A megosztott hozzáférésű aláírás (SAS) lehetővé teszi, hogy korlátozott hozzáférést biztosítson a tárolókhoz és blobokhoz a tárfiókban. SAS létrehozásakor megadhatja a korlátozásokat, beleértve azt is, hogy az ügyfél mely Azure Storage-erőforrásokhoz férhet hozzá, milyen engedélyekkel rendelkeznek ezekre az erőforrásokra, és mennyi ideig érvényes a SAS.

Minden SAS egy kulccsal van aláírva. A SAS-t kétféleképpen írhatja alá:

- Az Azure Active Directory (Azure AD) hitelesítő adataival létrehozott kulccsal. Az Azure AD-hitelesítő adatokkal aláírt SAS egy *felhasználói delegálási* SAS.
- A tárfiók kulcsával. A *szolgáltatás SAS* és egy *fiók SAS* alá vannak írva a tárfiók kulcsával.

A felhasználói delegálássas SAS a tárfiók kulcsával aláírt SAS-nek nyújt magasabb szintű biztonságot. A Microsoft azt javasolja, hogy ha lehetséges, használjon felhasználói delegálásos SAS-t. További információ: [Korlátozott hozzáférés megadása megosztott hozzáférésű hozzáféréssel (SAS) rendelkező adatokhoz.](../articles/storage/common/storage-sas-overview.md)
