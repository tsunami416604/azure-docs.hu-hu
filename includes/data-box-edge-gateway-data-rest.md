---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684472"
---
Az adatok inaktív: a

- Az adatok inaktív, a BitLocker az XTS-AES-256 titkosítás segítségével a helyi adatok védelme érdekében.
- Az adatok a megosztásokat a hozzáférés korlátozva.

    - A megosztás adatokhoz hozzáférő SMB-ügyfelek esetében van szükségük a megosztás társított felhasználói hitelesítő adatokat. Ezeket a hitelesítő adatokat határozza meg a megosztás létrehozása idején.
    - Az NFS-ügyfelek, amelyek éri el a megosztásokat az ügyfelek IP-címét kell adható hozzá a megosztás létrehozásakor.