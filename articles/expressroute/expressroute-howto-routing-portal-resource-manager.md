<properties
   pageTitle="ExpressRoute-kapcsolatcsoport útválasztásának konfigurálása az Azure Portal használatával | Microsoft Azure"
   description="A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc"/>

# ExpressRoute-kapcsolatcsoport útválasztásának létrehozása és módosítása



> [AZURE.SELECTOR]
[Azure Portal – Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell – Resource Manager](expressroute-howto-routing-arm.md)
[PowerShell – Klasszikus](expressroute-howto-routing-classic.md)



A cikk az ExpressRoute-kapcsolatcsoportok útválasztási konfigurációjának létrehozási és módosítási lépéseit ismerteti az Azure Portal és a Resource Manager-alapú üzemi modell használatával.

**Tudnivalók az Azure üzembehelyezési modellekről**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Konfigurációs előfeltételek

- A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
- Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-arm.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoportnak kiosztott és engedélyezett állapotban kell lennie, hogy az alább ismertetett parancsmagokat futtatni lehessen.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha 3. rétegbeli szolgáltatásokat kínáló szolgáltatóval rendelkezik (tipikusan IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálja és felügyeli az útválasztást Ön helyett. 


>[AZURE.IMPORTANT] A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. Mielőtt konfigurálná a BGP társviszony-létesítéseket, tájékozódjon szolgáltatójánál.

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. 

## Azure privát társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

    - Egy /30 alhálózat az elsődleges kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
    - Egy /30 alhálózat a másodlagos kapcsolat számára. Ez nem képezheti semmilyen, virtuális hálózatok számára lefoglalt címtér részét.
    - Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
    - Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
    - Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.


3. Válassza ki az Azure privát társviszony-létesítési sort, amint alább látható.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
    

4. Konfigurálja a privát társviszony-létesítést. Az alábbi kép egy példakonfigurációt mutat be.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

    
5. Mentse a konfigurációt, miután megadta az összes paramétert. A konfiguráció sikeres elfogadása után az alábbi példához hasonló tartalom jelenik meg.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)
    

### Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)


### Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját az alább látható törlés ikonra kattintva törölheti.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)


## Azure nyilvános társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### Azure nyilvános társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

    - Egy /30 alhálózat az elsődleges kapcsolat számára. 
    - Egy /30 alhálózat a másodlagos kapcsolat számára. 
    - A társviszony-létesítés létrehozásához használt minden IP-címnek érvényes nyilvános IPv4-címnek kell lennie.
    - Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
    - Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
    - Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező**.

3. Válassza ki az Azure nyilvános társviszony-létesítés sort, amint alább látható.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
    

4. Konfigurálja a nyilvános társviszony-létesítést. Az alábbi kép egy példakonfigurációt mutat be.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

    
5. Mentse a konfigurációt, miután megadta az összes paramétert. A konfiguráció sikeres elfogadása után az alábbi példához hasonló tartalom jelenik meg.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)
    

### Azure nyilvános társviszony-létesítés részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)


### Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítés konfigurációját az alább látható törlés ikonra kattintva törölheti.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)


## Microsoft társviszony-létesítés

Ez a szakasz tartalmazza az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának létrehozására, lekérésére, frissítésére és törlésére vonatkozó utasításokat. 

### Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot.

    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

    - Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
    - Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
    - Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
    - Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
    - **Meghirdetett előtagok**: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha több előtagot kíván megadni, vesszővel tagolt listában adhatja meg őket. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
    - **Ügyfél ASN**: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-számra vannak regisztrálva, megadhatja az AS-számot, amelyre regisztrálva vannak. **Ez nem kötelező**.
    - **Útválasztási jegyzék neve**: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak. **Ez nem kötelező.**
    - Egy MD5-kivonat, ha használni kívánja. **Ez nem kötelező.**
    
3. A konfigurálni kívánt társviszony-létesítést az alábbiakban bemutatott módon választhatja ki. Válassza ki a Microsoft társviszony-létesítés sorát.
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
    

4.  Konfigurálja a Microsoft társviszony-beállítást. Az alábbi kép egy példakonfigurációt mutat be.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)

    
5. Mentse a konfigurációt, miután megadta az összes paramétert. 

    Ha a kapcsolatcsoport érvényesítést igénylő állapotba kerül (amint az alább látható), küldjön egy hibajegyet támogatási csapatunknak, amelyben bemutatja az előtagok tulajdonjogának igazolását.  
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)


    A hibajegyet közvetlenül a portálról is megnyithatja, amint az alább látható.   
    
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


6. A konfiguráció sikeres elfogadása után az alábbi példához hasonló tartalom jelenik meg.

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)
    

### Microsoft társviszony-létesítés részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)


### Microsoft társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. 


![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)


### Microsoft társviszony-létesítés törlése

A társviszony-létesítés konfigurációját az alább látható törlés ikonra kattintva törölheti.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)



## Következő lépések

A következő lépés egy [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md).

-  Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).

-  A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).

-  További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).




<!--HONumber=sep16_HO1-->


