---
title: Tudnivalók az Azure-beli hálózatkezelésről az Azure vész-helyreállítási Azure Site Recovery használatával | Microsoft Docs
description: Áttekintést nyújt az Azure-beli virtuális gépek Azure Site Recovery használatával történő replikálásához szükséges hálózatkezelésről.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 844563e03529e472624b35d2b545c3e432e4ea17
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876301"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Tudnivalók az Azure-beli hálózatkezelésről az Azure-ba – replikálás



Ez a cikk hálózatkezelési útmutatást nyújt az Azure-beli virtuális gépek egyik régióból a másikba történő replikálásához és helyreállításához [Azure site Recovery](site-recovery-overview.md)használatával.

## <a name="before-you-start"></a>Előkészületek

[Ebből a forgatókönyvből](azure-to-azure-architecture.md)megtudhatja, hogyan biztosít a site Recovery vész-helyreállítást.

## <a name="typical-network-infrastructure"></a>Tipikus hálózati infrastruktúra

Az alábbi ábra egy tipikus Azure-környezetet ábrázol az Azure-beli virtuális gépeken futó alkalmazások esetében:

![ügyfél – környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Ha Azure-ExpressRoute használ, vagy a helyszíni hálózatról az Azure-ba VPN-kapcsolattal rendelkezik, a környezet a következő:

![ügyfél – környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

A hálózatok általában tűzfalak és hálózati biztonsági csoportok (NSG-EK) használatával védettek. A tűzfalak az URL-cím vagy az IP-alapú engedélyezési lista használatával vezérlik a hálózati kapcsolatot. A NSG olyan szabályokat biztosítanak, amelyek IP-címtartományok használatával vezérlik a hálózati kapcsolatot.

>[!IMPORTANT]
> Site Recovery nem támogatja hitelesített proxy használatát a hálózati kapcsolat vezérléséhez, és a replikáció nem engedélyezhető.


## <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolat vezérléséhez, engedélyezze ezeket a Site Recovery URL-címeket:


**URL-cím** | **Részletek**  
--- | ---
*.blob.core.windows.net | Kötelező megadni, hogy az adatok a virtuális gépről származó forrás régióban lévő cache Storage-fiókba írhatók legyenek. Ha ismeri a virtuális gépekhez tartozó összes gyorsítótárbeli Storage-fiókot, akkor az adott Storage-fiók URL-címeit (pl.: cache1.blob.core.windows.net és cache2.blob.core.windows.net) a *. blob.core.windows.net
login.microsoftonline.com | Az engedélyezéshez és a hitelesítéshez szükséges a Site Recovery szolgáltatás URL-címeihez.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténjen. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery IP-címet".
*.servicebus.windows.net | Szükséges, hogy a Site Recovery monitorozási és diagnosztikai adatok a virtuális gépről is írhatók legyenek. Ha a tűzfal proxyja támogatja az IP-címeket, használhatja a megfelelő "Site Recovery figyelési IP-címet".

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha IP-alapú tűzfal-proxyt használ, vagy a kimenő kapcsolat vezérléséhez NSG szabályokat, akkor ezeket az IP-tartományokat engedélyezni kell.

- Minden olyan IP-címtartomány, amely a forrástartomány Storage-fiókjainak felel meg
    - Hozzon létre egy [tárolási szolgáltatás címkén](../virtual-network/security-overview.md#service-tags) ALAPULó NSG-szabályt a forrás régióhoz.
    - Engedélyezze ezeket a címeket úgy, hogy az adatok a virtuális gépről a gyorsítótárbeli Storage-fiókba legyenek írva.
- Hozzon létre egy [Azure Active Directory (AAD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést az aad-hez tartozó összes IP-címek számára
    - Hozzáadja az új címeket az Azure Active Directory (AAD) a jövőben, ha szeretne létrehozni új NSG-szabályokat.
- Site Recovery szolgáltatási végpont IP-címei – egy [XML-fájlban](https://aka.ms/site-recovery-public-ips) érhetők el, és a célhelytől függenek.
- Javasoljuk, hogy hozza létre a szükséges NSG-szabályokat egy teszt NSG, és ellenőrizze, hogy nincsenek-e problémák a szabályok éles NSG való létrehozása előtt.


Site Recovery IP-címtartományok a következők:

   **Target** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
   --- | --- | ---
   Kelet-Ázsia | 52.175.17.132 | 13.94.47.61
   Délkelet-Ázsia | 52.187.58.193 | 13.76.179.223
   Közép-India | 52.172.187.37 | 104.211.98.185
   Dél-India | 52.172.46.220 | 104.211.224.190
   USA északi középső régiója | 23.96.195.247 | 168.62.249.226
   Észak-Európa | 40.69.212.238 | 52.169.18.8
   Nyugat-Európa | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   USA nyugati régiója | 40.83.179.48 | 104.40.26.199
   USA déli középső régiója | 13.84.148.14 | 104.210.146.250
   USA középső régiója | 40.69.144.231 | 52.165.34.144
   USA 2. keleti régiója | 52.184.158.163 | 40.79.44.59
   Kelet-Japán | 52.185.150.140 | 138.91.1.105
   Nyugat-Japán | 52.175.146.69 | 138.91.17.38
   Dél-Brazília | 191.234.185.172 | 23.97.97.36
   Kelet-Ausztrália | 104.210.113.114 | 191.239.64.144
   Délkelet-Ausztrália | 13.70.159.158 | 191.239.160.45
   Közép-Kanada | 52.228.36.192 | 40.85.226.62
   Kelet-Kanada | 52.229.125.98 | 40.86.225.142
   USA nyugati középső régiója | 52.161.20.168 | 13.78.149.209
   USA nyugati régiója, 2. | 52.183.45.166 | 13.66.228.204
   Az Egyesült Királyság nyugati régiója | 51.141.3.203 | 51.141.14.113
   Az Egyesült Királyság déli régiója | 51.140.43.158 | 51.140.189.52
   Egyesült Királyság 2. déli régiója | 13.87.37.4| 13.87.34.139
   Egyesült Királyság északi régiója | 51.142.209.167 | 13.87.102.68
   Korea középső régiója | 52.231.28.253 | 52.231.32.85
   Korea déli régiója | 52.231.198.185 | 52.231.200.144
   Közép-Franciaország | 52.143.138.106 | 52.143.136.55
   Dél-Franciaország | 52.136.139.227 |52.136.136.62
   Közép-Ausztrália| 20.36.34.70 | 20.36.46.142
   Ausztrália 2. középső régiója| 20.36.69.62 | 20.36.74.130
   Dél-Afrika nyugati régiója | 102.133.72.51 | 102.133.26.128
   Dél-Afrika északi régiója | 102.133.160.44 | 102.133.154.128
   USA-beli államigazgatás – Virginia | 52.227.178.114 | 23.97.0.197
   US Gov Iowa | 13.72.184.23 | 23.97.16.186
   USA-beli államigazgatás – Arizona | 52.244.205.45 | 52.244.48.85
   USA-beli államigazgatás – Texas | 52.238.119.218 | 52.238.116.60
   US DoD – Kelet | 52.181.164.103 | 52.181.162.129
   US DoD – Középső régió | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja a virtuális gépek NSG-szabályait a replikáláshoz.

- Ha NSG szabályokat használ a kimenő kapcsolatok vezérlésére, használja a "HTTPS kimenő" szabályokat a 443-as portra a szükséges IP-címtartományok esetében.
- A példa azt feltételezi, hogy a virtuális gép forrása "az USA keleti régiója", a célhely pedig az "USA középső régiója".

### <a name="nsg-rules---east-us"></a>NSG-szabályok – USA keleti régiója

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. EastUS" számára a NSG, ahogy az alábbi képernyőképen is látható.

      ![Storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a NSG "AzureActiveDirectory" számára, ahogy az alábbi képernyőképen is látható.

      ![HRE – címke](./media/azure-to-azure-about-networking/aad-tag.png)

3. Kimenő HTTPS (443) szabályok létrehozása a célhelynek megfelelő Site Recovery IP-címekhez:

   **Location** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG-szabályok – USA középső régiója

Ezek a szabályok azért szükségesek, hogy a replikáció engedélyezhető legyen a célként megadott régióból a feladatátvételt követően:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage. CentralUS" számára a NSG.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "AzureActiveDirectory" számára a NSG.

3. Kimenő HTTPS (443) szabályok létrehozása a forrás helyének megfelelő Site Recovery IP-címekhez:

   **Location** | **Site Recovery IP-cím** |  **Site Recovery figyelési IP-cím**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Hálózati virtuális berendezés konfigurációja

Ha hálózati virtuális berendezéseket (NVA-ket) használ a virtuális gépek kimenő hálózati forgalmának szabályozására, akkor előfordulhat, hogy a berendezés szabályozása megtörténik, ha az összes replikációs forgalom a NVA halad át. Javasoljuk, hogy hozzon létre egy hálózati szolgáltatási végpontot a virtuális hálózaton a "Storage" számára, hogy a replikálási forgalom ne lépjen a NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Hálózati szolgáltatás végpontjának létrehozása a Storage-hoz
Létrehozhat egy hálózati szolgáltatási végpontot a virtuális hálózatban a "Storage" számára, így a replikálási forgalom nem hagyja el az Azure-határt.

- Válassza ki az Azure-beli virtuális hálózatot, és kattintson a "szolgáltatási végpontok" elemre.

    ![tárolás – végpont](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kattintson a "Hozzáadás" és a "szolgáltatási végpontok hozzáadása" fülre.
- Válassza a "Microsoft. Storage" lehetőséget a "szolgáltatás" alatt, valamint a szükséges alhálózatokat az "alhálózatok" mezőben, és kattintson a "Hozzáadás" gombra.

>[!NOTE]
>Ne korlátozza a virtuális hálózati hozzáférést az ASR szolgáltatáshoz használt Storage-fiókokhoz. Engedélyeznie kell a hozzáférést az összes hálózatról

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Az Azure alapértelmezett rendszerútvonalát felülbírálhatja a 0.0.0.0/0 címek előtagja számára egy [Egyéni útvonallal](../virtual-network/virtual-networks-udr-overview.md#custom-routes) , és átirányíthatja a virtuális gépek forgalmát egy helyszíni hálózati virtuális készülékre (NVA), de ez a konfiguráció nem ajánlott site Recovery replikáláshoz. Ha egyéni útvonalakat használ, [hozzon létre egy virtuális hálózati szolgáltatási végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a "Storage" virtuális hálózatában, hogy a replikálási forgalom ne hagyja el az Azure-határt.

## <a name="next-steps"></a>További lépések
- Az Azure-beli [virtuális gépek replikálásával](site-recovery-azure-to-azure.md)megkezdheti a számítási feladatok védelmét.
- További információ az Azure-beli virtuális gépek feladatátvételének [IP-címének megőrzéséről](site-recovery-retain-ip-azure-vm-failover.md) .
- További információ az Azure-beli [virtuális gépek ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md)-mel való vész-helyreállításáról.
