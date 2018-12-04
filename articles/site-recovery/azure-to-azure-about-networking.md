---
title: Tudnivalók az Azure Site Recovery használatával az Azure-bA vész-helyreállítási hálózat |} A Microsoft Docs
description: Az Azure Site Recovery használatával az Azure virtuális gépek replikálását hálózatkezelés áttekintése.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sujayt
ms.openlocfilehash: 76b3d23a042fae452b25b0a707e3c7ff9bbda613
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838192"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Hálózatkezelés az Azure-bA replikációs kapcsolatban



A cikkből hálózati takarít meg replikálása és helyreállítása Azure virtuális gépek egyik régióból egy másik használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Előkészületek

Bemutatjuk, hogy a Site Recovery hogyan nyújt vészhelyreállítás [ebben a forgatókönyvben](azure-to-azure-architecture.md).

## <a name="typical-network-infrastructure"></a>Tipikus hálózati infrastruktúrát

Az alábbi ábrán egy tipikus Azure-környezethez, Azure virtuális gépeken futó alkalmazások ábrázolja:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Azure ExpressRoute vagy VPN-kapcsolatot használ a helyszíni hálózatból az Azure-ba, ha a környezet a következőképpen:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

Hálózatok általában védettek, a tűzfalak és hálózati biztonsági csoportok (NSG-k) használatával. Tűzfalak URL-cím vagy IP-alapú engedélyezési segítségével szabályozza a hálózati kapcsolatot. Az NSG-k adja meg a szabályokat, amelyek IP-címtartományok segítségével szabályozza a hálózati kapcsolatot.

>[!IMPORTANT]
> A Site Recovery nem támogatja hálózati kapcsolatok szabályozásához hitelesített proxyt használ, és a replikáció nem engedélyezhető.


## <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

URL-alapú tűzfalproxyt használ a kimenő kapcsolat szabályozásához, ha a Site Recovery URL-címek engedélyezése:


**URL-cím** | **Részletek**  
--- | ---
*.blob.core.windows.net | Szükséges, hogy az adatokat a virtuális gépről is írható a gyorsítótárfiók a forrásrégióban.
login.microsoftonline.com | A Site Recovery szolgáltatás URL-címeinek a hitelesítési és engedélyezési szükséges.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja fordulhat elő, a virtuális gépről.
*.servicebus.windows.net | Szükséges, hogy a virtuális Gépet a Site Recovery monitorozási és diagnosztikai adatok beírhatók.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>Kimenő kapcsolat az IP-címtartományokhoz

Ha egy IP-alapú tűzfalproxyt vagy NSG-szabályokat használ a kimenő kapcsolat szabályozásához, ezeket az IP-címtartományok kell engedélyezni kell.

- Összes IP-címtartományokat, amelyek megfelelnek a forrásrégióban tárfiókokba
    - Hozzon létre egy [tárolási szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) a forrásrégióban az NSG-szabály alapján.
    - Ezek a címek lehetővé teszik, úgy, hogy az adatokat a virtuális gépről a gyorsítótárfiókba írhatók.
