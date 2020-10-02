---
title: A Azure NetApp Files kettős protokollú köteteinek hibáinak megoldása | Microsoft Docs
description: Azokat a hibaüzeneteket és megoldásokat ismerteti, amelyek segítséget nyújtanak a kettős protokollal kapcsolatos problémák megoldásában Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/01/2020
ms.author: b-juche
ms.openlocfilehash: cc046a27fec1b9e361ff840c7ae0f077e2987b67
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654208"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Dual-Protocol kötetek – problémamegoldás

Ez a cikk a kettős protokollú kötetek létrehozásakor vagy felügyeletekor fellépett hibákra vonatkozó megoldásokat ismerteti.

## <a name="error-conditions-and-resolutions"></a>Hibákra vonatkozó feltételek és megoldások

|     Hiba feltételei    |     Feloldás    |
|-|-|
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `This Active Directory has no Server root CA Certificate` .    |     Ha ez a hiba kettős protokollú kötet létrehozásakor fordul elő, győződjön meg arról, hogy a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fel van töltve a NetApp-fiókba.    |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  Vegye figyelembe a következő megoldásokat:   <ul><li>Győződjön meg arról, hogy a szükséges főtanúsítvány hozzá van adva a Active Directory (AD) a NetApp-fiókhoz való csatlakoztatásakor. Lásd: [Active Directory hitelesítésszolgáltató nyilvános főtanúsítványának feltöltése](create-volumes-dual-protocol.md#upload-active-directory-certificate-authority-public-root-certificate).   </li><li>Lehet, hogy a DNS-kiszolgálón hiányzik az AD Host Machine mutató (PTR) rekordja. Létre kell hoznia egy névkeresési zónát a DNS-kiszolgálón, majd hozzá kell adnia egy PTR-rekordot az adott névkeresési zónában. <br> Tegyük fel például, hogy az AD-gép IP-címe `1.1.1.1` , az ad-gép állomásneve (a parancs használatával megtalált `hostname` ) `AD1` , és a tartománynév `myDomain.com` .  A névkeresési zónához hozzáadott PTR-rekordnak a következőnek kell lennie: `1.1.1.1`  ->  `AD1.myDomain.com` . </li></ul>  |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Ez a hiba azt jelzi, hogy az AD-jelszó helytelen, ha Active Directory csatlakozik a NetApp-fiókhoz. Frissítse az AD-kapcsolatokat a megfelelő jelszóval, és próbálkozzon újra. |
| A kétprotokollos kötet létrehozása a következő hibával meghiúsul `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Ez a hiba azt jelzi, hogy a DNS nem érhető el. Ennek oka az lehet, hogy a DNS IP-címe helytelen, vagy hálózati probléma van. Ellenőrizze az AD-kapcsolatban megadott DNS-IP-címet, és győződjön meg arról, hogy az IP-cím helyes. <br> Győződjön meg arról is, hogy az AD és a kötet ugyanabban a régióban és ugyanabban a VNet található. Ha különböző virtuális hálózatok vannak, győződjön meg arról, hogy a két virtuális hálózatok között létrejött a VNet-társítás.|
| A kettős protokollú kötetek csatlakoztatásakor a rendszer megtagadta az engedélyt. | A kettős protokollú kötetek az NFS-és az SMB-protokollokat is támogatják.  Amikor megpróbál hozzáférni a csatlakoztatott kötethez a UNIX rendszerű rendszeren, a rendszer megkísérli leképezni a Windows-felhasználóhoz használt UNIX-felhasználót. Ha nem található leképezés, az "engedély megtagadva" hibaüzenet jelenik meg. <br> Ez a helyzet akkor is érvényes, ha a "root" felhasználót használja a hozzáféréshez. <br> Az "engedély megtagadva" probléma elkerüléséhez győződjön meg arról, hogy a Windows Active Directory tartalmazza `pcuser` a csatlakoztatási pont elérését. Ha az `pcuser` "engedély megtagadva" problémával találkozik, várjon 24 órát a gyorsítótár-bejegyzés törlésére a hozzáférés ismételt megkísérlése előtt. |

## <a name="next-steps"></a>Következő lépések  

* [Kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md)
* [NFS-ügyfél konfigurálása az Azure NetApp Fileshoz](configure-nfs-clients.md)
