---
title: Elérhető Azure Files protokollok
description: Az Azure-fájlmegosztás létrehozása előtt megismerheti az elérhető protokollokat.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 90d51d7eb5425530ebad0fc9ac66ba2c95776076
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564896"
---
# <a name="azure-file-share-protocols"></a>Azure fájlmegosztás protokollok

Azure Files két protokollt biztosít az Azure-fájlmegosztás csatlakoztatásához és csatlakoztatásához. A [Server Message Block (SMB) protokoll](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) és a [hálózati fájlrendszer (NFS) protokollja](https://en.wikipedia.org/wiki/Network_File_System) (előzetes verzió). Azure Files jelenleg nem támogatja a többprotokollos hozzáférést, így a megosztás csak NFS-megosztás vagy SMB-megosztás lehet. Ennek köszönhetően azt javasoljuk, hogy az Azure-fájlmegosztás létrehozása előtt határozza meg, melyik protokoll felel meg legjobban az igényeinek.

## <a name="differences-at-a-glance"></a>Különbségek egy pillantással

|Funkció  |NFS (előzetes verzió)  |SMB  |
|---------|---------|---------|
|Hozzáférési protokollok     |NFS 4,1         |SMB 2,1, SMB 3,0         |
|Támogatott operációs rendszer     |Linux kernel 4.3-as és újabb verziója         |Windows 2008 R2 +, Linux kernel 4.11 +         |
|Elérhető szintek     |Prémium szintű Storage         |Premium Storage, standard szintű tárolás, gyors elérésű, ritka elérésű         |
|Replikáció     |LRS         |LRS, ZRS, GRS         |
|Hitelesítés     |Csak gazdagép-alapú hitelesítés        |Identitás-alapú hitelesítés, felhasználó alapú hitelesítés         |
|Engedélyek     |UNIX-stílusú engedélyek         |NTFS-stílusú engedélyek         |
|Fájlrendszer-szemantika     |POSIX-kompatibilis         |Nem POSIX-kompatibilis         |
|Kis-és nagybetűk megkülönböztetése     |Kis- és nagybetűk megkülönböztetése         |Kis-és nagybetűk megkülönböztetése         |
|Rögzített hivatkozás támogatása     |Támogatott         |Nem támogatott         |
|Szimbolikus hivatkozások támogatása     |Támogatott         |Nem támogatott         |
|Megnyitott fájlok törlése vagy módosítása     |Támogatott         |Nem támogatott         |
|Zárolás     |Byte-Range tanácsadó hálózati zárolás kezelője         |Támogatott         |
|Nyilvános IP-címek biztonságos listázása | Nem támogatott | Támogatott|
|Protokoll-együttműködés| Nem támogatott | Legtöbbet|

## <a name="nfs-shares-preview"></a>NFS-megosztások (előzetes verzió)

Az Azure-fájlmegosztás NFS 4,1-mel való csatlakoztatása jelenleg előzetes verzióban érhető el. A Linux rendszerrel való szorosabb integrációt biztosít. Ez egy teljes mértékben POSIX-kompatibilis ajánlat, amely a UNIX-és egyéb * NIX-alapú operációs rendszerek standard változatán alapul. Ez a nagyvállalati szintű file Storage szolgáltatás a tárolási igényeknek megfelelően méretezhető, és egyidejűleg több ezer számítási példányban is elérhető.

### <a name="limitations"></a>Korlátozások

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Legmegfelelőbb

Az NFS Azure Files a következőkhöz ideális:

- A POSIX-kompatibilis fájlmegosztást, kis-és nagybetűket megkülönböztető vagy UNIX-stílusú engedélyeket igénylő munkaterhelések (UID/GID).
- Olyan Linux-központú számítási feladatok, amelyek nem igénylik az SMB-hozzáférést.

### <a name="security"></a>Biztonság

Az összes Azure Files adatok titkosítva maradnak. A továbbítás során az Azure egy titkosítási réteget biztosít az Azure-adatközpontok közötti, a [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE)-t használó összes adatforgalomhoz. Ezzel a titkosítás az Azure-adatközpontok közötti adatátvitel során is fennáll. Az SMB protokollt használó Azure Filestól eltérően az NFS protokollt használó fájlmegosztás nem nyújt felhasználói hitelesítést. Az NFS-megosztások hitelesítése a konfigurált hálózati biztonsági szabályokon alapul. Ennek köszönhetően annak biztosítása érdekében, hogy csak a biztonságos kapcsolatok legyenek létrehozva az NFS-megosztáshoz, mindkét szolgáltatási végpontot vagy privát végpontot kell használnia. Ha a helyszíni megosztásokat a privát végponton kívül szeretné elérni, a VPN-t vagy a ExpressRoute is be kell állítani. A következő forrásokból nem származó kérelmek el lesznek utasítva:

- [Privát végpont](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Pont – hely (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [Helyek közötti](https://docs.microsoft.com/azure/vpn-gateway/design#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Korlátozott nyilvános végpont](storage-files-networking-overview.md#storage-account-firewall-settings)

Az elérhető hálózati beállításokkal kapcsolatos további információkért lásd: [Azure Files hálózatkezelési megfontolások](storage-files-networking-overview.md).

## <a name="smb-shares"></a>SMB-megosztások

Az SMB-vel csatlakoztatott Azure-fájlmegosztás további Azure Files funkciókat kínál, és nem rendelkezik Azure Files szolgáltatás korlátozásával, mivel általánosan elérhető.

### <a name="features"></a>Szolgáltatások

- Azure-fájlok szinkronizálása
- Identitás-alapú hitelesítés
- Azure Backup támogatás
- Pillanatképek
- Helyreállítható törlés
- Titkosítás-átvitel és titkosítás – Rest

### <a name="best-suited"></a>Legmegfelelőbb

Az SMB Azure Files a következőkhöz ideális:

- Éles környezetek
- A [funkciókban](#features) felsorolt funkciók bármelyikét igénylő ügyfelek

## <a name="next-steps"></a>További lépések

- [NFS-fájlmegosztás létrehozása](storage-files-how-to-create-nfs-shares.md)
- [SMB-fájlmegosztás létrehozása](storage-how-to-create-file-share.md)