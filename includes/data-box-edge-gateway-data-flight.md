---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684433"
---
A data-az-repülési:

- Olyan adatok, amely az eszköz és az Azure között, a szabványos TLS 1.2 használnak. A TLS 1.1-es és korábbi nincs tartalék van. Ha a TLS 1.2-es nem támogatott. az ügynök kommunikációja blokkolva lesznek. A TLS 1.2-es is portál és az SDK felügyeleti műveletekhez szükség.
- Amikor az ügyfelek hozzáférnek az eszköz a böngészőben helyi webes felületén keresztül, a szabványos TLS 1.2-es biztonságos alapértelmezett protokollként szolgál.

    - Az ajánlott eljárás, hogy a böngészőben a TLS 1.2 használatára.
    - Ha a böngésző nem támogatja a TLS 1.2, TLS 1.1-es vagy a TLS 1.0 is használhatja.
- Az adatok védelme érdekében, ha a data-kiszolgálókról másolja, azt javasoljuk, hogy használja-e SMB 3.0-s titkosítással.
