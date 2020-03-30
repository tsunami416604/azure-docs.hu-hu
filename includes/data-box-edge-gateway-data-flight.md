---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179756"
---
Repülés közbeni adatok esetén:

- Standard TLS 1.2 az eszköz és az Azure között futó adatok. A TLS 1.1 és korábbi ad tartalékot. Az ügynökkommunikáció le lesz tiltva, ha a TLS 1.2 nem támogatott. TLS 1.2 is szükséges a portál- és SDK-kezeléshez.
- Amikor az ügyfelek egy böngésző helyi webes felhasználói felületén keresztül férnek hozzá az eszközhöz, a szabványos TLS 1.2 lesz az alapértelmezett biztonságos protokoll.

    - Az ajánlott eljárás az, hogy konfigurálja a böngészőt a TLS 1.2 használatára.
    - Ha a böngésző nem támogatja a TLS 1.2-t, használhatja a TLS 1.1-es vagy tls 1.0-s.
- Azt javasoljuk, hogy az SMB 3.0-s titkosítást használja az adatok védelmére, amikor az adatkiszolgálókról másolja őket.
