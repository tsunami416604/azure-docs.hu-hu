---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3cfdca99c91dc54a711801d92aa0da91fb9703e4
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246935"
---
> [!IMPORTANT]
> A tárfiók kulcsa hasonlít a tárfiók rendszergazdai jelszavához. Mindig ügyeljen a fiókja kulcsának védelmére. Ne adja ki másoknak, ne kódolja fixen, és ne mentse mások számára elérhető, egyszerű szöveges fájlban. Ha azt gyanítja, hogy a fiókkulcs biztonsága sérült, az Azure Portal segítségével generálja újra.
> 
> SAS (közös hozzáférésű Jogosultságkód) jogkivonatok olyan kritikus fontosságú, csakúgy, mint a hozzáférési kulcsainak védelmét. Miközben biztosító granularitási SAS ügyfelek számára hozzáférést biztosít a tárfiókban található erőforrásokhoz, és nem szabad nyilvánosan megosztani. Ha megosztása a hibaelhárításhoz szükséges okokból fontolja meg a naplófájlokat kivonatosan verzióját használja, vagy törlése a naplófájlokban a SAS-tokeneket (ha van ilyen), és győződjön meg arról, hogy a képernyőképek vagy az SAS-információk nem tartalmaznak.
> 
> A Microsoft azt javasolja, Blobok és a Queue storage alkalmazásai számára (előzetes verzió), amikor csak lehetséges, a fokozott biztonság az Azure Active Directory (Azure AD-) hitelesítéssel. További információkért lásd: [hitelesítés hozzáférés az Azure-blobok és üzenetsorok az Azure Active Directory (előzetes verzió) használatával](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
