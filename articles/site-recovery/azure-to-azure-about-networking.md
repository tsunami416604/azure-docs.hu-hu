---
title: Az Azure VM vész-helyreállítási hálózatának az Azure Site Recovery szolgáltatással kapcsolatos hálózati szolgáltatása
description: Áttekintést nyújt a hálózati azure-beli virtuális gépek az Azure Site Recovery használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: sutalasi
ms.openlocfilehash: 58348c9aed14a5cc9126be780fe01817274a0b47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283259"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Hálózatkezelés az Azure Virtuálisgép vész-helyreállítási



Ez a cikk hálózati útmutatást nyújt az Azure-beli virtuális gépek egyik régióból a másikba történő replikálása és helyreállítása során az [Azure Site Recovery](site-recovery-overview.md)használatával.

## <a name="before-you-start"></a>Előkészületek

Ismerje meg, hogyan biztosít a Site Recovery vészhelyreállítást ebben a [forgatókönyvben.](azure-to-azure-architecture.md)

## <a name="typical-network-infrastructure"></a>Tipikus hálózati infrastruktúra

Az alábbi ábra egy tipikus Azure-környezetet ábrázol az Azure virtuális gépeken futó alkalmazások számára:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Ha Azure ExpressRoute-ot vagy VPN-kapcsolatot használ a helyszíni hálózatról az Azure-ba, a környezet a következő:

