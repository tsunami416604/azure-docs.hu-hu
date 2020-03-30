---
title: Biztonsági javaslatok a várólista-tároláshoz
titleSuffix: Azure Storage
description: További információ a várólista-tárolásra vonatkozó biztonsági javaslatokról. Ezen útmutató végrehajtása segít önnek a megosztott felelősségi modellünkben leírt biztonsági kötelezettségek teljesítésében.
services: storage
author: tamram
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.custom: security-recommendations
ms.openlocfilehash: 8bb56db9eed962ac8f8202c61a7446527c15dfc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060901"
---
# <a name="security-recommendations-for-queue-storage"></a>Biztonsági javaslatok a várólista-tároláshoz

Ez a cikk biztonsági javaslatokat tartalmaz a várólista-tároláshoz. Ezeknek az ajánlásoknak a végrehajtása segít a közös felelősségi modellünkben leírt biztonsági kötelezettségek teljesítésében. Ha többet szeretne tudni arról, hogy a Microsoft mit tesz a szolgáltatói feladatok teljesítése érdekében, olvassa el [a Megosztott felelősségi körök a felhőalapú számítástechnikával](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf)kapcsolatos című információt.

A cikkben szereplő javaslatok közül néhányat az Azure Security Center automatikusan figyelhet. Az Azure Security Center az első védelmi vonal az erőforrások védelmében az Azure-ban. Az Azure Security Centerről a [Mi az Azure Security Center?](../../security-center/security-center-intro.md)című témakörben talál további információt.

Az Azure Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát a potenciális biztonsági rések azonosítása érdekében. Ezután ajánlásokat ad arra vonatkozóan, hogyan kell kezelni őket. Az Azure Security Center-javaslatokról az [Azure Security Center biztonsági javaslatai](../../security-center/security-center-recommendations.md)című témakörben talál további információt.

## <a name="data-protection"></a>Adatvédelem

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Az Azure Resource Manager telepítési modelljének használata | Új tárfiókok létrehozása az Azure Resource Manager telepítési modelljével a fontos biztonsági fejlesztésekhez, beleértve a kiváló hozzáférés-vezérlést (RBAC) és a naplózást, az Erőforrás-kezelőn alapuló telepítést és cégirányítási rendszert, a felügyelt identitásokhoz való hozzáférést, a hozzáférést az Azure Key Vault titkos kulcsok, és az Azure AD-alapú hitelesítés és engedélyezés az Azure Storage-adatok hoz és -erőforrásokhoz való hozzáféréshez. Ha lehetséges, telepítse át a meglévő tárfiókokat, amelyek a klasszikus üzembe helyezési modellt használják az Azure Resource Manager használatához. Az Azure Resource Managerről az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](/azure/azure-resource-manager/resource-group-overview) | - |
| A **Szükséges biztonságos átvitel** beállítás engedélyezése az összes tárfiókon | Ha engedélyezi a **Biztonságos átvitel szükséges** beállítást, a tárolófiókkal kapcsolatban minden kérésnek biztonságos kapcsolatokon keresztül kell lezajlania. A HTTP-n keresztül érkező kérések sikertelenek lesznek. További információ: [Biztonságos átvitel megkövetelése az Azure Storage-ban.](../common/storage-require-secure-transfer.md) | [Igen](../../security-center/security-center-sql-service-recommendations.md) |
| Speciális veszélyforrások elleni védelem engedélyezése az összes tárfiókhoz | Az Azure Storage speciális veszélyforrások elleni védelme egy további biztonsági intelligenciaréteget biztosít, amely észleli a tárfiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. A biztonsági riasztások akkor aktiválódnak az Azure Security Centerben, amikor a tevékenységben anomáliák lépnek fel, és e-mailben is elküldésre kerülnek az előfizetés rendszergazdáinak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és elhárításával kapcsolatos javaslatokkal. További információ: [Komplex veszélyforrások elleni védelem az Azure Storage számára.](../common/storage-advanced-threat-protection.md) | [Igen](../../security-center/security-center-sql-service-recommendations.md) |
| A megosztott hozzáférés-aláírási (SAS) jogkivonatok korlátozása csak HTTPS-kapcsolatokra | HTTPS megkövetelése, ha egy ügyfél sas-jogkivonatot használ a várólista-adatok eléréséhez, segít a lehallgatás kockázatának minimalizálása. További információ: [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű hozzáférési adatok (SAS) használatával](../common/storage-sas-overview.md)című témakörben. | - |

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Az Azure Active Directory (Azure AD) használata a várólistaadatokhoz való hozzáférés engedélyezéséhez | Az Azure AD kiváló biztonságot és egyszerű használatot biztosít a megosztott kulcson keresztül a várólista-tárolókra vonatkozó kérelmek engedélyezéséhez. További információ: [Hozzáférés engedélyezése az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával című témakörben.](../common/storage-auth-aad.md) | - |
| Ne feledje, hogy a legkisebb jogosultság i. fő, amikor engedélyeket rendel egy Azure AD rendszerbiztonsági taghoz az RBAC-on keresztül | Amikor szerepkört rendel egy felhasználóhoz, csoporthoz vagy alkalmazáshoz, csak azokat az engedélyeket adja meg, amelyek szükségesek a feladatok elvégzéséhez. Az erőforrásokhoz való hozzáférés korlátozása segít megelőzni az adatokkal való véletlen és rosszindulatú visszaéléseket. | - |
| A fiókhozzáférési kulcsok biztonságossá tétele az Azure Key Vault segítségével | A Microsoft azt javasolja, hogy az Azure AD használatával engedélyezze az Azure Storage-nak érkező kérelmeket. Ha azonban megosztott kulcsengedélyezést kell használnia, az Azure Key Vault segítségével biztonságossá kell tennia a fiókkulcsokat. A kulcsokat a key vault futásidőben, ahelyett, hogy az alkalmazással mentené őket. | - |
| A fiókkulcsok rendszeres újragenerálása rendszeres időközönként | A fiókkulcsok rendszeres elforgatása csökkenti annak kockázatát, hogy az adatok rosszindulatú szereplőknek vannak kitéve. | - |
| Ne feledje, hogy a rendszer a legkevésbé jogosultságot, amikor engedélyeket rendel egy SAS | SAS létrehozásakor csak azokat az engedélyeket adja meg, amelyek az ügyfél számára szükségesek a funkció végrehajtásához. Az erőforrásokhoz való hozzáférés korlátozása segít megelőzni az adatokkal való véletlen és rosszindulatú visszaéléseket. | - |
| Visszavonási tervvel kell rendelkeznie az ügyfeleknek kibocsátott SAS-hez | Ha egy SAS veszélybe kerül, a lehető leghamarabb vissza kell vonnia ezt a SAS-t. Felhasználói delegálási SAS visszavonásához vonja vissza a felhasználói delegálási kulcsot, hogy gyorsan érvénytelenítse a kulcshoz társított összes aláírást. A tárolt hozzáférési házirendhez társított szolgáltatás SAS-ének visszavonásához törölheti a tárolt hozzáférési házirendet, átnevezheti a házirendet, vagy módosíthatja a lejárati idejét egy múltbeli időpontra. További információ: [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz megosztott hozzáférésű hozzáférési adatok (SAS) használatával](../common/storage-sas-overview.md)című témakörben.  | - |
| Ha egy szolgáltatás SAS nincs társítva a tárolt hozzáférési szabályzat, majd állítsa be a lejárati idő egy óra vagy kevesebb | A tárolt hozzáférési házirendhez nem társított szolgáltatás SAS nem vonható vissza. Emiatt a lejárati idő korlátozása, hogy a SAS egy órán keresztül vagy annál kevesebb ideig érvényes. | - |

