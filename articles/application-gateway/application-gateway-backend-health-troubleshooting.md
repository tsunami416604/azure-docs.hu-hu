---
title: Háttérrendszer-állapotokkal kapcsolatos problémák elhárítása az Azure Application Gateway alkalmazásban
description: A háttérrendszer állapotproblémáinak elhárítása az Azure Application Gateway alkalmazásban
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: a16120194b1b8015466005f42336828c2b4ace6c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983840"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Háttérrendszer-állapotokkal kapcsolatos problémák elhárítása az Application Gateway alkalmazásban
==================================================

<a name="overview"></a>Áttekintés
--------

Alapértelmezés szerint az Azure Application Gateway megvizsgálja a háttérkiszolgálókat, hogy ellenőrizze az állapotukat, és ellenőrizze, hogy készen állnak-e a kérések kiszolgálására. A felhasználók egyéni mintavételeket is létrehozhatnak az állomásnév, a megvizsgálandó elérési út és a kifogástalanként elfogadandó állapotkódok említésére. Minden esetben, ha a háttérkiszolgáló nem válaszol sikeresen, az Application Gateway a kiszolgálót nem megfelelő állapotúként jelöli meg, és leállítja a kérelmek továbbítását a kiszolgálóra. Miután a kiszolgáló sikeresen válaszol, az Application Gateway folytatja a kérelmek továbbítását.

### <a name="how-to-check-backend-health"></a>A háttér-állapot ának ellenőrzése

A háttérkészlet állapotának ellenőrzéséhez használhatja a **háttérállapot-lapot** az Azure Portalon. Vagy használhatja [az Azure PowerShellt,](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0) [a CLI-t](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)vagy [a REST API-t.](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth)

A módszerek bármelyike által beolvasott állapot a következők bármelyike lehet:

- Kifogástalan

- Nem kifogástalan

- Ismeretlen

Ha a kiszolgáló háttérállapota kifogástalan, az azt jelenti, hogy az Application Gateway továbbítja a kérelmeket a kiszolgálónak. De ha a háttérrendszer állapota a háttérrendszer-készlet összes kiszolgálója nem kifogástalan vagy ismeretlen, problémák léphetnek fel, amikor megpróbál hozzáférni az alkalmazásokhoz. Ez a cikk az egyes megjelenített hibák tüneteit, okait és megoldását ismerteti.

<a name="backend-health-status-unhealthy"></a>Háttérprogram állapota: Nem kifogástalan
-------------------------------

Ha a háttérprogram állapota nem megfelelő, a portálnézet a következő képernyőképhez fog hasonlítani:

![Application Gateway háttérrendszer állapota – nem kifogástalan](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ha Egy Azure PowerShell, CLI vagy Azure REST API-lekérdezést használ, a következőhöz hasonló választ kap:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Miután egy háttérkészlet összes kiszolgálójának nem kifogástalan háttérkiszolgáló-állapotát kapta, a kérelmek et a rendszer nem továbbítja a kiszolgálóknak, és az Application Gateway "502 Bad Gateway" hibaüzenetet ad vissza a kérelmező ügyfélnek. A probléma elhárításához tekintse meg a **Háttérállapot** lap **Részletek** oszlopát.

A **Részletek** oszlopban megjelenő üzenet részletesebb információkat nyújt a problémáról, és ezek alapján megkezdheti a probléma elhárítását.

> [!NOTE]
> Az alapértelmezett mintavételi kérelmet \<a\>rendszer a(z) ://127.0.0.1:\<port\>/. Például http://127.0.0.1:80 egy http-szonda a 80-as porton. Csak a 200 és 399 év között található HTTP-állapotkódok tekinthetők egészségesnek. A protokoll és a célport a HTTP-beállításoktól öröklődik. Ha azt szeretné, hogy az Application Gateway egy másik protokollt, állomásnevet vagy elérési utat vizsgálja meg, és egy másik állapotkódot kifogástalanállapotúként ismerjen fel, konfiguráljon egyéni mintavételt, és társítsa azt a HTTP-beállításokhoz.

<a name="error-messages"></a>Hibaüzenetek
------------------------
#### <a name="backend-server-timeout"></a>Háttérkiszolgáló időmegmaradása

**Üzenet:** A háttérrendszer által az alkalmazásátjáró\'állapotmintára adott válaszidő több, mint a mintavételi beállítás időmegadási küszöbértéke.

**Ok:** Miután az Application Gateway HTTP(S) mintavételi kérelmet küld a háttérkiszolgálónak, egy konfigurált időszakra vár a háttérkiszolgáló válaszára. Ha a háttérkiszolgáló nem válaszol a beállított időszakon belül (az időkorlát érték), akkor a rendszer nem kifogástalanként van megjelölve, amíg a beállított időtúladási időszakon belül újra meg nem kezdi a válaszadást.

**Felbontás:** Ellenőrizze, hogy a háttérkiszolgáló vagy -alkalmazás miért nem válaszol a beállított időkorláton belül, és ellenőrizze az alkalmazásfüggőségeket is. Ellenőrizze például, hogy az adatbázisban vannak-e olyan problémák, amelyek késleltethetik a válaszadást. Ha tisztában van az alkalmazás viselkedésével, és csak az időtúlérték után kell válaszolnia, növelje az időtúltöltési értéket az egyéni mintavételi beállításokból. Az időtúlérték módosításához egyéni mintavételre van szüksége. Az egyéni mintavétel konfigurálásáról [a dokumentációs lapon olvashat.](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)

Az időtúllépés értékének növeléséhez hajtsa végre az alábbi lépéseket:

1.  Közvetlenül elérheti a háttérkiszolgálót, és ellenőrizheti, hogy a kiszolgáló mikor válaszol az adott oldalon. Bármilyen eszközzel elérheti a háttérkiszolgálót, beleértve a böngészőt is a fejlesztői eszközök használatával.

1.  Miután rájött, hogy az alkalmazás nak mikell a válaszadása, válassza az **Állapotpróbák** lapot, majd válassza ki a HTTP-beállításokhoz társított mintavételt.

1.  Adja meg az alkalmazás válaszidejének idejének nagyobb időtúllépését másodpercben.

1.  Mentse az egyéni mintavételi beállításokat, és ellenőrizze, hogy a háttérrendszer állapota kifogástalan állapotúként jelenik-e meg.

#### <a name="dns-resolution-error"></a>DNS-feloldási hiba

**Üzenet:** Az Application Gateway nem tudott mintavételt létrehozni ehhez a háttérrendszerhez. Ez általában akkor fordul elő, ha a háttérrendszer teljes tartományneve nem lett megfelelően megadva. 

**Ok:** Ha a háttérkészlet IP-cím/FQDN vagy App Service típusú, az Application Gateway a DNS-en keresztül megadott teljes tartománynév (egyéni vagy Azure alapértelmezett) IP-címére kerül, és megpróbál csatlakozni a http-beállításokban említett TCP-porton lévő kiszolgálóhoz. Ha azonban ez az üzenet megjelenik, azt sugallja, hogy az Application Gateway nem tudta sikeresen feloldani a megadott teljes tartománynn a ta-címét.

**Felbontás:**

1.  Ellenőrizze, hogy a háttérkészletbe megadott teljes tartománynév helyes-e, és hogy nyilvános tartományról van-e szó, majd próbálja meg feloldani a helyi számítógépről.

1.  Ha fel tudja oldani az IP-címet, lehet, hogy valami baj van a virtuális hálózat DNS-konfigurációjával.

1.  Ellenőrizze, hogy a virtuális hálózat egyéni DNS-kiszolgálóval van-e konfigurálva. Ha igen, ellenőrizze a DNS-kiszolgálón, hogy miért nem tudja feloldani a megadott teljes tartománynna címére.

1.  Ha az Azure alapértelmezett DNS-ét használja, érdeklődjön a tartománynév-regisztrálónál arról, hogy befejeződött-e a megfelelő A rekord vagy CNAME rekord leképezése.

1.  Ha a tartomány magán- vagy belső, próbálja meg feloldani egy virtuális gépugyanabból a virtuális hálózatból. Ha fel tudja oldani, indítsa újra az Application Gateway alkalmazást, és ellenőrizze újra. Az Application Gateway újraindításához le kell [állítania,](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) és el kell [kezdenie](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) az ezekben a csatolt erőforrásokban ismertetett PowerShell-parancsokkal.

#### <a name="tcp-connect-error"></a>TCP-csatlakozási hiba

**Üzenet:** Az Application Gateway nem tudott csatlakozni a háttérrendszerhez.
Ellenőrizze, hogy a háttérkapcsolat reagál-e a mintavételhez használt porton.
Azt is ellenőrizze, hogy az NSG/UDR/Firewall blokkolja-e a hozzáférést a háttérrendszer ip-címéhez és portjához

**Ok:** A DNS-feloldási fázis után az Application Gateway megpróbál csatlakozni a HTTP-beállításokban konfigurált TCP-port háttérkiszolgálójához. Ha az Application Gateway nem tud TCP-munkamenetet létrehozni a megadott porton, a mintavétel ezzel az üzenettel nem kifogástalanállapotúként van megjelölve.

**Megoldás:** Ha ez a hibaüzenet jelenik meg, kövesse az alábbi lépéseket:

1.  Ellenőrizze, hogy tud-e csatlakozni a háttérkiszolgálóhoz a HTTP-beállításokban említett porton egy böngésző vagy a PowerShell használatával. Futtassa például a következő parancsot:`Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Ha az említett port nem a kívánt port, adja meg a megfelelő portszámot, amelyet az Application Gateway a háttérkiszolgálóhoz való csatlakozáshoz kíván megadni.

1.  Ha a porton nem tud csatlakozni a helyi számítógépről is, akkor:

    a.  Ellenőrizze a háttérkiszolgáló hálózati adapterének és alhálózatának hálózati biztonsági csoport (NSG) beállításait, valamint azt, hogy engedélyezettek-e a beállított porttal létesített bejövő kapcsolatok. Ha nem, hozzon létre egy új szabályt, amely engedélyezi a kapcsolatokat. Az NSG-szabályok létrehozásáról [a dokumentációs oldalon tájékozódhat.](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules)

    b.  Ellenőrizze, hogy az Application Gateway alhálózat NSG-beállításai engedélyezik-e a kimenő nyilvános és privát forgalmat, hogy kapcsolatot lehessen létesíteni. A 3a.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Ellenőrizze az Application Gateway és a háttérkiszolgáló alhálózatának felhasználó által definiált útvonalait (UDR) az útválasztási anomáliák szempontjából. Győződjön meg arról, hogy az UDR nem irányítja a forgalmat a háttér-alhálózattól. Például ellenőrizze, hogy vannak-e a hálózati virtuális készülékekhez vezető útvonalak, vagy az Azure ExpressRoute és/vagy VPN-en keresztül az Application Gateway alhálózatra hirdetett alapértelmezett útvonalak.

    d.  A hálózati adapterek hatékony útvonalainak és szabályainak ellenőrzéséhez a következő PowerShell-parancsokat használhatja:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Ha nem talál semmilyen problémát az NSG-vel vagy az UDR-rel kapcsolatban, ellenőrizze a háttérkiszolgálón az alkalmazásokkal kapcsolatos problémákat, amelyek megakadályozzák, hogy az ügyfelek TCP-munkamenetet hozzanak létre a konfigurált portokon. Néhány dolog, hogy ellenőrizze:

    a.  Nyisson meg egy parancssort\> (Win+R - cmd), írja be `netstat`a t, és válassza az Enter lehetőséget.

    b.  Ellenőrizze, hogy a kiszolgáló figyeli-e a konfigurált portot. Például:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Ha nem figyel a beállított porton, ellenőrizze a webkiszolgáló beállításait. Például: helykötések az IIS-ben, kiszolgálóblokk az NGINX-ben és virtuális állomás az Apache-ban.

    d.  Ellenőrizze az operációs rendszer tűzfalának beállításait, és győződjön meg arról, hogy a bejövő forgalom a portra engedélyezett.

#### <a name="http-status-code-mismatch"></a>A HTTP-állapotkód nem egyezik

**Üzenet:** A háttérrendszer\'HTTP-válaszának állapotkódja nem egyezik meg a mintavételi beállítással. Várt:{HTTPStatusCode0} beérkezett:{HTTPStatusCode1}.

**Ok:** A TCP-kapcsolat létrehozása és a TLS-kézfogás befejezése után (ha a TLS engedélyezve van), az Application Gateway http GET-kérelemként elküldi a mintavételt a háttérkiszolgálónak. Ahogy korábban már korábban \<leírtuk, az alapértelmezett mintavétel a\>\<(protokoll: a(z) ://127.0.0.1 protokoll: port\>/, és a 200 és 399 közötti dühben a válaszállapot-kódokat kifogástalannak tekinti. Ha a kiszolgáló bármilyen más állapotkódot ad vissza, ezzel az üzenettel nem kifogástalanként lesz megjelölve.

**Megoldás:** A háttérkiszolgáló válaszkódjától függően a következő lépéseket teheti meg. Néhány a közös állapot kódok itt felsorolt:

| **Hiba** | **Műveletek** |
| --- | --- |
| A mintavételi állapotkód nem egyezik: Fogadott 401 | Ellenőrizze, hogy a háttérkiszolgáló hitelesítést igényel-e. Az Application Gateway-mintavételek ezen a ponton nem tudnak hitelesítő adatokat átadni a hitelesítéshez. Vagy \"engedélyezze a\" HTTP 401-et egy mintavételi állapotkódban, vagy vizsgálja meg azt az elérési utat, ahol a kiszolgáló nem igényel hitelesítést. | |
| A mintavételi állapotkód nem egyezik: Fogadott 403 | Belépés tilos. Ellenőrizze, hogy az elérési út hoz való hozzáférés engedélyezett-e a háttérkiszolgálón. | |
| A mintavételi állapotkód nem egyezik: Fogadott 404 | Az oldal nem található. Ellenőrizze, hogy az állomásnév elérési útja elérhető-e a háttérkiszolgálón. Módosítsa az állomásnevet vagy elérési út paraméterét elérhető értékre. | |
| A mintavételi állapotkód nem egyezik: Fogadott 405 | Az Application Gateway mintavételi kérelmei a HTTP GET metódust használják. Ellenőrizze, hogy a kiszolgáló engedélyezi-e ezt a módszert. | |
| A mintavételi állapotkód nem egyezik: Fogadott 500 | Belső kiszolgálóhiba. Ellenőrizze a háttérkiszolgáló állapotát, valamint azt, hogy futnak-e a szolgáltatások. | |
| A mintavételi állapotkód nem egyezik: Fogadott 503 | A szolgáltatás nem érhető el. Ellenőrizze a háttérkiszolgáló állapotát, valamint azt, hogy futnak-e a szolgáltatások. | |

Vagy ha úgy gondolja, hogy a válasz jogos, és azt szeretné, hogy az Application Gateway más állapotkódokat is kifogástalan állapotként fogadjon el, létrehozhat egy egyéni mintavételt. Ez a megközelítés olyan helyzetekben hasznos, amikor a háttérwebhelyhitelesítést igényel. Mivel a mintavételi kérelmek nem rendelkeznek felhasználói hitelesítő adatokkal, azok sikertelenek lesznek, és a háttérkiszolgáló egy HTTP 401-es állapotkódot ad vissza.

Egyéni mintavétel létrehozásához kövesse [az alábbi lépéseket.](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)

#### <a name="http-response-body-mismatch"></a>A HTTP-választörzs nem egyezik

**Üzenet:** A háttérrendszer\'HTTP-válaszának törzse nem felelt meg a mintavételi beállításnak. A fogadott választörzs nem tartalmazza a(z) {string} elemet.

**Ok:** Amikor létrehoz egy egyéni mintavételt, lehetősége van arra, hogy a háttérkiszolgálót kifogástalanállapotúként jelöljön meg a választörzsből származó karakterlánc egyeztetésével. Beállíthatja például, hogy az Application Gateway elfogadja a "jogosulatlan" karakterláncot. Ha a mintavételi kérelem háttérkiszolgálói válasza **nem engedélyezett**karakterláncot tartalmaz, akkor kifogástalanként lesz megjelölve. Ellenkező esetben ezzel az üzenettel nem megfelelő állapotúként lesz megjelölve.

**Megoldás:** A probléma megoldásához hajtsa végre az alábbi lépéseket:

1.  A háttérkiszolgáló helyi vagy a mintavételi útvonalon lévő ügyfélgépről érhető el, és ellenőrizze a választörzset.

1.  Ellenőrizze, hogy az Application Gateway egyéni mintavételkonfigurációjában lévő választörzs megegyezik-e a konfigurált konfigurációval.

1.  Ha nem egyeznek, módosítsa a mintavételkonfigurációt úgy, hogy a megfelelő karakterlánc-értékkel rendelkezzen.

További információ az [Application Gateway mintavételezéséről.](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)

#### <a name="backend-server-certificate-invalid-ca"></a>A háttérkiszolgáló tanúsítványa érvénytelen a hitelesítésszolgáltatóban

**Üzenet:** A háttérrendszer által használt kiszolgálói tanúsítványt nem írja alá egy jól ismert hitelesítésszolgáltató. Az alkalmazásátjáró háttérrendszerének engedélyezési listázása a háttérrendszer által használt kiszolgálói tanúsítvány főtanúsítványának feltöltésével.

**Ok:** Az Alkalmazás-átjáró v2-vel rendelkező végpontok között az SSL-t ellenőrizni kell a háttérkiszolgáló tanúsítványának ellenőrzése érdekében ahhoz, hogy a kiszolgáló kifogástalan állapotúnak minősüljön.
Ahhoz, hogy egy TLS/SSL-tanúsítvány megbízható legyen, a háttérkiszolgáló tanúsítványát az Application Gateway megbízható tárolójában található hitelesítésszolgáltatónak kell kiállítania. Ha a tanúsítványt nem megbízható hitelesítésszolgáltató bocsátotta ki (például önaláírt tanúsítvány t használták), a felhasználóknak fel kell tölteniük a kiállító tanúsítványát az Application Gateway alkalmazásba.

**Megoldás:** Az alábbi lépésekkel exportálhatja és feltöltheti a megbízható főtanúsítványt az Application Gateway alkalmazásba. (Ezek a lépések a Windows-ügyfelekre vannak.)

1.  Jelentkezzen be arra a gépre, ahol az alkalmazás üzemelteti.

1.  Válassza a Win+R parancsot, vagy kattintson a jobb gombbal a **Start** gombra, és válassza a **Futtatás parancsot.**

1.  Írja `certmgr.msc` be az Enter billentyűt, és válassza az Enter lehetőséget. A **Start** menüben is kereshet tanúsítványkezelőt.

1.  Keresse meg a tanúsítványt, általában a ban, `\Certificates - Current User\\Personal\\Certificates\`és nyissa meg.

1.  Jelölje ki a főtanúsítványt, majd válassza **a Tanúsítvány megtekintése**lehetőséget.

1.  A Tanúsítvány tulajdonságai lapon válassza a **Részletek** lapot.

1.  A **Részletek** lapon jelölje be a **Másolás fájlba** jelölőnégyzetet, és mentse a fájlt a Base-64 kódolású X.509 (. CER) formátumban.

1.  Nyissa meg az Application Gateway **HTTP-beállítások** lapját az Azure Portalon.

1. Nyissa meg a HTTP-beállításokat, válassza **a Tanúsítvány hozzáadása**lehetőséget, és keresse meg az imént mentett tanúsítványfájlt.

1. A **HTTP-beállítások** mentéséhez válassza a Mentés gombot.

Azt is megteheti, hogy exportálja a főtanúsítványt egy ügyfélgépről, ha közvetlenül hozzáfér a kiszolgálóhoz (megkerülve az Application Gateway-t) a böngészőn keresztül, és exportálja a főtanúsítványt a böngészőből.

A megbízható főtanúsítványok alkalmazásátjáróban történő kibontásáról és feltöltéséről a [Megbízható legfelső szintű tanúsítvány exportálása (v2 Termékváltozat esetén) című](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)témakörben talál további információt.

#### <a name="trusted-root-certificate-mismatch"></a>Megbízható legfelső szintű tanúsítvány eltérése

**Üzenet:** A háttérrendszer által használt kiszolgálói tanúsítvány főtanúsítványa nem egyezik meg az alkalmazásátjáróhoz hozzáadott megbízható főtanúsítványsal. Győződjön meg arról, hogy a megfelelő főtanúsítványt adja hozzá a háttérlistához

**Ok:** Az Alkalmazás-átjáró v2-vel rendelkező végpontok között az SSL-t ellenőrizni kell a háttérkiszolgáló tanúsítványának ellenőrzése érdekében ahhoz, hogy a kiszolgáló kifogástalan állapotúnak minősüljön.
Ahhoz, hogy egy TLS/SSL-tanúsítvány megbízható legyen, a háttérkiszolgálói tanúsítványt az Application Gateway megbízható tárolójában található hitelesítésszolgáltatónak kell kiállítania. Ha a tanúsítványt nem megbízható hitelesítésszolgáltató bocsátotta ki (például önaláírt tanúsítványt használtak), a felhasználóknak fel kell tölteniük a kiállító tanúsítványát az Application Gateway alkalmazásba.

Az Application Gateway HTTP-beállításaiba feltöltött tanúsítványnak meg kell egyeznie a háttérkiszolgálói tanúsítvány főtanúsítványával.

**Megoldás:** Ha ez a hibaüzenet jelenik meg, eltérés van az Application Gateway-be feltöltött tanúsítvány és a háttérkiszolgálóra feltöltött tanúsítvány között.

Kövesse az előző módszer 1-11.

A megbízható főtanúsítványok alkalmazásátjáróban történő kibontásáról és feltöltéséről a [Megbízható legfelső szintű tanúsítvány exportálása (v2 Termékváltozat esetén) című](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku)témakörben talál további információt.
> [!NOTE]
> Ez a hiba akkor is előfordulhat, ha a háttérkiszolgáló nem cseréli ki a tanúsítvány teljes láncát, beleértve a Root > Intermediate (ha van ilyen) > Leaf-et a TLS-kézfogás során. Az ellenőrzéshez bármely ügyfél OpenSSL parancsait használhatja, és az Alkalmazásátjáró mintavételben beállított beállításokkal csatlakozhat a háttérkiszolgálóhoz.

Például:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Ha a kimenet nem jeleníti meg a visszaküldött tanúsítvány teljes láncát, exportálja újra a tanúsítványt a teljes lánccal, beleértve a főtanúsítványt is. Konfigurálja a tanúsítványt a háttérkiszolgálón. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>A háttérszolgáltatás-tanúsítvány érvénytelen köznapi névvel (CN)

**Üzenet:** A háttértanúsítvány köznapi neve (CN) nem egyezik meg a mintavétel gazdagépfejlécével.

**Ok:** Az Application Gateway ellenőrzi, hogy a háttérrendszer HTTP-beállításaiban megadott állomásnév megegyezik-e a háttérkiszolgáló TLS/SSL-tanúsítványa által bemutatott KN-névnévvel. Ez Standard_v2 és WAF_v2 termékváltozat viselkedése. A standard és waf termékváltozat kiszolgálónév-jelzése (SNI) a háttérkészlet címében teljes tartománynévként van beállítva.

A v2 termékváltozatban, ha van egy alapértelmezett mintavétel (nincs egyéni mintavétel konfigurálva és társítva), SNI lesz beállítva a HTTP-beállításokban említett állomásnév. Vagy ha a HTTP-beállításokban szerepel a "Host name from backend address" szó, ahol a háttércímkészlet érvényes teljes tartománynevet tartalmaz, akkor ez a beállítás lesz alkalmazva.

Ha a HTTP-beállításokhoz egyéni mintavétel van társítva, az SNI az egyéni mintavételkonfigurációban említett állomásnévből lesz beállítva. Vagy ha az egyéni mintavételben a **háttérrendszer HTTP-beállításaiból** választja ki az állomásnevet, az SNI a HTTP-beállításokban említett állomásnévből lesz beállítva.

Ha a **háttércímből állomásnév választása** van beállítva a HTTP-beállításokban, a háttércímkészletnek érvényes teljes tartománynevet kell tartalmaznia.

Ha ez a hibaüzenet jelenik meg, a háttértanúsítvány CN-je nem egyezik meg az egyéni mintavételben vagy a HTTP-beállításokban konfigurált állomásnévvel (ha az **állomásnév kiválasztása háttérrendszerbeli HTTP-beállításokból** be van jelölve). Ha alapértelmezett mintavételt használ, az állomásnév **127.0.0.1**lesz. Ha ez nem a kívánt érték, hozzon létre egy egyéni mintavételt, és társítsa azt a HTTP-beállításokkal.

**Megoldás:**

A probléma megoldásához kövesse az alábbi lépéseket.

Windows esetén:

1.  Jelentkezzen be arra a gépre, ahol az alkalmazás üzemelteti.

1.  Válassza a Win+R parancsot, vagy kattintson a jobb gombbal a **Start** gombra, és válassza a **Futtatás parancsot.**

1.  Írja be **a certmgr.msc parancsot,** és válassza az Enter lehetőséget. A **Start** menüben is kereshet tanúsítványkezelőt.

1.  Keresse meg a tanúsítványt (általában `\Certificates - Current User\\Personal\\Certificates`a ) és nyissa meg a tanúsítványt.

1.  A **Részletek** lapon ellenőrizze a tanúsítvány **tulajdonosát.**

1.  Ellenőrizze a tanúsítvány KN-jét a részletekből, és adja meg ugyanezt az egyéni mintavétel állomásnév mezőjében vagy a HTTP-beállításokban (ha az **állomásnév kiválasztása háttérrendszerből HTTP-beállítások** közül van kiválasztva). Ha nem ez a kívánt állomásnév a webhelyhez, akkor be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a megfelelő állomásnevet az egyéni mintavételben vagy a HTTP-beállítás konfigurációjában.

Linuxos OpenSSL esetén:

1.  Futtassa ezt a parancsot az OpenSSL-ben:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A megjelenített tulajdonságok közül keresse meg a tanúsítvány KN-jét, és írja be ugyanezt a http-beállítások állomásnév mezőjébe. Ha nem ez a kívánt állomásnév a webhelyhez, akkor be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a megfelelő állomásnevet az egyéni mintavételben vagy a HTTP-beállítás konfigurációjában.

#### <a name="backend-certificate-is-invalid"></a>A háttérszolgáltatás-tanúsítvány érvénytelen

**Üzenet:** A háttértanúsítvány érvénytelen. Az aktuális dátum \"nincs\" a \"tanúsítvány\" Érvényesség kezdete és érvényessége a dátumtartományon belül.

**Ok:** Minden tanúsítvány hoz egy érvényességi tartományt, és a HTTPS-kapcsolat csak akkor lesz biztonságos, ha a kiszolgáló TLS/SSL tanúsítványa érvényes. Az aktuális adatoknak az **érvényes from to** és **érvényes** tartományon belül kell lenniük. Ha nem, a tanúsítvány érvénytelennek minősül, és ez olyan biztonsági problémát okoz, amely nek amelyben az Application Gateway a háttérkiszolgálót nem kifogástalanállapotúként jelöli meg.

**Megoldás:** Ha a TLS/SSL tanúsítvány lejárt, újítsa meg a tanúsítványt a szállítóval, és frissítse a kiszolgáló beállításait az új tanúsítvánnyal. Ha önaláírt tanúsítványról van szó, létre kell hoznia egy érvényes tanúsítványt, és fel kell töltenie a főtanúsítványt az Application Gateway HTTP-beállításaiba. Ehhez kövesse az alábbi lépéseket:

1.  Nyissa meg az Application Gateway HTTP-beállításait a portálon.

1.  Válassza ki a lejárt tanúsítvánnyal rendelkező beállítást, válassza a **Tanúsítvány hozzáadása**lehetőséget, és nyissa meg az új tanúsítványfájlt.

1.  Távolítsa el a régi tanúsítványt a tanúsítvány melletti **Törlés** ikonnal, majd válassza a **Mentés gombot.**

#### <a name="certificate-verification-failed"></a>A tanúsítvány ellenőrzése nem sikerült

**Üzenet:** A háttértanúsítvány érvényessége nem ellenőrizhető. Az ok kiderítéséhez ellenőrizze a(z) {errorCode} hibakóddal kapcsolatos üzenet OpenSSL-diagnosztikájának ellenőrzését.

**Ok:** Ez a hiba akkor fordul elő, ha az Application Gateway nem tudja ellenőrizni a tanúsítvány érvényességét.

**Megoldás:** A probléma megoldásához ellenőrizze, hogy a kiszolgálón lévő tanúsítvány megfelelően lett-e létrehozva. Az [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) segítségével például ellenőrizheti a tanúsítványt és annak tulajdonságait, majd megpróbálhatja újra feltölteni a tanúsítványt az Application Gateway HTTP-beállításaiba.

<a name="backend-health-status-unknown"></a>Háttérprogram állapota: ismeretlen
-------------------------------
Ha a háttérprogram állapota Ismeretlen ként jelenik meg, a portálnézet a következő képernyőképhez fog hasonlítani:

![Application Gateway háttérrendszer állapota – Ismeretlen](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Ez a viselkedés a következő okok miatt fordulhat elő:

1.  Az NSG az Application Gateway alhálózaton blokkolja a bejövő hozzáférést a 65503-65534 (v1 Termékváltozat) vagy a 65200-65535 (v2 Termékváltozat) portokhoz az "internetről".
1.  Az Alkalmazásátjáró alhálózatának UDR-je az alapértelmezett útvonalra van állítva (0.0.0.0/0), és a következő ugrás nincs "Internet" értékben.
1.  Az alapértelmezett útvonalat egy ExpressRoute/VPN-kapcsolat hirdeti egy virtuális hálózathoz A BGP-n keresztül.
1.  Az egyéni DNS-kiszolgáló olyan virtuális hálózaton van konfigurálva, amely nem tudja feloldani a nyilvános tartományneveket.
1.  Az Application Gateway nem kifogástalan állapotban van.

**Megoldás:**

1.  Ellenőrizze, hogy az NSG blokkolja-e a hozzáférést a 65503-65534 (v1 Termékváltozat) vagy a 65200-65535 (v2 Termékváltozat) **portokhoz az internetről:**

    a.  Az Alkalmazásátjáró **áttekintése** lapon válassza a **Virtuális hálózat/Alhálózat** hivatkozást.

    b.  A virtuális hálózat **Alhálózatok** lapján válassza ki azt az alhálózatot, amelyen az Application Gateway telepítve van.

    c.  Ellenőrizze, hogy van-e konfigurálva nsg.

    d.  Ha nsg van beállítva, keresse meg az NSG-erőforrást a **Keresés** lapon vagy a **Minden erőforrás területen.**

    e.  A **Bejövő szabályok** szakaszban adjon hozzá egy bejövő szabályt a 65503-65534 célport tartományengedélyezéséhez a v1 Termékváltozathoz, vagy a 65200-65535 v2 termékváltozathoz, amelynek **a forrás** beállítása **Bármely** vagy **Internet.**

    f.  Válassza a **Mentés lehetőséget,** és ellenőrizze, hogy a háttérrendszer kifogástalan állapotúként tekinthető-e meg. Ezt a [PowerShell/CLI-n](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)keresztül is megteheti.

1.  Ellenőrizze, hogy az UDR rendelkezik-e alapértelmezett útvonallal (0.0.0.0/0), és a következő ugrás nincs **internetként**beállítva:
    
    a.  Az alhálózat meghatározásához kövesse az 1a.

    b.  Ellenőrizze, hogy van-e konfigurálva UDR. Ha van ilyen, keresse meg az erőforrást a keresősávban vagy a **Minden erőforrás területen.**

    c.  Ellenőrizze, hogy vannak-e alapértelmezett útvonalak (0.0.0.0/0), és a következő ugrás nincs **internetként**beállítva. Ha a beállítás vagy **a Virtual Appliance** vagy a Virtual Network **Gateway,** győződjön meg arról, hogy a virtuális készülék vagy a helyszíni eszköz megfelelően átirányítja a csomagot az internetes célhelyre a csomag módosítása nélkül.

    d.  Ellenkező esetben módosítsa a következő ugrást **az Internetre**, válassza a **Mentés**lehetőséget, és ellenőrizze a háttérrendszer állapotát.

1.  Az ExpressRoute/VPN-kapcsolat által a virtuális hálózathoz BGP-n keresztül meghirdetett alapértelmezett útvonal:

    a.  Ha ExpressRoute/VPN-kapcsolattal rendelkezik a virtuális hálózattal a BGP-n keresztül, és ha alapértelmezett útvonalat hirdet, győződjön meg arról, hogy a csomag módosítás nélkül visszakerül az internetes célhelyre. Az Alkalmazásátjáró portál **Kapcsolathiba elhárítása** beállításával ellenőrizheti.

    b.  Válassza ki a célt manuálisan, mint bármely internet-irányítható IP-cím, például 1.1.1.1. Állítsa be a célportot bármiként, és ellenőrizze a kapcsolatot.

    c.  Ha a következő ugrás a virtuális hálózati átjáró, előfordulhat, hogy egy alapértelmezett útvonal at hirdetett ExpressRoute vagy VPN.

1.  Ha a virtuális hálózaton egyéni DNS-kiszolgáló van konfigurálva, ellenőrizze, hogy a kiszolgáló (vagy kiszolgálók) fel tudják-e oldani a nyilvános tartományokat. Nyilvános tartománynév-feloldásra lehet szükség olyan esetekben, amikor az Application Gateway-nek külső tartományokat, például OCSP-kiszolgálókat kell elérnie, vagy ellenőriznie kell a tanúsítvány visszavonási állapotát.

1.  Annak ellenőrzéséhez, hogy az Application Gateway kifogástalan és fut-e, nyissa meg a portál **Erőforrás-állapot** beállítását, és ellenőrizze, hogy az állapot **kifogástalan-e.** Ha nem **megfelelő állapotú** vagy **leromlott állapotot** lát, [forduljon az ügyfélszolgálathoz.](https://azure.microsoft.com/support/options/)

<a name="next-steps"></a>További lépések
----------

További információ az [Application Gateway diagnosztikájáról és naplózásáról.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