![ügyfél-környezet](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

A hálózatok általában tűzfalak és hálózati biztonsági csoportok (NSG-k) használatával védettek. A tűzfalak URL- vagy IP-alapú engedélyezési listát használnak a hálózati kapcsolatok szabályozására. Az NSG-k olyan szabályokat biztosítanak, amelyek IP-címtartományokat használnak a hálózati kapcsolat szabályozásához.

>[!IMPORTANT]
> A site recovery nem támogatja a hitelesített proxy használatát a hálózati kapcsolat vezérlésére, és a replikáció nem engedélyezhető.


## <a name="outbound-connectivity-for-urls"></a>Kimenő kapcsolat URL-címek esetén

Ha URL-alapú tűzfalproxyt használ a kimenő kapcsolatok szabályozásához, engedélyezze a következő hely-helyreállítási URL-címeket:


**Url** | **Részletek**
--- | ---
*.blob.core.windows.net | Szükséges, hogy az adatok at lehet írni a gyorsítótár-tárfiók a forrásrégióban a virtuális gépről. Ha ismeri a virtuális gépek összes gyorsítótár-tárfiókját, a *.blob.core.windows.net helyett engedélyezheti a hozzáférést az adott tárfiók URL-címeihez (pl. cache1.blob.core.windows.net és cache2.blob.core.windows.net).
login.microsoftonline.com | A Site Recovery szolgáltatás URL-címének engedélyezéséhez és hitelesítéséhez szükséges.
*.hypervrecoverymanager.windowsazure.com | Szükséges, hogy a Site Recovery szolgáltatás kommunikációja a virtuális gépről is megtörténhessen.
*.servicebus.windows.net | Szükséges, hogy a site recovery figyelési és diagnosztikai adatokat lehet írni a virtuális gépről.
*.vault.azure.net | Lehetővé teszi az ADE-kompatibilis virtuális gépek replikációjának engedélyezését a portálon keresztül
*.automation.ext.azure.com | Lehetővé teszi a mobilügynök automatikus frissítésének engedélyezését egy replikált elemhez a portálon keresztül

## <a name="outbound-connectivity-using-service-tags"></a>Kimenő kapcsolat szolgáltatáscímkék használatával

Ha nsg-t használ a kimenő kapcsolatok vezérléséhez, ezeket a szolgáltatáscímkéket engedélyezni kell.

- A forrásrégióban lévő tárfiókok esetén:
    - Hozzon létre egy [Storage szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) alapú NSG-szabályt a forrásrégióhoz.
    - Engedélyezze ezeket a címeket, hogy az adatok a virtuális gépről a gyorsítótár-tárfiókba írhatók legyenek.
- Az [Azure Active Directory (AAD) szolgáltatáscímke-alapú](../virtual-network/security-overview.md#service-tags) NSG-szabály létrehozása az AAD-nek megfelelő összes IP-címhez való hozzáférés engedélyezéséhez
- Hozzon létre egy EventsHub szolgáltatáscímke alapú NSG-szabályt a célrégióhoz, amely lehetővé teszi a site recovery figyelésének elérését.
- Hozzon létre egy AzureSiteRecovery szolgáltatáscímke alapú NSG-szabályt, amely lehetővé teszi a hozzáférést a Site Recovery szolgáltatáshoz bármely régióban.
- Hozzon létre egy AzureKeyVault szolgáltatáscímke alapú NSG-szabályt. Ez csak az ADE-kompatibilis virtuális gépek portálon keresztüli replikációjának engedélyezéséhez szükséges.
- Hozzon létre egy GuestAndHybridManagement szolgáltatáscímke alapú NSG-szabályt. Ez csak a mobilügynök automatikus frissítésének engedélyezéséhez szükséges egy replikált elem hez a portálon keresztül.
- Azt javasoljuk, hogy hozza létre a szükséges NSG-szabályok egy teszt NSG, és ellenőrizze, hogy nincsenek-e problémák, mielőtt a szabályok at egy éles NSG létrehozása előtt.

## <a name="example-nsg-configuration"></a>Példa NSG-konfigurációra

Ez a példa bemutatja, hogyan konfigurálhatja az NSG-szabályok at a virtuális gép replikálására.

- Ha NSG-szabályokat használ a kimenő kapcsolatok vezérléséhez, használja a "HTTPS kimenő kimenő" szabályok port:443 az összes szükséges IP-címtartományok.
- A példa feltételezi, hogy a virtuális gép forráshelye "USA keleti régiója", a célhely pedig "USA középső régiója".

### <a name="nsg-rules---east-us"></a>NSG szabályok - USA keleti része

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage.EastUS" számára az NSG-n, ahogy az az alábbi képernyőképen látható.

      ![tároló címke](./media/azure-to-azure-about-networking/storage-tag.png)

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureActiveDirectory" számára az NSG-n, ahogy az az alábbi képernyőképen látható.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. A fenti biztonsági szabályokhoz hasonlóan hozzon létre kimenő HTTPS (443) biztonsági szabályt az "EventHub.CentralUS" számára az NSG-n, amely megfelel a célhelynek. Ez lehetővé teszi a hozzáférést a site recovery figyeléséhez.

4. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureSiteRecovery" számára az NSG-n. Ez bármely régióban hozzáférést biztosít a hely-helyreállítási szolgáltatáshoz.

### <a name="nsg-rules---central-us"></a>NSG-szabályok - USA középső régiója

Ezekre a szabályokra azért van szükség, hogy a replikáció engedélyezhető legyen a célrégióból a forrásrégióba a feladatátvétel után:

1. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt a "Storage.CentralUS" számára az NSG-n.

2. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureActiveDirectory" számára az NSG-n.

3. A fenti biztonsági szabályokhoz hasonlóan hozzon létre kimenő HTTPS (443) biztonsági szabályt az "EventHub.EastUS" számára az NSG-n, amely megfelel a forráshelynek. Ez lehetővé teszi a hozzáférést a site recovery figyeléséhez.

4. Hozzon létre egy kimenő HTTPS (443) biztonsági szabályt az "AzureSiteRecovery" számára az NSG-n. Ez bármely régióban hozzáférést biztosít a hely-helyreállítási szolgáltatáshoz.

## <a name="network-virtual-appliance-configuration"></a>Hálózati virtuális berendezés konfigurációja

Ha hálózati virtuális berendezések (NVA-k) segítségével szabályozza a kimenő hálózati forgalmat a virtuális gépekről, a készülék előfordulhat, hogy a rendszer szabályozza, ha az összes replikációs forgalom áthalad az NVA.If you are using network virtual appliances (NVA) to control outbound network traffic from Vms, the appliance might get throttled if all the replication traffic passes through the NVA. Azt javasoljuk, hogy hozzon létre egy hálózati szolgáltatás végpontot a virtuális hálózatban a "Storage", hogy a replikációs forgalom nem megy az NVA.

### <a name="create-network-service-endpoint-for-storage"></a>Hálózati szolgáltatásvégpont létrehozása a storage számára
Létrehozhat egy hálózati szolgáltatásvégpontot a virtuális hálózatban a "Storage" számára, hogy a replikációs forgalom ne hagyja el az Azure-határt.

- Válassza ki az Azure virtuális hálózatát, és kattintson a "Szolgáltatás végpontjai"

    ![tárolási végpont](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- Megnyílik a "Hozzáadás" és a "Szolgáltatásvégpontok hozzáadása" fülre
- Válassza a "Microsoft.Storage" lehetőséget a "Szolgáltatás" területen, és a szükséges alhálózatokat az "Alhálózatok" mezőben, és kattintson a "Hozzáadás" gombra.

>[!NOTE]
>Ne korlátozza a virtuális hálózati hozzáférést az asr-hez használt tárfiókokhoz. Engedélyeznie kell a hozzáférést a "Minden hálózat"

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Felülbírálhatja az Azure alapértelmezett rendszerútvonalát a 0.0.0.0/0-s címelőtaghoz [egyéni útvonallal,](../virtual-network/virtual-networks-udr-overview.md#custom-routes) és átirányíthatja a virtuális gép forgalmát egy helyszíni hálózati virtuális berendezésre (NVA), de ez a konfiguráció nem ajánlott a Site Recovery replikációjához. Ha egyéni útvonalakat használ, [hozzon létre egy virtuális hálózati szolgáltatás végpontot](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) a virtuális hálózatban a "Storage" számára, hogy a replikációs forgalom ne hagyja el az Azure-határt.

## <a name="next-steps"></a>További lépések
- Kezdje el védeni a számítási feladatokat [az Azure virtuális gépek replikálásával.](site-recovery-azure-to-azure.md)
- További információ az Azure virtuálisgép-feladatátvétel [IP-címmegőrzéséről.](site-recovery-retain-ip-azure-vm-failover.md)
- További információ az [Azure virtuális gépek vészutáni](azure-vm-disaster-recovery-with-expressroute.md)helyreállításáról az ExpressRoute segítségével.