- Hozzon létre egy [Azure Active Directory (AAD) szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést az aad-hez tartozó összes IP-címek számára
    - Hozzáadja az új címeket az Azure Active Directory (AAD) a jövőben, ha szeretne létrehozni új NSG-szabályokat.
- Site Recovery szolgáltatás végpont IP-címek – elérhető egy [XML-fájl](https://aka.ms/site-recovery-public-ips) , és a célhely függnek.
- Azt javasoljuk, hogy a szükséges NSG-szabályok létrehozása egy teszt NSG-t, és ellenőrizze, hogy nincsenek-e problémák a szabályokat az NSG-t éles létrehozása előtt.


Site Recovery IP-címtartományok a következők:

   **Target** | **Site Recovery IP** |  **A Site Recovery IP figyelése**
   --- | --- | ---
   Kelet-Ázsia | 52.175.17.132 | 13.94.47.61
   Délkelet-Ázsia | 52.187.58.193 | 13.76.179.223
   Közép-India | 52.172.187.37 | 104.211.98.185
   Dél-India | 52.172.46.220 | 104.211.224.190
   USA északi középső régiója | 23.96.195.247 | 168.62.249.226
   Észak-Európa | 40.69.212.238 | 52.169.18.8
   Nyugat-Európa | 52.166.13.64 | 40.68.93.145
   USA keleti régiója | 13.82.88.226 | 104.45.147.24
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
   Korea déli régiója | 52.231.298.185 | 52.231.200.144
   Közép-Franciaország | 52.143.138.106 | 52.143.136.55
   Dél-Franciaország | 52.136.139.227 |52.136.136.62


## <a name="example-nsg-configuration"></a>Az NSG konfigurálása – példa

Ez a példa bemutatja egy virtuális gép replikálása NSG-szabályok konfigurálása.

- NSG-szabályokat használ a kimenő kapcsolat szabályozásához, ha az összes szükséges IP-címtartományok "Kimenő HTTPS engedélyezése" szabályokat a: 443-as portot használja.
- A példa feltételezi, hogy a virtuális gép helyét "USA keleti régiója" és a célhelyen található az "USA középső Régiója".

### <a name="nsg-rules---east-us"></a>Az NSG-szabályok – USA keleti RÉGIÓJA

1. HTTPS (443) kimenő biztonsági szabály létrehozása "Storage.EastUS" az NSG-t az alábbi képernyőképen látható módon.

      ![Storage-címke](./media/azure-to-azure-about-networking/storage-tag.png)

2. Kimenő HTTPS (443) szabályok létrehozását az összes IP-címtartományokat, amelyek megfelelnek az Office 365 [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).
3. A Site Recovery IP-címek, amelyek megfelelnek a célhelyen kimenő HTTPS (443) a szabályok létrehozása:

   **Hely** | **Site Recovery IP-cím** |  **Site Recovery monitorozási IP-cím**
    --- | --- | ---
   USA középső régiója | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>Az NSG-szabályok – USA középső RÉGIÓJA

Ezek a szabályok szükségesek, hogy replikációs lehet engedélyezni a a célrégióban a forrás régió-feladatátvétel után:

1. HTTPS (443) kimenő biztonsági szabály létrehozása "Storage.CentralUS" az NSG-t.

2. Kimenő HTTPS (443) szabályok létrehozását az összes IP-címtartományokat, amelyek megfelelnek az Office 365 [hitelesítés és identitás IP V4 végpontok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity).

3. Kimenő HTTPS (443) a szabályok létrehozása a Site Recovery IP-címek, amelyek megfelelnek a forrás helye:

   **Hely** | **Site Recovery IP-cím** |  **Site Recovery monitorozási IP-cím**
    --- | --- | ---
   USA középső régiója | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>Hálózati virtuális berendezés konfigurációjának

Ha a hálózati virtuális berendezések (nva-k) segítségével szabályozhatja a virtuális gépek kimenő hálózati forgalmát, a készülék előfordulhat, hogy leszabályozza, ha a replikációs forgalmat az nva-n keresztül továbbítja. Azt javasoljuk, hogy hoz létre egy hálózati végpontot a virtuális hálózat "Tároló" úgy, hogy a replikálás forgalma nem halad, az nva-n.

### <a name="create-network-service-endpoint-for-storage"></a>A tárolási hálózati szolgáltatásvégpont létrehozása
Létrehozhat egy hálózati végpontot a virtuális hálózat "Tároló" úgy, hogy a replikálás forgalma nem hagyja el az Azure-határ.

- Válassza ki az Azure virtuális hálózatot, majd kattintson a "szolgáltatásvégpontokkal:

    ![Storage-végpont](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Kattintson a "Hozzáadás" és "Szolgáltatásvégpontok hozzáadása" lap megnyitása
- Válassza ki a "Microsoft.Storage" a "Service" és "Alhálózatok" mező mellett a szükséges alhálózatokat, és kattintson a "Hozzáadás" gombra

>[!NOTE]
>Az ASR használt tárfiókok nem virtuális hálózati hozzáférésének korlátozása. Érdemes engedélyezi a hozzáférést minden hálózatból

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Az Azure alapértelmezett rendszer útvonalat a 0.0.0.0/0 címelőtag a felülbírálhatja egy [egyéni útválasztási](../virtual-network/virtual-networks-udr-overview.md#custom-routes) és átirányít a VM forgalmat egy a helyszíni hálózati virtuális berendezésre (NVA), de ez a konfiguráció nem ajánlott a Site Recovery a replikáció. Ha egyéni útvonalakat használ, akkor [hozzon létre egy virtuális hálózati szolgáltatásvégpont](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatot "Tároló" úgy, hogy a replikálás forgalma nem hagyja el az Azure határain.

## <a name="next-steps"></a>További lépések
- A számítási feladatai védelméről [Azure-beli virtuális gépek replikálása](site-recovery-azure-to-azure.md).
- Tudjon meg többet [IP-cím megőrzése](site-recovery-retain-ip-azure-vm-failover.md) Azure virtuális gép feladatátvételi.
- További információ a vész-helyreállítási [Azure-beli virtuális gépek az expressroute-tal ](azure-vm-disaster-recovery-with-expressroute.md).
