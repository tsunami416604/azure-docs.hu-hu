Nyissa meg a portot, vagy hozzon létre egy végpontot a virtuális gép (VM), az Azure-ban egy alhálózatot vagy a virtuális gép hálózati illesztő hálózati szűrő létrehozásával. Ezek a szűrők, amely szabályozza a bejövő és kimenő forgalmat, a hálózati biztonsági csoport az erőforrás a forgalmat fogadó csatolva helyezze el.

Ilyenek például a webes forgalom most használja a 80-as porton. Ha elvégezte a kiszolgálására konfigurált virtuális gépek a szabványos TCP a webes kérelmek port 80-as (ne felejtse el a indítsa el a megfelelő szolgáltatásokat, és nyissa meg az összes operációs rendszer tűzfalszabályok, valamint a virtuális gépen), hogy:

1. Hálózati biztonsági csoport létrehozása.
2. Hozzon létre egy bejövő forgalomra vonatkozó szabály átengedi a forgalmat:
   * a "80" Célporttartomány
   * a Forrásporttartomány a "*" (amely lehetővé teszi bármely forrásportból)
   * a prioritás értéke kevesebb 65 500 (a magasabb prioritású, mint az alapértelmezett catch-all a kell megtagadási bejövő szabály)
3. A hálózati biztonsági csoport társítani a virtuális gép hálózati adapter vagy az alhálózatot.

Létrehozhat összetett hálózati konfigurációt megvédeni környezetét a hálózati biztonsági csoportok és a szabályok használatával. A példában csak egy vagy két olyan szabályok, amelyek lehetővé teszik a HTTP-forgalom vagy a távoli felügyelet. További információkért tekintse meg a következőt ["További információk"](#more-information-on-network-security-groups) szakasz vagy [Mi az a hálózati biztonsági csoport?](../articles/virtual-network/virtual-networks-nsg.md)

