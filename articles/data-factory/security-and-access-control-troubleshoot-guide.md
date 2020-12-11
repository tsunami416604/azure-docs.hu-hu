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
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109766"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Biztonsági és hozzáférés-vezérlési problémák elhárítása Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory biztonsági és hozzáférés-vezérlésének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>A másolási tevékenység kapcsolódási problémája a felhőalapú adattárban

#### <a name="symptoms"></a>Hibajelenségek

A forrás/fogadó adattár kapcsolódási problémája miatt különféle típusú hibaüzenetek adhatók vissza.

#### <a name="cause"></a>Ok 

A problémát többnyire a következő tényezők okozzák:

1. Proxy beállítása a saját üzemeltetésű integrációs modul csomópontjában (ha saját üzemeltetésű IR-t használ)

1. Tűzfal beállítása a saját üzemeltetésű integrációs modul csomópontjában (ha saját üzemeltetésű IR-t használ)

1. Tűzfal beállítása a felhőalapú adattárban

#### <a name="resolution"></a>Feloldás

1. Először ellenőrizze a következő pontokat, hogy a probléma a kapcsolódási probléma miatt következik-e be:

   - A hiba a forrás/fogadó összekötőtől származik.

   - A tevékenység a másolás elején meghiúsul

   - A Azure IR vagy a saját üzemeltetésű integrációs modul konzisztens meghibásodása egy csomóponttal, mivel ez véletlenszerű hiba lehet a több csomópontos, saját üzemeltetésű integrációs modul esetében, ha csak a csomópontok egy része rendelkezik a problémával.

1. Ellenőrizze a proxyt, a tűzfalat és a hálózati beállításokat, ha **saját** üzemeltetésű integrációs modult használ, mert az ugyanazon az adattáron való futtatás sikerült Azure IR. A következő hivatkozásokat tekintse át a hibakereséshez:

   [Saját üzemeltetésű IR-portok és tűzfalak](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    [ADLS-összekötő](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Ha **Azure IR** használ, próbálja meg letiltani az adattár tűzfal-beállítását. Ezzel a módszerrel a következő két esettel kapcsolatos problémát lehet megállapítani:
  
   * [Azure IR IP-címek](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) nincsenek az engedélyezési listán.

   * A *megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése ehhez a Storage-fiókhoz* funkció ki van kapcsolva az [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) és a [ADLS Gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities)esetében.

   * Az *Azure-szolgáltatásokhoz való hozzáférés engedélyezése* nincs engedélyezve a ADLS Gen1.

1. Ha a fenti módszerek nem működnek, kérje a Microsoft segítségét.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Érvénytelen vagy üres hitelesítési kulcs probléma a nyilvános hálózati hozzáférés letiltása után

#### <a name="symptoms"></a>Hibajelenségek

Miután letiltotta Data Factory nyilvános hálózati hozzáférését, mert a saját üzemeltetésű integrációs modul a következő hibát veti fel: "a hitelesítési kulcs érvénytelen vagy üres."

#### <a name="cause"></a>Ok

A probléma valószínűleg a DNS-feloldási probléma okozza, mivel a nyilvános kapcsolatok letiltása és a privát végpontok létrehozása nem segít az újracsatlakozásban.

Az alábbi lépések végrehajtásával ellenőrizheti, hogy az Data Factory teljes tartománynevet a nyilvános IP-címhez oldja-e fel:

1. Győződjön meg arról, hogy az Azure-beli virtuális gépet ugyanabban a VNET hozta létre, mint Data Factory privát végpontot.

2. PsPing futtatása és pingelése az Azure-beli virtuális gépről Data Factory FQDN-re:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > A PsPing parancshoz meg kell adni egy portot, míg a 443-es port nem kötelező.

3. Ellenőrizze, hogy mindkét parancs feloldása egy, a megadott régión alapuló ADF nyilvános IP-címhez történt-e (xxx. xxx. xxx. 0 formátum).

#### <a name="resolution"></a>Feloldás

- A [Azure Data Factoryra vonatkozó Azure-beli privát hivatkozásban](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints)található cikkben talál további információt. Az utasítás a magánhálózati DNS-zóna/-kiszolgáló konfigurálására szolgál az Data Factory teljes tartománynevek magánhálózati IP-címként való feloldásához.

- Ha jelenleg nem kívánja konfigurálni a magánhálózati DNS-zónát vagy-kiszolgálót, kérjük, végezze el az alábbi lépéseket ideiglenes megoldásként. Az egyéni DNS azonban továbbra is ajánlott hosszú távú megoldásként.

  1. Módosítsa a Windows rendszerű gazdagépet, és a privát IP-címet (ADF-t) Az ADF teljes tartománynevére.
  
     Navigáljon a "C:\Windows\System32\drivers\etc" elérési útra az Azure-beli virtuális gépen, és nyissa meg **a fájlt a** Jegyzettömbben. A fájl végén adja hozzá a privát IP-címek leképezése a teljes tartománynévhez lehetőséget, és mentse a változást.
     
     ![Hozzárendelés hozzáadása a gazdagéphez](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Futtassa újra a fenti ellenőrzési lépések utasításait a válasz ellenőrzéséhez, amelynek tartalmaznia kell a magánhálózati IP-címet.

  1. Regisztrálja újra a saját üzemeltetésű integrációs modult, és a problémát meg kell oldani.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépeken privát hivatkozás miatt

#### <a name="symptoms"></a>Hibajelenségek

Nem lehet regisztrálni az IR hitelesítési kulcsot a saját üzemeltetésű virtuális gépen, mert a magánhálózati kapcsolat engedélyezve van.

A hiba adatai az alábbiak szerint jelennek meg:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Ok

A problémát a virtuális gép okozhatja, amelyben a saját üzemeltetésű integrációs modul telepítését kísérli meg. A felhőhöz való kapcsolódáshoz győződjön meg arról, hogy a nyilvános hálózati hozzáférés engedélyezve van.

#### <a name="resolution"></a>Feloldás

 **1. megoldás:** A probléma megoldásához kövesse az alábbi lépéseket:

1. Nyissa meg a [gyárak – frissítés](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) lapot.

1. A jobb felső sarokban kattintson a **TRY IT (kipróbálás** ) gombra.

1. A **Paraméterek** területen végezze el a szükséges információkat. 

1. A **törzs** alatt illessze be a következő tulajdonságot:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Válassza a **Futtatás** lehetőséget a függvény futtatásához. 

1. Győződjön meg arról, hogy a 200-es **hibakód** jelenik meg. A beillesztett tulajdonságnak a JSON-definícióban is szerepelnie kell.

1. Adja hozzá ismét az IR-hitelesítési kulcsot az Integration Runtime-ban.


**2. megoldás:** A megoldásról a következő cikkben talál további információt:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Próbálja meg engedélyezni a nyilvános hálózati hozzáférést a felhasználói felületen a következő képernyőképen látható módon:

![Nyilvános hálózati hozzáférés engedélyezése](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos további segítségért próbálkozzon a következő erőforrásokkal:

*  [Privát hivatkozás a Data Factory](data-factory-private-link.md)
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [A Microsoft Q&egy oldalt](/answers/topics/azure-data-factory.html)
*  [Stack túlfolyó fórum a Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
