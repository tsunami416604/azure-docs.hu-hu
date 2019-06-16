---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120565"
---
A továbbított adatok:

- Az eszköz és az Azure között átvitt adatok szabványos TLS 1.2 használható. A TLS 1.1-es és korábbi nincs tartalék van. Ügynök kommunikációja blokkolva lesznek, ha a TLS 1.2-es nem támogatott. A TLS 1.2 is portál és az SDK felügyeleti szükség.
- Amikor az ügyfelek számára az eszköz helyi webes felületén, a böngésző keresztül hozzáfér, a szabványos TLS 1.2-es biztonságos alapértelmezett protokollként szolgál.

    - Az ajánlott eljárás, hogy a böngészőben a TLS 1.2 használatára.
    - Ha a böngésző nem támogatja a TLS 1.2, TLS 1.1-es vagy a TLS 1.0-t is használhatja.
- Azt javasoljuk, hogy használjon SMB 3.0-s titkosítással adatok védelme, ha a data-kiszolgálókról másolja.
