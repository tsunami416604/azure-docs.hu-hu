---
title: Az Azure NFS-fájlmegosztás problémáinak elhárítása – Azure Files
description: Az Azure NFS-fájlmegosztás problémáinak elhárítása.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 661cfd5bb410a714bc42e0cd9676ac2ec08f8a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708897"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Az Azure NFS-fájlmegosztás hibáinak megoldása

Ez a cikk az Azure NFS-fájlmegosztás-megosztásokkal kapcsolatos gyakori problémákat sorolja fel. A probléma előfordulásakor lehetséges okokat és megkerülő megoldásokat biztosít.

## <a name="unable-to-create-an-nfs-share"></a>Nem hozható létre NFS-megosztás

### <a name="cause-1-subscription-is-not-enabled"></a>1. ok: az előfizetés nincs engedélyezve

Előfordulhat, hogy az előfizetése nincs regisztrálva a Azure Files NFS előzetes verziójában. A funkció engedélyezéséhez néhány további parancsmagok kell futtatnia Cloud Shell vagy helyi terminálról.

> [!NOTE]
> Előfordulhat, hogy a regisztráció befejezéséhez akár 30 percet is várnia kell.


#### <a name="solution"></a>Megoldás

A következő szkripttel regisztrálja a szolgáltatást és az erőforrás-szolgáltatót, és cserélje le a `<yourSubscriptionIDHere>` szkript futtatása előtt:

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares - ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>2. ok: nem támogatott Storage-fiók beállításai

Az NFS csak a következő konfigurációval rendelkező Storage-fiókokban érhető el:

- Prémium szint
- Fiók típusa – FileStorage
- Redundancia – LRS
- Régiók – USA keleti régiója, USA 2. keleti régiója, Egyesült Királyság déli régiója, Délkelet-Ázsia

#### <a name="solution"></a>Megoldás

Kövesse a cikk utasításait: [NFS-megosztás létrehozása](storage-files-how-to-create-nfs-shares.md).

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>3. ok: a Storage-fiók létrehozása a regisztráció befejeződése előtt megszakadt

Ahhoz, hogy egy Storage-fiók használhassa a szolgáltatást, létre kell hoznia, ha az előfizetés befejezte az NFS-regisztrációt. A regisztráció befejezéséhez akár 30 percet is igénybe vehet.

#### <a name="solution"></a>Megoldás

A regisztráció befejeződése után kövesse a cikk utasításait: [NFS-megosztás létrehozása](storage-files-how-to-create-nfs-shares.md).

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Nem lehet csatlakozni az Azure NFS-fájlmegosztás vagy csatlakoztatásához

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>1. ok: a kérelem olyan ügyféltől származik, amely nem megbízható hálózat/nem megbízható IP-cím

Az SMB-vel ellentétben az NFS nem rendelkezik felhasználó-alapú hitelesítéssel. A megosztások hitelesítése a hálózati biztonsági szabály konfigurációjától függ. Ennek köszönhetően annak biztosítása érdekében, hogy csak a biztonságos kapcsolatok legyenek létrehozva az NFS-megosztáshoz, a szolgáltatási végpontot vagy a magánhálózati végpontokat kell használnia. A privát végpontokon kívül a helyszíni megosztások eléréséhez VPN-vagy ExpressRoute kell beállítania. A rendszer figyelmen kívül hagyja a Storage-fiók engedélyezési listájának a tűzfalhoz hozzáadott IP-címeket. Az NFS-megosztásokhoz való hozzáférés beállításához az alábbi módszerek egyikét kell használnia:


- [Szolgáltatási végpont](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - A nyilvános végpont fér hozzá
    - Csak ugyanabban a régióban érhető el.
    - A VNet-társítás nem biztosít hozzáférést a megosztáshoz.
    - Minden virtuális hálózatot vagy alhálózatot egyedileg kell hozzáadni az engedélyezési listához.
    - Helyszíni hozzáférés esetén a szolgáltatási végpontok ExpressRoute, pont – hely és helyek közötti VPN-kapcsolatokkal használhatók, de javasoljuk, hogy használjon privát végpontot, mivel az biztonságosabb.

Az alábbi ábra a nyilvános végpontok használatával történő kapcsolódást ábrázolja.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="A nyilvános végpont kapcsolatának ábrája." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [Privát végpont](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - A hozzáférés biztonságosabb, mint a szolgáltatási végpont.
    - Az NFS-megosztás magánhálózati kapcsolaton keresztüli elérését a Storage-fiók Azure-régiójában és azon kívül is elérhetővé kell tennie (régión belüli, helyi)
    - A privát végponton üzemeltetett virtuális hálózatokkal rendelkező virtuális hálózatok egymáshoz való hozzáférést biztosítanak az NFS-megosztáshoz a társ virtuális hálózatokban lévő ügyfelek számára.
    - A magánhálózati végpontok ExpressRoute, pont – hely és helyek közötti VPN-eket is használhatnak.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="A nyilvános végpont kapcsolatának ábrája." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>2. ok: a biztonságos átvitelre van szükség

A kettős titkosítás még nem támogatott az NFS-megosztások esetében. Az Azure egy titkosítási réteget biztosít az Azure-adatközpontok közötti, a MACSec-t használó összes adatforgalomhoz. Az NFS-megosztások csak megbízható virtuális hálózatokból és VPN-alagutakból érhetők el. Az NFS-megosztásokon nem érhető el további átviteli rétegbeli titkosítás.

#### <a name="solution"></a>Megoldás

A Storage-fiók konfiguráció paneljén tiltsa le a biztonságos átvitelt.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="A nyilvános végpont kapcsolatának ábrája.":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>3. ok: az NFS-Common csomag nincs telepítve
A csatlakoztatási parancs futtatása előtt telepítse a csomagot az alábbi disztribúció-specifikus parancs futtatásával.

A következő futtatásával ellenőrizze, hogy telepítve van-e az NFS-csomag: `rpm qa | grep nfs-utils`

#### <a name="solution"></a>Megoldás

Ha a csomag nincs telepítve, telepítse a csomagot a disztribúcióban.

##### <a name="ubuntu-or-debian"></a>Ubuntu vagy Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8 +, CentOS 8 +

Használja a DNF csomagkezelő eszközt: `sudo dnf install nfs-common` .

A Red Hat Enterprise Linux és a CentOS régebbi verziói a yum Package Managert használják: `sudo yum install nfs-common` .

##### <a name="opensuse"></a>openSUSE

Használja a Zypper csomagkezelő eszközt: `sudo zypper install-nfscommon` .

### <a name="cause-4-firewall-blocking-port-2049"></a>4. ok: tűzfal blokkolja a 2049-es portot

Az NFS protokoll a 2049-as porton keresztül kommunikál a kiszolgálóval, győződjön meg arról, hogy ez a port nyitott a Storage-fiókhoz (az NFS-kiszolgáló).

#### <a name="solution"></a>Megoldás

A következő parancs futtatásával ellenőrizze, hogy a 2049-es port nyitva van-e az ügyfélen: `telnet <storageaccountnamehere>.file.core.windows.net 2049` . Ha a port nincs megnyitva, nyissa meg.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és kérje meg a probléma gyors megoldását.