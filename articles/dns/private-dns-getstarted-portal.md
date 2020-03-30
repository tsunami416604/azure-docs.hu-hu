---
title: Rövid útmutató – Azure-beli privát DNS-zóna létrehozása az Azure Portalhasználatával
description: Ebben a rövid útmutatóban létrehozhat és tesztel egy privát DNS-zónát, és rekordot az Azure DNS-ben. Ez egy lépésről lépésre útmutató az első privát DNS-zóna létrehozásához és kezeléséhez, és az Azure Portalon keresztül rögzíti.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244972"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy Azure-beli privát DNS-zónát az Azure Portalhasználatával

Ez a rövid útmutató végigvezeti a lépéseket, hogy hozza létre az első privát DNS-zóna és rekord az Azure Portalhasználatával.

Az egyes tartományokhoz tartozó DNS-rekordok üzemeltetése DNS-zónákban történik. A tartománya Azure DNS-ben való üzemeltetésének megkezdéséhez létre kell hoznia egy DNS-zónát az adott tartománynévhez. Ezután a tartománya összes DNS-rekordja ebben a DNS-zónában jön létre. A saját DNS-zóna virtuális hálózaton történő közzétételéhez meg kell adnia azon virtuális hálózatok listáját, amelyek számára engedélyezett a zónán belüli rekordok feloldása.  Ezeket *összekapcsolt* virtuális hálózatoknak nevezzük. Ha az automatikus regisztráció engedélyezve van, az Azure DNS is frissíti a zónarekordokat, amikor egy virtuális gép jön létre, módosítja annak IP-címét, vagy törlődik.

Ezen rövid útmutató segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Saját DNS-zóna létrehozása
> * Virtuális hálózat létrehozása
> * A virtuális hálózat összekapcsolása
> * Tesztelési célú virtuális gépek létrehozása
> * További DNS-rekord létrehozása
> * A saját zóna tesztelése

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

Ha szeretné, ezt a rövid útmutatót az [Azure PowerShell](private-dns-getstarted-powershell.md) vagy az [Azure CLI](private-dns-getstarted-cli.md)használatával végezheti el.

## <a name="create-a-private-dns-zone"></a>Saját DNS-zóna létrehozása

A következő példa létrehoz egy **PRIVATE.CONTOSO.COM** nevű DNS-zónát a **MyAzureResourceGroup**nevű erőforráscsoportban.

A DNS-zóna egy tartomány DNS-bejegyzéseit tartalmazza. Ha meg szeretné kezdeni a tartomány üzemeltetését az Azure DNS-ben, hozzon létre egy DNS-zónát az adott tartománynévhez.

![Privát DNS-zónák keresése](media/private-dns-portal/search-private-dns.png)

1. A portál keresősávján írja be a **privát dns zónákat** a keresőmezőbe, és nyomja le az **Enter billentyűt.**
1. Válassza a **Privát DNS zóna lehetőséget.**
2. Válassza **a Privát dns zóna létrehozása lehetőséget.**

1. A **Magándns-zóna létrehozása** lapon írja be vagy válassza ki a következő értékeket:

   - **Erőforráscsoport**: Válassza **az Új létrehozása**lehetőséget, írja be a *MyAzureResourceGroup*parancsot, és válassza **az OK**gombot. Az erőforráscsoport nevének egyedinek kell lennie az Azure-előfizetésen belül.
   -  **Name**: Írja be *private.contoso.com* ehhez a példához.
1. Az **Erőforráscsoport helye (Forráscsoport helye)** lehetőséget választja **az USA nyugati középső régiója lehetőségre.**

1. Válassza **a Véleményezés + Létrehozás lehetőséget.**

1. Kattintson a **Létrehozás** gombra.

A zóna létrehozása eltarthat néhány percig.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | MyAzureResourceGroup (Meglévő erőforráscsoport kiválasztása) |
| **\<virtuális hálózat neve>** | MyAzureVNet          |
| **\<régiónév>**          | USA nyugati középső régiója      |
| **\<IPv4-címtér>**   | 10.2.0.0\16          |
| **\<alhálózat-név>**          | SajátAzureSubnet        |
| **\<alhálózati címtartomány>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

Ha a magánDNS-zónát egy virtuális hálózathoz szeretné kapcsolni, hozzon létre egy virtuális hálózati kapcsolatot.

