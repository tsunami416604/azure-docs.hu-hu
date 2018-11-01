---
title: Egy internetkapcsolattal rendelkező load balancer IPv6 - Azure-sablon üzembe helyezése |} A Microsoft Docs
description: Hogyan helyezheti üzembe az Azure Load Balancer és a virtuális gépek elosztott terhelésű IPv6-támogatás.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6-alapú, az azure load balancer, kettős verem, nyilvános IP-cím, natív ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 14a28fb341692c309ff4f965628b38a767c56633
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740742"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Megoldás üzembe helyezése egy internetkapcsolattal rendelkező load balancer konfigurálása IPv6-tal sablon használatával

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [Sablon](load-balancer-ipv6-internet-template.md)



Az Azure Load Balancer 4. szintű (TCP, UDP) terheléselosztónak minősül. A terheléselosztó a felhőszolgáltatások vagy virtuális gépek kifogástalan állapotú szolgáltatási példányai között osztja meg a bejövő forgalmat egy terheléselosztói készletben, és ezáltal biztosítja a magas rendelkezésre állást. Az Azure Load Balancer a szolgáltatásokat több portra vagy több IP-címre, illetve portokra és IP-címekre egyaránt továbbíthatja.

## <a name="example-deployment-scenario"></a>A példában üzembe helyezési forgatókönyv

A következő ábra szemlélteti a terheléselosztási megoldás üzembe helyezéséhez a cikkben bemutatott példa-sablon használatával.

