---
title: Internetes terheléselosztó üzembe helyezése IPv6 - Azure sablonnal
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogy miként telepítheti az IPv6-támogatást az Azure Load Balancer és a terheléselosztásos virtuális gépek számára egy Azure-sablon használatával.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azúrkék terheléselosztó, kettős verem, nyilvános ip, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: eb9703a1944a650f41d76c05d79764f8bdf8cd52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045451"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Internetes terheléselosztó megoldás telepítése iPv6-tal sablon használatával

> [!div class="op_single_selector"]
> * [Powershell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>Ez a cikk egy bevezető IPv6-szolgáltatást ismertet, amely lehetővé teszi, hogy az egyszerű terheléselosztók iPv4- és IPv6-kapcsolatot is biztosítsanak. Az IPv6-kapcsolat már elérhető az [IPv6 for Azure VNET-k](../virtual-network/ipv6-overview.md) esetében, amely integrálja az IPv6-kapcsolatot a virtuális hálózatokkal, és olyan kulcsfontosságú funkciókat tartalmaz, mint az IPv6 hálózati biztonsági csoport szabályai, az IPv6-felhasználó által definiált útválasztás, az IPv6 alapszintű és a szabványos terheléselosztás stb.  Az IPv6 azure-beli VNET-k az Azure-beli IPv6-alkalmazások ajánlott szabványai. Lásd: [IPv6 az Azure VNET Powershell üzembe helyezéséhez](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

## <a name="example-deployment-scenario"></a>Példa telepítési forgatókönyvre

Az alábbi ábra a jelen cikkben ismertetett példasablon használatával üzembe helyezett terheléselosztási megoldást mutatja be.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat hozza létre:

* virtuális hálózati adapter minden virtuális géphez, amelyhez IPv4- és IPv6-címek is vannak hozzárendelve
* Internetkapcsolattal rendelkező terheléselosztó IPv4-vel és IPv6 nyilvános IP-címmel
* két terheléselosztási szabály a nyilvános VIP-k hozzárendeléséhez a magánvégpontokhoz
* a két virtuális gépet tartalmazó rendelkezésre állási csoport
* két virtuális gép (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>A sablon üzembe helyezése az Azure Portalhasználatával

Ez a cikk az [Azure gyorsútmutató-sablonok](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) gyűjteményében közzétett sablonra hivatkozik. Letöltheti a sablont a katalógusból, vagy elindíthatja a központi telepítést az Azure-ban közvetlenül a katalógusból. Ez a cikk feltételezi, hogy letöltötte a sablont a helyi számítógépre.

1. Nyissa meg az Azure Portalon, és jelentkezzen be egy olyan fiókkal, amely rendelkezik a virtuális gépek és hálózati erőforrások azure-előfizetésen belüli létrehozásához szükséges engedélyekkel. Továbbá, ha nem használja a meglévő erőforrásokat, a fióknak engedélyre van szüksége egy erőforráscsoport és egy tárfiók létrehozásához.
2. Kattintson a menü "+Új" parancsára, majd írja be a keresőmezőbe a "template" kifejezést. Válassza ki a "Sablon központi telepítése" lehetőséget a keresési eredmények között.

    ![lb-ipv6-portal-lépés2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. A Minden panelen kattintson a "Sablon üzembe helyezése" elemre.

    ![lb-ipv6-portal-lépés3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kattintson a "Létrehozás" gombra.

    ![lb-ipv6-portal-lépés4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kattintson a "Sablon szerkesztése" gombra. Törölje a meglévő tartalmat, és másolja/illessze be a sablonfájl teljes tartalmát (a kezdő és záró { }), majd kattintson a "Mentés" gombra.

    > [!NOTE]
    > Microsoft Internet Explorer használata esetén a beillesztéskor megjelenik egy párbeszédpanel, amely a Windows vágólapelérésének engedélyezését kéri. Kattintson a "Hozzáférés engedélyezése" gombra.

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kattintson a "Paraméterek szerkesztése" gombra. A Paraméterek panelen adja meg a Sablon paraméterek szakaszban a útmutatásonként megadott értékeket, majd kattintson a "Mentés" gombra a Paraméterek panel bezárásához. Az egyéni üzembe helyezés panelen válassza ki az előfizetést, egy meglévő erőforráscsoportot, vagy hozzon létre egyet. Ha erőforráscsoportot hoz létre, akkor válassza ki az erőforráscsoport helyét. Ezután kattintson a **Jogi feltételek gombra,** majd a jogi feltételekhez a **Vásárlás** parancsra. Az Azure megkezdi az erőforrások üzembe helyezését. Az összes erőforrás üzembe helyezése több percet vesz igénybe.

    ![lb-ipv6-portal-lépés6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Ezekről a paraméterekről a [cikk későbbi, sablonparaméterek és -változók](#template-parameters-and-variables) című szakaszában olvashat bővebben.

7. A sablon által létrehozott erőforrások megtekintéséhez kattintson a Tallózás gombra, görgessen lefelé a listában, amíg meg nem jelenik az "Erőforráscsoportok" lista, majd kattintson rá.

    ![lb-ipv6-portal-lépés7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Az Erőforráscsoportok panelen kattintson a 6. Megjelenik az összes üzembe helyezett erőforrás listája. Ha minden jól ment, akkor a "Sikeres" szót kell mondania az "Utolsó telepítés" alatt. Ha nem, győződjön meg arról, hogy a használt fiók rendelkezik a szükséges erőforrások létrehozásához szükséges engedélyekkel.

    ![lb-ipv6-portal-lépés8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Ha a 6.

9. Kattintson a "myIPv6PublicIP" elemre az erőforrások listájában. Láthatja, hogy rendelkezik egy IPv6-címmel az IP-cím alatt, és hogy dns-neve a dnsNameforIPv6LbIP paraméterhez megadott érték a 6. Ez az erőforrás az internetes ügyfelek számára elérhető nyilvános IPv6-cím és állomásnév.

    ![lb-ipv6-portal-lépés9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Ha a sablon telepítése sikeresen megtörtént, a következő feladatok elvégzésével ellenőrizheti a kapcsolatot:

1. Jelentkezzen be az Azure Portalon, és csatlakozzon a sablon üzembe helyezése által létrehozott virtuális gépekhez. Ha windows server virtuális gép telepítése, futtassa az ipconfig /all parancsot. Láthatja, hogy a virtuális gépek iPv4- és IPv6-címmel is rendelkeznek. Ha linuxos virtuális gépeket telepített, konfigurálnia kell a Linux operációs rendszert, hogy dinamikus IPv6-címeket kapjon a Linux-disztribúcióhoz megadott utasítások alapján.
2. Egy IPv6-internetkapcsolattal rendelkező ügyfélből kezdeményezzen kapcsolatot a terheléselosztó nyilvános IPv6-címével. Annak ellenőrzéséhez, hogy a terheléselosztó egyensúlyban van-e a két virtuális gép között, telepíthet egy webkiszolgálót, például a Microsoft Internet Information Services (IIS) szolgáltatást az egyes virtuális gépekre. Az alapértelmezett weblap minden kiszolgálón tartalmazhatja a "Server0" vagy a "Server1" szöveget, hogy egyedileg azonosíthassa azt. Ezután nyisson meg egy internetböngészőt egy IPv6-hoz csatlakoztatott ügyfélen, és keresse meg a terheléselosztó dnsNameforIPv6LbIP paraméteréhez megadott állomásnevet, és erősítse meg az egyes virtuális gépekhez való végpontok közötti IPv6-kapcsolatot. Ha csak egy kiszolgálóról látja a weblapot, előfordulhat, hogy törölnie kell a böngésző gyorsítótárát. Nyisson meg több privát böngészési munkamenetet. Meg kell látni a választ minden szerver.
3. Egy IPv4-hez csatlakoztatott iPv4-ügyfélről kezdeményezzen kapcsolatot a terheléselosztó nyilvános IPv4-címével. Annak ellenőrzéséhez, hogy a terheléselosztó terheléselosztás a két virtuális gép, tesztelheti az IIS, a 2.
4. Minden virtuális gépről kezdeményezzen kimenő kapcsolatot egy IPv6-vagy IPv4-kapcsolattal rendelkező interneteszközzel. A céleszköz által látott forrás IP-cím mindkét esetben a terheléselosztó nyilvános IPv4- vagy IPv6-címe.

> [!NOTE]
> Az ICMP az IPv4 és az IPv6 protokollhoz egyaránt le van tiltva az Azure-hálózatban. Ennek eredményeként, ICMP eszközök, mint a ping mindig nem. A kapcsolat teszteléséhez használjon TCP-alternatívát, például a TCPing vagy a PowerShell Test-NetConnection parancsmag. Vegye figyelembe, hogy az ábrán látható IP-címek példák a megjelenő értékekre. Mivel az IPv6-címek dinamikusan vannak hozzárendelve, a kapott címek régiónként eltérőek lesznek. Emellett gyakori, hogy a terheléselosztó nyilvános IPv6-címe a háttérkészletben lévő magánjellegű IPv6-címeknél eltérő előtaggal kezdődik.

## <a name="template-parameters-and-variables"></a>Sablonparaméterek és -változók

Az Azure Resource Manager-sablon több változót és paramétert tartalmaz, amelyeket igény szerint szabhat testre. A változók olyan rögzített értékekhez használatosak, amelyeket a felhasználónak nem kell módosítania. A paraméterek olyan értékekhez használatosak, amelyeket a felhasználónak meg kell adnia a sablon telepítésekor. A példasablon a cikkben ismertetett forgatókönyvhöz van konfigurálva. Ezt a környezet igényeihez szabhatja.

A cikkben használt példasablon a következő változókat és paramétereket tartalmazza:

| Paraméter / Változó | Megjegyzések |
| --- | --- |
| adminUsername |Adja meg a virtuális gépekre való bejelentkezéshez használt rendszergazdai fiók nevét. |
| adminPassword |Adja meg a virtuális gépekre való bejelentkezéshez használt rendszergazdai fiók jelszavát. |
| dnsNameforIPv4LbIP |Adja meg a terheléselosztó nyilvános neveként hozzárendelni kívánt DNS-állomásnevet. Ez a név a terheléselosztó nyilvános IPv4-címére oldódik fel. A névnek kisbetűsnek kell lennie, és meg kell egyeznie a{1,61}regex-el: ^[a-z][a-z0-9-] [a-z0-9]$. |
| dnsNameforIPv6LbIP |Adja meg a terheléselosztó nyilvános neveként hozzárendelni kívánt DNS-állomásnevet. Ez a név a terheléselosztó nyilvános IPv6-címére oldódik fel. A névnek kisbetűsnek kell lennie, és meg kell egyeznie a{1,61}regex-el: ^[a-z][a-z0-9-] [a-z0-9]$. Ez megegyezhet az IPv4-címmel. Amikor egy ügyfél DNS-lekérdezést küld ehhez a névhez, az Azure a név megosztásakor az A és AAAA rekordokat is visszaadja. |
| vmNamePrefix |Adja meg a virtuális gép névelőtagja. A sablon hozzáfűz egy számot (0, 1, stb) a névhez, amikor a virtuális gépek jönnek létre. |
| nicNamePrefix |Adja meg a hálózati adapter névelőnevét. A sablon a hálózati adapterek létrehozásakor egy számot (0, 1 stb.) fűz a névhez. |
| storageAccountName |Adja meg egy meglévő tárfiók nevét, vagy adja meg a sablon által létrehozandó új nevét. |
| availabilitySetName |Írja be a virtuális gépekhez használandó rendelkezésre állási készlet nevét. |
| addressPrefix |A virtuális hálózat címtartományának meghatározásához használt címelőtag |
| alhálózatneve |A virtuális hálózathoz létrehozott alhálózat neve |
| subnetPrefix |Az alhálózat címtartományának meghatározásához használt címelőtag |
| vnetName |Adja meg a virtuális gépek által használt virtuális hálózat nevét. |
| ipv4PrivateIPAddressType |A privát IP-cím (statikus vagy dinamikus) kiosztási módszere |
| ipv6PrivateIPAddressType |A privát IP-cím (Dinamikus) kiosztási módszere. Az IPv6 csak a dinamikus lefoglalást támogatja. |
| numberOfInstances |A sablon által telepített terheléselosztási példányok száma |
| ipv4PublicIPAddressName |Adja meg a terheléselosztó nyilvános IPv4-címével való kommunikációhoz használni kívánt DNS-nevet. |
| ipv4PublicIPAddressType típus |A nyilvános IP-címhez használt allokációs módszer (statikus vagy dinamikus) |
| Ipv6PublicIPAddressName |Adja meg a terheléselosztó nyilvános IPv6-címével való kommunikációhoz használni kívánt DNS-nevet. |
| ipv6PublicIPAddressType típus |A nyilvános IP-címhez (Dinamikus) használt elosztási módszer. Az IPv6 csak a dinamikus lefoglalást támogatja. |
| lbName |Adja meg a terheléselosztó nevét. Ez a név jelenik meg a portálon, vagy használt, amikor hivatkozik rá egy CLI vagy PowerShell parancsot. |

A sablon ban fennmaradó változók származtatott értékeket tartalmaznak, amelyek az erőforrások létrehozásakor vannak hozzárendelve. Ne változtassa meg ezeket a változókat.

## <a name="next-steps"></a>További lépések

A Sablonban lévő terheléselosztó JSON-szintaxisát és tulajdonságait a [Microsoft.Network/loadBalancers című](/azure/templates/microsoft.network/loadbalancers)témakörben olvashatja.
