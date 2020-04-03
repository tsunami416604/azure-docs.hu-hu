---
title: 'Rövid útmutató: Csatlakozás egy virtuális géphez egy privát IP-cím használatával: Azure Bastion'
description: Ebben a cikkben megtudhatja, hogyan hozhat létre egy Azure-bastion gazdagépet egy virtuális gépről, és biztonságosan csatlakozhat egy privát IP-cím használatával.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619253"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>Rövid útmutató: Csatlakozás virtuális géphez egy privát IP-cím és az Azure-bastion használatával

Ez a rövid útmutató cikk bemutatja, hogyan csatlakozhat egy virtuális géphez egy privát IP-cím használatával. Ha a Bastion-on keresztül csatlakozik, a virtuális gépeknek nincs szükségük nyilvános IP-címre. Ebben a cikkben leírt lépések segítségével telepítheti a Bastion-t a virtuális hálózatra a virtuális gépen keresztül a portálon. A szolgáltatás kiépítése után az RDP/SSH felület érhető el az összes virtuális gép ugyanabban a virtuális hálózatban.

## <a name="prerequisites"></a><a name="prereq"></a>Előfeltételek

* Egy Azure virtuális hálózat.
* A virtuális hálózatban található, nyitott 3389-es porttal rendelkező Azure-virtuális gép.

### <a name="example-values"></a>Példaértékek

|**Név** | **Érték** |
| --- | --- |
| Név |  VNet1Bastion |
| Régió | eastus |
| Virtuális hálózat |  VNet1 |
| + Alhálózat neve | AzureBastionSubnet |
| Az AzureBastionSubnet-címek |  10.1.254.0/27 |
| Nyilvános IP-cím |  Új létrehozása |
| Nyilvános IP-cím | VNet1BastionPIP  |
| Nyilvános IP-cím Termékváltozat |  Standard  |
| Hozzárendelés  | Statikus |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion-gazdagép létrehozása

Ha egy meglévő virtuális gép használatával hoz létre megerősített állomást a portálon, a különböző beállítások automatikusan a virtuális gépnek és/vagy a virtuális hálózatnak felelnek meg.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com). Nyissa meg a virtuális gépet, majd kattintson a **Csatlakozás gombra.**

   ![virtuális gép beállításai](./media/quickstart-host-portal/vm-settings.png)
1. A legördülő menüben válassza a **Bástya**lehetőséget.
1. A Csatlakozás lapon válassza **a Bástya használata**lehetőséget.

   ![bástya kiválasztása](./media/quickstart-host-portal/select-bastion.png)

1. A Bástya lapon töltse ki a következő beállításmezőket:

   * **Név**: A megerősített állomás elnevezése
   * **Alhálózat:** Az az alhálózat a virtuális hálózaton belül, amelyre a megerősített erőforrás telepítve lesz. Az alhálózatot **Az AzureBastionSubnet**névvel kell létrehozni. A név lehetővé teszi az Azure-nak, hogy melyik alhálózatra telepítse a Bástya erőforrást. Ez eltér az átjáró alhálózatátótól. Legalább /27 vagy nagyobb (/27, /26, /25 és így tovább) alhálózatot használjon.
   
      * Válassza **az Alhálózat-konfiguráció kezelése**lehetőséget, majd a + **Alhálózat**lehetőséget.
      * Az Alhálózat hozzáadása lapon írja be az **AzureBastionSubnet .In**the Add subnet page, type AzureBastionSubnet .
      * Adja meg a címtartományt cidr jelöléssel. Például: 10.1.254.0/27.
      * Az alhálózat létrehozásához válassza az **OK gombot.** A lap tetején lépjen vissza a Bastion oldalra a többi beállítás befejezéséhez.

         ![ugrás a megerősített beállításokra](./media/quickstart-host-portal/navigate-bastion.png)
   * **Nyilvános IP-cím**: Ez annak a megerősített erőforrásnak a nyilvános IP-je, amelyen az RDP/SSH-t (a 443-as porton keresztül) hozzá fogják férni. Hozzon létre egy új nyilvános IP-címet, vagy használjon egy meglévőt. A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozott megerősített erőforrásnak.
   * **Nyilvános IP-cím neve**: A nyilvános IP-cím erőforrás neve.
1. Az érvényesítési képernyőn kattintson a **Létrehozás gombra.** Várjon körülbelül 5 percet a megerősített erőforrás létrehozásához és üzembe helyezéséhez.

   ![bástyagazdagép létrehozása](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>Kapcsolódás

Miután a Bastion telepítve van a virtuális hálózatra, a képernyő a csatlakozási lapra változik.

1. Írja be a virtuális gép felhasználónevét és jelszavát. Ezután válassza a **Csatlakozás lehetőséget.**

   ![csatlakozásra](./media/quickstart-host-portal/connect.png)
1. Az RDP-kapcsolat a virtuális gép hez keresztül Bastion nyílik meg közvetlenül az Azure Portalon (HTML5 felett) a 443-as port és a Bástya szolgáltatás használatával.

   ![RDP-csatlakozás](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte a virtuális hálózat és a virtuális gépek használatát, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *TestRG1* értéket a portál tetején lévő **keresőmezőbe,** és válassza a **TestRG1** lehetőséget a keresési eredmények között.

2. Válassza az **Erőforráscsoport törlése** elemet.

3. Írja be *a TestRG1* értéket **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy bástya-állomást a virtuális hálózathoz, majd biztonságosan csatlakozott egy virtuális géphez a Bástyagazdagazdagépen keresztül.

* Ha többet szeretne megtudni az Azure-bástyáról, olvassa el a [Bástya áttekintése](bastion-overview.md) és a [Bástya gyakori kérdések című témakört.](bastion-faq.md)
* Ha hálózati biztonsági csoportokat szeretne használni az Azure Bastion alhálózattal, olvassa el a [Munka az NSG-kkel című témakört.](bastion-nsg.md)
* Az Azure Bastion gazdagép beállításainak magyarázatát tartalmazó utasításokat az [oktatóanyag](bastion-create-host-portal.md)ban találja.
* Virtuálisgép-méretezési csoporthoz való csatlakozásról a [Csatlakozás virtuálisgép-méretezési csoporthoz az Azure Bastion használatával (Csatlakozás virtuálisgép-méretezési csoporthoz) témakörben.](bastion-connect-vm-scale-set.md)