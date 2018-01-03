---
title: "Helyez üzembe egy internetre irányuló terheléselosztót IPv6 - Azure-sablon alapján |} Microsoft Docs"
description: "Ügyfélszoftverek központi telepítése az Azure terheléselosztó és a virtuális gépek elosztott terhelésű IPv6 támogatása."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
keywords: "IPv6-alapú, azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot"
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 90439d792eac618671a9de9938302d8930c986d8
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Megoldás üzembe helyezéséhez egy internetre irányuló terheléselosztót IPv6-sablon használatával

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

## <a name="example-deployment-scenario"></a>Központi telepítési példa

A következő ábra szemlélteti a terheléselosztási megoldás üzembe helyezéséhez a jelen cikkben ismertetett példa sablonja segítségével.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrások hoz létre:

* az egyes virtuális gépekhez rendelt IPv4 és IPv6-címmel rendelkező virtuális hálózati illesztő
* egy internetre irányuló terheléselosztót egy IPv4-és IPv6 nyilvános IP-cím
* két terheléselosztási szabályok a nyilvános virtuális IP-címek hozzárendelését a saját végpontokhoz való betöltése
* rendelkezésre állási csoport, amely tartalmazza a két virtuális gépek
* két virtuális gépek (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>A sablon használata az Azure-portál telepítése

Ez a cikk hivatkozik a sablont, amely a közzé van téve a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) gyűjteménye. A sablon letöltése a gyűjteményből, vagy indítsa el a központi telepítést, az Azure közvetlenül a gyűjteményből. Ez a cikk feltételezi, hogy a sablon már letöltötte a helyi számítógépen.

