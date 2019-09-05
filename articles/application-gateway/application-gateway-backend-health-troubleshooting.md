---
title: A háttér állapotával kapcsolatos problémák elhárítása az Azure-ban Application Gateway
description: Ez a cikk végigvezeti az Azure-Application Gateway háttérbeli állapotával kapcsolatos problémák elhárításában.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: article
ms.date: 08/30/2019
ms.author: surmb
ms.openlocfilehash: ce1d0542530c4f190ace52d45e2369d6ecc18772
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384085"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>A Application Gateway háttérbeli állapotával kapcsolatos problémák elhárítása
==================================================

<a name="overview"></a>Áttekintés
--------

Application Gateway alapértelmezés szerint a háttér-kiszolgálókat a rendszer ellenőrzi az állapotukat, és ha készen állnak a kérelmek kiszolgálására. A felhasználók létrehozhatnak egyéni mintavételeket is, így megemlíthető az állomásnév, a mintavételi útvonal és az állapotkód, amelyet kifogástalanként kell elfogadni. Mindkét esetben, ha a háttérrendszer-kiszolgáló nem válaszol sikeresen, Application Gateway a kiszolgálót nem megfelelőként jelöli meg, és leállítja a kérés továbbítását a kiszolgálónak. A kiszolgáló sikeres megválaszolása után a rendszer folytatja a kérések kiszolgálását.

### <a name="how-to-check-backend-health"></a>A háttér állapotának ellenõrzése

