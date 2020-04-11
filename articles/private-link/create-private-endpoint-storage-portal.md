---
title: Privát csatlakozás tárfiókokhoz az Azure privát végpontok használatával
description: Ismerje meg, hogyan csatlakozhat privát módon egy tárfiókhoz az Azure-ban egy privát végpont használatával.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 111e6e2f80c3460f363c496b7b32befdca16250d
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115108"
---
# <a name="connect-privately-to-a-storage-account-using-azure-private-endpoint"></a>Privát csatlakozás tárfiókokhoz az Azure privát végpontok használatával
Az Azure Private Endpoint az Azure-beli privát kapcsolat alapvető építőköve. Lehetővé teszi, hogy az Azure-erőforrások, például a virtuális gépek (VM-ek) privát módon kommunikáljanak a Private Link-erőforrásokkal.

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre virtuális gépet egy Azure virtuális hálózaton, egy tárfiókot egy privát végponthasználatával az Azure Portalon. Ezután biztonságosan elérheti a tárfiókot a virtuális gépről.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Ebben a szakaszban virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás (ebben a példában egy tárfiók) eléréséhez használt virtuális gép üzemeltetéséhez.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban virtuális hálózatot és az alhálózatot hoz létre a private link erőforrás eléréséhez használt virtuális gép üzemeltetéséhez.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroup |
| **\<virtuális hálózat neve>** | myVirtualNetwork          |
| **\<régiónév>**          | USA nyugati középső régiója      |
| **\<IPv4-címtér>**   | 10.1.0.0\16          |
| **\<alhálózat-név>**          | mySubnet        |
| **\<alhálózati címtartomány>** | 10.1.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]


### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az**Compute** >  **Erőforrás** > létrehozása**virtuális gép**létrehozása lehetőséget.

