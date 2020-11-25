---
title: Biztonsági és hozzáférés-vezérlési problémák elhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Data Factory biztonsági és hozzáférés-vezérlési problémáit.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008725"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Biztonsági és hozzáférés-vezérlési problémák elhárítása Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory biztonsági és hozzáférés-vezérlésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Érvénytelen vagy üres hitelesítési kulcs a nyilvános hálózati hozzáférés letiltása után

#### <a name="symptoms"></a>Hibajelenségek

A saját üzemeltetésű integrációs modul a Data Factory nyilvános hálózati hozzáférésének letiltása után hibát jelez "a hitelesítési kulcs érvénytelen vagy üres".

#### <a name="cause"></a>Ok

A probléma valószínűleg a DNS-feloldási probléma okozza, mivel a nyilvános kapcsolatok letiltása és a privát végpontok létrehozása nem segít az újracsatlakozásban.

#### <a name="resolution"></a>Feloldás

1. Az ADF teljes tartománynevére PsPing, és azt találta, hogy a puffer az ADF nyilvános végpontján lett kikapcsolva, még a letiltásuk után is.

1. Módosítsa a virtuális gépen található gazdagépet úgy, hogy a magánhálózati IP-címet a teljes tartománynévhez társítsa, és futtasson egy PsPing. A puffer ezután az ADF helyes magánhálózati IP-címére fog tudni jutni.

1. Regisztrálja újra a saját üzemeltetésű integrációs modult, és a rendszer megkeresi.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépeken privát hivatkozás miatt

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépen, mert a magánhálózati kapcsolat engedélyezve van.

A hiba adatai az alábbiak szerint jelennek meg:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Ok

A probléma oka az lehet, hogy az a virtuális gép, amelyen a-t telepíteni kívánja. A nyilvános hálózati hozzáférésnek engedélyezve kell lennie a felhőhöz való csatlakozáshoz.

#### <a name="resolution"></a>Feloldás

 **1. megoldás:** A probléma megoldásához kövesse az alábbi lépéseket:

1. Navigáljon az alábbi hivatkozásra: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Kattintson a **kipróbálás** lehetőségre, és töltse ki az összes szükséges adatot. Illessze be az alábbi tulajdonságot a **törzsbe** is:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. A függvény futtatásához kattintson a lap végén a **Futtatás** gombra. Győződjön meg arról, hogy a 200-es kódú hibakódot kapja. A beillesztett tulajdonság a JSON-definícióban is megjelenik.

1. Ezután az integrációs modulban ismét próbálkozhat az IR-hitelesítési kulcs hozzáadásával is.


**2. megoldás:** A megoldásról a következő cikkben talál további információt:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Próbálja meg engedélyezni a nyilvános hálózati hozzáférést a felhasználói felülettel.

![Nyilvános hálózati hozzáférés engedélyezése](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>További lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Privát hivatkozás a Data Factory](data-factory-private-link.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)