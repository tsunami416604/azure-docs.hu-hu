---
title: Hozzon létre egy privát Azure DNS-zónát, az Azure portal használatával
description: Ebben az oktatóanyagban egy saját DNS-zónát és -rekordot hozunk létre és tesztelünk az Azure DNS-ben. Ez az egy lépésenkénti útmutató, létrehozása és kezelése az első saját DNS-zóna és -rekordhalmazok az Azure portal használatával.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147818"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Oktatóanyag: Hozzon létre egy privát Azure DNS-zónát, az Azure portal használatával

Ez az oktatóanyag végigvezeti a lépéseken, hozhat létre az első saját DNS-zóna és -rekordhalmazok az Azure portal használatával.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezek az úgynevezett *társított* virtuális hálózatok. Automatikus regisztrációnak engedélyezve van, amikor az Azure DNS-ben is frissíti a zóna rekordok, amikor egy virtuális gép létrejött, módosításokat az "IP-címet, vagy törölték.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Saját DNS-zóna létrehozása
> * Virtuális hálózat létrehozása
> * A virtuális hálózat csatolása
> * Tesztelési célú virtuális gépek létrehozása
> * További DNS-rekord létrehozása
> * A saját zóna tesztelése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Ha szeretné, ez az oktatóanyag segítségével befejezheti [Azure PowerShell-lel](private-dns-getstarted-powershell.md) vagy [Azure CLI-vel](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Saját DNS-zóna létrehozása

A következő példában létrehozunk egy DNS-zónát **private.contoso.com** erőforráscsoportból nevezett **MyAzureResourceGroup**.

A DNS zone contains the DNS entries for a domain. Üzemeltessen saját tartományt az Azure DNS-ben, hozzon létre egy DNS-zónát az adott tartománynévhez.

![Privát DNS-zónák keresése](media/private-dns-portal/search-private-dns.png)

1. A portál keresősávba írja be a **privát dns-zónák** a keresési mezőbe, majd nyomja le **Enter**.
1. Válassza ki **privát DNS-zóna**.
2. Válassza ki **saját dns-zóna létrehozása**.

1. Az a **privát DNS-zóna létrehozása** lapon adja meg a következő értékeket:

   - **Erőforráscsoport**: Válassza ki **új létrehozása**, adja meg *MyAzureResourceGroup*, és válassza ki **OK**. Az erőforráscsoport nevét az Azure-előfizetésen belül egyedinek kell lennie. 
   -  **Név**: Típus *private.contoso.com* ebben a példában.
1. A **erőforráscsoport helye**válassza **USA nyugati középső Régiója**.

1. Válassza ki **felülvizsgálat + létrehozás**.

1. Kattintson a **Létrehozás** gombra.

A zóna létrehozása eltarthat néhány percig.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. A portáloldal bal felső sarokban, válassza ki **erőforrás létrehozása**, majd **hálózatkezelés**, majd **virtuális hálózati**.
2. A **neve**, típus **myAzureVNet**.
3. A **erőforráscsoport**válassza **MyAzureResourceGroup**.
4. A **hely**válassza **USA nyugati középső Régiója**.
5. Fogadja el az alapértelmezett értékeket, és válassza ki **létrehozás**.

## <a name="link-the-virtual-network"></a>A virtuális hálózat csatolása

A privát DNS-zóna összekapcsolása egy virtuális hálózatot, hozzon létre egy virtuális hálózati kapcsolat.

![Virtuális hálózati kapcsolat hozzáadása](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Nyissa meg a **MyAzureResourceGroup** erőforrás-csoport, és válassza a **private.contoso.com** saját zóna.
2. A bal oldali panelen válassza ki a **virtuális hálózati kapcsolatok**.
3. Válassza a **Hozzáadás** lehetőséget.
4. Típus **myLink** számára a **hivatkozásnév**.
5. A **virtuális hálózati**válassza **myAzureVNet**.
6. Válassza ki a **automatikus regisztráció engedélyezése** jelölőnégyzetet.
7. Kattintson az **OK** gombra.

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

1. A portáloldal bal felső sarokban, válassza ki a **erőforrás létrehozása**, majd válassza ki **Windows Server 2016 Datacenter**.
1. Válassza ki **MyAzureResourceGroup** az erőforráscsoportnak.
1. Típus **myVM01** – a virtuális gép neve.
1. Válassza ki **USA nyugati középső RÉGIÓJA** számára a **régió**.
1. Típus **azureadmin** az rendszergazdájának felhasználóneve.
2. Típus **Azure12345678** a jelszót és erősítse meg a jelszót.

5. A **nyilvános bejövő portok**, jelölje be **lehetővé teszi a kiválasztott portok**, majd válassza ki **RDP (3389-es)** a **bejövő portok kiválasztása**.
10. Fogadja el az oldal többi alapértelmezett értéket, és kattintson a **tovább: Lemezek >** .
11. Elfogadhatja az alapértelmezett beállításokat a a **lemezek** lapon, majd kattintson a **tovább: Hálózatkezelés >** .
1. Győződjön meg arról, hogy **myAzureVNet** a virtuális hálózat van kiválasztva.
1. Fogadja el az oldal többi alapértelmezett értéket, és kattintson **tovább: Felügyelet >** .
2. A **rendszerindítási diagnosztika**válassza **ki**, fogadja el a többi alapértelmezett beállítást, és válassza **felülvizsgálat + létrehozása**.
1. Tekintse át a beállításokat, és kattintson a **létrehozás**.

Ismételje meg ezeket a lépéseket, és hozzon létre egy másik virtuális gépet nevű **myVM02**.

Eltarthat néhány percig, mindkét virtuális gép végrehajtásához.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

 Az alábbi példa létrehoz egy rekordot a relatív nevű **db** a DNS-zónában **private.contoso.com**, erőforráscsoportban **MyAzureResourceGroup**. A beállított rekord teljes neve **db.private.contoso.com**. A rekord típusa "A", akkor az IP-címét **myVM01**.

1. Nyissa meg a **MyAzureResourceGroup** erőforrás-csoport, és válassza a **private.contoso.com** saját zóna.
2. Kattintson a **+ Rekordhalmaz** gombra.
3. A **neve**, típus **db**.
4. A **IP-cím**, írja be az IP-címet lát a **myVM01**. Automatikus, a virtuális gép indításakor regisztrálva kell lennie.
5. Kattintson az **OK** gombra.

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a névfeloldás a **private.contoso.com** saját zóna.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa a következő parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Ismételje meg ezt a myVM02 gép esetében is.

### <a name="ping-the-vms-by-name"></a>Virtuális gépek pingelése név alapján

1. A myVM02 gép Windows PowerShell parancssorából pingelje meg a myVM01 gépet az automatikusan regisztrált gazdagépnév használatával:
   ```
   ping myVM01.private.contoso.com
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Most pingelje meg a korábban létrehozott **db** nevet:
   ```
   ping db.private.contoso.com
   ```
   A következőhöz hasonló kimenetnek kell megjelennie:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Az összes erőforrás törlése

Ha már nincs rájuk szükség, a **MyAzureResourceGroup** erőforráscsoport törlésével törölheti az oktatóanyagban létrehozott erőforrásokat.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy privát DNS-zónát, létrehozott egy DNS-rekordot, és tesztelte a zónát.
Ezután behatóbban megismerheti a DNS-zónákat.

> [!div class="nextstepaction"]
> [Az Azure DNS használata saját tartományok esetében](private-dns-overview.md)
