---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/25/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3af8077627d56ce44c5e2959e2c79b967b09d9e5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011946"
---
A közös hozzáférésű aláírás (SAS) lehetővé teszi, hogy a Storage-fiókban korlátozott hozzáférést biztosítson a tárolók és a Blobok számára. SAS létrehozásakor meg kell adnia a megkötéseit, beleértve az ügyfél számára engedélyezett Azure Storage-erőforrásokat, milyen engedélyekkel rendelkeznek az adott erőforrásokon, és hogy mennyi ideig érvényes az SAS.

Minden SAS egy kulccsal van aláírva. Az SAS-t kétféleképpen lehet aláírni:

- Azure Active Directory (Azure AD) hitelesítő adatok használatával létrehozott kulccsal. Az Azure AD-beli hitelesítő adatokkal aláírt SAS egy *felhasználói delegálási* sas.
- A Storage-fiók kulcsaként. A *Service sas* és a *fiók sas* is a Storage-fiók kulcsával van aláírva.
