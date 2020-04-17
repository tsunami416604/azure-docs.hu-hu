---
title: Privát IP-címek konfigurálása virtuális gépekhez – Azure Portal
description: Ismerje meg, hogyan konfigurálhatja a privát IP-címeket az Azure Portalhasználatával a virtuális gépekhez.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461548"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Virtuális gép privát IP-címének konfigurálása az Azure Portal használatával

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

A következő mintalépések egy egyszerű környezet már létrevételét várják. Ha a lépéseket úgy szeretné futtatni, ahogy azok ebben a dokumentumban megjelennek, először [hozzon létre egy virtuális hálózatot.](quick-create-portal.md#create-a-virtual-network) A 3.

| Beállítás | Érték |
| ------- | ----- |
| Name (Név) | *TestVNet* |
| Címtér | *192.168.0.0/16* |
| Erőforráscsoport | **TestRG** (ha szükséges, válassza **az Új létrehozása** a létrehozásához) |
| Alhálózat - név | *Frontend* |
| Alhálózat – címtartomány | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Virtuális gép létrehozása statikus magánjellegű IP-címek teszteléséhez
Amikor létrehoz egy virtuális gép erőforrás-kezelő telepítési módban, nem állíthat be statikus privát IP-címet az Azure Portal használatával. Ehelyett először hozza létre a virtuális gép. Ezután beállíthatja, hogy a privát IP statikus legyen.

*Dns01* nevű virtuális gép létrehozásához a *TestVNet*nevű virtuális hálózat *FrontEnd* alhálózatában hajtson végre alábbi lépéseket:

1. Az [Azure Portal](https://portal.azure.com) menüben válassza az **Erőforrás létrehozása parancsot.**

    ![Erőforrás létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Válassza a Virtuális **gép számítása** > **lehetőséget.**

    ![Virtuális gép létrehozása, Azure portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. Az **Alapok területen**adja meg az alábbi táblázatban leírt értékeket az elemekhez. Ezután válassza **a Tovább&nbsp;:&nbsp;Lemezek,** majd a Tovább **&nbsp;:&nbsp;Hálózat lehetőséget.**

    | Elem | Érték |
    | --- | --- |
    | **Előfizetés** | Jelenlegi előfizetése |
    | **Erőforráscsoport** | **TestRG** (válasszon a legördülő listából) |
    | **Virtuális gép neve** | *DNS01* |
    | **Régió** | **(US) USA keleti része** |
    | **Kép** | **Windows Server 2019 Datacenter** |
    | **Méret** | **VM mérete** **B1ls**, **felajánlása** **Standard** |
    | **Felhasználónév** | A rendszergazdai fiók felhasználóneve |
    | **Jelszó** | A rendszergazdai fiók felhasználónevének jelszava |
    | **Jelszó megerősítése** | A jelszó újra |

    ![Alapok lap, Virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. A **Hálózat csoportban**adja meg az elemek értékeit az alábbi táblázatban leírtak szerint, majd kattintson a **Tovább**gombra.

    | Elem | Érték |
    | --- | --- |
    | **Virtuális hálózat** | **TestVNet** |
    | **Alhálózat** | **Frontend** |

    ![Hálózat lap, Virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. A **Kezelés csoportban**válassza **a Diagnosztikai tárfiók**csoportban a **vnetstorage lehetőséget.** Ha a tárfiók nem jelenik meg a listában, válassza az **Új létrehozása**lehetőséget, adja meg a *vnetstorage* **nevét,** és válassza **az OK gombot.** Végül válassza **&nbsp;+&nbsp;a Létrehozás áttekintése**lehetőséget.

    ![Kezelés lap, Virtuális gép létrehozása, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Az **Ellenőrzés + create csoportban**tekintse át az áttekintő információkat, majd kattintson a **Létrehozás gombra.**

    ![Véleményezés + Létrehozás lap, Virtuális gép létrehozása, Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

A virtuális gép létrehozása után a következő üzenet jelenik meg.

![Üzembe helyezés befejezésének üzenet, Hozzon létre egy virtuális gépet, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Virtuális gép személyes IP-címadatainak lekérése
Az új virtuális gép személyes IP-címadatainak megtekintése:

1. A virtuális gép megkereséséhez keresse meg az [Azure Portalon.](https://portal.azure.com) Keressen és válasszon **virtuális gépek**lehetőséget.

    ![Virtuális gépek, Keresőmező, Azure portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Válassza ki az új virtuális gép (**DNS01**) nevét.

    ![Virtuálisgépek listája, Azure portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Válassza **a Hálózat lehetőséget,** és válassza ki a felsorolt egyetlen hálózati illesztőelemet.

    ![Hálózati felület, hálózatkezelés, virtuális gép, Azure portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Válassza **az IP-konfigurációk**lehetőséget, és válassza ki a táblázatban felsorolt IP-konfigurációt.

    ![IP-konfiguráció, hálózati felület, hálózatkezelés, virtuális gép, Azure portál](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. A **Privát IP-cím beállításaiban**a **TestVNet/FrontEnd** virtuális hálózat/alhálózat alatt jegyezze fel a **Hozzárendelés** értékét (**dinamikus** vagy **statikus**) és az **IP-címet.**

    ![Dinamikus vagy statikus hozzárendelés, régi privát IP-címbeállítások, IP-konfiguráció, hálózati adapter, hálózatkezelés, virtuális gép, Azure portál](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Statikus privát IP-cím hozzáadása meglévő virtuális géphez
Statikus privát IP-cím hozzáadása az új virtuális géphez:

1. Az IP-konfigurációs lapon állítsa a privát IP-cím hozzárendelését **Static (Statikus) beállításra.**
2. Módosítsa a privát **IP-címet** *192.168.1.101-re,* majd válassza a **Mentés**lehetőséget.
   
    ![Dinamikus vagy statikus hozzárendelés, új privát IP-címbeállítások, IP-konfiguráció, hálózati adapter, hálózatkezelés, virtuális gép, Azure portál](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Ha a **Mentés** gombra kattintva azt veszi észre, hogy a hozzárendelés továbbra is **Dinamikus,** a beírt IP-cím már használatban van. Próbálkozzon egy másik IP-címmel.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Statikus privát IP-cím eltávolítása virtuális gépről
A statikus privát IP-cím eltávolítása a virtuális gépről:

Az IP-konfigurációs lapon állítsa a privát IP-cím hozzárendelését **Dinamikus**beállításra, majd kattintson a **Mentés gombra.**

## <a name="set-ip-addresses-within-the-operating-system"></a>IP-címek beállítása az operációs rendszeren belül

A virtuális gép operációs rendszerén belül nem kell statikusan hozzárendelni az Azure virtuális géphez rendelt *privát* IP-címet. Csak akkor végezze el a privát IP-cím statikus hozzárendelését, ha szükséges, például [ha sok IP-címet rendel a virtuális gépekhez.](virtual-network-multiple-ip-addresses-portal.md) Ha manuálisan állítja be a privát IP-címet az operációs rendszeren belül, győződjön meg arról, hogy az megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt privát IP-címmel. Ellenkező esetben elveszhet a virtuális géphez való kapcsolat. További információ a [privát IP-címbeállításokról.](virtual-network-network-interface-addresses.md#private)

Emellett soha ne rendelje hozzá manuálisan az Azure virtuális géphez rendelt *nyilvános* IP-címet a virtuális gép operációs rendszerén belül.

## <a name="next-steps"></a>További lépések

További információ az [IP-címbeállítások](virtual-network-network-interface-addresses.md)kezeléséről.
