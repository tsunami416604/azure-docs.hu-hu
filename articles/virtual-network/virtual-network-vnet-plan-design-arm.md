---
title: "Az Azure Virtual Network (VNet) tervezési és kialakítási útmutató |} Microsoft Docs"
description: "Megtudhatja, hogyan tervezése és kialakítása elkülönítési, a kapcsolat és a hely igény Azure virtuális hálózatairól."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 9a0126235c9ff3fec05d7709bdee95ab4832a33b
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="plan-and-design-azure-virtual-networks"></a>Azure virtuális hálózatok megtervezése
Létre virtuális hálózatok kísérletezhet elég egyszerűen, de valószínűleg több Vnetek adott idő alatt a szervezet a termelési igényeinek támogatásához telepíteni fogja. Az egyes tervezési és kialakítási lesz Vnetek telepítéséhez, és csatlakozzon a hatékonyabb szükséges erőforrások. Ha nem ismeri a Vneteket, javasoljuk, hogy Ön [Vnetek megismerése](virtual-networks-overview.md) és [telepítése](virtual-networks-create-vnet-arm-pportal.md) egy, a folytatás előtt.

## <a name="plan"></a>Felkészülés
Azure-előfizetések, régiók és hálózati erőforrásokat alapos ismerete fontos a sikeres. Alább szempontok listáját használhatja kiindulási pontként. E szempontok elsajátítása után hálózatterv adhat meg a követelményeknek.

### <a name="considerations"></a>Megfontolandó szempontok
Mielőtt az alábbi kérdések megválaszolása, a tervezési, vegye figyelembe a következőket:

