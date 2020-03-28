---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67178821"
---
A hozzáférési jogkivonatokhoz hasonlóan, ha egy Azure AD-jogkivonat nincs beállítva, kezelnie kell a TokenRequired eseményt, vagy végre kell hajtania a tokenRequired metódust a delegált protokollon.

Az esemény szinkron módon kezelhető, ha a tulajdonságot az esemény argumentumaira állítja be.