1. A **Virtuális gép létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY részletei** |  |
    | Virtuális gép neve | Adja meg *a myVm*- |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Rendelkezésre állási beállítások | Hagyja meg az alapértelmezett **Nincs szükség infrastruktúra-redundanciára**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter lehetőséget.** |
    | Méret | Hagyja meg az alapértelmezett **Standard DS1 v2 -t.** |
    | **RENDSZERGAZDAI FIÓK** |  |
    | Felhasználónév | Adja meg az Ön által választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúságúnak kell lennie, [az összetettségre vonatkozó követelmények teljesülése mellett](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Írja be újra a jelszót. |
    | **BEJÖVŐ PORTSZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **Nincs**. |
    | **TAKARÍTSON MEG PÉNZT** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **Nem értéket.** |
    |||

1. Válassza a **Tovább: Lemezek**lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek**csoportban hagyja meg az alapértelmezett beállításokat, és válassza a **Tovább: Hálózat lehetőséget.**

1. A **Virtuális gép létrehozása - Hálózat csoportban**válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24**értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-ip**. |
    | Nyilvános bejövő portok | Válassza **a Kijelölt portok engedélyezése**lehetőséget. |
    | Bejövő portok kiválasztása | Válassza a **HTTP** és **az RDP**lehetőséget.|
    ||

1. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt.

1. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.**

## <a name="create-your-private-endpoint"></a>A privát végpont létrehozása
Ebben a szakaszban egy privát tárfiókot hoz létre egy privát végpont használatával. 

1. Az Azure Portalképernyő bal felső részén válassza az **Erőforrás** > **tárterület-fiók****Storage** > létrehozása lehetőséget.

1. A **Tárfiók létrehozása – Alapismeretek**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY részletei** |  |
    | Storage account name (Tárfiók neve)  | Adja meg *a mystorageaccount -t.* Ha ez a név foglalt, hozzon létre egy egyedi nevet. |
    | Régió | Válassza a **WestCentralUS**lehetőséget. |
    | Teljesítmény| Hagyja meg az alapértelmezett **Standard**. |
    | Fióktípus | Hagyja meg az alapértelmezett **storage (általános célú v2)**. |
    | Replikáció | Válassza **az Olvasási hozzáférésű georedundáns tárolás (RA-GRS) lehetőséget.** |
    |||
  
3. Válassza a **Tovább: Hálózat lehetőséget.**
4. A **Tárfiók létrehozása - Hálózat**, kapcsolódási módszer csoportban válassza a Privát végpont **lehetőséget.**
5. A **Tárfiók létrehozása - Hálózat csoportban**válassza **a Privát végpont hozzáadása**lehetőséget. 
6. A **Saját végfelhasználói végpont létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | **A PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup**lehetőséget. Ezt az előző szakaszban hozta létre.|
    |Hely|Válassza a **WestCentralUS**lehetőséget.|
    |Név|Adja meg *a myPrivateEndpoint*.  |
    |Tárolási részerőforrás|Hagyja meg az alapértelmezett **Blob**. |
    | **Hálózati** |  |
    | Virtuális hálózat  | Válassza a *MyVirtualNetwork* elemet a *myResourceGroup*erőforráscsoportból. |
    | Alhálózat | Válassza a *mySubnet*lehetőséget . |
    | **PRIVÁT DNS-INTEGRÁCIÓ**|  |
    | Integrálás privát DNS-zónával  | Hagyja meg az alapértelmezett **Igen**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett **(Új) privatelink.blob.core.windows.net**. |
    |||
7. Válassza **az OK gombot.** 
8. Válassza az **Áttekintés + létrehozás** lehetőséget. A véleményezés + **létrehozás** lap, ahol az Azure érvényesíti a konfigurációt. 
9. Amikor megjelenik az **Érvényesítési átadott** üzenet, válassza a **Létrehozás gombot.** 
10. Tallózással keresse meg az imént létrehozott tárfiók-erőforrást.
11. Válassza a bal oldali tartalom menü **Hozzáférési kulcsok parancsát.**
12. Válassza a **Másolás** lehetőséget a key1 kapcsolati karakterláncán.
 
## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

Csatlakozzon a vm *myVm* az internetről az alábbiak szerint:

1. A portál keresősávjában írja be a *myVm*.

1. Kattintson a **Csatlakozás** gombra. A **Csatlakozás** gomb kiválasztása után megnyílik **a Csatlakozás a virtuális géphez.**

1. Válassza **az RDP-fájl letöltése lehetőséget.** Az Azure létrehoz egy Remote Desktop Protocol (*.rdp*) fájlt, és letölti azt a számítógépre.

1. Nyissa meg a *downloaded.rdp* fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy **további lehetőségek közül** > **választhat: Másik fiók használatával**adja meg a virtuális gép létrehozásakor megadott hitelesítő adatokat.

1. Válassza **az OK gombot.**

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az **Igen** vagy **a Folytatás**lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.  

## <a name="access-storage-account-privately-from-the-vm"></a>Tárfiók elérése a virtuális gépről

Ebben a szakaszban a magán-fiókhoz a privát végpont használatával csatlakozik.

1. A *myVM*távoli asztalán nyissa meg a PowerShellt.
2. Enter `nslookup mystorageaccount.blob.core.windows.net` A következőhöz hasonló üzenetet fog kapni:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mystorageaccount123123.privatelink.blob.core.windows.net
    Address:  10.0.0.5
    Aliases:  mystorageaccount.blob.core.windows.net
    ```
3. Telepítse a [Microsoft Azure Storage Explorert](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=windows).
4. Válassza ki **a Storage-fiókokat** a jobb gombbal.
5. Válassza **a Csatlakozás azure-tárolóhoz lehetőséget.**
6. Válassza **a Kapcsolati karakterlánc használata lehetőséget.**
7. Válassza a **Tovább lehetőséget.**
8. Adja meg a kapcsolati karakterláncot a korábban másolt adatok beillesztésével.
9. Válassza a **Tovább lehetőséget.**
10. Kattintson a **Csatlakozás** gombra.
11. Böngésszen a Blob-tárolók között a mystorageaccount-ból 
12. (Opcionálisan) Mappák létrehozása és/vagy fájlok feltöltése a *mystorageaccount-ba.* 
13. Zárja be a távoli asztali kapcsolatot a *myVM-mel.* 

További lehetőségek a tárfiók eléréséhez:
- A Microsoft Azure Storage Explorer egy önálló ingyenes alkalmazás a Microsofttól, amely lehetővé teszi, hogy vizuálisan dolgozzon az Azure tárolási adataival Windows, macOS és Linux rendszeren. Telepítheti az alkalmazást a tárfiók tartalmának privát böngészéséhez. 
 
- Az AzCopy segédprogram egy másik lehetőség a nagy teljesítményű, parancsfájlon cserélhető adatátvitelhez az Azure storage-hoz. Az AzCopy segítségével blob, fájl és tábla típusú tárolókból és tárolókba vihet át adatokat. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha elkészült a privát végpont, a tárfiók és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást: 
1. Írja be a *myResourceGroup* kifejezést a portál tetején lévő **Keresőmezőbe,** és válassza a *myResourceGroup* elemet a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Írja be a *myResourceGroup értéket* **az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához,** és válassza a **Törlés**lehetőséget. 

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban virtuális gépet hozott létre egy virtuális hálózaton és tárfiókon, valamint egy privát végponton. Ön az internetről egy virtuális géphez csatlakozott, és a Privát kapcsolat használatával biztonságosan kommunikált a tárfiókkal. Ha többet szeretne megtudni a Privát végpontról, olvassa el [a Mi az Azure-beli privát végpont?](private-endpoint-overview.md).