![Terheléselosztói forgatókönyv](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Ebben a forgatókönyvben a következő Azure-erőforrásokat hoz létre:

* Az egyes virtuális Gépekhez rendelt IPv4- és IPv6-címekkel rendelkező virtuális hálózati adapter
* egy internetkapcsolattal rendelkező Load Balancer egy IPv4-és IPv6-alapú nyilvános IP-cím
* Két a nyilvános VIP-címek leképezése a privát végpontok terheléselosztási szabályok betöltése
* rendelkezésre állási csoport, amely tartalmazza a két virtuális gép
* Két virtuális gép (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Helyezi üzembe a sablont az Azure portal használatával

Ez a cikk hivatkozik, amely közzé van téve a sablon a [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) gyűjteménye. A sablon letöltése a katalógusból, vagy indítsa el az üzembe helyezés az Azure-ban közvetlenül a katalógusból. Ez a cikk feltételezi, hogy a sablon letöltötte a helyi számítógépen.

1. Nyissa meg az Azure portal és a virtuális gépek és a egy Azure-előfizetésen belül a hálózati erőforrások létrehozásához szükséges engedélyek rendelkező fiókkal jelentkezzen be. Is kivéve, ha meglévő erőforrásokat használ, a fiókot kell egy erőforráscsoportot és a egy storage-fiók létrehozásához szükséges engedéllyel.
2. Kattintson a "+ új" az a menüben, majd írja be a keresőmezőbe a "sablon". A keresési eredmények közül válassza ki a "Sablon telepítése".

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. A minden erőforrás panelen kattintson a "Központi telepítési sablont."

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Kattintson a "Create".

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Kattintson a "Sablon szerkesztése." Meglévő tartalmának törlése másolja és illessze be a teljes tartalmát a sablonfájlt (a kezdete és vége {}), majd kattintson a "Mentés."

    > [!NOTE]
    > Ha használja a Microsoft Internet Explorer, illessze be, amikor egy párbeszédpanel rákérdez arra, hogy engedélyezze a hozzáférést a Windows-vágólapra. Kattintson a "Hozzáférés engedélyezése".

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Kattintson a "Paraméterek szerkesztése." A sablon paraméterek szakaszban határozza meg az értéket, az útmutatót az (paraméterek) panelen, majd kattintson a "Mentés" gombra kattintva zárja be a (paraméterek) panelen. Az egyéni üzembe helyezés panelen válassza ki az előfizetést, egy meglévő erőforráscsoportot, vagy hozzon létre egyet. Ha hoz létre egy erőforráscsoportot, majd válassza ki az erőforráscsoport helyét. Ezután kattintson **jogi feltételek**, majd kattintson a **beszerzési** a jogi feltételek. Az Azure elkezdi üzembe helyezni az erőforrásokat. Erőforrások üzembe helyezése néhány percet vesz igénybe.

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    További információ ezekről a paraméterekről: a [sablon paramétereket és változókat](#template-parameters-and-variables) Ez a cikk későbbi szakaszában talál.

7. A sablon által létrehozott erőforrások megtekintéséhez kattintson a Tallózás gombra, görgessen lefelé a listában, amíg meg nem tekintse meg a "Erőforráscsoportok", majd kattintson rá.

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Az erőforrás csoportok paneljén kattintson az eljárás 6. lépésében megadott erőforráscsoport nevét. Az üzembe helyezett összes erőforrás listáját láthatja. Jól történt összes, "Succeeded" kell kapnia a "Legutóbbi üzembe helyezés." Ha nem, akkor győződjön meg arról, hogy a használt fiók rendelkezik-e a szükséges erőforrások létrehozásához szükséges engedélyek.

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Az erőforráscsoportok közvetlenül a 6. lépés befejezése után keresse meg, ha "A legutóbbi üzembe helyezés" közben a rendszer telepíti az erőforrások "Üzembe helyezés" állapotát megjeleníti.

9. Kattintson a "myIPv6PublicIP" az erőforrások listájában. Láthatja, hogy rendelkezik-e az IP-cím IPv6-címet, és hogy a DNS-név a 6. lépésben dnsNameforIPv6LbIP paraméterben megadott értéket. Ezt az erőforrást a nyilvános IPv6 cím és a gazdagépcsoport nevét, amely az Internet-ügyfelek számára elérhető.

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Sikeresen helyezte üzembe a sablont, amikor kapcsolat a következő feladatok végrehajtásával ellenőrizheti:

1. Jelentkezzen be az Azure Portalon, és csatlakozzon az egyes szerint a sablon üzembe helyezéséhez létrehozott virtuális gépek. Ha telepítette a Windows Server virtuális gép, IP-konfiguráció futtatása és- tárolókról az összes parancsot a parancssorba. Láthatja, hogy a virtuális gépek rendelkeznek-e az IPv4 és IPv6-címeket is. Ha Linux rendszerű virtuális gépek üzembe helyezte, kell fogadni a megjelenő utasításokat a Linux-disztribúció használatával dinamikus IPv6-címek a Linux operációs rendszer konfigurálása.
2. A terheléselosztó a nyilvános IPv6-cím kapcsolatot kezdeményeznie a IPv6-alapú internetre csatlakozó ügyfélszámítógépről. Annak ellenőrzéséhez, hogy a két virtuális gép között osztja el a terheléselosztó, telepítheti az egyes virtuális gépek egy webkiszolgáló, például a Microsoft Internet Information Services (IIS). Az alapértelmezett weblapot minden kiszolgálón tartalmazhatnak a szöveget "Server0" vagy "Kiszolgáló1" egyedi azonosításához. Ezután nyisson meg egy webböngészőt IPv6 internetkapcsolat-ügyfélre, és keresse meg az állomásnevet, erősítse meg az egyes virtuális gépek teljes körű IPv6-kapcsolatot, hogy a terheléselosztó dnsNameforIPv6LbIP paraméterében megadott. Ha csak a weblap csak egy kiszolgálóról, szükség lehet törölni a böngésző gyorsítótárát. Nyisson meg több privát böngészési munkamenetet. Megtekintheti az egyes kiszolgáló válaszára.
3. A terheléselosztó a nyilvános IPv4-cím kapcsolatot kezdeményeznie a IPv4 internetkapcsolattal rendelkező ügyfélszámítógépről. Annak ellenőrzése, hogy a terheléselosztó terheléselosztási a két virtuális gép, tesztelhet, az IIS használatával a 2. lépésben leírt módon.
4. Minden egyes virtuális gépről kezdeményezzen egy kimenő kapcsolatot egy IPv6-alapú vagy internetes IPv4-csatlakoztatott eszközön. Mindkét esetben a céleszköz által látott forrás IP-címe a terheléselosztó nyilvános IPv4- vagy IPv6-címét.

> [!NOTE]
> IPv4 és IPv6 ICMP le van tiltva, a az Azure-hálózatot. Ennek eredményeképpen ICMP-eszközök például mindig pingelése sikertelen. A kapcsolat teszteléséhez használja a TCP helyett például TCPing vagy a PowerShell Test-NetConnection parancsmagról. Vegye figyelembe, hogy az IP-címek az ábrán látható értékeket, amelyeket láthat példát. Az IPv6-címeket dinamikusan hozzá van rendelve, mert a címek kap eltérőek lehetnek, és régiónként. Azt is gyakori, hogy a magánhálózati IPv6-címek a háttér-címkészletben lévő, mint a különböző előtaggal indítsa el a terheléselosztó a nyilvános IPv6-cím.

## <a name="template-parameters-and-variables"></a>Sablon paraméterek és változók

Az Azure Resource Manager-sablon több változók és paraméterek, amelyeket az igényeinek megfelelően testre is tartalmazza. Rögzített értékek, amelyek nem szeretné, hogy a felhasználó módosíthatja a változókat használják. Paraméterek használhatók értékeket, hogy szeretné-e a felhasználónak meg kell adnia a sablon üzembe helyezésekor. A példában a sablon a cikkben leírt forgatókönyvhöz van konfigurálva. A környezet igényeinek megfelelően szabhatja.

Ebben a cikkben használt példa sablon tartalmazza az alábbi változók és paraméterek:

| A paraméter / változó | Megjegyzések |
| --- | --- |
| adminUsername |Adja meg a virtuális gépekhez való bejelentkezéshez használt rendszergazdai fiók nevét. |
| adminPassword |Adja meg a jelszót a rendszergazdai fiók segítségével jelentkezzen be a virtuális gépek számára. |
| dnsNameforIPv4LbIP |Adja meg a terheléselosztó a nyilvános nevet hozzárendelni kívánt DNS-állomásnevét. Ez a név a terheléselosztó nyilvános IPv4-cím mutat. A neve kisbetűket és felel meg a következő reguláris kifejezésre: ^ [a – z] [a-z0 - 9-]{1,61}[a-z0-9] $. |
| dnsNameforIPv6LbIP |Adja meg a terheléselosztó a nyilvános nevet hozzárendelni kívánt DNS-állomásnevét. Ez a név a terheléselosztó nyilvános IPv6-címére oldja fel. A neve kisbetűket és felel meg a következő reguláris kifejezésre: ^ [a – z] [a-z0 - 9-]{1,61}[a-z0-9] $. Ez lehet a neve megegyezik az IPv4-címet. Amikor egy ügyfél az Azure ad vissza. Ez a név DNS-lekérdezést küld mind a és AAAA típusú rögzíti, amikor a neve meg van osztva. |
| vmNamePrefix |Adja meg a virtuális gép nevének előtagja. A sablon hozzáfűz egy számot (0, 1, stb.), a neve, ha a virtuális gépek jönnek létre. |
| nicNamePrefix |Adja meg a hálózati adapter nevének előtagja. A sablon hozzáfűz egy számot (0, 1, stb.) a nevére, a hálózati adapterek létrehozásakor. |
| storageAccountName |Adja meg a nevét, egy meglévő tárfiókot, vagy adjon meg egy újat kell létrehoznia a sablon nevét. |
| availabilitySetName |Írja be a nevét, a rendelkezésre állási csoportot a virtuális gépek használandó |
| addressPrefix |A címelőtag segítségével határozhatók meg azok a virtuális hálózat címtartománya |
| subnetName |A virtuális hálózat számára létrehozott alhálózat neve |
| subnetPrefix |A címelőtag segítségével határozhatók meg az alhálózat címtartománya |
| vnetName |Adja meg a virtuális gépek által használt virtuális hálózat nevét. |
| ipv4PrivateIPAddressType |A lefoglalási módszert használni a magánhálózati IP-cím (statikus vagy dinamikus) |
| ipv6PrivateIPAddressType |A lefoglalási módszert használni a magánhálózati IP-cím (dinamikus). Az IPv6 csak dinamikus kiosztási támogatja. |
| numberOfInstances |A sablon által üzembe helyezett terhelésű példányok száma |
| ipv4PublicIPAddressName |Adja meg a terheléselosztó a nyilvános IPv4-címmel folytatott kommunikációhoz használni kívánt DNS-név. |
| ipv4PublicIPAddressType |A lefoglalási módszert használja a nyilvános IP-cím (statikus vagy dinamikus) |
| Ipv6PublicIPAddressName |Adja meg a terheléselosztó a nyilvános IPv6-cím folytatott kommunikációhoz használni kívánt DNS-név. |
| ipv6PublicIPAddressType |A lefoglalási módszert használja a nyilvános IP-cím (dinamikus). Az IPv6 csak dinamikus kiosztási támogatja. |
| lbName |Adja meg a terheléselosztó nevét. Ez a név megjelenik a portálon vagy megnevezése, a parancssori felület vagy PowerShell-paranccsal. |

A fennmaradó változók a sablonban vannak hozzárendelve, amikor az Azure létrehozza az erőforrásokat származtatott értékeket tartalmaznak. Ne módosítsa ezeket a változókat.