* Minden Azure-ban létrehozhat egy vagy több erőforrás áll. Egy virtuális gép (VM) egy olyan erőforrás, egy virtuális gép által használt hálózati adapter kapcsolatának (NIC) egy erőforrás, a nyilvános IP-cím egy hálózati adapter által használt erőforrás, a virtuális hálózatot a hálózati adapter csatlakoztatva van egy olyan erőforrás.
* Erőforrások létrehozása egy [az Azure-régió](https://azure.microsoft.com/regions/#services) és -előfizetése. Erőforrások csak egy virtuális hálózat már szerepel ugyanabban a régióban kell csatlakoztatni, és az erőforrás előfizetése.
* Virtuális hálózatok kapcsolódhatnak egymáshoz, a használatával:
    * **[Virtuális hálózati társviszony-létesítés](virtual-network-peering-overview.md)**: már léteznie kell a virtuális hálózatok ugyanazon Azure-régiót. Erőforrások között lévő virtuális hálózatok társviszonyban sávszélessége ugyanaz, mintha ugyanahhoz a virtuális hálózathoz csatlakoztatva az erőforrásokat.
    * **Egy Azure [VPN-átjáró](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: A virtuális hálózatok ugyanazon vagy másik Azure-régiókban is szerepel. Egy VPN-átjárón keresztül csatlakozó virtuális hálózatokon lévő erőforrások közötti sávszélesség korlátozza a sávszélességet a VPN-átjáró.
* Csatlakozhat Vnetek a helyszíni hálózat használatával a [kapcsolati lehetőségek](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) érhető el az Azure-ban.
* Különböző erőforrások foghatók egybe a [erőforráscsoportok](../azure-resource-manager/resource-group-overview.md#resource-groups), így könnyebben kezelheti az erőforrás egy egységként. Erőforráscsoport több régióba származó erőforrásokat is tartalmaznak, mindaddig, amíg az erőforrás ugyanahhoz az előfizetéshez tartozik.

### <a name="define-requirements"></a>Követelmények megadása
Az alábbi kérdéseket használja kiindulópontként meghatározásához az Azure-hálózatot.    

1. Milyen Azure helyek használatával állomás Vnetekhez?
2. Meg kell adnia a következő Azure helyek közötti kommunikáció?
3. Meg kell adnia a Azure VNet(s) és a helyszíni datacenter(s) közötti kommunikáció?
4. Egy szolgáltatási (IaaS) virtuális gép, hogy hány infrastruktúra cloud services – szerepkörök és web apps nincs szüksége a megoldás?
5. El kell különítenie a forgalom virtuális (azaz előtér-webkiszolgáló és háttérbeli adatbázis-kiszolgálók) alapján?
6. Virtuális készülékek használata adatforgalom szabályozásához kell?
7. Felhasználók más-más részhalmazához különböző Azure-erőforrások engedélyek igényelnek?

### <a name="understand-vnet-and-subnet-properties"></a>VNet és alhálózat tulajdonságok ismertetése
Hálózatok és alhálózatok erőforrások segítségével határozza meg az Azure-ban futó munkaterhelések biztonsági korlátot. Egy VNet jellemzőek, címterekhez, meghatározott CIDR-blokkok gyűjteménye.

> [!NOTE]
> A hálózati rendszergazdák jártas CIDR-formátumban. Ha nem ismeri a CIDR, [olvashat azokról bővebben](http://whatismyipaddress.com/cidr).
>
>

Vnetek az alábbi tulajdonságokat tartalmazzák.

| Tulajdonság | Leírás | Korlátozások |
| --- | --- | --- |
| **név** |VNet neve |Legfeljebb 80 karakterből álló karakterlánc. Betűk, számok, aláhúzásjelet, pontokat és kötőjeleket tartalmazhat. Betűvel vagy számmal kell kezdődnie. Betűvel, számmal vagy aláhúzásjellel kell végződnie. Felső vagy kisbetűket is tartalmaz. |
| **hely** |Azure-beli hely (más néven régió). |Az érvényes Azure helyek valamelyikén kell lennie. |
| **Címtartományt** |A virtuális hálózat CIDR-jelöléssel alkotó címelőtagokat gyűjteménye. |Érvényes CIDR címblokkokat, beleértve a nyilvános IP-címtartományok tömbje kell lennie. |
| **alhálózatok** |A virtuális hálózatot alkotó alhálózatok gyűjteménye |az alhálózati tulajdonságok az alábbi táblázatban találja. |
| **dhcpOptions** |Egy kötelező tulajdonság nevű tartalmazó objektum **dnsServers**. | |
| **dnsServers** |A virtuális hálózat által használt DNS-kiszolgálók tömbje. Ha nincs megadva kiszolgáló, az Azure belső névfeloldást szolgál. |Legfeljebb 10 DNS-kiszolgálók, IP-cím szerint tömbnek kell lennie. |

Egy alhálózat egy Vnetet gyermek erőforrása, és segítségével meghatározhatja a címterek belül használja az IP-cím előtagokat CIDR-blokkja részeit. Hálózati adapter alhálózatok hozzá, és kapcsolódik a virtuális gépek, a különböző munkaterhelések biztosítható a kapcsolat.

Alhálózatok az alábbi tulajdonságokat tartalmazzák.

| Tulajdonság | Leírás | Korlátozások |
| --- | --- | --- |
| **név** |Alhálózat neve |Legfeljebb 80 karakterből álló karakterlánc. Betűk, számok, aláhúzásjelet, pontokat és kötőjeleket tartalmazhat. Betűvel vagy számmal kell kezdődnie. Betűvel, számmal vagy aláhúzásjellel kell végződnie. Felső vagy kisbetűket is tartalmaz. |
| **hely** |Azure-beli hely (más néven régió). |Az érvényes Azure helyek valamelyikén kell lennie. |
| **addressPrefix** |Az alhálózat CIDR-jelöléssel alkotó egyetlen címelőtag |Egy egyetlen CIDR-blokkja címterületeket a virtuális hálózat egyik részét képező kell lennie. |
| **hálózati biztonsági csoporthoz tartozik** |Az alhálózat alkalmazott NSG | |
| **Migrálták** |Az útvonaltábla alkalmazva | |
| **IP-konfigurációk** |Az alhálózathoz csatlakoztatott hálózati adapter által használt IP-konfigurációs objektumok gyűjteménye | |

### <a name="name-resolution"></a>Névfeloldás
Alapértelmezés szerint a virtuális hálózat használja [Azure által biztosított névfeloldás](virtual-networks-name-resolution-for-vms-and-role-instances.md) feloldani a virtuális hálózaton belül, és a nyilvános interneten. Azonban ha a Vnetek csatlakozhat a helyszíni adatközpont, meg kell adni [a saját DNS-kiszolgáló](virtual-networks-name-resolution-for-vms-and-role-instances.md) feloldani a hálózatok között.  

### <a name="limits"></a>Korlátok
Tekintse át a hálózati korlátok a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikk annak érdekében, hogy a tervező nem ütközik a határokon. Néhány korlátot lehet növelni egy támogatási jegy megnyitásával.

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
Használhat [Azure RBAC](../active-directory/role-based-access-built-in-roles.md) vezérelhető a különböző felhasználók úgy lehet, hogy az Azure-ban a különböző erőforrások hozzáférési szint. Ily módon is elkülönítse a csapat a igényeiknek dolgozott.

Csakúgy, mint a virtuális hálózatok az érintett felhasználók számára a **hálózat közreműködő** szerepkör Azure Resource Manager virtuális hálózati erőforrások teljes hozzáféréssel rendelkeznek. Hasonlóképpen, a felhasználók a **klasszikus hálózat közreműködő** szerepkör klasszikus virtuális hálózati erőforrások teljes hozzáféréssel rendelkeznek.

> [!NOTE]
> Emellett [saját szerepköröket hozhat létre](../active-directory/role-based-access-control-configure.md) külön felügyeleti igényeinek.
>
>

## <a name="design"></a>Tervezés
Miután eldöntötte, hogy a kérdésekre adott válaszokat a [megtervezése](#Plan) szakaszban, tekintse át az alábbiakat, mielőtt meghatározná a Vnetek.

### <a name="number-of-subscriptions-and-vnets"></a>Az előfizetések és Vnetek száma
Érdemes lehet több Vnetek létrehozása a következő esetekben:

* **Virtuális gép között kell lenniük az Azure különböző helyeken**. Az Azure Vnetekhez regionális. Ezek nem terjedhetnek ki helyét. Ezért szükség van legalább egy virtuális hálózat minden egyes állomás virtuális gépek a kívánt Azure-beli hely.
* **Munkaterhelések, csak teljesen elkülönülnek egymástól**. Is használhatja a azonos IP-címterek elkülöníti egymástól a különböző terhelésekhez Vnetek hozhat létre.

Ne feledje, hogy a fent látható korlátai régiónként, amelyek. Ez azt jelenti, hogy több előfizetéssel segítségével kezelheti az Azure-erőforrások a korlát növeléséhez. Használhatja a telephelyek közötti VPN, vagy egy ExpressRoute-kapcsolatcsoportot Vnetekhez különböző előfizetésekhez való kapcsolódáshoz.

### <a name="subscription-and-vnet-design-patterns"></a>Előfizetés és hálózatok tervezési minták
Az alábbi táblázat néhány gyakori tervminták előfizetéssel és Vnetek jeleníti meg.

| Forgatókönyv | Ábra | Informatikai szakemberek | Hátrányok |
| --- | --- | --- | --- |
| Egyetlen előfizetés, két Vnetek alkalmazásonkénti |![Egyetlen előfizetés](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Csak egy előfizetése kezeléséhez. |Maximális száma Vnetek Azure-régiót. Legalább egy előfizetésre van szüksége, amely után. Tekintse át a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikkben alább. |
| Alkalmazásonkénti alkalmazásonkénti két Vnetek egy előfizetés |![Egyetlen előfizetés](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Előfizetésenként csak két Vnetek használja. |Nehezebben, ha túl sok alkalmazásokat kezeléséhez. |
| Egy előfizetés üzleti egység, két Vnetek alkalmazásonkénti. |![Egyetlen előfizetés](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Előfizetések számának és a Vnetek közötti egyensúly. |Maximális száma Vnetek részleg (előfizetés). Tekintse át a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikkben alább. |
| Egy előfizetés üzleti egység, az egyes alkalmazások két Vnetek. |![Egyetlen előfizetés](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Előfizetések számának és a Vnetek közötti egyensúly. |Alkalmazások kell lehet különíteni alhálózatokat és az NSG-ket. |

### <a name="number-of-subnets"></a>Alhálózatok száma
A következő esetekben a Vneten belül több alhálózaton kell figyelembe vennie:

* **Az alhálózat összes hálózati adapter nem elég saját IP-címek**. Ha a alhálózati cím tárhely nem található elegendő IP-címet a hálózati adapterek száma az alhálózat, hozzon létre több alhálózattal szeretné. Ne feledje, hogy Azure fenntartja az egyes alhálózatokon, amelyek nem használhatók 5 magánhálózati IP-címek: az első és utolsó által a címtartomány (az alhálózat címét, és a csoportos küldés) és 3 címeit belső (célokra DHCP és DNS-) használható.
* **Biztonság**. Alhálózatok segítségével virtuális elválasztása egymástól, amelyek rendelkeznek egy többrétegű struktúra, és alkalmazza a különböző munkaterhelések [hálózati biztonsági csoportokkal (NSG-k)](virtual-networks-nsg.md#subnets) ezek alhálózatok esetében.
* **A hibrid kapcsolat**. VPN-átjárók és az ExpressRoute-Kapcsolatcsoportok [csatlakozás](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) a Vnetek egymással, és a helyszíni adatok center(s). VPN-átjárók és az ExpressRoute-Kapcsolatcsoportok kell létrehozni saját alhálózat.
* **Virtuális készülékek**. Egy Azure virtuális hálózatot a virtuális készülék, például tűzfalat, WAN gyorsító vagy VPN-átjáró használható. Ha ezt választja, meg kell [forgalmat](virtual-networks-udr-overview.md) e készülékek számára és a saját alhálózat elkülöníti azokat.

### <a name="subnet-and-nsg-design-patterns"></a>Alhálózat és NSG-tervezési minták
Az alábbi táblázat néhány gyakori tervminták alhálózatok használatával.

| Forgatókönyv | Ábra | Informatikai szakemberek | Hátrányok |
| --- | --- | --- | --- |
| Egyetlen alhálózattal, NSG-k száma alkalmazásonként-alkalmazási rétegben |![Egyetlen alhálózattal](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Csak egy alhálózat kezeléséhez. |Több NSG-ket különítheti el az egyes alkalmazásokhoz szükséges. |
| Egy alhálózat alkalmazásonkénti alkalmazás rétegenként NSG-k |![Alkalmazásonkénti alhálózati](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Kevesebb NSG-k kezelése. |Több alhálózattal kezeléséhez. |
| Egy alhálózat alkalmazás rétegenként alkalmazásonkénti NSG-ket. |![Rétegenként alhálózati](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Kiegyenlítheti száma alhálózat és NSG-k között. |Előfizetésenként NSG-k maximális száma. Tekintse át a [Azure korlátozza](../azure-subscription-service-limits.md#networking-limits) cikkben alább. |
| Alkalmazás rétegenként alkalmazásonkénti NSG-k száma alhálózat több alhálózattal |![Alkalmazásonkénti rétegenként alhálózati](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Valószínűleg kisebb NSG-k száma. |Több alhálózattal kezeléséhez. |

## <a name="sample-design"></a>A minta-Tervező
Az alkalmazás ebben a cikkben szereplő információk mutatja be, a következő eset.

A vállalatnál, amelyen 2 adatközpontok Észak-Amerikában, és két adatközpontok Európa dolgozik. 6 különböző ügyfél irányuló alkalmazások különböző 2 által fenntartott meghatározott üzleti egység az Azure-bA a próbaüzem áttelepíteni kívánt. Az alkalmazások alapszintű architektúrája a következők:

* Az App1, App2, App3 és App4 futtató Ubuntu Linux-kiszolgálókon lévő webalkalmazások. Minden alkalmazás csatlakozik egy önálló kiszolgáló, amely futtatja a RESTful-szolgáltatásokat a Linux-kiszolgálókon. A RESTful-szolgáltatásokat háttérből MySQL-adatbázis csatlakozni.
* App5 és App6 is a Windows a Windows Server 2012 R2 rendszerű kiszolgálókon tárolt webes alkalmazásokhoz. Minden alkalmazás háttérben SQL Server-adatbázis csatlakozik.
* Minden alkalmazás jelenleg üzemelteti a vállalati adatközpontok Észak-Amerikában egyikében.
* A helyszíni adatközpontokban a 10.0.0.0/8 címterület használata.

Kell terveznie a virtuális hálózati megoldás, amely megfelel az alábbi követelményeknek:

* Mindegyik üzleti egység más üzleti egységek hálózatierőforrás-fogyasztás nem érinti.
* Akkor érdemes a lehető legkevesebb Vnetek és alhálózatok kezelésének megkönnyítése.
* Mindegyik üzleti egység egy teszt/fejlesztői VNet összes alkalmazás használt kell rendelkeznie.
* Minden alkalmazás 2 különböző Azure-adatközpont / kontinensen (Észak-Amerikából és Európából végzik a munkájukat) helyezkedik el.
* Minden alkalmazás teljesen elkülönülnek egymástól.
* Minden alkalmazás elérhetők, az ügyfelek HTTP használatával az interneten keresztül.
* Minden alkalmazás elérhető egy titkosított alagúton keresztül csatlakoznak a helyszíni adatközpontok felhasználók.
* Kapcsolat a helyszíni adatközpontokban használjon meglévő VPN-eszközök.
* A vállalati hálózati csoport a VNet konfigurációját teljes hozzáféréssel kell rendelkeznie.
* Mindegyik üzleti egység a fejlesztők használatuk csak akkor tudja telepíteni a virtuális gépek meglévő alhálózatokra.
* Minden alkalmazást telepíti át, mivel ezek az Azure-ba (növekedési-és-shift).
* Az egyes helyeken az adatbázisokat naponta egyszer kell replikálni Azure helyekre.
* Minden alkalmazás kell használni, 5 előtér-webkiszolgálók, 2 alkalmazáskiszolgálók (ha szükséges) és 2 adatbázis-kiszolgálókhoz.

### <a name="plan"></a>Felkészülés
Először meg kell válaszolnia a Tervező tervezése az a kérdés megválaszolásával a [követelményeinek meghatározása](#Define-requirements) szakaszban, ahogy alább látható.

1. Milyen Azure helyek használatával állomás Vnetekhez?

    Észak-Amerikában 2 helyeket, és az Európai 2 helyek. Ki kell választania azokat a meglévő helyszíni adatközpontok fizikai helye alapján. Így a kapcsolat a fizikai helyek és Azure jobb késleltetése lesz.
2. Meg kell adnia a következő Azure helyek közötti kommunikáció?

    Igen. Mivel az adatbázisok replikálni kell az összes helyen.
3. Meg kell adnia a Azure VNet(s) és a helyszíni közötti kommunikáció adatok center(s)?

    Igen. Mivel felhasználók kapcsolódnak a helyszíni adatközpontok kell tudni hozzáférni az alkalmazások egy titkosított alagúton keresztül.
4. Hány IaaS virtuális gépeket kell megoldást?

    200 IaaS virtuális gépeket. Az App1, App2, App3 és App4 minden 5 webkiszolgálók, minden egyes 2 alkalmazáskiszolgálók és szükséges 2 adatbázis-kiszolgálókhoz minden. Ez összesen alkalmazásonként 9 IaaS virtuális gépeket, vagy 36 IaaS virtuális gépeket. App5 és App6 5 webkiszolgálókat és a 2 adatbázis-kiszolgálókhoz minden szükséges. Ez összesen alkalmazásonként 7 IaaS virtuális gépeket, illetve 14 IaaS virtuális gépeket. Ezért figyelembe kell 50 IaaS virtuális gépek minden egyes Azure-régióban lévő alkalmazásokhoz. 4 régiók használni kell, mert nem lesznek 200 IaaS virtuális gépeket.

    Akkor adja meg minden egyes virtuális hálózatot, vagy a helyszíni adatközpontokban, amelyek az Azure IaaS virtuális gépeket és a helyszíni hálózat között név feloldása DNS-kiszolgálókat is.
5. El kell különítenie a forgalom virtuális (azaz előtér-webkiszolgáló és háttérbeli adatbázis-kiszolgálók) alapján?

    Igen. Minden alkalmazás legyen teljesen elkülönülnek egymástól, és minden alkalmazásréteg is el kell különíteni.
6. Virtuális készülékek használata adatforgalom szabályozásához kell?

    Nem. Virtuális készülékek segítségével adja meg a forgalom áramlását – beleértve a részletes adatok vezérlősík naplózási teljesebb körű vezérlése.
7. Felhasználók más-más részhalmazához különböző Azure-erőforrások engedélyek igényelnek?

    Igen. A hálózati csoport teljes hozzáférést a virtuális hálózati beállításokat kell, közben fejlesztők csak nem tudja telepíteni a virtuális Gépeik a már meglévő alhálózatokra.

### <a name="design"></a>Tervezés
A Tervező előfizetések, Vnetek, alhálózatok és egyéb NSG-ket kell követnie. Itt ismertetik az NSG-k, de kapcsolatos kell további [NSG-k](virtual-networks-nsg.md) a tervezés befejezése előtt.

**Az előfizetések és Vnetek száma**

Az alábbi követelmények előfizetések és Vnetek kapcsolódnak:

* Mindegyik üzleti egység más üzleti egységek hálózatierőforrás-fogyasztás nem érinti.
* A virtuális hálózatokat és alhálózatokat mennyisége kell minimálisra csökkenthető.
* Mindegyik üzleti egység egy teszt/fejlesztői VNet összes alkalmazás használt kell rendelkeznie.
* Minden alkalmazás 2 különböző Azure-adatközpont / kontinensen (Észak-Amerikából és Európából végzik a munkájukat) helyezkedik el.

Ezek a követelmények, meg kell egy előfizetési részlegek számára. Így az üzleti egységek az erőforrások fogyasztásának is nem beleszámít korlátok egyéb üzleti egységek számára. És mivel Vnetek számának minimalizálása érdekében szeretné, érdemes lehet használni a **üzleti egység, az egyes alkalmazások két Vnetek egy előfizetés** mintát, az alább látható módon.

![Egyetlen előfizetés](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Meg kell adnia a a címterület minden vnet is. Mivel kell a helyszíni adatok közötti kapcsolat adatközpontok Azure-régiókban, a használt Azure Vnet-terület nem ütközhetnek a helyszíni hálózat és minden egyes virtuális hálózat által használt címterület nem kell más meglévő Vnetek ütközhetnek. A címterek, az alábbi táblázat segítségével ezeknek a követelményeknek.  

| **Előfizetés** | **Virtuális hálózat** | **Az Azure-régió** | **Címtér** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |USA nyugati régiója |172.16.0.0/16 |
| BU1 |ProdBU1US2 |USA keleti régiója |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Észak-Európa |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Nyugat-Európa |172.19.0.0/16 |
| BU1 |TestDevBU1 |USA nyugati régiója |172.20.0.0/16 |
| BU2 |TestDevBU2 |USA nyugati régiója |172.21.0.0/16 |
| BU2 |ProdBU2US1 |USA nyugati régiója |172.22.0.0/16 |
| BU2 |ProdBU2US2 |USA keleti régiója |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Észak-Európa |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Nyugat-Európa |172.25.0.0/16 |

**Alhálózatokat és az NSG-k száma**

Az alábbi követelmények alhálózatokat és az NSG-ket kapcsolódó:

* A virtuális hálózatokat és alhálózatokat mennyisége kell minimálisra csökkenthető.
* Minden alkalmazás teljesen elkülönülnek egymástól.
* Minden alkalmazás elérhetők, az ügyfelek HTTP használatával az interneten keresztül.
* Minden alkalmazás elérhető egy titkosított alagúton keresztül csatlakoznak a helyszíni adatközpontok felhasználók.
* Kapcsolat a helyszíni adatközpontokban használjon meglévő VPN-eszközök.
* Az egyes helyeken az adatbázisokat naponta egyszer kell replikálni Azure helyekre.

Ezek a követelmények alapján, akkor képes használni, egy alkalmazás rétegenként, és NSG-k segítségével alkalmazásonként forgalmának szűrésére. Ezzel a módszerrel csak akkor kell minden egyes virtuális hálózat (előtér alkalmazásréteg és adatrétege) 3 alhálózatai és alkalmazásonként száma alhálózat egy NSG. Ebben az esetben érdemes lehet használni a **alkalmazás rétegenként alkalmazásonkénti NSG-ket egy alhálózat** tervezési minta alapján. Az alábbi ábra a kialakítási mintában képviselő használatát a **ProdBU1US1** virtuális hálózat.

![Rétegenként rétegenként alkalmazásonként egy NSG több alhálózattal](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Azonban akkor is szeretne létrehozni egy külön alhálózatot a VPN-kapcsolatot a virtuális hálózatokat, és a helyszíni adatközpont között. És meg kell adnia a címtér az egyes alhálózatokon. Az alábbi ábra egy minta megoldást mutat a **ProdBU1US1** virtuális hálózat. Ebben a forgatókönyvben minden vnet replikálna. A szín jelöli egy másik alkalmazás.

![A minta virtuális hálózat](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Hozzáférés-vezérlés**

Az alábbi követelmények kapcsolódó hozzáférés-vezérlés:

* A vállalati hálózati csoport a VNet konfigurációját teljes hozzáféréssel kell rendelkeznie.
* Mindegyik üzleti egység a fejlesztők használatuk csak akkor tudja telepíteni a virtuális gépek meglévő alhálózatokra.

Ezek a követelmények alapján sikerült felhasználókat adja hozzá a hálózati csapat a beépített **hálózat közreműködő** minden előfizetésben; szerepkör, és hozzon létre egy egyéni biztonsági szerepkört minden előfizetésben jogok jogosultságot ad az alkalmazásfejlesztők virtuális gépek hozzáadása meglévő alhálózatokat.

## <a name="next-steps"></a>Következő lépések
* [Telepíthet egy virtuális hálózatot](virtual-networks-create-vnet-arm-template-click.md) eset alapján.
* Megértéséhez hogyan [terheléselosztásához](../load-balancer/load-balancer-overview.md) IaaS virtuális gépeket és [kezelése az útválasztást a több Azure-régiók](../traffic-manager/traffic-manager-overview.md).
* További információ [NSG-ket és a tervezés és kialakítás](virtual-networks-nsg.md) egy NSG-megoldáshoz.
* További információ a [létesítmények közötti és VNet kapcsolati lehetőségek](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
