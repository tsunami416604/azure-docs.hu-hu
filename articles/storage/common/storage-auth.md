---
title: Az Azure Storage-hozzáférés engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogy engedélyezze a hozzáférést az Azure Storage, beleértve az Azure Active Directory, a megosztott kulcsos hitelesítés vagy a közös hozzáférésű jogosultságkódok különböző lehetőségeket.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 9e8c9755c444ca7b81891f5f83164bc51aa694eb
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147069"
---
# <a name="authorizing-access-to-azure-storage"></a>Az Azure Storage-hozzáférés engedélyezése

Minden alkalommal, amikor a tárfiókban lévő adatelérés az ügyfél kérést küld a HTTP/HTTPS-kapcsolaton keresztül az Azure Storage. Minden kérés biztonságos erőforrás kell engedélyezni, úgy, hogy a szolgáltatás biztosítja, hogy az ügyfél az adatok eléréséhez szükséges engedélyekkel. Az Azure Storage kínál, ezek a beállítások, amelyek engedélyezik a hozzáférést az erőforrások védelme:

- **Az Azure Active Directory (Azure AD) integrálása** a blobok és üzenetsorok. Az Azure AD kínál részletesen szabályozhatja az ügyfél hozzáférés a tárfiókban lévő erőforrásokhoz való szerepköralapú hozzáférés-vezérlés (RBAC). További információkért lásd: [Azure Storage, Azure Active Directory használatával irányuló kérelmek hitelesítéséhez](storage-auth-aad.md).
- **Megosztott kulcs engedélyezési** blobok, fájlok, üzenetsorok és táblák esetében. Egy megosztott kulcsot használó ügyfél átadja egy fejlécet minden kéréssel, amely a tárfiók hozzáférési kulcsát használatával van aláírva. További információkért lásd: [a megosztott kulcsos hitelesítés](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/).
- **A közös hozzáférésű jogosultságkódot** blobok, fájlok, üzenetsorok és táblák esetében. Közös hozzáférésű jogosultságkódok (SAS) csak korlátozott delegált erőforráshoz nyújtanak hozzáférést a storage-fiókban. Megjelenik a megkötések az időintervallum esetében, amely az aláírás érvényes, illetve engedélyeket ad a hozzáférés-kezelés rugalmasságot biztosít. További információkért lásd: [a közös hozzáférésű jogosultságkód (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Névtelen nyilvános olvasási hozzáférés** tárolók és blobok. Engedélyezési, nem szükséges. További információkért lás a [tárolók és blobok névtelen olvasási hozzáférésének kezelésével](../blobs/storage-manage-access-to-resources.md) foglalkozó témakört.  

Alapértelmezés szerint az Azure Storage-ban minden erőforrás biztonságosak, és csak a fiók tulajdonosa számára érhető el. Bár az ügyfelek a tárfiókban lévő erőforrásokhoz való hozzáférés biztosítása a fent vázolt engedélyezési stratégiák bármelyike használható, Microsoft Azure AD használatát javasolja amikor csak lehetséges, a maximális biztonság és a könnyű használatra. 



