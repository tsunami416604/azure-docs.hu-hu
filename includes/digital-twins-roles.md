---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53795922"
---
A következő táblázat ismerteti az Azure digitális Twins elérhető szerepkörök:

| **Szerepkör** | **Leírás** | **azonosító** |
| --- | --- | --- |
| Hely rendszergazdája | *Hozzon létre*, *OLVASÁSI*, *frissítés*, és *törlése* engedéllyel a megadott hely és az összes csomópont alatt. Globális engedélyt. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Felhasználói rendszergazda| *Hozzon létre*, *OLVASÁSI*, *frissítés*, és *törlése* engedéllyel a felhasználók és felhasználói biztonsággal kapcsolatos objektumok. *Olvassa el* tárolóhelyek engedély. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Eszközadminisztrátor | *Hozzon létre*, *OLVASÁSI*, *frissítés*, és *törlése* eszközök és az eszközzel kapcsolatos objektumokra vonatkozó engedélyt. *Olvassa el* tárolóhelyek engedély. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Kulcs-rendszergazda | *Hozzon létre*, *OLVASÁSI*, *frissítés*, és *törlése* tárelérési kulcsok engedély. *Olvassa el* tárolóhelyek engedély. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Jogkivonat rendszergazdák |  *Olvassa el* és *frissítés* tárelérési kulcsok engedély. *Olvassa el* tárolóhelyek engedély. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Felhasználó |  *Olvassa el* tárolóhelyek eszközök, érzékelők és felhasználókat, beleértve a kapcsolódó engedély kapcsolódó objektumok. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Támogatási szakértő |  *Olvassa el* tárelérési kulcsok kivételével minden engedélyt. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Eszköz-telepítő | *Olvassa el* és *frissítés* eszközeit és érzékelőit, amely tartalmazza a megfelelő engedély kapcsolódó objektumok. *Olvassa el* tárolóhelyek engedély. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Átjáróeszköz | *Hozzon létre* engedély érzékelő számára. *Olvassa el* eszközeit és érzékelőit, amely tartalmazza a megfelelő engedély kapcsolódó objektumok. | d4c69766-e9bd-4e61-BFC1-d8b6e686c7a8 |