A háttérbeli készlet állapotának vizsgálatához használhatja a "háttér állapota" lapot a Azure Portal. A [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhealth?view=azps-2.6.0), a [CLI](https://docs.microsoft.com/cli/azure/network/application-gateway?view=azure-cli-latest#az-network-application-gateway-show-backend-health)vagy a [REST API](https://docs.microsoft.com/rest/api/application-gateway/applicationgateways/backendhealth) is használható az állapot állapotának lekéréséhez. További információkért tekintse meg a csatolt cikket az egyes módszerekhez.

A fent említett metódusok által lekért állapot három típusú lehet:

1.  Kifogástalan

2.  Nem kifogástalan

3.  Ismeretlen

Ha a kiszolgáló háttér-állapotának állapota Kifogástalan, az azt jelenti, hogy Application Gateway továbbítja a kéréseket a kiszolgálónak. Ha azonban egy háttér-készlet összes kiszolgálójának háttér-állapota nem kifogástalan vagy ismeretlen, akkor előfordulhat, hogy bizonyos problémák merülhetnek fel az alkalmazás-hozzáférés során. Ez a dokumentum leírja az egyes hibák tünetét, okát és megoldását, ha a háttér-kiszolgálók nem kifogástalanak vagy ismeretlenek.

<a name="backend-health-status-unhealthy"></a>Háttér állapotának sérült állapota
-------------------------------

Ha a háttér állapota nem kifogástalan állapotú, akkor a portálon, például az alábbi képernyőképen fog megjelenni:

![Application Gateway háttér állapota – nem megfelelő állapotú](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ha Azure PowerShellt, CLI-t vagy Azure REST API lekérdezést használ, az alábbihoz hasonló választ fog látni:
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
Ha a háttérrendszer-kiszolgáló állapota nem kifogástalan állapotú a háttér-készlet összes kiszolgálóján, a kérések nem lesznek továbbítva, és a Application Gateway az 502 hibás átjáró hibáját visszaküldi a kérelmező ügyfélnek. A probléma megoldásához keresse meg a háttér állapota lap részletek oszlopát.

A háttér állapota lap részletek oszlopában megjelenő üzenet részletesebb információkat nyújt a problémáról, és ezek alapján elindítható a probléma elhárítása.

> [!NOTE]
> Az alapértelmezett mintavételi kérelmet <protocol>://127.0.0.1:<port> <http://127.0.0.1/> /, például a 80-es porton belüli http-mintavételre küldi a rendszer, és csak a 200-399-es HTTP-állapotkódok válaszát tekinti meg kifogástalan válaszként. A protokoll és a célport a HTTP-beállításoktól örökölt. Ha azt Application Gateway szeretné, hogy a mintavétel egy másik protokollon, állomásnéven vagy elérési úton történjen, és más állapotkódot fogadjon el kifogástalan állapotként, állítson be egy egyéni mintavételt, és társítsa a HTTP-beállításokhoz.

### <a name="error-messages"></a>Hibaüzenetek

#### <a name="backend-server-timeout"></a>Háttér-kiszolgáló időtúllépése

**Üzenetet** A háttér által az Application Gateway\'s Health mintavételre való reagáláshoz szükséges idő nagyobb, mint a mintavételi beállítás időtúllépési küszöbértéke.

**Okozhat** Miután Application Gateway elküld egy HTTP (S) mintavételi kérelmet a háttér-kiszolgálónak, megvárja a háttér-kiszolgáló válaszát egy bizonyos ideig beállított időtartamra. Ha a háttérrendszer-kiszolgáló nem válaszol az időtúllépési értéken belül, a rendszer nem megfelelő állapotba kerül, amíg újra nem válaszol az időkorláton belül.

**Megoldás:** Ellenőrizze a háttér-kiszolgálót vagy az alkalmazást úgy, hogy miért nem válaszol az időtúllépési beállításon belül, és ellenőrizze az alkalmazás függőségeit is, például ha az adatbázis problémába ütközik, ami a válasz késleltetéséhez vezethet. Ha ismeri az alkalmazás viselkedését, és csak az időtúllépési érték után válaszol, akkor növelje az időtúllépési értéket az egyéni mintavételi beállítások alapján.
Az időtúllépés értékének módosításához egyéni mintavételre van szükség. Ha tudni szeretné, hogyan konfigurálhat egyéni mintavételt, [tekintse meg a dokumentációs oldalt](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal).

Az időkorlát növeléséhez kövesse az alábbi lépéseket:

1.  Közvetlenül elérheti a háttér-kiszolgálót, és megvizsgálhatja, hogy a kiszolgáló milyen időpontban válaszol az adott oldalon. Bármilyen eszközt használhat, beleértve a fejlesztői eszközöket használó böngészőt is.

2.  Ha kitalálta az alkalmazás válaszához szükséges időt, kattintson az állapot-mintavételek lapra, és válassza ki a HTTP-beállításokhoz társított mintavételt.

3.  Adja meg a magasabb időtúllépési értéket, mint az alkalmazás válaszideje (másodpercben).

4.  Mentse az egyéni mintavételi beállításokat, és ellenőrizze a háttér állapotát, ha az állapota most kifogástalan.

#### <a name="dns-resolution-error"></a>DNS-feloldási hiba

**Üzenetet** Az Application Gateway nem tudott mintavételt létrehozni ehhez a háttérhöz. Ez általában akkor fordul elő, ha a háttér teljes tartományneve nem lett megfelelően megadva. 

**Okozhat** Ha a háttér-készlet IP-cím/FQDN vagy App Service típusú, Application Gateway a DNS-kiszolgáló (egyéni vagy Azure alapértelmezett) használatával megadott teljes tartománynév IP-címére oldja fel, és megpróbál csatlakozni a kiszolgálóhoz a HTTP-beállításokban említett TCP-porton. Ha azonban ez az üzenet jelenik meg, az azt jelenti, hogy Application Gateway nem tudta sikeresen feloldani a megadott teljes tartománynév IP-címét.

**Megoldás:**

1.  Ellenőrizze, hogy a háttér-készletben megadott teljes tartománynév helyes-e, és hogy a nyilvános tartomány-e, próbálja meg feloldani a helyi gépről.

2.  Ha feloldja az IP-címet, akkor a VNet DNS-konfigurációjában nem lehet valami hiba.

3.  Ellenőrizze, hogy a VNet egyéni DNS-kiszolgálóval van-e konfigurálva. Ha igen, ellenőrizze a DNS-kiszolgálón, hogy miért nem oldható fel a megadott FQDN IP-címére.

4.  Ha az Azure alapértelmezett DNS, ellenőrizze a tartománynév-regisztrálót, ha A megfelelő rekord vagy CNAME rekord leképezése megtörtént.

5.  Ha a tartomány magán/belső, próbálja meg feloldani egy azonos VNet lévő virtuális gépről, és ha fel tudja oldani, indítsa újra Application Gateway és ellenőrizze újra. Application Gateway újraindításához [le kell állítania](https://docs.microsoft.com/powershell/module/azurerm.network/stop-azurermapplicationgateway?view=azurermps-6.13.0) és [el kell indítania](https://docs.microsoft.com/powershell/module/azurerm.network/start-azurermapplicationgateway?view=azurermps-6.13.0) a megfelelő dokumentum-hivatkozásokban megadott PowerShell-parancsokat.

#### <a name="tcp-connect-error"></a>TCP-csatlakozási hiba

**Üzenetet** Az Application Gateway nem tudott kapcsolódni a háttérhez.
Győződjön meg arról, hogy a háttér válaszol a mintavételhez használt porton.
Győződjön meg arról is, hogy a NSG/UDR/tűzfal blokkolja-e a háttér IP-címét és portját.

**Okozhat** A DNS-feloldási fázis után Application Gateway megpróbál csatlakozni a háttér-kiszolgálóhoz a HTTP-beállításokban konfigurált TCP-porton. Ha Application Gateway nem tud TCP-munkamenetet létesíteni a megadott porton, akkor a mintavétel nem megfelelőként van megjelölve ezzel az üzenettel.

**Megoldás:** Ha ezt a hibát látja, ellenőrizze a következőket:

1.  Ellenőrizze, hogy tud-e csatlakozni a háttér-kiszolgálóhoz a HTTP-beállításokban a böngészőben vagy a PowerShell használatával, például használhatja a következő parancsot: Test-NetConnection-számítógépnév www.bing.com-port 443

2.  Ha a megadott port nem a kívánt port, a háttér-kiszolgálóhoz való csatlakozáshoz adja meg a Application Gateway megfelelő portszámot.

3.  Ha a helyi gépről nem tud kapcsolatot létesíteni a porttal, akkor:

    a.  Ellenőrizze a háttér-kiszolgáló hálózati adaptere és alhálózata NSG beállításait, és ellenőrizze, hogy engedélyezett-e a bejövő kapcsolatok a konfigurált porton. Ha nem engedélyezettek, hozzon létre egy új szabályt a kapcsolatok engedélyezéséhez. A NSG-szabályok létrehozásával kapcsolatban [tekintse meg a dokumentációs oldalt](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic#create-security-rules).

    b.  Ellenőrizze Application Gateway alhálózatának NSG beállításait, ha a nyilvános és a magánhálózati forgalom engedélyezett, hogy a rendszer a kapcsolatokat is lehetővé teszi. A NSG-szabályok létrehozásával kapcsolatos további információkért olvassa el az (a)-ben csatolt dokumentumot.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Minden útválasztási rendellenesség esetén keresse meg a Application Gateway és a háttér-kiszolgáló alhálózatának UDR beállításait. Győződjön meg arról, hogy a UDR nem irányítja át a forgalmat a háttér-alhálózatból. Keressen például útvonalakat a hálózati virtuális berendezésekhez vagy a ExpressRoute/VPN-en keresztül az Application Gateway alhálózatra hirdetett alapértelmezett útvonalakat.

    d.  A hálózati adapter érvényes útvonalait és szabályait az alábbi PowerShell-parancsokkal tekintheti meg.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
4.  Ha nem talál problémát a NSG vagy a UDR használatával kapcsolatban, ellenőrizze a háttér-kiszolgálót az alkalmazásokkal kapcsolatos problémákról, amelyek miatt az ügyfelek nem tudnak TCP-munkamenetet létrehozni a konfigurált port (ok) hoz. Néhány dolog, amit érdemes ellenőriznie:

    e.  Nyisson meg egy parancssort (Win +\> R-cmd), írja be a netstat parancsot, és nyomja le az ENTER billentyűt

    f.  Ellenőrizze, hogy a kiszolgáló figyeli-e a konfigurált portot.
        Példa:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    g.  Ha nem, akkor ellenőrizze a webkiszolgáló beállításait, például a helyek kötéseit az IIS-ben, az NGINX-ben lévő kiszolgálói blokkot és a virtuális gazdagépet az Apache-ban

    h.  Ellenőrizze az operációs rendszer tűzfalának beállításait, és győződjön meg arról, hogy a portra irányuló bejövő forgalom engedélyezett

#### <a name="http-status-code-mismatch"></a>HTTP-állapotkód eltérése

**Üzenetet** A háttérbeli\'s http-válasz állapotkód nem felelt meg a mintavételi beállításnak. Várt: {HTTPStatusCode0} érkezett: {HTTPStatusCode1}.

**Okozhat** Miután létrejött a TCP-kapcsolat, és az SSL-kézfogás elkészült (ha az SSL engedélyezve van), Application Gateway a mintavételt HTTP GET kérelemként küldi el a háttér-kiszolgálónak. A fentiekben \<leírtaknak megfelelően az alapértelmezett\>mintavétel a://127.0.0.1:\<port\>/, és a Rage 200-399-as kifogástalan állapotú kódokat veszi figyelembe. Ha a kiszolgáló más állapotkódot ad vissza, az üzenet nem megfelelőként lesz megjelölve.

**Megoldás:** A háttér-kiszolgáló válaszának kódjától függően a következő lépéseket hajthatja végre. Néhány gyakori állapotkód itt látható:

| **Hiba** | **Műveletek** |
| --- | --- |
| Nem megfelelő az állapot kódja: Fogadott 401 | Győződjön meg arról, hogy a háttér-kiszolgáló hitelesítést igényel. Az Application Gateway-mintavételek nem tudnak átadni hitelesítő adatokat a hitelesítéshez ezen a ponton. Engedélyezze \"a HTTP 401\" -et a mintavételi állapotkód-egyeztetés vagy a mintavétel egy olyan elérési útra, ahol a kiszolgáló nem követeli meg a hitelesítést. | |
| Nem megfelelő az állapot kódja: Fogadott 403 | Hozzáférés megtiltva. Győződjön meg arról, hogy az elérési úthoz való hozzáférés engedélyezve van-e a háttér-kiszolgálón. | |
| Nem megfelelő az állapot kódja: Fogadott 404 | A lap nem található. Ellenőrizze az állomásnév elérési útját, ha az elérhető a háttér-kiszolgálón. Módosítsa az állomásnév vagy az elérési út paramétert egy elérhető értékre. | |
| Nem megfelelő az állapot kódja: Fogadott 405 | Application Gateway mintavételi kérelmei a HTTP GET metódust használják. Győződjön meg arról, hogy a kiszolgáló engedélyezi-e. | |
| Nem megfelelő az állapot kódja: Fogadott 500 | Belső kiszolgálóhiba. Ellenőrizze a háttér-kiszolgáló állapotát, és hogy a szolgáltatások futnak-e. | |
| Nem megfelelő az állapot kódja: Fogadott 503 | A szolgáltatás nem érhető el. Ellenőrizze a háttér-kiszolgáló állapotát, és hogy a szolgáltatások futnak-e. | |

Vagy ha úgy gondolja, hogy a válasz legit, és azt szeretné, Application Gateway hogy a többi állapotkódot is Kifogástalan állapotba fogadja, létrehozhat egy egyéni mintavételt. Ez a módosítás olyan helyzetekben hasznos, amikor a háttérbeli webhelynek hitelesítést kell végeznie, és mivel a mintavételi kérelmek nem rendelkeznek felhasználói hitelesítő adatokkal, sikertelenek lesznek, és a háttér-kiszolgáló HTTP 401 állapotkódot ad vissza.

Egyéni mintavétel létrehozásához kövesse az [itt](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal)felsorolt lépéseket.

#### <a name="http-response-body-mismatch"></a>HTTP-válasz törzsének eltérése

**Üzenetet** A háttérbeli\'s http-válasz törzse nem felelt meg a mintavételi beállításnak. A kapott válasz törzse nem tartalmaz {string}.

**Okozhat** Egyéni mintavétel létrehozásakor lehetősége van a háttér-kiszolgáló megfelelő állapotának megjelölésére a válasz törzsében szereplő karakterláncok egyeztetésével. Beállíthatja például, hogy Application Gateway fogadja el a "jogosulatlan" karakterláncot az egyeztetéshez. Ha a háttér-kiszolgáló válasza a mintavételi kérelemre a "nem engedélyezett" karakterláncot tartalmazza, akkor a rendszer kifogástalanként jelöli meg.
Ellenkező esetben a rendszer nem megfelelőként jelöli meg ezt az üzenetet.

**Megoldás:** A probléma megoldásához kövesse az alábbi lépéseket:

1.  A háttér-kiszolgálót helyileg vagy egy, a mintavételi útvonalon található ügyfélszámítógépről is elérheti, és megvizsgálhatja a válasz törzsét.

2.  Ellenőrizze Application Gateway egyéni mintavételi konfigurációját annak ellenőrzéséhez, hogy a válasz törzse megfelel-e a konfigurált beállításoknak.

3.  Ha nem egyeznek, módosítsa a mintavételi konfigurációt a megfelelő karakterlánc-értékkel a fogadáshoz.

Az Application Gateway mintavételről [itt](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)olvashat bővebben.

#### <a name="backend-server-certificate-invalid-ca"></a>A háttér-kiszolgáló tanúsítványa érvénytelen CA

**Üzenetet** A háttérrendszer által használt kiszolgálói tanúsítványt nem a jól ismert hitelesítésszolgáltató (CA) írta alá. A háttérrendszer által használt kiszolgálói tanúsítvány főtanúsítványának feltöltésével engedélyezheti az Application Gateway hátterét.

**Okozhat** Application Gateway v2 végpontok közötti SSL használatához a háttér-kiszolgáló tanúsítványát ellenőrizni kell, hogy a kiszolgáló kifogástalan állapotú legyen.
Ahhoz, hogy egy SSL-tanúsítvány megbízható legyen, a háttér-kiszolgáló tanúsítványát ki kell állítani egy olyan HITELESÍTÉSSZOLGÁLTATÓ, amely szerepel a Application Gateway megbízható tárolójában. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki, például önaláírt tanúsítványok, a felhasználóknak fel kell tölteniük a kiállító tanúsítványát Application Gateway.

**Megoldás:** Kövesse az alábbi lépéseket a megbízható főtanúsítvány Application Gateway való exportálásához és feltöltéséhez (az alábbi lépések a Windows-ügyfelekre vonatkoznak)

1.  Jelentkezzen be arra a gépre, amelyen az alkalmazás üzemeltetve van

2.  Futtassa a Futtatás gombot a Win + R gomb megnyomásával vagy a jobb gombbal a Start gombra, és válassza a Futtatás lehetőséget.

3.  Adja meg a certmgr. msc parancsot, majd nyomja le az ENTER billentyűt A Tanúsítványkezelőt a Start menüben is megkeresheti.

4.  Keresse meg a tanúsítványt, jellemzően \'a tanúsítványok-aktuális\\felhasználói\\személyes\'tanúsítványok területen, és nyissa meg a tanúsítványt.

5.  A tanúsítvány tulajdonságai lapon válassza a minősítési útvonal lapot.

6.  Válassza ki a főtanúsítványt, és válassza a "tanúsítvány megtekintése" lehetőséget.

7.  A tanúsítvány tulajdonságai között váltson a Részletek lapra.

8.  A Részletek lapon válassza a Másolás fájlba lehetőséget, és mentse a fájlt a Base-64 kódolású X. 509 (. CER) formátum

9.  A mentés után nyissa meg a Application Gateway HTTP-beállítások lapot a Azure Portal

10. Nyissa meg a HTTP-beállításokat, és kattintson a "tanúsítvány hozzáadása" lehetőségre, és keresse meg a közelmúltban mentett tanúsítványfájl

11. A HTTP-beállítások mentéséhez kattintson a Mentés gombra.

Másik lehetőségként a főtanúsítványt egy ügyfélszámítógépről is exportálhatja közvetlenül a kiszolgálóhoz (a Application Gateway megkerüléséhez) egy böngésző használatával, és a főtanúsítvány a böngészőből való exportálásával.

A megbízható legfelső szintű tanúsítványok Application Gateway való kinyerésével és feltöltésével kapcsolatos részletes útmutatásért lásd [itt](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Nem megfelelő a megbízható főtanúsítvány

**Üzenetet** A háttérrendszer által használt kiszolgálói tanúsítvány főtanúsítványa nem felel meg az Application Gateway számára hozzáadott megbízható főtanúsítványnak. Győződjön meg arról, hogy a megfelelő főtanúsítványt adja hozzá a háttér-engedélyezési listához

**Okozhat** Application Gateway v2 végpontok közötti SSL használatához a háttér-kiszolgáló tanúsítványát ellenőrizni kell, hogy a kiszolgáló kifogástalan állapotú legyen.
Ahhoz, hogy egy SSL-tanúsítvány megbízható legyen, a háttér-kiszolgáló tanúsítványát ki kell állítani egy olyan HITELESÍTÉSSZOLGÁLTATÓ, amely szerepel a Application Gateway megbízható tárolójában. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki, például önaláírt tanúsítványok, a felhasználóknak fel kell tölteniük a kiállító tanúsítványát Application Gateway.

A Application Gateway HTTP-beállításokba feltöltött tanúsítványnak meg kell egyeznie a háttér-kiszolgáló tanúsítványának főtanúsítványával.

**Megoldás:** Ha a fent említett hibaüzenet jelenik meg, az azt jelenti, hogy eltérés van a Application Gatewayba feltöltött tanúsítvány és a háttér-kiszolgálóra feltöltött egyik között.

Kövesse a fent említett 1-11-es lépéseket a megfelelő megbízható legfelső szintű tanúsítvány feltöltéséhez a Application Gateway.

A megbízható legfelső szintű tanúsítványok Application Gateway való kinyerésével és feltöltésével kapcsolatos részletes útmutatásért lásd [itt](https://docs.microsoft.com/azure/application-gateway/certificates-for-backend-authentication#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Az említett hiba akkor is előfordulhat, ha a háttér-kiszolgáló nem cseréli ki a tanúsítvány teljes láncát, beleértve a legfelső szintű > köztes (ha alkalmazható) > a TLS-kézfogás során. Annak ellenőrzéséhez, hogy az OpenSSL-parancsokat bármely ügyfélről használhatja, és a Application Gateway mintavétel konfigurált beállításaival csatlakozhat a háttér-kiszolgálóhoz.

Például:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Ha a kimenet nem jeleníti meg a visszaadott tanúsítvány teljes láncát, a teljes lánctal együtt exportálja a tanúsítványt, beleértve a főtanúsítványt, és konfigurálja azt a háttér-kiszolgálón. 

CSATLAKOZTATVA (00000188) \
mélység = 0 ou = tartomány-vezérlőelem érvényesítve, CN \*=. example.com \
a hiba ellenőrzése: NUM = 20: nem sikerült beolvasni a helyi kiállítói tanúsítványt \
visszatérés ellenőrzése: 1 \
mélység = 0 ou = tartomány-vezérlőelem érvényesítve, CN \*=. example.com \
a hiba ellenőrzése: NUM = 21: az első tanúsítvány nem ellenőrizhető \
visszatérés ellenőrzése: 1 \
\-\-\-\
Tanúsítványlánc \
 0 s:/OU = tartomány-vezérlés érvényesítve/CN = *. example. com \
   i:/C = US/St = Arizona/L = Scottsdale/O = GoDaddy. com, Inc./ou =http://certs.godaddy.com/repository//CN=Go Daddy biztonságos hitelesítésszolgáltató – G2 \
\-----A TANÚSÍTVÁNY MEGKEZDÉSE-----\
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
\-----ZÁRÓ TANÚSÍTVÁNY-----

#### <a name="backend-certificate-invalid-common-name-cn"></a>A háttér-tanúsítvány érvénytelen köznapi neve (CN)

**Üzenetet** A háttérbeli tanúsítvány köznapi neve (CN) nem egyezik meg a mintavétel állomásneve.

**Okozhat** Application Gateway ellenőrzi, hogy a háttérbeli http-beállításban megadott állomásnév megegyezik-e a háttér-kiszolgáló SSL-tanúsítványa által megjelenített köznapi névvel (CN). Ez csak Standard_v2 és WAF_v2 SKU-viselkedés. A standard és a WAF SKU SNI a háttérbeli készlet címe teljes tartományneveként van beállítva.

Ha a v2 SKU-ban alapértelmezett mintavétel van megadva (nincs egyéni mintavétel konfigurálva/társítva), a SNI a HTTP-beállításokban megemlített állomásnévről, vagy ha a "válasszon állomásnév a háttérről" címről van megadva a HTTP-beállításokban, ahol a háttér-címkészlet tartalmaz egy érvényes FQDN.

Ha a HTTP-beállításokhoz egy egyéni mintavétel van társítva, a SNI az egyéni mintavételi konfigurációban megemlített állomásnév alapján lesz beállítva, vagy ha a "válasszon állomásnév a háttérbeli HTTP-beállításokból" jelölőnégyzet be van jelölve az egyéni mintavételben, akkor a HTTP-ben említett állomásnév alapján lesz beállítva. Beállítások.

Ha a "kiválasztási állomásnév a háttérbeli címről" beállítás be van állítva a HTTP-beállításokban, a háttérbeli címkészlet érvényes teljes tartománynevet kell tartalmaznia.

Ha a fenti hibaüzenet jelenik meg, az azt jelenti, hogy a háttérbeli tanúsítvány köznapi neve (CN) nem egyezik meg az egyéni mintavételben vagy a HTTP-beállításokban konfigurált állomásnévvel (ha a "válasszon állomásnév a háttérbeli HTTP-beállításokból" lehetőséget választja). Ha alapértelmezett mintavételt használ, a hostname értéke "127.0.0.1" lesz. Ha ez nem a kívánt érték, hozzon létre egy egyéni mintavételt, és rendelje hozzá a HTTP-beállításokhoz.

**Megoldás:**

A probléma megoldásához kövesse az alábbi lépéseket:

Windows esetén:

1.  Jelentkezzen be arra a gépre, amelyen az alkalmazás üzemeltetve van

2.  Futtassa a Futtatás gombot a Win + R gomb megnyomásával vagy a jobb gombbal a Start gombra, és válassza a Futtatás lehetőséget.

3.  Adja meg a certmgr. msc parancsot, majd nyomja le az ENTER billentyűt A Tanúsítványkezelőt a Start menüben is megkeresheti.

4.  Keresse meg a tanúsítványt, jellemzően \'a tanúsítványok-aktuális\\felhasználói\\személyes\'tanúsítványok területen, és nyissa meg a tanúsítványt.

5.  A Részletek lapon keresse meg a tanúsítvány tárgyát.

6.  Ellenőrizze a tanúsítvány CN-tartalmát a részletek közül, és adja meg ugyanezt az egyéni mintavétel vagy a HTTP-beállítások állomásnév mezőjében (ha a "válasszon állomásnév a háttérbeli HTTP-beállításokból" lehetőséget választja). Ha ez nem a kívánt állomásnév a webhelyhez, be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a megfelelő állomásnevet az egyéni mintavétel/http beállítás konfigurációjában.

Linux rendszerhez OpenSSL használatával

1.  Futtassa ezt a parancsot az OpenSSL-ben 
```
openssl x509 -in certificate.crt -text -noout
```

2.  A megjelenített tulajdonságok közül keresse meg a tanúsítvány CN-t, és adja meg ugyanezt a http-beállítások állomásnév mezőjében. Ha ez nem a kívánt állomásnév a webhelyhez, be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a megfelelő állomásnevet az egyéni mintavétel/http beállítás konfigurációjában.

#### <a name="backend-certificate-is-invalid"></a>A háttér-tanúsítvány érvénytelen

**Üzenetet** A háttér-tanúsítvány érvénytelen. Az aktuális dátum nem \"a tanúsítvány\" érvényességi\" ideje \"és a dátumtartomány érvényességi tartománya.

**Okozhat** Minden tanúsítvány érvényességgel rendelkezik, és a HTTPS-kapcsolat nem lesz biztonságos, ha a kiszolgáló SSL-tanúsítványa érvényes.
Az aktuális adatoknak érvényes és érvényes tartományon belül kell lenniük. Ha nem, akkor a tanúsítvány érvénytelennek minősül, és biztonsági problémát jelent. Ezen a ponton Application Gateway a háttér-kiszolgálót nem Kifogástalan állapotba fogja megjelölni.

**Megoldás:** Ha az SSL-tanúsítvány lejárt, újítsa meg a tanúsítványt a szállítóval, és frissítse a kiszolgáló beállításait az új tanúsítvánnyal. Ha ez egy önaláírt tanúsítvány, érvényes tanúsítványt kell létrehoznia, és fel kell töltenie a főtanúsítványt a Application Gateway HTTP-beállításaiba. Ehhez kövesse az alábbi lépéseket:

1.  A Application Gateway HTTP-beállításainak megnyitása a portálon

2.  Válassza ki a lejárt tanúsítvánnyal rendelkező HTTP-beállításokat, válassza a tanúsítvány hozzáadása lehetőséget, és nyissa meg az új tanúsítványfájl

3.  Távolítsa el a régi tanúsítványt a tanúsítvány melletti Delete (Törlés) ikon használatával, és kattintson a Mentés gombra.

#### <a name="certificate-verification-failed"></a>A tanúsítvány ellenőrzése nem sikerült

**Üzenetet** Nem lehetett ellenőrizni a háttér-tanúsítvány érvényességét. Az ok megállapításához tekintse meg az SSL-diagnosztika megnyitása a következő hibakóddal társított üzenethez: {errorCode}

**Okozhat** Ez a hiba akkor fordul elő, ha Application Gateway nem tudja ellenőrizni a tanúsítvány érvényességét.

**Megoldás:** A probléma megoldásához ellenőrizze a tanúsítványt a kiszolgálón, ha az megfelelően van létrehozva. Például az [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) használatával ellenőrizheti a tanúsítványt és annak tulajdonságait, majd megpróbálhatja újból feltölteni a tanúsítványt a Application Gateway http-beállításaiba.

<a name="backend-health-status-unknown"></a>A háttér állapotának állapota ismeretlen
-------------------------------
Ha a háttér állapota ismeretlenként jelenik meg, akkor az a portálon fog megjelenni, például az alábbi képernyőképen:

![Application Gateway háttér állapota – ismeretlen](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Ha a háttér állapota ismeretlenként jelenik meg, akkor az a következő okok valamelyike miatt lehet:

1.  A Application Gateway alhálózat NSG blokkolja a 65503-65534 (v1 SKU) vagy 65200-65535 (v2 SKU) portokhoz való bejövő hozzáférést az "internetről"
2.  UDR a Application Gateway alhálózat alapértelmezett útvonalával (0.0.0.0/0) a következő ugrással nem Internetként
3.  ExpressRoute/VPN-kapcsolat által meghirdetett alapértelmezett útvonal a BGP-VNet keresztül
4.  Az egyéni DNS-kiszolgáló olyan VNet van konfigurálva, amely nem tudja feloldani a nyilvános tartományneveket
5.  Application Gateway sérült állapotban van

**Megoldás:**

1.  Ellenőrizze, hogy a NSG blokkolta-e a 65503-65534 (v1 SKU) vagy 65200-65535 (v2 SKU) portok elérését az internetről

    a.  A Application Gateway "áttekintés" lapon válassza a Virtual Network/alhálózat hivatkozást.

    b.  A Virtual Network alhálózatok lapján válassza ki azt az alhálózatot, ahol a Application Gateway üzembe lett helyezve

    c.  Ellenőrizze, hogy van-e konfigurált NSG

    d.  Ha van ilyen, keresse meg a NSG-erőforrást a keresés lapon vagy az összes erőforrás területen.

    e.  A bejövő szabályok szakaszban adjon hozzá egy bejövő szabályt, amely lehetővé teszi, hogy a 65503-65534-es célport tartománya v1 SKU vagy 65200-65535 v2 SKU legyen bármely vagy internetes forrásként

    f.  Kattintson a Save (Mentés) gombra, és ellenőrizze, hogy a háttér állapota sikeresen megtekinthető-e

    g.  Azt is megteheti, hogy a [PowerShell/parancssori](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) felület segítségével

2.  Ellenőrizze, hogy a UDR van-e alapértelmezett útvonala (0.0.0.0/0) a következő ugrással nem Internetként
    
    a.  Az alhálózat meghatározásához kövesse az 1. a és az 1. b lépést.

    b.  Ellenőrizze, hogy vannak-e konfigurált UDR. Ha van ilyen, keresse meg az erőforrást a keresősáv vagy az összes erőforrás területen.

    c.  Ellenőrizze, hogy vannak-e alapértelmezett útvonalak (0.0.0.0/0) a következő ugrással nem Internetként. Ha a virtuális berendezés vagy a Virtual Network átjáró, meg kell győződnie arról, hogy a virtuális berendezés vagy a helyszíni eszköz képes lesz a csomagnak az internetre való visszairányítására a csomag módosítása nélkül.

    d.  Ellenkező esetben módosítsa a következő ugrást az internetre, kattintson a Mentés gombra, és ellenőrizze a háttér állapotát.

3.  ExpressRoute/VPN-kapcsolat által meghirdetett alapértelmezett útvonal a BGP-VNet keresztül

    a.  Ha ExpressRoute/VPN-kapcsolattal rendelkezik a VNet-en keresztül a BGP-vel, és ha az alapértelmezett útvonalat hirdeti, meg kell győződnie arról, hogy a csomagot az internetre irányítja át a módosítás nélkül.

    b.  A Application Gateway-portálon a "kapcsolatfelvételi hibák" lehetőség használatával ellenőrizheti

    c.  Válassza ki manuálisan a célhelyet bármely internetre irányítható IP-cím, például a "1.1.1.1" és a célport, és keresse meg a kapcsolatot.

    d.  Ha a következő ugrás Virtual Network átjáró, akkor lehet, hogy a ExpressRoute vagy VPN-en keresztül meghirdetett alapértelmezett útvonal

4.  Ha a VNet egyéni DNS-kiszolgáló van konfigurálva, ellenőrizze, hogy a kiszolgáló (k) képes-e a nyilvános tartományok feloldására. Előfordulhat, hogy a nyilvános tartománynevek feloldásához olyan helyzetekben van szükség, amikor a Application Gatewaynak olyan külső tartományokhoz kell érkezniük, mint például az OCSP-kiszolgálók, illetve a tanúsítvány visszavonási állapotának ellenőrzése stb.

5.  Annak ellenőrzéséhez, hogy a Application Gateway állapota Kifogástalan-e, nyissa meg az Resource Health lehetőséget a portálon, és ellenőrizze, hogy az "kifogástalan" állapotú-e. Ha sérült vagy csökkentett teljesítményű állapotot lát, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>További lépések
----------

Ha többet szeretne megtudni a Application Gateway diagnosztikát és a naplózásról [, tekintse](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)meg a következő témakört:.