1. Nyissa meg az Azure portál, és jelentkezzen be egy virtuális gépek és a hálózati erőforrások az Azure-előfizetés létrehozásához engedéllyel rendelkező fiók. Is kivéve, ha meglévő erőforrásokat használ, a fiók beleegyezését kéri az erőforráscsoportot és a storage-fiók létrehozásához.
2. Kattintson a "+ új" az a menü, majd a keresőmezőbe a "sablon" típus. A keresési eredmények közül válassza ki a "Sablon-üzembehelyezés".

    ![LB-ipv6-portál – 2. lépés](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. A teljes tartalmának a panelen, kattintson a "Sablon-üzembehelyezés."

    ![LB-ipv6-portál – 3. lépés](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kattintson a "Create".

    ![LB-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kattintson a "Sablon szerkesztése." Törölje a meglévő tartalmat másolja és illessze be a sablonfájl (a kezdő és végződhet {}) teljes tartalmát, majd kattintson a "Mentés."

    > [!NOTE]
    > Használata a Microsoft Internet Explorer, illessze be, amikor megjelenik egy párbeszédpanel, engedélyezi a hozzáférést a Windows vágólapra kéri. Kattintson a "Hozzá lehessen férni."

    ![LB-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kattintson a "Paraméterek szerkesztése." A (paraméterek) panelen a sablon paraméterek szakaszban határozza meg az értékeket a útmutatásának, majd kattintson a "Mentés" gombra a paraméterek panel. Az egyéni telepítés panelen jelölje ki az előfizetését, egy meglévő erőforráscsoportot, vagy hozzon létre egyet. Ha egy erőforráscsoportot hoz létre, majd válassza ki az erőforrásnak a helyét. Ezután kattintson **jogi feltételeket**, majd kattintson a **beszerzési** a jogi feltételek. Azure kezdődik, az erőforrásokat üzembe helyezi. Az erőforrások telepítése több percet vesz igénybe.

    ![LB-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Ezek a paraméterek kapcsolatos további információkért tekintse meg a [sablon paraméterek és változók](#template-parameters-and-variables) szakasz a cikk későbbi részében.

7. A sablon által létrehozott erőforrások megtekintéséhez kattintson a Tallózás gombra, görgessen lefelé a listában, amíg tekintse meg a "Erőforráscsoportok", majd kattintson rá.

    ![LB-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Az erőforrás-csoportok panel kattintson a 6. lépésben megadott erőforráscsoport nevét. Láthatja, hogy telepítve vannak-e az összes erőforrás listáját. Összes hiba is, ha az üzenetnek kell megjelennie "Succeeded" a "Legutóbbi telepítés." Ha nem, akkor győződjön meg arról, hogy a használt fiók rendelkezik-e a szükséges erőforrások létrehozásához szükséges engedélyek.

    ![LB-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Ha közvetlenül a 6. lépés befejezése után az erőforráscsoportokat tallózással, "Utolsó telepítés" közben az erőforrások telepítése alatt álló "A" állapotát megjeleníti.

9. Kattintson az erőforrások listájához a "myIPv6PublicIP". Láthatja, hogy rendelkezik-e az IP-cím IPv6-címet, és hogy a DNS-névvel a 6. lépésben a dnsNameforIPv6LbIP paraméterhez megadott érték. Ehhez az erőforráshoz esetén nyilvános IPv6 cím és az Internet-ügyfelek által elérhető.

    ![LB-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A sablon sikeresen telepítve van, ellenőrizheti kapcsolatot az alábbi feladatok elvégzésével:

1. Jelentkezzen be az Azure-portálon, és minden egyes hozta létre a sablon-üzembehelyezés virtuális kapcsolódjon. Ha telepítette a Windows Server virtuális gép, futtassa az ipconfig/minden a parancssorból. Láthatja, hogy a virtuális gépek rendelkezik-e az IPv4 és IPv6-címeket is. Ha telepített Linux virtuális gépek, konfigurálása a Linux operációs rendszert futtató, dinamikus IPv6-címeket az utasításokat követve a Linux terjesztéshez fogadni szeretné.
2. Egy IPv6-alapú internetre csatlakozó ügyfélszámítógépről a terheléselosztó a nyilvános IPv6-címére kapcsolat létrehozására. Annak ellenőrzéséhez, hogy a terheléselosztó terheléselosztási-e a két virtuális gépek közötti, egy webkiszolgáló, például a Microsoft Internet Information Services (IIS) telepítheti minden egyes a virtuális gépen. Alapértelmezett weblap minden kiszolgálón tartalmazhatnak a szöveges, "Server0" vagy "Kiszolgáló1" egyedi azonosításához. Ezután nyisson meg egy böngészőprogramot, egy IPv6-alapú internetre csatlakoztatott ügyfélen, és keresse meg az állomásnevet, a terheléselosztó-végpontok közötti IPv6-kapcsolatot az egyes virtuális megerősítéséhez dnsNameforIPv6LbIP paraméterhez megadott. Ha csak weblap jelenik meg a csak egy kiszolgálóról, szükség lehet a böngésző gyorsítótár. Nyissa meg a több privát böngészés ideje. Meg kell jelennie egy minden kiszolgáló válaszát.
3. Egy IPv4-alapú internetre csatlakozó ügyfélszámítógépről a terheléselosztó a nyilvános IPv4-címére kapcsolat létrehozására. Annak ellenőrzéséhez, hogy a terheléselosztó terheléselosztási a két virtuális gépeket, sikerült tesztelése használatával az IIS, 2. lépésben ismertetett módon.
4. Minden virtuális gép egy IPv6-alapú vagy az interneten IPv4-csatlakozóval csatlakoztatott eszközre kimenő kapcsolatot kezdeményezzen. Mindkét esetben a forrás IP-címe szerinti a céleszköz a terheléselosztó a nyilvános IPv4 vagy IPv6-címet.

> [!NOTE]
> Az IPv4 és IPv6 ICMP le van tiltva, az Azure-hálózat. Ennek eredményeképpen ICMP eszközök, például a ping mindig sikertelen lesz. Kapcsolat tesztelése, használja a TCP alternatív például TCPing vagy a PowerShell Test-NetConnection parancsmagról. Ne feledje, hogy az IP-címeket, az ábrán is látható értékeket, amelyeket láthat példát. Az IPv6-címeket dinamikusan hozzárendelt, mivel kap a címek eltérnek, és régiónként eltérőek lehetnek. Azt is gyakori, hogy a nyilvános IPv6-cím a terheléselosztó elérhető, mint a saját IPv6-címeket a háttér-készlet különböző előtaggal kezdődik.

## <a name="template-parameters-and-variables"></a>Sablon paraméterek és változók

Az Azure Resource Manager-sablon tartalmazza a változók és a paraméterek az igényeinek megfelelően testre szabható. Változók nem szeretné, hogy a felhasználó megváltoztathatja rögzített értékeket használ. Paraméterek, amelyet a felhasználónak meg kell adnia a sablon telepítésekor értékek szerepelnek. A példa sablon ebben a cikkben ismertetett forgatókönyv van konfigurálva. Testre szabhatja ezt a környezet igényeinek.

A példa sablon, a cikk ezt használja a következő változók és paramétereket tartalmazza:

| A paraméter / változó | Megjegyzések |
| --- | --- |
| adminUsername |Adja meg a rendszergazdai fiók használatával jelentkezzen be a virtuális gépek nevét. |
| adminPassword |Adja meg a jelszót a rendszergazdai fiók használatával jelentkezzen be a virtuális gépekre. |
| dnsNameforIPv4LbIP |Adja meg a DNS-állomásnevet, mint a terheléselosztó a nyilvános nevet hozzárendelni kívánt. Ez a név oldja fel a terheléselosztó nyilvános IPv4-címére. A név lehet kis és felel meg a reguláris kifejezéssel: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Adja meg a DNS-állomásnevet, mint a terheléselosztó a nyilvános nevet hozzárendelni kívánt. Ez a név oldja fel a terheléselosztó nyilvános IPv6-cím. A név lehet kis és felel meg a reguláris kifejezéssel: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Ez a neve megegyezik az IPv4-cím lehet. Amikor egy ügyfél az Azure ad vissza. Ez a név DNS-lekérdezést küld az A és AAAA rögzíti, amikor a neve meg van osztva. |
| vmNamePrefix |Adja meg a virtuális gép nevének előtagja. A sablon hozzáfűz egy számot (0, 1, stb.) a neve, a virtuális gépek jönnek létre. |
| nicNamePrefix |Adja meg a hálózati illesztő előtagja. A sablon hozzáfűz egy számot (0, 1, stb.) a neve, a hálózati adapterek létrehozásakor. |
| storageAccountName |Adja meg a meglévő tárfiók neve, vagy adja meg a sablont kell létrehozni egy új nevét. |
| availabilitySetName |A rendelkezésre állási csoportot a virtuális gépek használni kívánt majd beírása |
| addressPrefix |A virtuális hálózat címtartományát definiáló címelőtag |
| subnetName |A vnet létrehozása az az alhálózat neve |
| subnetPrefix |Az alhálózat címtartománya definiáló címelőtag |
| vnetName |Adja meg a virtuális hálózat a virtuális gépek által használt nevét. |
| ipv4PrivateIPAddressType |A magánhálózati IP-cím (statikus vagy dinamikus) használt elosztási módszer. |
| ipv6PrivateIPAddressType |A kiosztási módszer a magánhálózati IP-cím (dinamikus) használt. IPv6 csak dinamikus foglalási támogatja. |
| numberOfInstances |A sablon által telepített terhelésű példányainak száma |
| ipv4PublicIPAddressName |Adja meg a terheléselosztó a nyilvános IPv4-cím folytatott kommunikációhoz használni kívánt DNS-nevét. |
| ipv4PublicIPAddressType |A nyilvános IP-cím (statikus vagy dinamikus) használt elosztási módszer. |
| Ipv6PublicIPAddressName |Adja meg a terheléselosztó a nyilvános IPv6-cím folytatott kommunikációhoz használni kívánt DNS-nevét. |
| ipv6PublicIPAddressType |A kiosztási módszer a nyilvános IP-cím (dinamikus) használt. IPv6 csak dinamikus foglalási támogatja. |
| lbName |Adja meg a terheléselosztó nevét. Ez a név megjelenik a portálon, vagy használható, ha arra való hivatkozás a parancssori felületen vagy a PowerShell-paranccsal. |

A sablonban levő többi változók értékeit származtatott vannak hozzárendelve, amikor Azure-hoz az erőforrásokat. Ne változtassa meg ezeket a változókat.
