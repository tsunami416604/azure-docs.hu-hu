## <a name="service-chaining-transit-through-peered-vnet"></a>Szolgáltatásláncolás – Átvitel társított virtuális hálózatokon keresztül
Bár a rendszerútvonalak használata automatikusan segíti az üzemelő példány forgalmát, vannak esetek, amikor a csomagok útválasztását érdemesebb egy virtuális készüléken keresztül irányítani.
Ebben a forgatókönyvben két virtuális hálózat, a HubVNet és a VNet1 található egy előfizetésben, ahogy az alábbi ábrán látható. A HubVNet virtuális hálózaton fog üzembe helyezni egy hálózati virtuális készüléket (NVA-t). A HubVNet és a VNet1 közötti társviszony létesítése után beállíthatja a felhasználó által megadott útvonalakat, és meghatározhatja, hogy a HubVNeten a következő ugrás célja az NVA legyen.

![NVA-átvitel](./media/virtual-networks-create-vnetpeering-scenario-transit-include/figure01.PNG)

> [!NOTE]
> Az egyszerűség végett azt feltételezzük, hogy mindegyik virtuális hálózat ugyanabban az előfizetésben található. Ennek ellenére az átvitel az eltérő előfizetésekre vonatkozó forgatókönyv esetén is működik.
> 
> 

Az átvitel útválasztásának engedélyezése a „Továbbított forgalom engedélyezése” paraméterrel lehetséges. Ez teszi lehetővé, hogy a társított virtuális hálózat adatokat fogadjon az NVA-tól és adatokat küldjön az NVA-nak.  



<!--HONumber=Nov16_HO2-->