![Virtuális hálózati kapcsolat hozzáadása](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Nyissa meg a **MyAzureResourceGroup** erőforráscsoportot, és válassza ki a **private.contoso.com** privát zónát.
2. A bal oldali ablaktáblán válassza a **Virtuális hálózati kapcsolatok lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget.
4. Írja be a **myLink** nevet a **Hivatkozás nevéhez.**
5. **Virtuális hálózat esetén**válassza a **myAzureVNet**lehetőséget.
6. Jelölje be az **Automatikus regisztráció engedélyezése** jelölőnégyzetet.
7. Válassza **az OK gombot.**

## <a name="create-the-test-virtual-machines"></a>A tesztelési célú virtuális gépek létrehozása

Most hozzon létre két virtuális gépet, amelyekkel tesztelheti saját DNS-zónáját:

1. A portálbal felső részén válassza az **Erőforrás létrehozása**lehetőséget, majd a Windows **Server 2016 Datacenter lehetőséget.**
1. Válassza a **MyAzureResourceGroup** lehetőséget az erőforráscsoporthoz.
1. Írja be a **myVM01 nevet** - a virtuális gép nevéhez.
1. Válassza ki **az USA nyugati középső régióját** a **régióhoz.**
1. Adja meg a rendszergazda felhasználónevének nevét.
2. Írjon be egy jelszót, és erősítse meg a jelszót.
5. **A Nyilvános bejövő portok**esetében válassza a Kijelölt **portok engedélyezése**lehetőséget, majd a Bejövő portok kiválasztása **(RDP)** lehetőséget. **Select inbound ports**
10. Fogadja el a lap többi alapértelmezett beállítását, majd kattintson a **Tovább: Lemezek >gombra. **
11. Fogadja el az alapértelmezett **értékeket** a Lemezek lapon, majd kattintson a **Tovább: Hálózat kezelés >gombra. **
1. Győződjön meg arról, hogy **a myAzureVNet** ki van jelölve a virtuális hálózathoz.
1. Fogadja el a lap többi alapértelmezett beállítását, majd kattintson a **Tovább: Kezelés >** gombra.
2. A **Rendszerindítási diagnosztika**beállításhoz válassza **a Ki**lehetőséget, fogadja el a többi alapértelmezést, majd válassza a Véleményezés + **create**lehetőséget.
1. Tekintse át a beállításokat, majd kattintson a **Létrehozás gombra.**

Ismételje meg ezeket a lépéseket, és hozzon létre egy másik virtuális gépet **myVM02**néven.

Mindkét virtuális gép befejezéséhez néhány percet vesz igénybe.

## <a name="create-an-additional-dns-record"></a>További DNS-rekord létrehozása

 A következő példa létrehoz egy rekordot a RELATÍV neve **db** a DNS Zone **private.contoso.com**, az erőforráscsoport **MyAzureResourceGroup**. A rekordhalmaz teljesen minősített neve **db.private.contoso.com**. A bejegyzés típusa "A", a **myVM01**IP-címével.

1. Nyissa meg a **MyAzureResourceGroup** erőforráscsoportot, és válassza ki a **private.contoso.com** privát zónát.
2. Kattintson a **+ Rekordhalmaz** gombra.
3. A **Name mezőbe**írja be az **adatbázist.**
4. Az **IP-cím**mezőbe írja be a **myVM01**protokollhoz megadott IP-címet. Ezt automatikusan regisztrálni kell, amikor a virtuális gép elindult.
5. Válassza **az OK gombot.**

## <a name="test-the-private-zone"></a>A saját zóna tesztelése

Most tesztelheti a **névfeloldást** a private.contoso.com privát zónában.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Virtuális gépek konfigurálása a befelé irányuló ICMP-forgalom engedélyezésére

A névfeloldás teszteléséhez használhatja a ping parancsot. Ehhez konfigurálja mindkét virtuális gépen a tűzfalat arra, hogy engedélyezze a bejövő ICMP-csomagokat.

1. Csatlakozzon a myVM01 virtuális géphez, és nyisson meg egy Windows PowerShell-ablakot rendszergazdai jogosultsággal.
2. Futtassa az alábbi parancsot:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
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

Ha már nincs rá szükség, törölje a **MyAzureResourceGroup** erőforráscsoportot az ebben a rövid útmutatóban létrehozott erőforrások törléséhez.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure DNS Private Zones forgatókönyvei](private-dns-scenarios.md)