## <a name="networking"></a>Hálózat

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| Tűzfalszabályok engedélyezése | Konfigurálja a tűzfalszabályokat, hogy korlátozza a tárfiókhoz való hozzáférést a megadott IP-címekről vagy tartományokból származó kérelmekre, vagy az Azure virtuális hálózat (VNet) alhálózatainak listájáról. A tűzfalszabályok konfigurálásáról az Azure File Sync proxy és a tűzfal beállításai című [témakörben](../files/storage-sync-files-firewall-and-proxy.md)olvashat bővebben. | - |
| Megbízható Microsoft-szolgáltatások hozzáférésének engedélyezése a tárfiókhoz | A tárfiók tűzfalszabályainak bekapcsolása alapértelmezés szerint blokkolja a bejövő adatkérelmeket, kivéve, ha a kérelmek egy Azure virtuális hálózaton (VNet) működő szolgáltatásból vagy az engedélyezett nyilvános IP-címekről származnak. A letiltott kérelmek közé tartoznak a más Azure-szolgáltatásokból, az Azure Portalról, a naplózási és metrikaszolgáltatásokból származó kérések, és így tovább. Más Azure-szolgáltatásokból érkező kérelmeket is engedélyezhet egy kivétel hozzáadásával, amely lehetővé teszi a megbízható Microsoft-szolgáltatások számára a tárfiók elérését. A megbízható Microsoft-szolgáltatásokkivételhozzáadásáról az [Azure File Sync proxy- és tűzfalbeállítások című témakörben](../files/storage-sync-files-firewall-and-proxy.md)olvashat bővebben.| - |
| Magánjellegű végpontok használata | A magánhálózati végpont egy privát IP-címet rendel az Azure virtuális hálózatból (VNet) a tárfiókhoz. Biztosítja a virtuális hálózat és a tárfiók közötti összes forgalmat egy privát kapcsolaton keresztül. A magánvégpontokról további információt a [Csatlakozás magánjellegű tárfiókhoz az Azure Private Endpoint használatával](../../private-link/create-private-endpoint-storage-portal.md)című témakörben talál. | - |
| Adott hálózatok hálózati hozzáférésének korlátozása | A hozzáférést igénylő ügyfeleket üzemeltető hálózatok hálózati hozzáférésének korlátozása csökkenti az erőforrások hálózati támadásoknak való kitettségét. | [Igen](../../security-center/security-center-sql-service-recommendations.md) |

## <a name="loggingmonitoring"></a>Naplózás/figyelés

| Ajánlás | Megjegyzések | Security Center |
|-|----|--|
| A kérelmek engedélyezésének nyomon követése | Engedélyezze az Azure Storage naplózását, és nyomon követheti, hogyan engedélyezték az Azure Storage-sal kapcsolatos egyes kérelmek et. A naplók azt jelzik, hogy a kérés névtelenül, OAuth 2.0 jogkivonat használatával, megosztott kulcs használatával vagy megosztott hozzáférésű aláírás (SAS) használatával történt.The logs indicate whether a request was made anonymously, using an OAuth 2.0 token, using Shared Key, or using a shared access signature (SAS). További információ: [Azure Storage analytics logging.](../common/storage-analytics-logging.md) | - |

## <a name="next-steps"></a>További lépések

- [Az Azure biztonsági dokumentációja](https://docs.microsoft.com//azure/security/)
- [Biztonságos fejlesztési dokumentáció](https://docs.microsoft.com/azure/security/develop/).
